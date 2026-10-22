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

**批量创建 Tokvi 账户并自动生成 API 密钥 — 兼容 OpenAI 的推理 + 9Router 连接器。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 快速开始](#-quick-start) · [🎁 免费额度](#-free-tier--how-much-you-get) · [🤖 模型](#-models--all-21-of-them) · [🧪 使用 API](#-how-to-use-the-api) · [✅ 已验证](#-verified-working--every-key-is-tested) · [🧭 工作原理](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ 选项](#️-cli-options) · [🛰️ 代理](#️-behind-a-proxy) · [🐞 问题反馈](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ 为什么会有这个项目

Tokvi 会向**每个新账户发放 342,466 配额单位（按 qpu=500,000 计算约合 $0.68）**——无需验证码、无需银行卡，仅需邮箱验证——而真正可用的密钥线路是它们各自渠道中的两个 DeepSeek flash 模型 ID：**`deepseek-v4-flash-0731`** 和 **`deepseek-v4.1-flash`**。该通道已将其扩展到 **101 个密钥**；对 6 个样本密钥的独立复核返回了 200 及真实内容，且随附的 CLI 会重新验证它写入的每一个密钥。

## 🎁 免费额度 — 你能获得多少

| 项目 | 数值 |
| --- | --- |
| 注册赠送额度（`quotaStart`） | **342,466 单位 ≈ $0.68**（qpu=500,000；在每个账户上实测） |
| 每日签到 | **关闭** — 面板返回 `签到功能未启用`（CLI 将该步骤保留在 `--checkin` 之后，以备其开启之日） |
| 注册 | 邮箱 + 6 位验证码 — **无验证码**（`turnstile_check: false`） |
| 目录 | 实时价格表上有 21 个模型条目 |
| 速率限制 | sendCode 返回 `429 发送过于频繁，请等待 N 秒后再试`（CLI 会等待所指定的秒数后重试）；突发情况下 register/login 限流 `429` |
| 可扩展的单位 | **一个账户 = 一个密钥** |

> 💡 免费额度由 Tokvi 设定，可能会变化。请在 [tokvi.net](https://tokvi.net) 查看实时数字——并让工具自身的关卡来决定：

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 模型 — 全部 21 个

实时价格表包含 **21 个模型条目**。两个已验证的密钥线路：

| 模型 ID | 已验证 |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** 及真实内容 — 铸造的主要关卡 |
| `deepseek-v4.1-flash` | **chat-200** — 当主渠道繁忙时 CLI 回退使用的备选 |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 如何使用 API

基础 URL **`https://tokvi.net/v1`** — 兼容 OpenAI。

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 快速开始

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

只有当 `/v1/chat/completions` 返回 200 及真实内容后，密钥才会被写入 `keys.txt`；`accounts.json` 保存用户名 / 邮箱 / 配额 / 验证码 / 分组。

### 🖱️ 启动器

启动器会询问代理列表和数量，记住它们，并运行 CLI——铸造、验证、筛选出口、设置、帮助、连接到 9Router。

## ✅ 已验证可用 — 每个密钥都经过测试

| 检查 | 结果 |
| --- | --- |
| `GET /api/status` | **200** — 注册开启、邮箱验证开启、验证码关闭、签到关闭（`签到功能未启用`） |
| `POST /api/user/register` | **200**，使用该分支自己的请求体（`{aff_code: null, acquisition_source: 'manual', …, verification_code}`） |
| 验证码模板 | 面板发送的邮件为 `您的验证码为: <strong>e18f62</strong>` — 一个 **6 位混合十六进制**；CLI 精确读取该锚点（仅扫描数字会把 `e12118` 读成 `12118`，面板会返回 "Verification code is incorrect or has expired"） |
| `POST /v1/chat/completions` — 对**每个**密钥在写入前 | **200** 及真实的 `content`/`reasoning_content`（`deepseek-v4-flash-0731`，回退 `deepseek-v4.1-flash`） |
| 独立复核（全新出口池，抽样） | **6/6 个密钥** 200 + 非空内容 |
| 共享邮箱注意事项 | 临时邮箱池是公开的：`Email address is already in use` → CLI 会换用新邮箱（每条线路最多 3 个），而不是浪费这次尝试 |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 工作原理

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| 步骤 | 调用 | 说明 |
| --- | --- | --- |
| status | `GET /api/status` | 关卡失败即关闭（register/mail 必须开启，captcha 必须关闭） |
| mailbox | Emailnator `POST /api/generate-email` | 每个账户一个全新的 @gmail.com（无点形式） |
| send code | `GET /api/verification?email=…` | 429 → 等待所指定的秒数（`请等待 N 秒`）并重试；地址重复 → 换新邮箱 |
| code | Emailnator `/api/message-list` → `/api/message/<id>` | 模板锚点优先；仅扫描数字是最后手段 |
| register | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`；验证码过期则重试下一个候选 |
| login | `POST /api/user/login` | 会话令牌 |
| key | `POST /api/token/`（+ 显示） | 写入一次 |
| proof | `POST /v1/chat/completions` | 模型遍历：`deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLI 选项

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 环境变量 | 默认值 | 含义 |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | 出口池，每行一个代理（可选） |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | 用于实时验证的模型 |

## 🛰️ 在代理之后

**可选——大规模使用时推荐（按 IP 限制、封禁）。** 该工具在没有代理的情况下也能工作。

- **有代理池时：** 传入 `--pool <file>`（或设置 `TOKVI_PROXIES`），每个请求都会经过该列表中的一个出口——失效线路会使该次尝试失败，工具会转向下一个（提供代理池时失败即关闭；代理池在使用时会重新探测）。
- **无代理池时：** CLI 会打印 `running WITHOUT a proxy (direct)` 并直接连接。注册、密钥铸造或验证都不会改变——只是数据包发出的位置不同。
- 对任一模式运行 `--screen`：它会在真实运行前，用你拥有的出口（或直连路由）对提供商进行测试。

## 📄 文件

| 路径 | 内容 |
| --- | --- |
| `keys.txt` | 每行一个实时验证的密钥 — 已被 git 忽略 |
| `accounts.json` | 用户名 / 邮箱 / 配额 / 验证码 / 分组 |
| `proxies.txt` | 你的出口 — 已被 git 忽略 |
| `outputs/` | 运行日志 — 已被 git 忽略 |

## 🔌 连接到 9Router

[9Router](https://9router.com) 是一个自托管、兼容 OpenAI 的路由器：

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

默认值：`--prefix tokvi`、`--base-url https://tokvi.net/v1`、`--model deepseek-v4-flash-0731`。模型以 `tokvi/<model id>` 形式提供。

## 🔑 一个密钥能给你带来什么

| | |
| --- | --- |
| 🏢 提供商 | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 提供商赠送 | 注册时 342,466 单位（≈ $0.68） |
| 🔑 一个密钥能给你带来什么 | 在其自己账户上的一个密钥，已在 `deepseek-v4-flash-0731` 上通过聊天验证 |
| 🌐 API 形态 | 兼容 OpenAI：`POST https://tokvi.net/v1/chat/completions` |
| 🔒 你的数据 | 仅联系 `tokvi.net` 和你的邮箱后端 |

## 🐞 问题反馈

如果 Tokvi 更改其注册请求体、验证码模板或关卡，工具会在此处停止并说明原因。请**[提交 issue](../../issues)**，附上命令和确切的输出。

## 📄 许可证

MIT © 2026 — 见 [LICENSE](LICENSE)。

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>