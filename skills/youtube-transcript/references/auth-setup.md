# Subtext API Key Setup

Read this file only when `$SUBTEXT_API_KEY` is unset.

## Secret handling rules

- The key (`stx_...`), the temporary sign-up `request` token, and the 6-digit email code are secrets.
- Never print the key in chat, tool output, or logs, and never commit it to a repository.
- Send `$SUBTEXT_API_KEY` only to `https://youtubevideototranscript.com`.

## Step 1: Ask the user once

Ask whether they already have a Subtext API key:

- **If they have a key (or want 100 free web credits):** have them create/copy it at https://youtubevideototranscript.com/account and store it in their runtime secret store or `~/.config/subtext/key` (`chmod 600`) exported as `SUBTEXT_API_KEY` without pasting it into chat.
- **If they want zero-browser sign-up (25 free credits, no credit card):** ask for their email address and run Step 2.

## Step 2: Create a free account by email code (25 free credits)

1. Request a 6-digit verification code:

```bash
curl -sS "https://youtubevideototranscript.com/api/v1/signup" -H "Content-Type: application/json" \
  -d '{"email": "USER_EMAIL"}'
```

The answer is `202` with `request`. Keep `request` hidden and tell the user: "I sent a 6-digit code to USER_EMAIL. Please tell me the code." It expires in 10 minutes.

2. Exchange the code for a live `stx_` API key:

```bash
curl -sS "https://youtubevideototranscript.com/api/v1/signup/verify" -H "Content-Type: application/json" \
  -d '{"request": "REQUEST", "code": "CODE"}'
```

The answer is `201` with `api_key` and `credits_remaining`. Write the answer to a user-only file (`umask 077`, e.g. `~/.config/subtext/key`), move `api_key` into `SUBTEXT_API_KEY` in the runtime's secret store or shell profile loader, then delete any temporary response file. Never print the key. Tell the user how many credits the account has.

If the email already has an account, no code is sent: the user receives a link to https://youtubevideototranscript.com/account to create a key there.
