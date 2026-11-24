---
name: youtube-transcript
description: Fetch the transcript (captions) of a YouTube video as text, Markdown, SRT, WebVTT or JSON through the Subtext API. Use when the user shares a YouTube link or video id and wants it summarized, quoted, translated, searched or turned into notes, or asks what was said in a video.
compatibility: Needs network access to youtubevideototranscript.com and the SUBTEXT_API_KEY environment variable. Uses curl; no other dependencies.
metadata:
  author: subtext
  version: "1.1.0"
  homepage: https://youtubevideototranscript.com/api
---

# YouTube transcript

Get the spoken text of a YouTube video from the Subtext API.

## Setup

The key is read from `$SUBTEXT_API_KEY` (it starts with `stx_`). Never print the key.

If it is not set, ask the user once: do they have a Subtext key? If they do, have them set `SUBTEXT_API_KEY` themselves (keys are on https://youtubevideototranscript.com/account). If they do not, offer to create a free account for them; it takes their email address and a code from their mail, and no browser.

1. Ask for their email address and send it:

```bash
curl -sS "https://youtubevideototranscript.com/api/v1/signup" -H "Content-Type: application/json" \
  -d '{"email": "USER_EMAIL"}'
```

   The answer is `202` with `request`. Keep it, do not show it, and tell the user: "I sent a 6-digit code to USER_EMAIL. Please tell me the code." It works for 10 minutes.

2. Send the code with that request:

```bash
curl -sS "https://youtubevideototranscript.com/api/v1/signup/verify" -H "Content-Type: application/json" \
  -d '{"request": "REQUEST", "code": "CODE"}'
```

   The answer is `201` with `api_key` and `credits_remaining`. Write the answer to a file only the user can read (`umask 077`), move `api_key` from it into `SUBTEXT_API_KEY` the way this environment keeps secrets across sessions, then delete the file. Do not print the key. Tell the user how many credits the account has.

If the address has an account already, no code is sent: the user gets a mail that points to the account page instead. Ask them to create a key there and set it themselves.

## Get a transcript

Costs 1 credit for every transcript returned: the same video again, another language or another format is another credit. Errors are free. Save a transcript you will need again instead of asking twice. Let a request for a new video finish: if you cancel it while the transcript is still being fetched, the credit is spent all the same, and your next request for that video is free, once.

```bash
curl -sS -G "https://youtubevideototranscript.com/api/v1/transcript" \
  --data-urlencode "video=VIDEO_URL_OR_ID" \
  -d "format=text" -d "timestamps=true" \
  -H "Authorization: Bearer $SUBTEXT_API_KEY"
```

The same request as one address: `https://youtubevideototranscript.com/api/v1/transcript?video=VIDEO_ID&format=text&timestamps=true`.

| Parameter | Required | Default | Values |
| --- | --- | --- | --- |
| `video` | yes | | 11-character id or any YouTube URL |
| `lang` | no | the video's own language | caption language code, e.g. `en`, `pt-BR` |
| `translate` | no | | target language code; best effort, YouTube often refuses |
| `format` | no | `json` | `json`, `text`, `md`, `srt`, `vtt` |
| `timestamps` | no | `false` | `true` adds `[m:ss]` to each paragraph of `format=text` |

Use `format=text` for summaries and `format=json` when exact timings matter.

## List caption languages first (free)

```bash
curl -sS -G "https://youtubevideototranscript.com/api/v1/languages" \
  --data-urlencode "video=VIDEO_URL_OR_ID" \
  -H "Authorization: Bearer $SUBTEXT_API_KEY"
```

## Errors

Errors are JSON: `{"error": {"code": "...", "message": "...", "retryable": true}}`. Branch on `code`.

| Status | Code | What to do |
| --- | --- | --- |
| 401 | `missing_api_key`, `invalid_api_key` | ask the user to set `SUBTEXT_API_KEY` |
| 402 | `insufficient_credits` | tell the user; link `error.topup_url` |
| 404 | `no_captions`, `video_unavailable`, `age_restricted` | do not retry; tell the user |
| 404 | `language_not_available` | retry with a code from `error.available_languages` |
| 404 | `translation_not_available` | retry without `translate` and translate the text yourself |
| 422 | `invalid_video`, `invalid_parameter` | fix the request (`error.param` names the parameter) |
| 422 | `invalid_code` | sign-up: not the mailed code; ask the user to read it again (`error.attempts_left` says how many tries remain) |
| 422 | `email_not_accepted` | sign-up: a temporary address; ask the user for a lasting one |
| 410 | `code_expired` | sign-up: the request was used, expired or tried 5 times; start sign-up again |
| 429 | `rate_limited` | the account sent too many requests in a minute, or has too many videos being fetched at once: wait `Retry-After` seconds, retry once, and ask for fewer videos at a time |
| 500 | `internal_error` | retry once, then tell the user and quote `error.request_id` |
| 503 | `source_unavailable`, `busy` | nothing was charged: wait `Retry-After` seconds, retry up to 2 times |
| 504 | `timeout` | the transcript is still being fetched: retry after 5 seconds, up to 2 times |

Full reference: https://youtubevideototranscript.com/api.md
