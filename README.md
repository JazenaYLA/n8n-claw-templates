# n8n-claw Templates

Template catalog for [n8n-claw](https://github.com/freddy-schuetz/n8n-claw) MCP servers. Install pre-built API integrations with a single chat command — no coding required.

---

## Available Templates

| Template | Category | Description | Credentials |
|----------|----------|-------------|-------------|
| [weather-openmeteo](templates/weather-openmeteo/) | weather | Current weather and 3-day forecast for any city | None |
| [wikipedia](templates/wikipedia/) | knowledge | Search Wikipedia and get article summaries | None |
| [exchange-rates](templates/exchange-rates/) | finance | Convert currencies using live exchange rates | None |
| [hackernews](templates/hackernews/) | news | Search Hacker News stories and get top stories | None |
| [dictionary](templates/dictionary/) | language | English word definitions, phonetics, and examples | None |
| [ip-geolocation](templates/ip-geolocation/) | network | Get location, ISP, and org info for any IP address | None |
| [website-check](templates/website-check/) | network | Website health check: load time, security headers, meta tags, structured data | None |
| [news-newsapi](templates/news-newsapi/) | news | Search news articles from 80,000+ sources | NewsAPI Key |
| [email-imap-smtp](templates/email-imap-smtp/) | communication | Read and send emails via IMAP/SMTP | Email address, password, IMAP host, SMTP host |
| [wiener-linien](templates/wiener-linien/) | transport | Plan routes with Vienna public transport (Wiener Linien), get your station ID from [this CSV File](https://data.wien.gv.at/csv/wienerlinien-ogd-haltestellen.csv) (yes, a CSV file, don't ask), more details can be found on the [Wiener Linien Routingservice Wien](https://www.data.gv.at/datasets/9c203fec-dc0d-412c-a7a3-7fd77d0346f1?locale=de) page | None |

---

## Usage

Templates are managed via chat with your n8n-claw agent:

```
"What templates are available?"     → lists all templates
"Install weather-openmeteo"         → installs the template
"Remove weather-openmeteo"          → uninstalls and cleans up
```

> **Important:** After installing a template, open the n8n UI and **deactivate → reactivate** the new MCP server workflow. This is required due to a webhook registration bug in n8n.

### Templates with API keys

Some templates require API credentials. When you install one, the agent sends you a **secure one-time link** (valid 10 minutes) to enter your API key via HTTPS form. Your key is never visible in the chat.

```
"Install news-newsapi"                → installs + sends credential link
"Add credential for news-newsapi"     → generates a new credential link
```

### Templates with infrastructure requirements

Some templates need additional services running alongside n8n:

| Template | Requires |
|----------|----------|
| `email-imap-smtp` | `email-bridge` service in docker-compose.yml (included in n8n-claw since v0.10) |

---

## Template Types

| Type | Description |
|------|-------------|
| `native` | n8n implements the tool logic directly (HTTP requests, Code nodes) |
| `bridge` | n8n proxies requests to an external MCP server (not yet supported) |

---

## Creating a Template

### Directory structure

Each template lives in its own directory under `templates/`:

```
templates/
  index.json                  ← catalog (add your template here)
  my-template/
    manifest.json             ← metadata (name, tools, credentials)
    workflow.json             ← n8n workflow bundle (two workflows)
    README.md                 ← optional: usage notes
```

### Step 1: Add to `index.json`

Add an entry to the `templates` array in [`templates/index.json`](templates/index.json):

```json
{
  "id": "my-template",
  "name": "My Template",
  "type": "native",
  "category": "utilities",
  "description": "Short description of what this template does",
  "credentials_required": [],
  "version": "1.0.0"
}
```

### Step 2: Create `manifest.json`

```json
{
  "id": "my-template",
  "name": "My Template",
  "version": "1.0.0",
  "updated": "2026-03-08",
  "type": "native",
  "category": "utilities",
  "description": "Short description of what this template does",
  "credentials_required": [],
  "credentials_optional": [
    {
      "key": "some_api_key",
      "label": "Some API Key",
      "hint": "Only needed for premium features"
    }
  ],
  "tools": [
    {
      "name": "my_tool",
      "description": "What this tool does"
    }
  ],
  "author": "your-github-username",
  "license": "MIT",
  "tested_n8n_version": "2.10.4"
}
```

#### Manifest fields

| Field | Required | Description |
|-------|----------|-------------|
| `id` | yes | Unique template ID (lowercase, hyphens only) |
| `name` | yes | Display name |
| `version` | yes | Semver version (e.g. `1.0.0`) |
| `updated` | yes | Last updated date (ISO format, e.g. `2026-03-08`) |
| `type` | yes | `native` or `bridge` |
| `category` | yes | Category for filtering (e.g. `weather`, `utilities`, `data`) |
| `description` | yes | Short description |
| `credentials_required` | yes | Array of credential keys needed (empty array if none) |
| `credentials_optional` | no | Array of optional credentials with hints |
| `tools` | yes | Array of tools this template provides (`name` + `description`) |
| `author` | yes | GitHub username |
| `license` | yes | License identifier (e.g. `MIT`) |
| `tested_n8n_version` | yes | n8n version this was tested on |

### Step 3: Create `workflow.json`

The workflow bundle uses the `n8n-claw-template` format. Every template consists of **two workflows**:

1. **Sub-workflow** (`sub`) — contains the actual tool logic (Code node)
2. **Server workflow** (`server`) — the MCP server that exposes the tool (mcpTrigger + toolWorkflow)

This two-workflow pattern is required because n8n's API ignores `specifyInputSchema` when creating workflows, so `toolCode` parameters don't work. The `toolWorkflow` pattern avoids this bug.

```json
{
  "format": "n8n-claw-template",
  "format_version": 1,
  "sub": {
    "name": "MCP Sub: My Template",
    "settings": {
      "executionOrder": "v1",
      "callerPolicy": "workflowsFromSameOwner"
    },
    "nodes": [
      {
        "id": "sub-trigger",
        "name": "Execute Workflow Trigger",
        "type": "n8n-nodes-base.executeWorkflowTrigger",
        "typeVersion": 1.1,
        "position": [0, 0],
        "parameters": { "inputSource": "passthrough" }
      },
      {
        "id": "sub-code",
        "name": "My Tool Logic",
        "type": "n8n-nodes-base.code",
        "typeVersion": 2,
        "position": [256, 0],
        "parameters": {
          "jsCode": "const input = $input.first().json;\nconst city = input.city || 'Berlin';\n\n// Make HTTP requests with helpers.httpRequest()\nconst data = await helpers.httpRequest({\n  method: 'GET',\n  url: 'https://api.example.com/data?q=' + encodeURIComponent(city)\n});\n\nreturn [{ json: { result: data } }];"
        }
      }
    ],
    "connections": {
      "Execute Workflow Trigger": {
        "main": [[{ "node": "My Tool Logic", "type": "main", "index": 0 }]]
      }
    }
  },
  "server": {
    "name": "MCP: My Template",
    "settings": { "executionOrder": "v1" },
    "nodes": [
      {
        "id": "mcp-trigger",
        "name": "MCP Server Trigger",
        "type": "@n8n/n8n-nodes-langchain.mcpTrigger",
        "typeVersion": 2,
        "position": [0, 0],
        "parameters": { "path": "my-template" }
      },
      {
        "id": "tool-wf",
        "name": "my_tool",
        "type": "@n8n/n8n-nodes-langchain.toolWorkflow",
        "typeVersion": 2.2,
        "position": [0, 300],
        "parameters": {
          "name": "my_tool",
          "description": "What this tool does. Parameter: city (city name)",
          "workflowId": {
            "__rl": true,
            "value": "REPLACE_SUB_WORKFLOW_ID",
            "mode": "id"
          },
          "workflowInputs": {
            "mappingMode": "defineBelow",
            "value": {
              "city": "={{ $fromAI('city', 'The city name', 'string') }}"
            },
            "matchingColumns": [],
            "schema": [
              {
                "id": "city",
                "displayName": "city",
                "type": "string",
                "description": "The city name",
                "required": true
              }
            ],
            "attemptToConvertTypes": false,
            "convertFieldsToString": false
          }
        }
      }
    ],
    "connections": {
      "my_tool": {
        "ai_tool": [[{ "node": "MCP Server Trigger", "type": "ai_tool", "index": 0 }]]
      }
    }
  }
}
```

### Key points

| Topic | Details |
|-------|---------|
| **HTTP requests** | Use `helpers.httpRequest()` in Code nodes — **not** `$helpers.httpRequest()` (undefined in Code node v2) |
| **Sub-workflow ID** | Use `REPLACE_SUB_WORKFLOW_ID` as placeholder — the Library Manager patches this automatically during install |
| **DB access** | Templates that read credentials use `{{SUPABASE_URL}}` and `{{SUPABASE_SERVICE_KEY}}` placeholders — replaced automatically during install |
| **MCP path** | The `path` in mcpTrigger should match your template ID |
| **Parameters** | Tool parameters arrive in the sub-workflow via `$input.first().json.paramName` |
| **`$fromAI()`** | Used in the server workflow to tell the AI agent which parameters to extract from the user's message |
| **Connections** | toolWorkflow connects to mcpTrigger via `ai_tool`, not `main` |

---

## Testing your template

Before submitting a pull request, test your template locally:

1. **Validate JSON** — ensure `manifest.json` and `workflow.json` are valid JSON
2. **Import manually** — import the sub-workflow and server workflow into your n8n instance via the API or UI
3. **Test the MCP server** — call the tool via the n8n-claw agent or directly via MCP client
4. **Check the response** — verify the tool returns useful data in the expected format

You can also test via the Library Manager if you push your template to a fork and temporarily change the CDN_BASE URL in the Library Manager workflow.

---

## Contributing

1. Fork this repository
2. Create a directory under `templates/` with your template ID
3. Add `manifest.json` and `workflow.json` (see above)
4. Add an entry to `templates/index.json`
5. Submit a pull request

### PR checklist

- [ ] Template ID is lowercase with hyphens only (e.g. `my-api-tool`)
- [ ] `manifest.json` has all required fields
- [ ] `workflow.json` uses `n8n-claw-template` format with `sub` and `server`
- [ ] `REPLACE_SUB_WORKFLOW_ID` is used as the workflowId placeholder
- [ ] Code uses `helpers.httpRequest()` (not `$helpers`)
- [ ] `index.json` entry matches manifest data
- [ ] Template tested on a live n8n instance
- [ ] All text in English

---

## CDN

Templates are served via jsDelivr CDN for fast, reliable delivery:

```
https://cdn.jsdelivr.net/gh/freddy-schuetz/n8n-claw-templates@master/templates/index.json
https://cdn.jsdelivr.net/gh/freddy-schuetz/n8n-claw-templates@master/templates/{id}/manifest.json
https://cdn.jsdelivr.net/gh/freddy-schuetz/n8n-claw-templates@master/templates/{id}/workflow.json
```

The CDN uses the `@master` branch reference. Cache updates may take a few minutes after pushing changes. For a reference of template files, see [`templates/TEMPLATE_EXAMPLE.md`](templates/TEMPLATE_EXAMPLE.md).

---

## License

MIT
