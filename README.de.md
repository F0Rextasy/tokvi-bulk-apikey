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

**Tokvi-Konten im Bulk erstellen mit automatisch generierten API-Keys — OpenAI-kompatible Inferenz + 9Router-Connector.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Schnellstart](#-quick-start) · [🎁 Free-Tier](#-free-tier--how-much-you-get) · [🤖 Modelle](#-models--all-21-of-them) · [🧪 Die API nutzen](#-how-to-use-the-api) · [✅ Verifiziert](#-verified-working--every-key-is-tested) · [🧭 So funktioniert es](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Optionen](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Issues](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Warum es das gibt

Tokvi verteilt **342.466 Quota-Einheiten (≈ $0,68 bei qpu=500.000) an jedes neue Konto** — kein Captcha, keine Karte, nur E-Mail-Verifikation — und die live verifizierten Key-Zeilen sind die beiden DeepSeek-Flash-IDs in ihren eigenen Channels: **`deepseek-v4-flash-0731`** und **`deepseek-v4.1-flash`**. Die Lane hat es auf **101 Keys** skaliert; eine unabhängige Nachprüfung von 6 Beispiel-Keys antwortete mit 200 und echtem Inhalt, und die mitgelieferte CLI verifiziert jeden Key, den sie schreibt, erneut.

## 🎁 Free-Tier — wie viel du bekommst

| Was | Wert |
| --- | --- |
| Anmeldeguthaben (`quotaStart`) | **342.466 Einheiten ≈ $0,68** (qpu=500.000; auf jedem Konto gemessen) |
| Täglicher Check-in | **aus** — das Panel antwortet `签到功能未启用` (die CLI behält den Schritt hinter `--checkin` für den Tag, an dem er aktiviert wird) |
| Anmeldung | E-Mail + 6-stelliger Code — **kein Captcha** (`turnstile_check: false`) |
| Katalog | 21 Modelleinträge auf dem Live-Preisblatt |
| Rate-Limits | sendCode antwortet `429 发送过于频繁，请等待 N 秒后再试` (die CLI wartet die genannte Zeit und versucht es erneut); register/login drosseln `429` bei Bursts |
| Die Einheit, die skaliert | **ein Konto = ein Key** |

> 💡 Der Free-Tier wird von Tokvi festgelegt und kann sich ändern. Prüfe die Live-Zahlen auf [tokvi.net](https://tokvi.net) — und lass das eigene Gate des Tools entscheiden:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelle — alle 21

Das Live-Preisblatt enthält **21 Modelleinträge**. Beide verifizierten Key-Zeilen:

| Modell-ID | Verifiziert |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** mit echtem Inhalt — das primäre Gate der Mint |
| `deepseek-v4.1-flash` | **chat-200** — die Alternative, auf die die CLI zurückfällt, wenn der primäre Channel ausgelastet ist |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 So nutzt du die API

Base-URL **`https://tokvi.net/v1`** — OpenAI-kompatibel.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Schnellstart

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30
```

Ein Key wird **nur** dann in `keys.txt` geschrieben, wenn `/v1/chat/completions` mit 200 und echtem Inhalt antwortet; `accounts.json` speichert Benutzername / E-Mail / Quota / Code / Gruppe.

### 🖱️ Der Launcher

Der Launcher fragt nach der Proxy-Liste und der Anzahl, merkt sie sich und führt die CLI aus — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ Verifiziert funktionierend — jeder Key wird getestet

| Prüfung | Ergebnis |
| --- | --- |
| `GET /api/status` | **200** — Registrierung an, E-Mail-Verifikation an, Captcha aus, Check-in aus (`签到功能未启用`) |
| `POST /api/user/register` | **200** mit dem eigenen Body des Forks (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| Code-Vorlage | das Panel mailt `您的验证码为: <strong>e18f62</strong>` — ein **6-stelliger gemischter Hex-Code**; die CLI liest genau diesen Anker (ein nur auf Ziffern ausgelegter Scan liest `e12118` als `12118` und das Panel antwortet „Verification code is incorrect or has expired") |
| `POST /v1/chat/completions` — für **jeden** Key, bevor er geschrieben wird | **200** mit echtem `content`/`reasoning_content` (`deepseek-v4-flash-0731`, Fallback `deepseek-v4.1-flash`) |
| unabhängige Nachprüfung (frischer Exit-Pool, Stichprobe) | **6/6 Keys** 200 + nicht-leerer Inhalt |
| Hinweis zum geteilten Postfach | der Temp-Mail-Pool ist öffentlich: `Email address is already in use` → die CLI nimmt ein frisches Postfach (bis zu 3 pro Rail), statt den Versuch zu verbrennen |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 So funktioniert es

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Schritt | Aufruf | Hinweise |
| --- | --- | --- |
| status | `GET /api/status` | Gates schlagen geschlossen fehl (register/mail müssen an sein, Captcha aus) |
| Mailbox | Emailnator `POST /api/generate-email` | eine frische @gmail.com pro Konto (punktfreie Form) |
| Code senden | `GET /api/verification?email=…` | 429 → die genannten Sekunden warten (`请等待 N 秒`) und erneut versuchen; doppelte Adresse → frisches Postfach |
| Code | Emailnator `/api/message-list` → `/api/message/<id>` | der Vorlagen-Anker gewinnt; nur auf Ziffern ausgelegte Läufe sind letzte Wahl |
| registrieren | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; ein veralteter Code versucht den nächsten Kandidaten erneut |
| login | `POST /api/user/login` | Session-Token |
| Key | `POST /api/token/` (+ reveal) | einmal geschrieben |
| Nachweis | `POST /v1/chat/completions` | Modell-Walk: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLI-Optionen

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Standard | Bedeutung |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | Exit-Pool, eine Zeile pro Proxy (optional) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | Modell für die Live-Verifikation |

## 🛰️ Hinter einem Proxy

**Optional — bei Skalierung empfohlen (Limits pro IP, Bans).** Das Tool funktioniert auch ohne Proxy.

- **Mit einem Pool:** übergib `--pool <file>` (oder setze `TOKVI_PROXIES`) und jede Anfrage geht durch einen Exit aus dieser Liste — eine tote Leitung lässt diesen Versuch fehlschlagen und das Tool geht zur nächsten über (fail-closed, solange ein Pool angegeben ist; der Pool wird bei Nutzung erneut geprüft).
- **Ohne Pool:** die CLI gibt `running WITHOUT a proxy (direct)` aus und verbindet direkt. An Anmeldung, Key-Minting oder Verifikation ändert sich nichts — nur daran, wo die Pakete rausgehen.
- Führe `--screen` gegen beide Modi aus: es testet die Exits, die du hast (oder die direkte Route), gegen den Provider vor einem echten Lauf.

## 📄 Dateien

| Pfad | Inhalt |
| --- | --- |
| `keys.txt` | ein live-verifizierter Key pro Zeile — von Git ignoriert |
| `accounts.json` | Benutzername / E-Mail / Quota / Code / Gruppe |
| `proxies.txt` | deine Exits — von Git ignoriert |
| `outputs/` | Lauf-Logs — von Git ignoriert |

## 🔌 Mit 9Router verbinden

[9Router](https://9router.com) ist ein selbst gehosteter, OpenAI-kompatibler Router:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Standardwerte: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Modelle werden als `tokvi/<model id>` bereitgestellt.

## 🔑 Was ein Key dir bringt

| | |
| --- | --- |
| 🏢 Anbieter | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 Was der Anbieter gibt | 342.466 Einheiten (≈ $0,68) bei der Anmeldung |
| 🔑 Was ein Key dir bringt | ein Key auf einem eigenen Konto, chat-verifiziert auf `deepseek-v4-flash-0731` |
| 🌐 API-Form | OpenAI-kompatibel: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Deine Daten | nur `tokvi.net` und dein Inbox-Backend werden kontaktiert |

## 🐞 Issues

Wenn Tokvi seinen Register-Body, seine Code-Vorlage oder seine Gates ändert, stoppt das Tool dort und sagt es. **[Öffne ein Issue](../../issues)** mit dem Befehl und der exakten Ausgabe.

## 📄 Lizenz

MIT © 2026 — siehe [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>