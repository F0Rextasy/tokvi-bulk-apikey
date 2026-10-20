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

**APIキーを自動生成してTokviアカウントを一括作成 — OpenAI互換の推論 + 9Routerコネクタ。**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 クイックスタート](#-quick-start) · [🎁 無料枠](#-free-tier--how-much-you-get) · [🤖 モデル](#-models--all-21-of-them) · [🧪 APIの使い方](#-how-to-use-the-api) · [✅ 検証済み](#-verified-working--every-key-is-tested) · [🧭 仕組み](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ オプション](#️-cli-options) · [🛰️ プロキシ](#️-behind-a-proxy) · [🐞 イシュー](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ なぜこれが存在するのか

Tokviは**新規アカウントごとに342,466クォータユニット（qpu=500,000で約$0.68）を配布します** — キャプチャなし、カード不要、メール認証のみ — そして実際に使えるキーは、それぞれのチャネルにある2つのDeepSeek flash IDです: **`deepseek-v4-flash-0731`** と **`deepseek-v4.1-flash`**。このレーンは**101キー**までスケールしました。6つのサンプルキーの独立した再チェックでは、実コンテンツ付きで200が返り、同梱のCLIは書き込むすべてのキーを再検証します。

## 🎁 無料枠 — どれだけ得られるか

| 項目 | 値 |
| --- | --- |
| 登録クレジット（`quotaStart`） | **342,466ユニット ≈ $0.68**（qpu=500,000、すべてのアカウントで計測） |
| 毎日のチェックイン | **オフ** — パネルは`签到功能未启用`と応答します（CLIは有効化された日に備えて`--checkin`の背後にこのステップを保持しています） |
| 登録 | メール + 6文字コード — **キャプチャなし**（`turnstile_check: false`） |
| カタログ | ライブ価格表に21のモデルエントリ |
| レート制限 | sendCodeは`429 发送过于频繁，请等待 N 秒后再试`と応答します（CLIは指定された秒数待って再試行します）。register/loginはバースト時に`429`でスロットルされます |
| スケールする単位 | **1アカウント = 1キー** |

> 💡 無料枠はTokviによって設定され、変更される可能性があります。最新の数値は[tokvi.net](https://tokvi.net)で確認してください — そしてツール自身のゲートに判断を委ねてください:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 モデル — 全21個

ライブ価格表には**21のモデルエントリ**があります。検証済みのキーは両方とも:

| モデルID | 検証済み |
| --- | --- |
| `deepseek-v4-flash-0731` | 実コンテンツ付きの**chat-200** — ミントの主要ゲート |
| `deepseek-v4.1-flash` | **chat-200** — 主要チャネルがビジーのときにCLIがフォールバックする代替 |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 APIの使い方

ベースURL **`https://tokvi.net/v1`** — OpenAI互換。

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 クイックスタート

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30
```

キーは`/v1/chat/completions`が実コンテンツ付きで200を返した**後にのみ**`keys.txt`に書き込まれます。`accounts.json`はユーザー名 / メール / クォータ / コード / グループを保持します。

### 🖱️ ランチャー

ランチャーはプロキシリストと件数を尋ね、それらを記憶し、CLIを実行します — ミント、検証、終了プールのスクリーニング、設定、ヘルプ、9Routerへの接続。

## ✅ 動作検証済み — すべてのキーがテストされます

| チェック | 結果 |
| --- | --- |
| `GET /api/status` | **200** — 登録オン、メール認証オン、キャプチャオフ、チェックインオフ（`签到功能未启用`） |
| `POST /api/user/register` | フォーク自身のボディで**200**（`{aff_code: null, acquisition_source: 'manual', …, verification_code}`） |
| コードテンプレート | パネルは`您的验证码为: <strong>e18f62</strong>`を送信します — **6文字の混合16進数**。CLIはまさにそのアンカーを読み取ります（数字のみのスキャンでは`e12118`を`12118`と読み取り、パネルは「Verification code is incorrect or has expired」と応答します） |
| `POST /v1/chat/completions` — 書き込まれる前の**すべての**キーに対して | 実際の`content`/`reasoning_content`付きで**200**（`deepseek-v4-flash-0731`、フォールバック`deepseek-v4.1-flash`） |
| 独立した再チェック（新しい終了プール、サンプル） | **6/6キー**が200 + 空でないコンテンツ |
| 共有メールボックスの注意 | 一時メールプールは公開されています: `Email address is already in use` → CLIは試行を無駄にせず、新しいメールボックスを取得します（レールごとに最大3つ） |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 仕組み

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| ステップ | 呼び出し | 備考 |
| --- | --- | --- |
| status | `GET /api/status` | ゲートはフェイルクローズ（register/mailがオン、キャプチャオフでなければならない） |
| mailbox | Emailnator `POST /api/generate-email` | アカウントごとに1つの新しい@gmail.com（ドットなし形式） |
| send code | `GET /api/verification?email=…` | 429 → 指定された秒数を待ち（`请等待 N 秒`）、再試行。重複アドレス → 新しいメールボックス |
| code | Emailnator `/api/message-list` → `/api/message/<id>` | テンプレートのアンカーが優先。数字のみの実行は最後の手段 |
| register | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`。古いコードは次の候補で再試行 |
| login | `POST /api/user/login` | セッショントークン |
| key | `POST /api/token/`（+ reveal） | 一度だけ書き込み |
| proof | `POST /v1/chat/completions` | モデルウォーク: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLIオプション

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| 環境変数 | デフォルト | 意味 |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | 終了プール、プロキシごとに1行（オプション） |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | ライブ検証に使用するモデル |

## 🛰️ プロキシの背後で

**オプション — 大規模運用では推奨（IPごとの制限、BAN）。** このツールはプロキシなしでも動作します。

- **プールあり:** `--pool <file>`を渡す（または`TOKVI_PROXIES`を設定する）と、すべてのリクエストがそのリストの終了プールを経由します — 死んだ回線はその試行を失敗させ、ツールは次へ進みます（プールが提供されている間はフェイルクローズ。プールは使用時に再プローブされます）。
- **プールなし:** CLIは`running WITHOUT a proxy (direct)`と表示し、直接接続します。登録、キーのミント、検証について何も変わりません — パケットが出ていく場所だけが変わります。
- どちらのモードでも`--screen`を実行してください: 実際の実行前に、持っている終了プール（または直接ルート）をプロバイダに対してテストします。

## 📄 ファイル

| パス | 内容 |
| --- | --- |
| `keys.txt` | ライブ検証済みのキーが1行に1つ — git無視 |
| `accounts.json` | ユーザー名 / メール / クォータ / コード / グループ |
| `proxies.txt` | あなたの終了プール — git無視 |
| `outputs/` | 実行ログ — git無視 |

## 🔌 9Routerへの接続

[9Router](https://9router.com)はセルフホスト型のOpenAI互換ルーターです:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

デフォルト: `--prefix tokvi`、`--base-url https://tokvi.net/v1`、`--model deepseek-v4-flash-0731`。モデルは`tokvi/<model id>`として提供されます。

## 🔑 1つのキーで得られるもの

| | |
| --- | --- |
| 🏢 プロバイダ | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 プロバイダが提供するもの | 登録時に342,466ユニット（≈ $0.68） |
| 🔑 1つのキーで得られるもの | 独自のアカウント上の1つのキー、`deepseek-v4-flash-0731`でチャット検証済み |
| 🌐 API形式 | OpenAI互換: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 あなたのデータ | `tokvi.net`とあなたの受信トレイバックエンドのみが接触されます |

## 🐞 イシュー

Tokviが登録ボディ、コードテンプレート、またはゲートを変更した場合、ツールはそこで停止し、その旨を報告します。コマンドと正確な出力を添えて**[イシューを開いてください](../../issues)**。

## 📄 ライセンス

MIT © 2026 — [LICENSE](LICENSE)を参照。

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>