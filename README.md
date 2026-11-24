# Subtext — Remote YouTube Transcript MCP Server & Agent Skill

Read public YouTube video transcripts as plain text, Markdown, SRT, WebVTT, or timestamped JSON directly from **Claude**, **ChatGPT**, **Claude Code**, **Cursor**, or any MCP / Agent Skills client.

- **Remote Streamable HTTP MCP server** (`https://youtubevideototranscript.com/mcp`) — no local `yt-dlp`, Python, or Node runtime to maintain, and no cloud IP blocks (`RequestBlocked` / `IpBlocked`).
- **OAuth 2.1 or API key** — connect with one URL in clients that support MCP OAuth, or use a free Bearer API key (`stx_...`).
- **Free starter credits** — 100 free credits when creating an account on the web ([youtubevideototranscript.com/account](https://youtubevideototranscript.com/account)), or 25 free credits via the zero-browser agent email-code flow.
- **Free web reader** — human visitors can read, search, copy for AI, and download transcripts free with no account at [youtubevideototranscript.com](https://youtubevideototranscript.com).

## Option 1: Remote MCP Server (OAuth — easiest)

In MCP clients that support remote OAuth 2.1 (Claude, ChatGPT, Cursor, etc.), add a single remote server URL with no API key:

```text
https://youtubevideototranscript.com/mcp
```

## Option 2: Remote MCP Server (API key)

Create a free API key at [youtubevideototranscript.com/account](https://youtubevideototranscript.com/account) (100 free credits) and export it as `SUBTEXT_API_KEY`.

### Claude Code (CLI)

```bash
claude mcp add --transport http subtext https://youtubevideototranscript.com/mcp \
  --header "Authorization: Bearer $SUBTEXT_API_KEY"
```

Or copy [`mcp/claude-code.mcp.json`](mcp/claude-code.mcp.json) into your project's `.mcp.json`.

### Cursor

Copy [`mcp/cursor.mcp.json`](mcp/cursor.mcp.json) into `.cursor/mcp.json` or `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "subtext": {
      "url": "https://youtubevideototranscript.com/mcp",
      "headers": {
        "Authorization": "Bearer ${env:SUBTEXT_API_KEY}"
      }
    }
  }
}
```

## Option 3: Agent Skill (`SKILL.md` for Claude Code, Cursor, Codex, OpenClaw, Hermes)

Install the portable [`agentskills.io`](https://agentskills.io) skill (uses plain `curl`, no MCP setup required, and can create a free 25-credit API key by email code):

```bash
npx skills add vsaranyuk/youtube-transcript-mcp
```

Skill definition: [`skills/youtube-transcript/SKILL.md`](skills/youtube-transcript/SKILL.md).

## Available MCP Tools

| Tool | Cost | Description |
| --- | --- | --- |
| `get_transcript` | 1 credit | Fetch captions for a YouTube URL or 11-character video ID in `json`, `text`, `md`, `srt`, or `vtt` (optional `lang`, `translate`, `timestamps`). Handles YouTube auto-dubbed tracks by selecting the original audio track by default. |
| `list_languages` | Free | List available caption tracks and translatable languages for a video. |

## REST API & Machine-Readable Docs

- Web UI (free, no account): [https://youtubevideototranscript.com](https://youtubevideototranscript.com)
- API & MCP docs (HTML): [https://youtubevideototranscript.com/api](https://youtubevideototranscript.com/api)
- API docs (Markdown for agents): [https://youtubevideototranscript.com/api.md](https://youtubevideototranscript.com/api.md)
- OpenAPI 3.1 schema: [https://youtubevideototranscript.com/api/openapi.json](https://youtubevideototranscript.com/api/openapi.json)
- `llms.txt`: [https://youtubevideototranscript.com/llms.txt](https://youtubevideototranscript.com/llms.txt)
- Pricing: [https://youtubevideototranscript.com/pricing](https://youtubevideototranscript.com/pricing)

## Registry Metadata

- Official MCP Registry name: `com.youtubevideototranscript/youtube-transcript` ([`server.json`](server.json))

## License

[MIT](LICENSE)
