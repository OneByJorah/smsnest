<div align="center">

# smsnest

**Your Android phone as an SMS gateway — self-hosted, API-driven, under your own roof.**

Send and receive SMS through a simple HTTP API. Your phone runs the agent app; this stack runs the API, database, and web console. A self-hosted packaging of the excellent open-source [httpSMS](https://github.com/NdoleStudio/httpsms) project.

[![CI](https://img.shields.io/github/actions/workflow/status/OneByJorah/smsnest/ci.yml?color=5b8def&label=CI&logo=github)](https://github.com/OneByJorah/smsnest/actions/workflows/ci.yml)
[![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)](https://www.docker.com/)
[![Upstream license: AGPL-3.0](https://img.shields.io/badge/upstream%20license-AGPL--3.0-blue)](LICENSE.upstream)
[![License: MIT (packaging)](https://img.shields.io/badge/packaging-MIT-yellow.svg)](LICENSE)

</div>

## What's Inside

| Service | Role | Port (localhost) |
| ------- | ---- | ---------------- |
| **API** (Go) | HTTP API for sending/receiving SMS, webhook delivery | `8000` |
| **Web** (Nuxt) | Console: messages, phones, settings | `3000` |
| **PostgreSQL** | Message + phone storage | `5435` → internal |
| **Redis** | Job queue | internal |

The **Android agent** app (the part that actually sends SMS) comes from the upstream project — install it on the phone and point it at this server.

## How This Was Built

Packaging was grounded with REA (Reverse Engineer Anything) static analysis of the upstream source; see [`docs/rea-analysis.md`](docs/rea-analysis.md).

## Quick Start

```bash
git clone https://github.com/OneByJorah/smsnest.git
cd smsnest
cp api/.env.example api/.env    # review secrets: openssl rand -hex 32 for JWT keys
docker compose up -d --build
```

| Surface | URL |
| ------- | --- |
| Web console | `http://127.0.0.1:3000` |
| API | `http://127.0.0.1:8000` |

Then install the upstream **Android agent** on your phone, point it at this server, and send your first SMS through the API:

```bash
curl -X POST http://127.0.0.1:8000/v1/messages/send \
  -H "X-API-KEY: your-key" -H "Content-Type: application/json" \
  -d '{"text":"hello from smsnest","contact":"+15550001234"}'
```

## Security Notes

- Everything binds to `127.0.0.1` — put your own reverse proxy/tunnel in front for remote access.
- `.env` files are gitignored; `.env.example` ships placeholders.
- Generate fresh secrets (`openssl rand -hex 32`) for anything beyond local testing.

## Credits & License

All application code is upstream [httpSMS](https://github.com/NdoleStudio/httpsms) by NdoleStudio, licensed **AGPL-3.0** (see [`LICENSE.upstream`](LICENSE.upstream)) — this repository packages it for self-hosting and keeps that license for the vendored code. The packaging glue (compose, portal scripts, CI, docs) is MIT.
