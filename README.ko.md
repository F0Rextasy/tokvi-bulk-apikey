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

**자동 생성된 API 키로 Tokvi 계정을 대량 생성 — OpenAI 호환 추론 + 9Router 커넥터.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 빠른 시작](#-quick-start) · [🎁 무료 티어](#-free-tier--how-much-you-get) · [🤖 모델](#-models--all-21-of-them) · [🧪 API 사용법](#-how-to-use-the-api) · [✅ 검증됨](#-verified-working--every-key-is-tested) · [🧭 작동 방식](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 옵션](#️-cli-options) · [🛰️ 프록시](#️-behind-a-proxy) · [🐞 이슈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 이 도구가 존재하는 이유

Tokvi는 **모든 신규 계정에 342,466 할당량 단위(qpu=500,000 기준 약 $0.68)를 지급합니다** — 캡차 없음, 카드 없음, 이메일 인증만 필요 — 그리고 실제로 작동하는 키 라인은 자체 채널에 있는 두 개의 DeepSeek flash ID입니다: **`deepseek-v4-flash-0731`** 및 **`deepseek-v4.1-flash`**. 이 레인은 **101개의 키**까지 확장되었으며, 6개의 샘플 키에 대한 독립적 재검증에서 실제 콘텐츠와 함께 200이 응답되었고, 제공된 CLI는 기록하는 모든 키를 재검증합니다.

## 🎁 무료 티어 — 얼마나 받을 수 있나

| 항목 | 값 |
| --- | --- |
| 가입 크레딧 (`quotaStart`) | **342,466 단위 ≈ $0.68** (qpu=500,000; 모든 계정에서 측정됨) |
| 일일 출석 체크 | **꺼짐** — 패널이 `签到功能未启用`라고 응답합니다 (CLI는 활성화되는 날을 대비해 `--checkin` 뒤에 이 단계를 유지합니다) |
| 가입 | 이메일 + 6자리 코드 — **캡차 없음** (`turnstile_check: false`) |
| 카탈로그 | 실시간 가격표에 21개의 모델 항목 |
| 속도 제한 | sendCode는 `429 发送过于频繁，请等待 N 秒后再试`라고 응답합니다 (CLI는 명시된 시간을 기다린 후 재시도합니다); 버스트 상황에서 register/login은 `429`로 제한됩니다 |
| 확장되는 단위 | **계정 하나 = 키 하나** |

> 💡 무료 티어는 Tokvi가 설정하며 변경될 수 있습니다. 실시간 수치는 [tokvi.net](https://tokvi.net)에서 확인하세요 — 그리고 도구 자체의 게이트가 판단하도록 하세요:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 모델 — 21개 전부

실시간 가격표에는 **21개의 모델 항목**이 있습니다. 검증된 두 키 라인:

| 모델 ID | 검증됨 |
| --- | --- |
| `deepseek-v4-flash-0731` | 실제 콘텐츠와 함께 **chat-200** — 민트의 기본 게이트 |
| `deepseek-v4.1-flash` | **chat-200** — 기본 채널이 바쁠 때 CLI가 대체로 사용하는 모델 |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API 사용법

Base URL **`https://tokvi.net/v1`** — OpenAI 호환.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 빠른 시작

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

키는 `/v1/chat/completions`가 실제 콘텐츠와 함께 200을 응답한 **후에만** `keys.txt`에 기록됩니다; `accounts.json`은 사용자 이름 / 이메일 / 할당량 / 코드 / 그룹을 보관합니다.

### 🖱️ 런처

런처는 프록시 목록과 개수를 묻고, 이를 기억한 뒤 CLI를 실행합니다 — 민트, 검증, 출구 스크린, 설정, 도움말, 9Router 연결.

## ✅ 검증된 작동 — 모든 키가 테스트됩니다

| 확인 | 결과 |
| --- | --- |
| `GET /api/status` | **200** — 가입 켜짐, 이메일 인증 켜짐, 캡차 꺼짐, 출석 체크 꺼짐 (`签到功能未启用`) |
| `POST /api/user/register` | 포크 자체의 본문과 함께 **200** (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| 코드 템플릿 | 패널이 `您的验证码为: <strong>e18f62</strong>`를 발송합니다 — **6자리 혼합 16진수**; CLI는 정확히 그 앵커를 읽습니다 (숫자만 스캔하면 `e12118`을 `12118`로 읽고 패널이 "Verification code is incorrect or has expired"라고 응답합니다) |
| `POST /v1/chat/completions` — 기록되기 전 **모든** 키에 대해 | 실제 `content`/`reasoning_content`와 함께 **200** (`deepseek-v4-flash-0731`, 대체 `deepseek-v4.1-flash`) |
| 독립적 재검증 (새 출구 풀, 샘플) | **6/6 키** 200 + 비어 있지 않은 콘텐츠 |
| 공유 메일박스 참고 | 임시 메일 풀은 공용입니다: `Email address is already in use` → CLI는 시도를 소진하는 대신 새 메일박스를 가져옵니다 (레일당 최대 3개) |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 작동 방식

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| 단계 | 호출 | 비고 |
| --- | --- | --- |
| 상태 | `GET /api/status` | 게이트는 실패 시 닫힘 (register/mail이 켜져 있어야 하고, 캡차는 꺼져 있어야 함) |
| 메일박스 | Emailnator `POST /api/generate-email` | 계정당 하나의 새 @gmail.com (점 없는 형식) |
| 코드 전송 | `GET /api/verification?email=…` | 429 → 명시된 초만큼 대기 (`请等待 N 秒`) 후 재시도; 중복 주소 → 새 메일박스 |
| 코드 | Emailnator `/api/message-list` → `/api/message/<id>` | 템플릿 앵커가 우선; 숫자만 추출하는 방식은 최후의 수단 |
| 가입 | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; 오래된 코드는 다음 후보로 재시도 |
| 로그인 | `POST /api/user/login` | 세션 토큰 |
| 키 | `POST /api/token/` (+ 공개) | 한 번 기록됨 |
| 증명 | `POST /v1/chat/completions` | 모델 순회: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLI 옵션

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 환경 변수 | 기본값 | 의미 |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | 출구 풀, 프록시당 한 줄 (선택 사항) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | 실시간 검증에 사용할 모델 |

## 🛰️ 프록시 뒤에서

**선택 사항 — 대규모에서는 권장됨 (IP당 제한, 차단).** 이 도구는 프록시 없이도 작동합니다.

- **풀이 있는 경우:** `--pool <file>`을 전달하거나 `TOKVI_PROXIES`를 설정하면 모든 요청이 해당 목록의 출구를 통해 나갑니다 — 죽은 라인은 해당 시도를 실패시키고 도구는 다음으로 넘어갑니다 (풀이 제공된 동안에는 실패 시 닫힘; 풀은 사용 시점에 재탐색됩니다).
- **풀이 없는 경우:** CLI는 `running WITHOUT a proxy (direct)`를 출력하고 직접 연결합니다. 가입, 키 발급 또는 검증에 관한 어떤 것도 바뀌지 않습니다 — 패킷이 나가는 위치만 바뀝니다.
- 어느 모드에서든 `--screen`을 실행하세요: 실제 실행 전에 보유한 출구(또는 직접 경로)를 제공자에 대해 테스트합니다.

## 📄 파일

| 경로 | 내용 |
| --- | --- |
| `keys.txt` | 실시간 검증된 키가 한 줄에 하나씩 — git에서 무시됨 |
| `accounts.json` | 사용자 이름 / 이메일 / 할당량 / 코드 / 그룹 |
| `proxies.txt` | 사용자의 출구 — git에서 무시됨 |
| `outputs/` | 실행 로그 — git에서 무시됨 |

## 🔌 9Router에 연결

[9Router](https://9router.com)는 자체 호스팅되는 OpenAI 호환 라우터입니다:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. 모델은 `tokvi/<model id>`로 제공됩니다.

## 🔌 OmniRoute에 연결

[OmniRoute](https://omniroute.online)는 자체 호스팅되는 OpenAI 호환 라우터입니다:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

기본값: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. 모델은 `tokvi/<model id>`로 제공됩니다.
## 🔑 키 하나로 얻는 것

| | |
| --- | --- |
| 🏢 제공자 | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 제공자가 주는 것 | 가입 시 342,466 단위 (≈ $0.68) |
| 🔑 키 하나로 얻는 것 | 자체 계정의 키 하나, `deepseek-v4-flash-0731`에서 채팅 검증됨 |
| 🌐 API 형태 | OpenAI 호환: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 데이터 | `tokvi.net`과 사용자의 메일박스 백엔드만 접속됩니다 |

## 🐞 이슈

Tokvi가 가입 본문, 코드 템플릿 또는 게이트를 변경하면 도구는 그 지점에서 멈추고 그 사실을 알립니다. 명령과 정확한 출력을 포함하여 **[이슈를 열어주세요](../../issues)**.

## 📄 라이선스

MIT © 2026 — [LICENSE](LICENSE) 참조.

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>