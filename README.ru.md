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

**Массовое создание аккаунтов Tokvi с автоматически сгенерированными API-ключами — инференс, совместимый с OpenAI, + коннектор 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Быстрый старт](#-quick-start) · [🎁 Бесплатный тариф](#-free-tier--how-much-you-get) · [🤖 Модели](#-models--all-21-of-them) · [🧪 Использование API](#-how-to-use-the-api) · [✅ Проверено](#-verified-working--every-key-is-tested) · [🧭 Как это работает](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Опции](#️-cli-options) · [🛰️ Прокси](#️-behind-a-proxy) · [🐞 Проблемы](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Зачем это нужно

Tokvi выдаёт **342 466 единиц квоты (≈ $0,68 при qpu=500 000) каждому новому аккаунту** — без капчи, без карты, только подтверждение по e-mail — а рабочие строки ключей — это два flash-идентификатора DeepSeek в их собственных каналах: **`deepseek-v4-flash-0731`** и **`deepseek-v4.1-flash`**. В рамках этого направления инструмент масштабировался до **101 ключа**; независимая перепроверка 6 выборочных ключей вернула 200 с реальным содержимым, а поставляемый CLI перепроверяет каждый записываемый ключ.

## 🎁 Бесплатный тариф — сколько вы получаете

| Что | Значение |
| --- | --- |
| Кредит при регистрации (`quotaStart`) | **342 466 единиц ≈ $0,68** (qpu=500 000; измерено на каждом аккаунте) |
| Ежедневная отметка | **выключена** — панель отвечает `签到功能未启用` (CLI оставляет этот шаг за флагом `--checkin` на день, когда его включат) |
| Регистрация | e-mail + 6-символьный код — **без капчи** (`turnstile_check: false`) |
| Каталог | 21 запись модели в актуальном прайс-листе |
| Ограничения частоты | sendCode отвечает `429 发送过于频繁，请等待 N 秒后再试` (CLI ждёт указанное время и повторяет); register/login ограничиваются `429` при всплесках |
| Единица масштабирования | **один аккаунт = один ключ** |

> 💡 Бесплатный тариф задаётся Tokvi и может измениться. Проверяйте актуальные цифры на [tokvi.net](https://tokvi.net) — и пусть собственный шлюз инструмента решает:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Модели — все 21

Актуальный прайс-лист содержит **21 запись модели**. Обе проверенные строки ключей:

| ID модели | Проверено |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** с реальным содержимым — основной шлюз выпуска |
| `deepseek-v4.1-flash` | **chat-200** — альтернатива, к которой CLI переходит, когда основной канал занят |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Как использовать API

Базовый URL **`https://tokvi.net/v1`** — совместим с OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Быстрый старт

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

Ключ записывается в `keys.txt` **только** после того, как `/v1/chat/completions` ответит 200 с реальным содержимым; `accounts.json` хранит имя пользователя / e-mail / квоту / код / группу.

### 🖱️ Лаунчер

Лаунчер запрашивает список прокси и количество, запоминает их и запускает CLI — выпуск, проверка, проверка выходов, настройки, справка, подключение к 9Router.

## ✅ Проверено в работе — каждый ключ тестируется

| Проверка | Результат |
| --- | --- |
| `GET /api/status` | **200** — регистрация включена, подтверждение по e-mail включено, капча выключена, отметка выключена (`签到功能未启用`) |
| `POST /api/user/register` | **200** с собственным телом форка (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| шаблон кода | панель отправляет `您的验证码为: <strong>e18f62</strong>` — **6-символьный смешанный hex**; CLI читает именно этот якорь (сканирование только цифр читает `e12118` как `12118`, и панель отвечает "Verification code is incorrect or has expired") |
| `POST /v1/chat/completions` — для **каждого** ключа перед записью | **200** с реальным `content`/`reasoning_content` (`deepseek-v4-flash-0731`, запасной `deepseek-v4.1-flash`) |
| независимая перепроверка (свежий пул выходов, выборка) | **6/6 ключей** 200 + непустое содержимое |
| примечание об общем почтовом ящике | пул временной почты публичный: `Email address is already in use` → CLI берёт свежий ящик (до 3 на рельс) вместо сжигания попытки |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Как это работает

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Шаг | Вызов | Примечания |
| --- | --- | --- |
| статус | `GET /api/status` | шлюзы отказывают закрыто (регистрация/почта должны быть включены, капча выключена) |
| почтовый ящик | Emailnator `POST /api/generate-email` | один свежий @gmail.com на аккаунт (форма без точек) |
| отправка кода | `GET /api/verification?email=…` | 429 → подождать указанные секунды (`请等待 N 秒`) и повторить; дублирующийся адрес → свежий ящик |
| код | Emailnator `/api/message-list` → `/api/message/<id>` | якорь шаблона имеет приоритет; прогоны только по цифрам — крайняя мера |
| регистрация | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; устаревший код повторяет попытку со следующим кандидатом |
| вход | `POST /api/user/login` | токен сессии |
| ключ | `POST /api/token/` (+ reveal) | записывается один раз |
| доказательство | `POST /v1/chat/completions` | перебор моделей: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ Опции CLI

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Переменная | По умолчанию | Значение |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | пул выходов, одна строка на прокси (необязательно) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | модель для живой проверки |

## 🛰️ За прокси

**Необязательно — рекомендуется при масштабе (лимиты на IP, баны).** Инструмент работает и без прокси.

- **С пулом:** передайте `--pool <file>` (или задайте `TOKVI_PROXIES`), и каждый запрос идёт через выход из этого списка — мёртвая линия проваливает эту попытку, и инструмент переходит к следующей (отказ закрыто, пока пул предоставлен; пул перепроверяется при использовании).
- **Без пула:** CLI выводит `running WITHOUT a proxy (direct)` и подключается напрямую. В регистрации, выпуске ключей или проверке ничего не меняется — только то, откуда уходят пакеты.
- Запустите `--screen` в любом режиме: он тестирует имеющиеся у вас выходы (или прямой маршрут) против провайдера перед реальным запуском.

## 📄 Файлы

| Путь | Содержимое |
| --- | --- |
| `keys.txt` | один проверенный в работе ключ на строку — игнорируется git |
| `accounts.json` | имя пользователя / e-mail / квота / код / группа |
| `proxies.txt` | ваши выходы — игнорируется git |
| `outputs/` | логи запусков — игнорируется git |

## 🔌 Подключение к 9Router

[9Router](https://9router.com) — это самостоятельно размещаемый роутер, совместимый с OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

По умолчанию: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Модели подаются как `tokvi/<model id>`.

## 🔑 Что даёт один ключ

| | |
| --- | --- |
| 🏢 Провайдер | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 Что даёт провайдер | 342 466 единиц (≈ $0,68) при регистрации |
| 🔑 Что даёт один ключ | один ключ на собственном аккаунте, проверенный в чате на `deepseek-v4-flash-0731` |
| 🌐 Форма API | Совместим с OpenAI: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Ваши данные | связь только с `tokvi.net` и вашим бэкендом почтового ящика |

## 🐞 Проблемы

Если Tokvi изменит тело регистрации, шаблон кода или шлюзы, инструмент остановится на этом месте и сообщит об этом. **[Откройте issue](../../issues)** с командой и точным выводом.

## 📄 Лицензия

MIT © 2026 — см. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>