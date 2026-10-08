<div align="center">

# Market Watch Service

**Daily portfolio watch: a Notion database of price limits, news, and volume for your holdings — with key alerts pushed to Telegram.**

![Status](https://img.shields.io/badge/status-planning%20%2F%20no%20code%20yet-lightgrey)
[![Notion](https://img.shields.io/badge/data%20store-Notion-black?logo=notion&logoColor=white)](#integrations)
[![Telegram](https://img.shields.io/badge/alerts-Telegram-26A5E4?logo=telegram&logoColor=white)](#integrations)

[한국어로 보기 →](README.ko.md)

</div>

---

## Status

🚧 **This repository is empty — planning stage only.** This README describes the intended design so implementation can start from a clear spec; nothing here is built yet. Sections below will be updated as code lands.

---

## Contents

- [Overview](#overview)
- [Planned Features](#planned-features)
- [Integrations](#integrations)
- [Design Notes](#design-notes)
- [Roadmap](#roadmap)
- [Secrets & Configuration](#secrets--configuration)

---

## Overview

Every day, the service checks the stocks in your portfolio and builds a Notion database entry summarizing what happened: price-limit hits, relevant news, and trading volume. When something notable shows up, it also sends a Telegram message so you don't have to go check Notion yourself.

---

## Planned Features

- **Daily Notion database** — one row per holding per day, built automatically
- **Price-limit detection** — flags stocks that hit the upper or lower price limit (상한가 / 하한가)
- **News aggregation** — recent news per held stock, attached to that day's entry
- **Trading volume tracking** — daily volume, likely compared against a recent average to flag spikes
- **Telegram alerts** — key signals (limit hits, notable news, volume spikes) pushed directly to the user, so Notion is a record, not the only place you'd find out

---

## Integrations

| Integration | Role |
| --- | --- |
| **Notion API** | Destination database for the daily summary — one source of truth you can browse later |
| **Telegram Bot API** | Push channel for time-sensitive alerts |
| **Market data source** | Not yet chosen — needs a provider for prices, price-limit status, volume, and news per ticker |

---

## Design Notes

- Intended to run on a daily schedule (e.g., once after market close) rather than continuously polling.
- The Notion database is the durable record; Telegram is for things worth interrupting you for — keep that split when deciding what triggers an alert.
- Portfolio/holdings list needs a source of truth (manual list in Notion? a config file? a brokerage API?) — to be decided before the first version.

---

## Roadmap

- [ ] Choose a market-data provider (prices, price-limit flag, volume, news)
- [ ] Define the Notion database schema (one row per holding per day)
- [ ] Implement the daily job (fetch → build Notion rows → decide what's alert-worthy)
- [ ] Wire up the Telegram bot and alert formatting
- [ ] Decide where the holdings list lives and how it's updated
- [ ] Add scheduling (cron, GitHub Actions, or similar)

---

## Secrets & Configuration

Not yet implemented, but the intended rule: **no API keys or tokens in source or commit history — environment variables only** (`NOTION_API_KEY`, `TELEGRAM_BOT_TOKEN`, and whatever the chosen market-data provider needs), loaded from the deployment environment or a local `.env` file that stays out of git.

---

<div align="center">

[한국어로 보기 →](README.ko.md)

</div>
