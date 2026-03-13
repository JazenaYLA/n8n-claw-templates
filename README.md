# n8n-claw Skills

Skill catalog for [n8n-claw](https://github.com/freddy-schuetz/n8n-claw) MCP servers. Install pre-built API integrations with a single chat command — no coding required.

---

## Available Skills

### Communication

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| Email (IMAP/SMTP) | Read and send emails via IMAP/SMTP | Email address, password, IMAP host, SMTP host | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Entertainment

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Recipes](https://www.themealdb.com/) | Search recipes or get a random recipe with ingredients and instructions | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [TMDB Movies & TV](https://www.themoviedb.org/) | Search movies and TV shows, discover trending titles | TMDB API Key | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Trivia](https://opentdb.com/) | Random trivia questions with answers from various categories | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Finance

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Crypto Prices](https://www.coingecko.com/) | Get cryptocurrency prices, market cap, and 24h changes | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Exchange Rates](https://www.frankfurter.app/) | Convert currencies using live exchange rates | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [KontoFlux](https://kontoflux.io/) | Access German bank accounts and transactions via KontoFlux Open-Banking API (5000+ banks, read-only) | KontoFlux API Key, Workspace ID | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Knowledge

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [OpenFoodFacts](https://world.openfoodfacts.org/) | Look up food products by barcode or name — nutrition facts, Nutri-Score, allergens, ingredients | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Wikipedia](https://www.wikipedia.org/) | Search Wikipedia and get article summaries | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Language

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [DeepL Translate](https://www.deepl.com/) | Translate text between 30+ languages and detect languages | DeepL API Key | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Dictionary](https://dictionaryapi.dev/) | English word definitions, phonetics, and examples | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Meetings

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Vexa Meetings](https://vexa.ai/) | Deploy transcription bots to Google Meet, Teams, and Zoom meetings | Vexa API Key | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Network

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [IP Geolocation](https://ip-api.com/) | Get location, ISP, and org info for any IP address | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| Website Check | Website health check: load time, security headers, meta tags, structured data | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Productivity

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [GitHub](https://github.com/) | Full GitHub integration: repos, issues, PRs, code search, file content, releases, notifications | GitHub PAT | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Notion](https://www.notion.so/) | Search, read, and create Notion pages with markdown content support | Notion Integration Token | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Todoist](https://todoist.com/) | Manage tasks: list, create, complete, organize with due dates and priorities | Todoist API Token | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### News

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Hacker News](https://news.ycombinator.com/) | Search Hacker News stories and get top stories | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [NewsAPI](https://newsapi.org/) | Search news articles from 80,000+ sources | NewsAPI Key | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Reference

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Country Info](https://restcountries.com/) | Country details: capital, population, languages, currencies | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Public Holidays](https://date.nager.at/) | Look up public holidays for any country | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [Timezone & World Clock](https://timeapi.io/) | Get current time in any timezone and convert between timezones | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Transport

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Wiener Linien](https://www.wienerlinien.at/) | Plan routes with Vienna public transport, get your station ID from [this CSV File](https://data.wien.gv.at/csv/wienerlinien-ogd-haltestellen.csv) (yes, a CSV file, don't ask), more details on the [Routingservice Wien](https://www.data.gv.at/datasets/9c203fec-dc0d-412c-a7a3-7fd77d0346f1?locale=de) page | None | [@lcx](https://github.com/lcx) |

### Utilities

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [PDF Tools](https://pdf-mcp.io/) | Generate PDFs from HTML, extract text, merge PDFs | pdf-mcp.io API Key | [@freddy-schuetz](https://github.com/freddy-schuetz) |
| [QR Code](https://goqr.me/) | Generate QR codes from text or URLs | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

### Weather

| Skill | Description | Credentials | Author |
|-------|-------------|-------------|--------|
| [Weather](https://open-meteo.com/) | Current weather and 3-day forecast for any city | None | [@freddy-schuetz](https://github.com/freddy-schuetz) |

---

## Usage

Skills are managed via chat with your n8n-claw agent:

```
"What skills are available?"        → lists all skills
"Install weather-openmeteo"         → installs the skill
"Remove weather-openmeteo"          → uninstalls and cleans up
```

> **Important:** After installing a skill, open the n8n UI and **deactivate → reactivate** the new MCP server workflow. This is required due to a webhook registration bug in n8n.

### Skills with API keys

Some skills require API credentials. When you install one, the agent sends you a **secure one-time link** (valid 10 minutes) to enter your API key via HTTPS form. Your key is never visible in the chat.

```
"Install news-newsapi"                → installs + sends credential link
"Add credential for news-newsapi"     → generates a new credential link
```

> **⚠️ Security notice — Skill credentials are stored in plain text**
>
> API keys entered via the credential form are currently stored **unencrypted** in the `template_credentials` table in PostgreSQL. This means:
>
> - Anyone with access to the database can read all stored API keys
> - Supabase Studio (`localhost:3001`, accessible via SSH tunnel) shows credentials in plain text
> - A compromised VPS exposes all stored API keys
>
> **What an attacker would need:** Neither the database nor the API are reachable from the internet. PostgREST runs on a Docker-internal network only, and PostgreSQL (port 5432) is bound to `127.0.0.1`. To read credentials, an attacker would need SSH access to your VPS — there is no remote network path.
>
> **Mitigation:** Secure SSH access (key-based auth, no root password, fail2ban), and use API keys with minimal permissions where possible.
>
> Encryption at rest for skill credentials is planned and in progress.

### Skills with infrastructure requirements

Some skills need additional services running alongside n8n:

| Skill | Requires |
|----------|----------|
| `email-imap-smtp` | `email-bridge` service in docker-compose.yml (included in n8n-claw since v0.10) |

---

## Skill Types

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
