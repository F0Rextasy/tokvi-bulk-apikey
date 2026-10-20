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

**Crea cuentas de Tokvi en masa con claves API autogeneradas — inferencia compatible con OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Inicio rápido](#-quick-start) · [🎁 Nivel gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-21-of-them) · [🧪 Usar la API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Cómo funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opciones](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por qué existe esto

Tokvi reparte **342.466 unidades de cuota (≈ $0,68 con qpu=500.000) a cada cuenta nueva** — sin captcha, sin tarjeta, solo verificación por correo electrónico — y las líneas de claves activas son los dos ids flash de DeepSeek en sus propios canales: **`deepseek-v4-flash-0731`** y **`deepseek-v4.1-flash`**. El carril lo escaló a **101 claves**; una reverificación independiente de 6 claves de muestra respondió 200 con contenido real, y la CLI incluida vuelve a verificar cada clave que escribe.

## 🎁 Nivel gratuito — cuánto obtienes

| Qué | Valor |
| --- | --- |
| Crédito de registro (`quotaStart`) | **342.466 unidades ≈ $0,68** (qpu=500.000; medido en cada cuenta) |
| Check-in diario | **desactivado** — el panel responde `签到功能未启用` (la CLI mantiene el paso detrás de `--checkin` para el día en que se active) |
| Registro | correo electrónico + código de 6 caracteres — **sin captcha** (`turnstile_check: false`) |
| Catálogo | 21 entradas de modelos en la hoja de precios activa |
| Límites de tasa | sendCode responde `429 发送过于频繁，请等待 N 秒后再试` (la CLI espera el tiempo indicado y reintenta); register/login limitan con `429` bajo ráfagas |
| La unidad que escala | **una cuenta = una clave** |

> 💡 El nivel gratuito lo define Tokvi y puede cambiar. Consulta los números activos en [tokvi.net](https://tokvi.net) — y deja que la propia puerta de la herramienta decida:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — los 21

La hoja de precios activa contiene **21 entradas de modelos**. Ambas líneas de claves verificadas:

| ID del modelo | Verificado |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** con contenido real — la puerta principal de la casa de moneda |
| `deepseek-v4.1-flash` | **chat-200** — la alternativa a la que la CLI recurre cuando el canal principal está ocupado |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Cómo usar la API

URL base **`https://tokvi.net/v1`** — compatible con OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Inicio rápido

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings
# or: node src/cli.js --count 30
```

Una clave se escribe en `keys.txt` **solo** después de que `/v1/chat/completions` responda 200 con contenido real; `accounts.json` guarda usuario / correo electrónico / cuota / código / grupo.

### 🖱️ El lanzador

El lanzador pide la lista de proxies y la cantidad, las recuerda y ejecuta la CLI — acuñar, verificar, cribar salidas, ajustes, ayuda, Conectar a 9Router.

## ✅ Verificado y funcionando — cada clave se prueba

| Comprobación | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro activado, verificación por correo activada, captcha desactivado, check-in desactivado (`签到功能未启用`) |
| `POST /api/user/register` | **200** con el cuerpo propio del fork (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| plantilla del código | el panel envía `您的验证码为: <strong>e18f62</strong>` — un **hex mixto de 6 caracteres**; la CLI lee exactamente ese ancla (un escaneo solo de dígitos lee `e12118` como `12118` y el panel responde "Verification code is incorrect or has expired") |
| `POST /v1/chat/completions` — para **cada** clave antes de escribirse | **200** con `content`/`reasoning_content` reales (`deepseek-v4-flash-0731`, con reserva `deepseek-v4.1-flash`) |
| reverificación independiente (grupo de salida nuevo, muestra) | **6/6 claves** 200 + contenido no vacío |
| nota sobre buzón compartido | el grupo de correo temporal es público: `Email address is already in use` → la CLI toma un buzón nuevo (hasta 3 por carril) en lugar de quemar el intento |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Cómo funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Paso | Llamada | Notas |
| --- | --- | --- |
| estado | `GET /api/status` | las puertas fallan en cerrado (registro/correo deben estar activados, captcha desactivado) |
| buzón | Emailnator `POST /api/generate-email` | un @gmail.com nuevo por cuenta (forma sin puntos) |
| enviar código | `GET /api/verification?email=…` | 429 → espera los segundos indicados (`请等待 N 秒`) y reintenta; dirección duplicada → buzón nuevo |
| código | Emailnator `/api/message-list` → `/api/message/<id>` | gana el ancla de la plantilla; los escaneos solo de dígitos son el último recurso |
| registro | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; un código obsoleto reintenta con el siguiente candidato |
| inicio de sesión | `POST /api/user/login` | token de sesión |
| clave | `POST /api/token/` (+ revelar) | se escribe una vez |
| prueba | `POST /v1/chat/completions` | recorrido de modelos: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ Opciones de la CLI

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Variable de entorno | Predeterminado | Significado |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | grupo de salida, una línea por proxy (opcional) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | modelo para la verificación en vivo |

## 🛰️ Detrás de un proxy

**Opcional — recomendado a escala (límites por IP, bloqueos).** La herramienta también funciona sin proxy.

- **Con un grupo:** pasa `--pool <file>` (o define `TOKVI_PROXIES`) y cada solicitud pasa por una salida de esa lista — una línea muerta hace fallar ese intento y la herramienta pasa a la siguiente (falla en cerrado mientras se proporciona un grupo; el grupo se vuelve a sondear al usarse).
- **Sin un grupo:** la CLI imprime `running WITHOUT a proxy (direct)` y se conecta directamente. Nada sobre el registro, la acuñación de claves o la verificación cambia — solo desde dónde salen los paquetes.
- Ejecuta `--screen` contra cualquiera de los dos modos: prueba las salidas que tengas (o la ruta directa) contra el proveedor antes de una ejecución real.

## 📄 Archivos

| Ruta | Contenido |
| --- | --- |
| `keys.txt` | una clave verificada en vivo por línea — ignorada por git |
| `accounts.json` | usuario / correo electrónico / cuota / código / grupo |
| `proxies.txt` | tus salidas — ignorado por git |
| `outputs/` | registros de ejecución — ignorados por git |

## 🔌 Conectar a 9Router

[9Router](https://9router.com) es un router autoalojado y compatible con OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Predeterminados: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Los modelos se sirven como `tokvi/<model id>`.

## 🔑 Qué te da una clave

| | |
| --- | --- |
| 🏢 Proveedor | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 Qué da el proveedor | 342.466 unidades (≈ $0,68) al registrarse |
| 🔑 Qué te da una clave | una clave en su propia cuenta, verificada por chat en `deepseek-v4-flash-0731` |
| 🌐 Forma de la API | Compatible con OpenAI: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Tus datos | solo se contacta a `tokvi.net` y a tu backend de buzón |

## 🐞 Problemas

Si Tokvi cambia su cuerpo de registro, plantilla de código o puertas, la herramienta se detiene ahí y lo indica. **[Abre un issue](../../issues)** con el comando y la salida exacta.

## 📄 Licencia

MIT © 2026 — consulta [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>