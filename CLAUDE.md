# CLAUDE.md — n8n-claw-templates

This file guides AI assistants (Claude Code, Copilot, Cursor, Antigravity, etc.)
when creating or modifying templates in this repository.

---

## What is this repo?

A template library for **n8n MCP skill workflows**, serving two purposes:

1. **General-purpose tools** — reusable n8n skills (weather, search, calendar, etc.)
   that any n8n-claw-pattern agent can install via chat command
2. **CTI-specific skills** — threat intelligence tools for the
   [ThreatLabs unified stack](https://github.com/JazenaYLA/n8n-ecosystem-unified)
   integrating with MISP, OpenCTI, TheHive, Cortex, Wazuh, and Flowise

This repo is maintained by [@JazenaYLA](https://github.com/JazenaYLA).
It accepts **upstream general template contributions** from the n8n community
but CTI templates (`cti-` prefix) are owned by this project.

---

## Project Context

This template library is consumed by:
- **[n8n-ecosystem-unified](https://github.com/JazenaYLA/n8n-ecosystem-unified)**
  — the main n8n + CTI automation stack (Proxmox LXC homelab)
- Any n8n instance using the MCP skill pattern

The main agent fetches skill JSON from this repo, deploys it into n8n via API,
and wires credentials via the `credential-form` workflow.

---

## Template Structure

Every template consists of 3 files:

```
templates/
  index.json                     <- catalog (one entry per template)
  {template-id}/
    manifest.json                <- metadata (name, tools, credentials)
    workflow.json                <- n8n workflow bundle
    schema.sql                   <- (CTI templates only) required DB tables
```

**Template ID rules:**
- Lowercase with hyphens only
- CTI templates must be prefixed: `cti-`
- General tools use the service name: `weather-openmeteo`, `github-api`, etc.

---

## Two-Workflow Pattern

Every template contains **two workflows** in `workflow.json`:

1. **`sub`** — Sub-workflow with actual tool logic
   (`executeWorkflowTrigger` -> `Code` node)
2. **`server`** — MCP server that exposes the tool
   (`mcpTrigger` -> `toolWorkflow`)

**Why two workflows?** n8n's API ignores `specifyInputSchema` when creating
workflows via API. The `toolWorkflow` + sub-workflow pattern avoids this bug —
parameters arrive via `$json.param` which always works.

The Library Manager imports `sub` first, gets its ID, patches
`REPLACE_SUB_WORKFLOW_ID` in `server`, then imports `server`.

---

## Critical Code Rules

### HTTP Requests
```javascript
// CORRECT -- use helpers (no $)
const data = await helpers.httpRequest({ method: 'GET', url: '...' });

// WRONG -- $helpers is undefined in Code node v2
const data = await $helpers.httpRequest({ method: 'GET', url: '...' });
```

### Parameter Passing

In the **sub-workflow** Code node, parameters arrive via `$input`:
```javascript
const input = $input.first().json;
const city = input.city || 'Berlin';
```

In the **server workflow**, parameters are extracted from the AI's message:
```javascript
"city": "={{ $fromAI('city', 'City name', 'string') }}"
```

### Required vs Optional Parameters

Only mark `"required": true` if the tool genuinely cannot work without it.
Parameters with Code-node defaults must be `"required": false`.

### Connections

The toolWorkflow connects to mcpTrigger via `ai_tool`, not `main`:
```json
"connections": {
  "tool_name": {
    "ai_tool": [[{ "node": "MCP Server Trigger", "type": "ai_tool", "index": 0 }]]
  }
}
```

### Placeholders

- `REPLACE_SUB_WORKFLOW_ID` -- patched automatically by Library Manager during install
- The `path` in mcpTrigger must match the template ID

---

## Multi-LLM Support

Templates must be **LLM-agnostic**. Do not hardcode any LLM provider.
All model calls in Code nodes must:
- Accept a `model_provider` parameter (`ollama`, `gemini`, `anthropic`, `openrouter`, `openai`, `mistral`)
- Accept a `model_name` parameter with a sensible default
- Use n8n Variables for API keys/URLs (never hardcoded)

Example pattern for a skill that calls an LLM:
```javascript
const input = $input.first().json;
const provider = input.model_provider || 'openrouter';
const model = input.model_name || 'meta-llama/llama-3.1-8b-instruct:free';
const baseUrls = {
  ollama:     $vars.OLLAMA_URL + '/v1',
  gemini:     'https://generativelanguage.googleapis.com/v1beta',
  anthropic:  'https://api.anthropic.com/v1',
  openrouter: 'https://openrouter.ai/api/v1',
  openai:     'https://api.openai.com/v1',
  mistral:    'https://api.mistral.ai/v1',
};
const url = baseUrls[provider] + '/chat/completions';
```

For CTI skills that use LLMs for analysis/summarization, add these
optional parameters to the tool schema:
```json
{ "id": "model_provider", "type": "string", "required": false },
{ "id": "model_name",     "type": "string", "required": false }
```

---

## CTI Template Specification

CTI templates (`cti-` prefix) have additional requirements:

### Additional Files
- `schema.sql` -- Any Postgres tables the skill requires in the `n8n` database
- Include `CREATE TABLE IF NOT EXISTS` and `CREATE INDEX IF NOT EXISTS` statements
- Always use the `public` schema

### API URL Handling
All CTI API URLs must come from n8n Variables (never hardcoded):
```javascript
const mispUrl  = $vars.MISP_URL;      // e.g. https://misp.lab.local
const mispKey  = $vars.MISP_API_KEY;
const ctiUrl   = $vars.OPENCTI_URL;
```

### SSL Handling
CTI platforms are often self-hosted with self-signed certs. Always support:
```javascript
const verifySsl = (input.verify_ssl !== 'false');
// Pass to httpRequest: { rejectUnauthorized: verifySsl }
```

### Credential Form Fields
All CTI templates must include these standard fields in `manifest.json`:
```json
"credentials_required": [
  { "key": "<SERVICE>_URL",        "label": "Base URL",   "type": "url"     },
  { "key": "<SERVICE>_API_KEY",    "label": "API Key",    "type": "password" },
  { "key": "<SERVICE>_VERIFY_SSL", "label": "Verify SSL", "type": "boolean", "default": "true" }
]
```

---

## Upstream Tracking

This repo accepts contributions from the n8n community for general templates
(non-CTI). To track upstream additions without polluting the CTI work:

1. General templates go in `templates/{tool-name}/` -- no prefix
2. CTI templates go in `templates/cti-{tool-name}/` -- `cti-` prefix required
3. The `index.json` `category` field separates them:
   - `"category": "general"` -- upstream-compatible
   - `"category": "cti"` -- this project only
4. AI assistants must never modify `cti-*` templates unless the operator
   is working in `JazenaYLA/n8n-ecosystem-unified` context
5. To pull new upstream general templates:
   ```bash
   git remote add upstream https://github.com/freddy-schuetz/n8n-claw-templates
   git fetch upstream
   git merge upstream/master --allow-unrelated-histories
   # Resolve only conflicts in templates/ (non cti-* folders)
   ```

---

## Checklist for New Templates

### General Templates
- [ ] Template ID is lowercase with hyphens only (no `cti-` prefix)
- [ ] `manifest.json` has all required fields (see `TEMPLATE_EXAMPLE.md`)
- [ ] `workflow.json` uses format `n8n-claw-template` with `sub` and `server` keys
- [ ] Code uses `helpers.httpRequest()` (NOT `$helpers`)
- [ ] `REPLACE_SUB_WORKFLOW_ID` used as workflowId placeholder
- [ ] `index.json` entry added with `"category": "general"`
- [ ] All text in English
- [ ] No API keys hardcoded

### CTI Templates (additional checks)
- [ ] Template ID starts with `cti-`
- [ ] `index.json` entry uses `"category": "cti"`
- [ ] API URL from `$vars.<SERVICE>_URL` (not hardcoded)
- [ ] `schema.sql` provided if DB tables are needed
- [ ] SSL verification is configurable
- [ ] Multi-LLM support if skill calls an LLM
- [ ] Credential form fields follow the standard pattern

---

## Reference Files

- `templates/TEMPLATE_EXAMPLE.md` -- annotated example with all fields
- `templates/weather-openmeteo/` -- working general template reference
- [CTI Skills Roadmap](https://github.com/JazenaYLA/n8n-ecosystem-unified/blob/main/docs/CTI_SKILLS_ROADMAP.md) -- planned CTI skills with full specs
