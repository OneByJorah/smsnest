# REA Static-Analysis Notes

Both REA passes ground the packaging decisions in this repository. Static
analysis parses source/binaries as inert artifacts — no code was executed.

## httpsms web — `web/` (Nuxt 3 console)

| Field | Value |
| ----- | ----- |
| Input | JavaScript/Vue tree of the upstream web console |
| Files | 38 relevant, 35 parsed, 1 parse failure |
| AST nodes | 12,843 |
| Findings | 289 |
| Evidence ID | `ev_95c3e92af163612030ac2f332f2fdfbb917103cf9472cf3` |

## httpsms api — compiled Go server binary

| Field | Value |
| ----- | ----- |
| Input | API server binary built from the upstream Dockerfile (`go1.27.2`, amd64, 124,077,917 bytes) |
| Binary SHA-256 | `c5eda9cb0c93d4efe0cee62b64ec194b8b0ff5dc2656da311ed78428a633e4c2` |
| Go module | `github.com/NdoleStudio/httpsms` |
| Evidence ID | `ev_e9086a2772af8cadba4785f9e5ce45aee2715b6cd2afdd9` |
| Confirmed | Full dependency inventory (GCP/Firestore stack, task queue, auth), build settings — no hidden native components |

**Packaging consequence:** the API is a single self-contained Go binary serving
HTTP on `:8000` with PostgreSQL + Redis dependencies; the web console is a
server-rendered Nuxt app on `:3000`. The compose carries exactly those four
services.

## Limitations

Static analysis parses source/binaries as inert text and does not prove runtime
behavior. The Android agent (Java/Kotlin, distributed via the Play Store) was
not analyzed — it is consumed as an upstream app.
