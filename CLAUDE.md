# CLAUDE.md — n8n-claw Templates

This file helps AI assistants (Claude Code, Copilot, etc.) create and contribute templates.

## What is this repo?

A template catalog for [n8n-claw](https://github.com/freddy-schuetz/n8n-claw). Each template is a pre-built MCP server that can be installed via chat command. For the main project architecture, see the [n8n-claw CLAUDE.md](https://github.com/freddy-schuetz/n8n-claw/blob/main/CLAUDE.md).

## Template Structure

Every template consists of 3 files:

```
templates/
  index.json                    ← catalog (one entry per template)
  {template-id}/
    manifest.json               ← metadata (name, tools, credentials)
    workflow.json               ← n8n workflow bundle
```

## Two-Workflow Pattern

Every template contains **two workflows** in `workflow.json`:

1. **`sub`** — Sub-workflow with actual tool logic (`executeWorkflowTrigger` → `Code` node)
2. **`server`** — MCP server that exposes the tool (`mcpTrigger` → `toolWorkflow`)

**Why two workflows?** n8n's API ignores `specifyInputSchema` when creating workflows via API. The `toolWorkflow` + sub-workflow pattern avoids this bug — parameters arrive via `$json.param` which always works.

The Library Manager imports `sub` first, gets its ID, patches `REPLACE_SUB_WORKFLOW_ID` in `server`, then imports `server`.

## Critical Rules

### HTTP Requests
```javascript
// CORRECT — use helpers (no $)
const data = await helpers.httpRequest({ method: 'GET', url: '...' });

// WRONG — $helpers is undefined in Code node v2
const data = await $helpers.httpRequest({ method: 'GET', url: '...' });
```

### Parameter Passing

In the **sub-workflow** Code node, parameters arrive via `$input`:
```javascript
const input = $input.first().json;
const city = input.city || 'Berlin';
```

In the **server workflow**, parameters are extracted from the user's message via `$fromAI()`:
```javascript
"city": "={{ $fromAI('city', 'City name', 'string') }}"
```

### Placeholders

- `REPLACE_SUB_WORKFLOW_ID` — patched automatically by Library Manager during install
- The `path` in mcpTrigger should match the template ID

### Connections

The toolWorkflow connects to mcpTrigger via `ai_tool`, not `main`:
```json
"connections": {
  "tool_name": {
    "ai_tool": [[{ "node": "MCP Server Trigger", "type": "ai_tool", "index": 0 }]]
  }
}
```

## Checklist for New Templates

- [ ] Template ID is lowercase with hyphens only
- [ ] `manifest.json` has all required fields (see `TEMPLATE_EXAMPLE.md`)
- [ ] `workflow.json` uses format `n8n-claw-template` with `sub` and `server` keys
- [ ] Code uses `helpers.httpRequest()` (NOT `$helpers`)
- [ ] `REPLACE_SUB_WORKFLOW_ID` used as workflowId placeholder
- [ ] `index.json` entry matches manifest data
- [ ] All text in English
- [ ] No API keys or credentials required (or clearly listed in `credentials_required`)

## Reference Files

- `templates/TEMPLATE_EXAMPLE.md` — annotated example with all fields explained
- `templates/weather-openmeteo/` — working reference template (free API, no credentials)
