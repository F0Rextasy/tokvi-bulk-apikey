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

**Otomatik oluşturulan API anahtarlarıyla toplu Tokvi hesabı oluşturun — OpenAI uyumlu çıkarım + 9Router bağlayıcısı.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Hızlı başlangıç](#-quick-start) · [🎁 Ücretsiz katman](#-free-tier--how-much-you-get) · [🤖 Modeller](#-models--all-21-of-them) · [🧪 API'yi kullanma](#-how-to-use-the-api) · [✅ Doğrulandı](#-verified-working--every-key-is-tested) · [🧭 Nasıl çalışır](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Seçenekler](#️-cli-options) · [🛰️ Proxy'ler](#️-behind-a-proxy) · [🐞 Sorunlar](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Bu neden var

Tokvi **her yeni hesaba 342.466 kota birimi (qpu=500.000 ile ≈ $0.68) veriyor** — captcha yok, kart yok, yalnızca e-posta doğrulaması — ve canlı anahtar hatları kendi kanallarındaki iki DeepSeek flash kimliğidir: **`deepseek-v4-flash-0731`** ve **`deepseek-v4.1-flash`**. Hat bunu her anahtara ölçekledi; 6 örnek anahtarın bağımsız yeniden kontrolü gerçek içerikle 200 yanıtı verdi ve gönderilen CLI yazdığı her anahtarı yeniden doğrular.

## 🎁 Ücretsiz katman — ne kadar alıyorsunuz

| Ne | Değer |
| --- | --- |
| Kayıt kredisi (`quotaStart`) | **342.466 birim ≈ $0.68** (qpu=500.000; her hesapta ölçüldü) |
| Günlük giriş | **kapalı** — panel `签到功能未启用` yanıtını veriyor (CLI, açıldığı gün için adımı `--checkin` arkasında tutar) |
| Kayıt | e-posta + 6 karakterli kod — **captcha yok** (`turnstile_check: false`) |
| Katalog | canlı fiyat listesinde 21 model girdisi |
| Hız limitleri | sendCode `429 发送过于频繁，请等待 N 秒后再试` yanıtını verir (CLI belirtilen süre bekler ve yeniden dener); kayıt/giriş yoğunlukta `429` ile sınırlar |
| Ölçeklenen birim | **bir hesap = bir anahtar** |

> 💡 Ücretsiz katman Tokvi tarafından belirlenir ve değişebilir. Canlı sayıları [tokvi.net](https://tokvi.net) adresinden kontrol edin — ve aracın kendi kapısının karar vermesine izin verin:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modeller — 21'inin tamamı

Canlı fiyat listesi **21 model girdisi** içeriyor. Doğrulanmış iki anahtar hattı:

| Model Kimliği | Doğrulandı |
| --- | --- |
| `deepseek-v4-flash-0731` | gerçek içerikle **chat-200** — basımın birincil kapısı |
| `deepseek-v4.1-flash` | **chat-200** — birincil kanal meşgul olduğunda CLI'nin geri döndüğü alternatif |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 API'yi nasıl kullanırım

Temel URL **`https://tokvi.net/v1`** — OpenAI uyumlu.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Hızlı başlangıç

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

Bir anahtar `keys.txt` dosyasına **yalnızca** `/v1/chat/completions` gerçek içerikle 200 yanıtı verdikten sonra yazılır; `accounts.json` kullanıcı adı / e-posta / kota / kod / grup bilgilerini tutar.

### 🖱️ Başlatıcı

Başlatıcı proxy listesini ve sayıyı sorar, bunları hatırlar ve CLI'yi çalıştırır — bas, doğrula, çıkışları tara, ayarlar, yardım, 9Router'a Bağlan.

## ✅ Çalıştığı doğrulandı — her anahtar test edilir

| Kontrol | Sonuç |
| --- | --- |
| `GET /api/status` | **200** — kayıt açık, e-posta doğrulaması açık, captcha kapalı, giriş kapalı (`签到功能未启用`) |
| `POST /api/user/register` | çatalın kendi gövdesiyle **200** (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| kod şablonu | panel `您的验证码为: <strong>e18f62</strong>` gönderir — **6 karakterli karışık hex**; CLI tam olarak bu bağlantıyı okur (yalnızca rakam taraması `e12118`'i `12118` olarak okur ve panel "Verification code is incorrect or has expired" yanıtını verir) |
| `POST /v1/chat/completions` — yazılmadan önce **her** anahtar için | gerçek `content`/`reasoning_content` ile **200** (`deepseek-v4-flash-0731`, geri dönüş `deepseek-v4.1-flash`) |
| bağımsız yeniden kontrol (taze çıkış havuzu, örnek) | her anahtar 200 + boş olmayan içerik |
| paylaşılan posta kutusu notu | geçici posta havuzu herkese açıktır: `Email address is already in use` → CLI denemeyi harcamak yerine taze bir posta kutusu alır (hat başına en fazla 3) |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Kalite kapıları.** Buradaki her anahtar `keys.txt` dosyasına yazılmadan önce canlı bir `chat-200` geçti
> (ölü anahtarlar asla gönderilmez). Yönlendirici kombinasyonları model düzeyinde aynı kuralı izler: bir girdi
> **yalnızca model `chat-200` yanıtı verirse ve araçları çağırabilirse** eklenir — araç desteği olmayan bir model,
> ölü bir anahtar veya ölü bir sağlayıcı eklenmek yerine çıkarılır. Doğrulama adımını istediğiniz zaman yeniden çalıştırın;
> hatalar bildirilir, asla gizlenmez.

## 🧭 Nasıl çalışır

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Adım | Çağrı | Notlar |
| --- | --- | --- |
| durum | `GET /api/status` | kapılar güvenli tarafta başarısız olur (kayıt/posta açık olmalı, captcha kapalı) |
| posta kutusu | Emailnator `POST /api/generate-email` | hesap başına bir taze @gmail.com (noktasız biçim) |
| kod gönder | `GET /api/verification?email=…` | 429 → belirtilen saniye kadar bekleyin (`请等待 N 秒`) ve yeniden deneyin; yinelenen adres → taze posta kutusu |
| kod | Emailnator `/api/message-list` → `/api/message/<id>` | şablon bağlantısı kazanır; yalnızca rakam çalıştırmaları son çaredir |
| kayıt | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; eski bir kod sonraki adayı dener |
| giriş | `POST /api/user/login` | oturum belirteci |
| anahtar | `POST /api/token/` (+ reveal) | bir kez yazılır |
| kanıt | `POST /v1/chat/completions` | model yürüyüşü: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ CLI seçenekleri

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Ortam | Varsayılan | Anlam |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | çıkış havuzu, proxy başına bir satır (isteğe bağlı) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | canlı doğrulama için model |

## 🛰️ Proxy arkasında

**İsteğe bağlı — ölçekte önerilir (IP başına limitler, yasaklar).** Araç proxy olmadan da çalışır.

- **Havuzla:** `--pool <file>` geçin (veya `TOKVI_PROXIES` ayarlayın) ve her istek o listeden bir çıkıştan geçer — ölü bir hat o denemeyi başarısız kılar ve araç bir sonrakine geçer (havuz sağlandığında güvenli tarafta başarısız olur; havuz kullanım sırasında yeniden yoklanır).
- **Havuz olmadan:** CLI `running WITHOUT a proxy (direct)` yazdırır ve doğrudan bağlanır. Kayıt, anahtar basımı veya doğrulamayla ilgili hiçbir şey değişmez — yalnızca paketlerin nereden çıktığı değişir.
- Her iki moda karşı `--screen` çalıştırın: gerçek bir çalıştırmadan önce sahip olduğunuz çıkışları (veya doğrudan rotayı) sağlayıcıya karşı test eder.

## 📄 Dosyalar

| Yol | İçerik |
| --- | --- |
| `keys.txt` | satır başına bir canlı doğrulanmış anahtar — git tarafından yok sayılır |
| `accounts.json` | kullanıcı adı / e-posta / kota / kod / grup |
| `proxies.txt` | çıkışlarınız — git tarafından yok sayılır |
| `outputs/` | çalıştırma günlükleri — git tarafından yok sayılır |

## 🔌 9Router'a bağlan

[9Router](https://9router.com) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Modeller `tokvi/<model id>` olarak sunulur.

## 🔌 OmniRoute'a bağlan

[OmniRoute](https://omniroute.online) kendi kendine barındırılan, OpenAI uyumlu bir yönlendiricidir:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Varsayılanlar: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Modeller `tokvi/<model id>` olarak sunulur.

## 🔑 Bir anahtar size ne kazandırır

| | |
| --- | --- |
| 🏢 Sağlayıcı | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 Sağlayıcının verdikleri | kayıtta 342.466 birim (≈ $0.68) |
| 🔑 Bir anahtarın size kazandırdıkları | kendi hesabında bir anahtar, `deepseek-v4-flash-0731` üzerinde sohbet doğrulamalı |
| 🌐 API biçimi | OpenAI uyumlu: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Verileriniz | yalnızca `tokvi.net` ve posta kutusu arka ucunuzla iletişim kurulur |

## 🐞 Sorunlar

Tokvi kayıt gövdesini, kod şablonunu veya kapılarını değiştirirse araç orada durur ve bunu söyler. Komut ve tam çıktıyla birlikte **[bir sorun açın](../../issues)**.

## 📄 Lisans

MIT © 2026 — bkz. [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey katkıda bulunanlar

</div>