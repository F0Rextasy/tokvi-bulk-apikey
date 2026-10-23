<div align="center">

```
████████╗ ██████╗ ██╗  ██╗██╗   ██╗██╗
╚══██╔══╝██╔═══██╗██║ ██╔╝██║   ██║██║
   ██║   ██║   ██║█████╔╝ ██║   ██║██║
   ██║   ██║   ██║██╔═██╗ ╚██╗ ██╔╝██║
   ██║   ╚██████╔╝██║  ██╗ ╚████╔╝ ██║
   ╚═╝    ╚═════╝ ╚═╝  ╚═╝  ╚═══╝  ╚═╝
```

# tokvi-bulk-apikey

**Bulk-create Tokvi accounts with auto-generated API keys — OpenAI-compatible inference + 9Router connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Quick start](#-quick-start) · [🎁 Free tier](#-free-tier--how-much-you-get) · [🤖 Models](#-models--all-21-of-them) · [🧪 Use the API](#-how-to-use-the-api) · [✅ Verified](#-verified-working--every-key-is-tested) · [🧭 How it works](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Why this exists

Tokvi hands out **342,466 quota units (≈ $0.68 at qpu=500,000) to every new account** — no captcha, no card, e-mail verification only — and the live key lines are the two DeepSeek flash ids in their own channels: **`deepseek-v4-flash-0731`** and **`deepseek-v4.1-flash`**. The lane scaled it to every key; an independent re-check of 6 sample keys answered 200 with real content, and the shipped CLI re-verifies every key it writes.

## 🎁 Free tier — how much you get

| What | Value |
| --- | --- |
| Signup credit (`quotaStart`) | **342,466 units ≈ $0.68** (qpu=500,000; measured on every account) |
| Daily check-in | **off** — the panel answers `签到功能未启用` (the CLI keeps the step behind `--checkin` for the day it turns on) |
| Signup | e-mail + 6-char code — **no captcha** (`turnstile_check: false`) |
| Catalogue | 21 model entries on the live pricing sheet |
| Rate limits | sendCode answers `429 发送过于频繁，请等待 N 秒后再试` (the CLI waits the named time and retries); register/login throttle `429` under bursts |
| The unit that scales | **one account = one key** |

> 💡 The free tier is set by Tokvi and can change. Check the live numbers at [tokvi.net](https://tokvi.net) — and let the tool's own gate decide:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Models — all 21 of them

The live pricing sheet carries **21 model entries**. Both verified keys lines:

| Model ID | Verified |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** with real content — the mint's primary gate |
| `deepseek-v4.1-flash` | **chat-200** — the alternate the CLI falls back to when the primary channel is busy |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 How to use the API

Base URL **`https://tokvi.net/v1`** — OpenAI-compatible.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Quick start

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30
```

A key is written to `keys.txt` **only** after `/v1/chat/completions` answers 200 with real content; `accounts.json` keeps username / e-mail / quota / code / group.

### 🖱️ The launcher

The launcher asks for the proxy list and count, remembers them, and runs the CLI — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ Verified working — every key is tested

| Check | Result |
| --- | --- |
| `GET /api/status` | **200** — register on, e-mail verification on, captcha off, check-in off (`签到功能未启用`) |
| `POST /api/user/register` | **200** with the fork's own body (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| code template | the panel mails `您的验证码为: <strong>e18f62</strong>` — a **6-char mixed hex**; the CLI reads exactly that anchor (a digits-only scan reads `e12118` as `12118` and the panel answers "Verification code is incorrect or has expired") |
| `POST /v1/chat/completions` — for **every** key before it is written | **200** with real `content`/`reasoning_content` (`deepseek-v4-flash-0731`, fall back `deepseek-v4.1-flash`) |
| independent re-check (fresh exit pool, sample) | every key 200 + non-empty content |
| shared-mailbox note | the temp-mail pool is public: `Email address is already in use` → the CLI takes a fresh mailbox (up to 3 per rail) instead of burning the attempt |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Quality gates.** Every key here passed a live `chat-200` before it was written to `keys.txt`
> (dead keys are never shipped). Router combos follow the same rule at the model level: an entry is
> added **only if the model answers `chat-200` and can call tools** — a model without tool support,
> a dead key or a dead provider is dropped instead of added. Re-run the verify step any time;
> failures are reported, never hidden.

## 🧭 How it works

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Step | Call | Notes |
| --- | --- | --- |
| status | `GET /api/status` | gates fail closed (register/mail must be on, captcha off) |
| mailbox | Emailnator `POST /api/generate-email` | one fresh @gmail.com per account (dot-free form) |
| send code | `GET /api/verification?email=…` | 429 → wait the named seconds (`请等待 N 秒`) and retry; duplicate address → fresh mailbox |
| code | Emailnator `/api/message-list` → `/api/message/<id>` | the template anchor wins; digit-only runs are a last resort |
| register | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; a stale code re-tries the next candidate |
| login | `POST /api/user/login` | session token |
| key | `POST /api/token/` (+ reveal) | written once |
| proof | `POST /v1/chat/completions` | model walk: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLI options

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Default | Meaning |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | exit pool, one line per proxy (optional) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | model for the live verification |

## 🛰️ Behind a proxy

**Optional — recommended at scale (per-IP limits, bans).** The tool works without a proxy too.

- **With a pool:** pass `--pool <file>` (or set `TOKVI_PROXIES`) and every request goes through an exit from that list — a dead line fails that attempt and the tool moves to the next one (fail-closed while a pool is provided; the pool is re-probed at use).
- **Without a pool:** the CLI prints `running WITHOUT a proxy (direct)` and connects directly. Nothing about sign-up, key minting or verification changes — only where the packets leave from.
- Run `--screen` against either mode: it tests the exits you have (or the direct route) against the provider before a real run.

## 📄 Files

| Path | Contents |
| --- | --- |
| `keys.txt` | one live-verified key per line — git-ignored |
| `accounts.json` | username / e-mail / quota / code / group |
| `proxies.txt` | your exits — git-ignored |
| `outputs/` | run logs — git-ignored |

## 🔌 Connect to 9Router

[9Router](https://9router.com) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Models are served as `tokvi/<model id>`.

## 🔌 Connect to OmniRoute

[OmniRoute](https://omniroute.online) is a self-hosted, OpenAI-compatible router:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Defaults: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Models are served as `tokvi/<model id>`.

## 🔑 What one key gets you

| | |
| --- | --- |
| 🏢 Provider | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 What the provider gives | 342,466 units (≈ $0.68) on signup |
| 🔑 What one key gets you | one key on its own account, chat-verified on `deepseek-v4-flash-0731` |
| 🌐 API shape | OpenAI-compatible: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Your data | only `tokvi.net` and your inbox backend are contacted |

## 🐞 Issues

If Tokvi changes its register body, code template or gates, the tool stops there and says so. **[Open an issue](../../issues)** with the command and the exact output.

## 📄 License

MIT © 2026 — see [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>
