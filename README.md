<!-- mcp-name: com.youtubevideototranscript/youtube-transcript -->

# Subtext — Remote YouTube Transcript MCP Server & Agent Skill

**Let your AI agent read any public YouTube video with captions — with one URL, OAuth 2.1, and zero local setup.**

[![Install in Cursor](https://img.shields.io/badge/Cursor-Install_MCP-000000?style=for-the-badge&logo=cursor&logoColor=white)](https://cursor.com/en/install-mcp?name=subtext&config=eyJ1cmwiOiJodHRwczovL3lvdXR1YmV2aWRlb3RvdHJhbnNjcmlwdC5jb20vbWNwIn0=)
[![Install in VS Code](https://img.shields.io/badge/VS_Code-Install_MCP-0098FF?style=for-the-badge&logo=visualstudiocode&logoColor=white)](https://insiders.vscode.dev/redirect?url=vscode%3Amcp%2Finstall%3F%7B%22name%22%3A%22subtext%22%2C%22url%22%3A%22https%3A%2F%2Fyoutubevideototranscript.com%2Fmcp%22%7D)
[![Website](https://img.shields.io/badge/Web_Tool-Free_No_Sign--Up-111111?style=for-the-badge)](https://youtubevideototranscript.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-4CAF50?style=for-the-badge)](LICENSE)

[![Subtext MCP connector](https://glama.ai/mcp/connectors/com.youtubevideototranscript/youtube-transcript/badges/score.svg)](https://glama.ai/mcp/connectors/com.youtubevideototranscript/youtube-transcript)

---

## Why

Without a transcript tool, every AI assistant hits the same wall:

```text
You:   Summarize the key claims in https://www.youtube.com/watch?v=arj7oStGLkU
Agent: I can't watch YouTube videos directly. Please paste the transcript here.
```

With **Subtext** connected over remote MCP:

```text
You:   Summarize the key claims in https://www.youtube.com/watch?v=arj7oStGLkU
Agent: -> get_transcript(video="arj7oStGLkU", format="text")
       Inside the Mind of a Master Procrastinator (Tim Urban, TED):
       [0:00] He contrasts the Rational Decision-Maker with the Instant Gratification Monkey...
```

### How Subtext compares

| | Subtext Remote MCP | Local `yt-dlp` / scraper MCP | YouTube Data API |
| --- | --- | --- | --- |
| **Setup** | Paste 1 URL + OAuth 2.1 | Install Python/Node + binaries locally | Google Cloud project + OAuth app |
| **Cloud / Datacenter IPs** | Works reliably (managed proxies + fallback) | Frequently hits `RequestBlocked` / `IpBlocked` | Doesn't serve third-party captions |
| **Auto-dubbed videos** | Picks original audio track by default | Often returns wrong auto-dubbed track | N/A |
| **Output formats** | `text`, `md`, `srt`, `vtt`, `json` | Varies | N/A |
| **Web & Mobile AI apps** | Works in Claude.ai, ChatGPT, mobile, IDEs | Desktop stdio only | Custom code only |

---

## Quick Start — Remote MCP (OAuth 2.1, no API key needed)

Remote Streamable HTTP endpoint:

```text
https://youtubevideototranscript.com/mcp
```

- **Claude.ai / Claude Desktop / Mobile & ChatGPT:** add `https://youtubevideototranscript.com/mcp` under **Settings → Connectors** and sign in via OAuth 2.1 (100 free starter credits on web sign-up, no credit card).
- **Cursor:** click **[Install in Cursor](https://cursor.com/en/install-mcp?name=subtext&config=eyJ1cmwiOiJodHRwczovL3lvdXR1YmV2aWRlb3RvdHJhbnNjcmlwdC5jb20vbWNwIn0=)** above, or paste the URL into MCP settings.
- **Claude Code (OAuth):**
  ```bash
  claude mcp add --transport http subtext https://youtubevideototranscript.com/mcp
  # Then run /mcp in Claude Code and choose Authenticate
  ```
- **OpenAI Codex CLI (OAuth):**
  ```bash
  codex mcp add subtext --url https://youtubevideototranscript.com/mcp
  codex mcp login subtext
  ```

<details>
<summary><strong>Connect with a static Bearer API key instead</strong></summary>

Create a free API key at [youtubevideototranscript.com/account](https://youtubevideototranscript.com/account) (100 free credits) and export `SUBTEXT_API_KEY`.

**Claude Code CLI:**
```bash
claude mcp add --transport http subtext https://youtubevideototranscript.com/mcp \
  --header "Authorization: Bearer $SUBTEXT_API_KEY"
```

- Claude Code config: [`mcp/claude-code.mcp.json`](mcp/claude-code.mcp.json)
- Cursor config: [`mcp/cursor.mcp.json`](mcp/cursor.mcp.json)

</details>

---

## Agent Skill (`SKILL.md` — no MCP required)

Install the portable [`agentskills.io`](https://agentskills.io) skill for **Claude Code, Cursor, Codex, OpenClaw, Hermes Agent**, and any skill-compatible coding agent. Uses plain `curl` and can create a free 25-credit API key right in chat via a 6-digit email code (no browser, no credit card):

```bash
npx skills add vsaranyuk/youtube-transcript-mcp
```

<details>
<summary><strong>Other Agent Skill install commands (Hermes, Codex, Manual)</strong></summary>

**Hermes Agent:**
```bash
hermes skills install skills-sh/vsaranyuk/youtube-transcript-mcp/skills/youtube-transcript
```

**Manual (`git clone`):**
```bash
git clone https://github.com/vsaranyuk/youtube-transcript-mcp.git
cp -r youtube-transcript-mcp/skills/youtube-transcript ~/.claude/skills/
```

</details>

Skill files: [`skills/youtube-transcript/SKILL.md`](skills/youtube-transcript/SKILL.md) · [`skills/youtube-transcript/references/auth-setup.md`](skills/youtube-transcript/references/auth-setup.md)

---

## Available MCP Tools

| Tool | Cost | Description |
| --- | --- | --- |
| `get_transcript` | 1 credit | Fetch captions for any public YouTube URL or 11-char video ID in `text`, `md`, `srt`, `vtt`, or `json` (optional `lang`, `translate`, `timestamps`). Failed calls cost 0 credits. |
| `list_languages` | Free | List available caption tracks, machine-translation targets, and video metadata (`title`, `author`, `default_language`) as JSON. |

---

## Links & Machine-Readable Docs

- **Free Web Reader (no sign-up):** [youtubevideototranscript.com](https://youtubevideototranscript.com)
- **API & MCP Reference:** [youtubevideototranscript.com/api](https://youtubevideototranscript.com/api) · [Markdown (`/api.md`)](https://youtubevideototranscript.com/api.md)
- **OpenAPI 3.1 Schema:** [youtubevideototranscript.com/api/openapi.json](https://youtubevideototranscript.com/api/openapi.json)
- **`llms.txt`:** [youtubevideototranscript.com/llms.txt](https://youtubevideototranscript.com/llms.txt)
- **Official MCP Registry:** `com.youtubevideototranscript/youtube-transcript` ([`server.json`](server.json))
- **Glama Connector:** [glama.ai/mcp/connectors/com.youtubevideototranscript/youtube-transcript](https://glama.ai/mcp/connectors/com.youtubevideototranscript/youtube-transcript)

## License

[MIT](LICENSE)
