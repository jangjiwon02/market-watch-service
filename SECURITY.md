# Security & Data Notes

[한국어로 보기 →](SECURITY.ko.md)

🚧 Like the rest of this repo, this document describes the intended design — nothing is implemented yet. Treat it as the security checklist to follow once code lands, not a report on code that exists.

## Contents

- [What this service touches](#what-this-service-touches)
- [Secrets](#secrets)
- [Access control](#access-control)
- [Logging](#logging)
- [If a secret leaks](#if-a-secret-leaks)

---

## What this service touches

This is a personal finance tool, so by design it handles data more sensitive than a typical side project:

- **Your holdings** — which stocks you own, and by extension your portfolio composition. Not account balances or brokerage credentials (no brokerage integration is planned), but still personal financial information.
- **A Notion database** — the daily summary (price-limit hits, news, volume) lives here. Whoever can open that database can see your holdings.
- **A Telegram chat** — alerts are pushed to a specific chat. Whoever controls that bot/chat receives them.

None of this is "telemetry" in the sense of usage analytics sent to a third party — there's no multi-user tracking here, just your own data flowing between services you control.

---

## Secrets

Expected secrets once implementation starts:

| Secret | Used for |
| --- | --- |
| `NOTION_API_KEY` | Writing the daily database entries |
| `NOTION_DATABASE_ID` | Target database (not strictly a secret, but keep it out of public issues/commits anyway) |
| `TELEGRAM_BOT_TOKEN` | Sending alert messages |
| `TELEGRAM_CHAT_ID` | Where alerts go (your personal chat or a private channel) |
| *(market-data provider key, once chosen)* | Fetching prices/news/volume |

Rules:

- **Environment variables only.** Never hardcode a key in source, a config file that gets committed, or a commit message.
- **`.env` stays local and gitignored.** Add a `.gitignore` with `.env` (and similar) before the first line of code that reads one.
- **Never paste a real key into a chat, issue, PR description, or log message** — including when asking an AI assistant for help debugging. Use a placeholder like `<redacted>` instead.
- If this ever runs in CI or a scheduled job (e.g., GitHub Actions), use the platform's encrypted secrets store, not a plaintext file in the repo.

---

## Access control

- **Keep the Notion database private.** It's your portfolio — don't set sharing to "anyone with the link."
- **Restrict the Telegram bot to you.** A Telegram bot token alone doesn't limit who can message the bot; the application code should check the sender's chat ID against an allowlist (just your own `TELEGRAM_CHAT_ID`) before acting on any incoming message, and should only ever *send* to that same chat ID.
- If a market-data API key has usage-based billing, treat it like any other secret — a leaked key is a billing risk, not just a privacy one.

---

## Logging

Once there's a daily job to log:

- Never log full secret values, even at debug level (log "token present" / "token missing," not the token).
- Keep portfolio details in logs to what's needed for debugging a specific run — a full daily dump of holdings in a log file is one more place that data can leak from.

---

## If a secret leaks

1. **Rotate it immediately** — regenerate the Notion API key (Notion integration settings) or the Telegram bot token (message `@BotFather` → `/revoke`), rather than just removing it from the file.
2. Check whether it was ever committed: `git log -p -- '*.env' '*secret*'` (or search the specific string) — a rotated key still means a leaked key sat in history, so rotation is the fix, not deletion from the latest commit alone.
3. Update whatever deployment holds the old value (local `.env`, CI secrets, etc.) with the new one.

---

[한국어로 보기 →](SECURITY.ko.md)
