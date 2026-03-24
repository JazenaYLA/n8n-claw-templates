# Architecture Decisions — n8n-claw-templates

> This document records the design principles governing what belongs in this template library vs. what belongs directly in yaoc2/n8n-unified core.

---

## Decision 1: OS vs. App Store Split

**Rule:** Code that is tied to yaoc2's internal schema, Postgres tables, or policy engine belongs *directly in yaoc2*. Code that is portable, parameterizable, and could work in any n8n install belongs here as a template.

| Belongs in yaoc2 directly | Belongs in n8n-claw-templates |
|---|---|
| Capability Guard (checks yaoc2 env vars) | `cti-forgejo` (portable Forgejo bridge) |
| WhatsApp/Telegram ingress normalization to ProposedAction | `channel-bluesky` (portable AT Protocol adapter) |
| Policy evaluation, audit log writes | `github-api`, `cti-misp`, `cti-opencti` (portable API tools) |
| User profile seeding to `yaoc2.user_profiles` | LLM provider selector (openai-compat endpoint pattern) |
| Channel fallback routing (WhatsApp → Telegram) | Any standalone tool callable from *any* n8n AI Agent |

---

## Decision 2: Channel Adapter Contract (ChannelMessage)

All `channel-*` templates **must** output the following standard shape from their `normalize_event` tool. This is what yaoc2's policy gateway ingress expects:

```json
{
  "channel": "bluesky",
  "user_id": "<platform-native-id>",
  "display_name": "<human-readable-name>",
  "text": "<raw message text>",
  "thread_id": "<platform thread/conversation ID or null>",
  "reply_to": "<ID of parent message or null>",
  "timestamp": "<ISO 8601>"
}
```

**Platform ID conventions:**
- Bluesky: `did:plc:...` (DID)
- Twitter/X: numeric user ID
- Discord: `<server_id>/<channel_id>/<user_id>`
- WhatsApp (Evolution): phone number (e.g. `15551234567`)
- Telegram: numeric chat ID

Adding a new channel template means: implement `normalize_event` → output this shape → yaoc2 ingress works with zero changes.

---

## Decision 3: Template Naming Conventions

| Prefix | Category | Example |
|---|---|---|
| `cti-` | Internal CTI/threat-intel infra tools | `cti-forgejo`, `cti-misp`, `cti-opencti` |
| `channel-` | Comms channel adapters (normalize in, send out) | `channel-bluesky`, `channel-discord` |
| *(no prefix)* | General-purpose tools (existing library) | `github-api`, `weather-openmeteo` |

---

## Decision 4: LLM Provider Templates (Not Needed)

New LLM providers do **not** need a template if they offer an OpenAI-compatible endpoint (`/v1/chat/completions`). yaoc2's existing `OPENAI_COMPAT_BASE_URL` + `OPENAI_COMPAT_KEY` pattern in the Brain handles new providers (DeepSeek, Grok, etc.) via environment variable alone. Only providers with fundamentally different API shapes (e.g. a future non-REST LLM) would warrant a template.

---

## Decision 5: forgejo-mcp SSE Sidecar

The `cti-forgejo` template requires a running `forgejo-mcp` SSE server as a sidecar. The canonical Dockge compose stack for this lives in the `enterprise` branch of the internal Forgejo instance at `opt/stacks/forgejo-mcp/`. It builds from `codeberg.org/goern/forgejo-mcp`, patches SSL verification for internal CAs, and exposes port 8080. The GitHub mirror at `github.com/JazenaYLA/forgejo-mcp` is read-only; all authoritative changes go to the internal instance first.
