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

**Crie contas Tokvi em massa com chaves de API geradas automaticamente — inferência compatível com OpenAI + conector 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Início rápido](#-quick-start) · [🎁 Nível gratuito](#-free-tier--how-much-you-get) · [🤖 Modelos](#-models--all-21-of-them) · [🧪 Use a API](#-how-to-use-the-api) · [✅ Verificado](#-verified-working--every-key-is-tested) · [🧭 Como funciona](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [⚙️ Opções](#️-cli-options) · [🛰️ Proxies](#️-behind-a-proxy) · [🐞 Problemas](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Por que isto existe

A Tokvi distribui **342.466 unidades de cota (≈ $0,68 com qpu=500.000) para cada nova conta** — sem captcha, sem cartão, apenas verificação por e-mail — e as linhas de chave ativas são os dois ids flash do DeepSeek em seus próprios canais: **`deepseek-v4-flash-0731`** e **`deepseek-v4.1-flash`**. A linha escalou para **101 chaves**; uma reverificação independente de 6 chaves de amostra respondeu 200 com conteúdo real, e a CLI fornecida reverifica cada chave que grava.

## 🎁 Nível gratuito — quanto você recebe

| O quê | Valor |
| --- | --- |
| Crédito de cadastro (`quotaStart`) | **342.466 unidades ≈ $0,68** (qpu=500.000; medido em cada conta) |
| Check-in diário | **desativado** — o painel responde `签到功能未启用` (a CLI mantém a etapa atrás de `--checkin` para o dia em que for ativada) |
| Cadastro | e-mail + código de 6 caracteres — **sem captcha** (`turnstile_check: false`) |
| Catálogo | 21 entradas de modelo na tabela de preços ativa |
| Limites de taxa | sendCode responde `429 发送过于频繁，请等待 N 秒后再试` (a CLI espera o tempo indicado e tenta novamente); register/login limitam com `429` sob rajadas |
| A unidade que escala | **uma conta = uma chave** |

> 💡 O nível gratuito é definido pela Tokvi e pode mudar. Confira os números ativos em [tokvi.net](https://tokvi.net) — e deixe o próprio portão da ferramenta decidir:

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modelos — todos os 21

A tabela de preços ativa contém **21 entradas de modelo**. Ambas as linhas de chave verificadas:

| ID do modelo | Verificado |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** com conteúdo real — o portão principal da casa da moeda |
| `deepseek-v4.1-flash` | **chat-200** — o alternativo para o qual a CLI recorre quando o canal principal está ocupado |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Como usar a API

URL base **`https://tokvi.net/v1`** — compatível com OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Início rápido

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

Uma chave é gravada em `keys.txt` **somente** depois que `/v1/chat/completions` responde 200 com conteúdo real; `accounts.json` mantém nome de usuário / e-mail / cota / código / grupo.

### 🖱️ O launcher

O launcher solicita a lista de proxies e a contagem, memoriza-os e executa a CLI — mint, verify, screen exits, settings, help, Connect to 9Router.

## ✅ Verificado e funcionando — cada chave é testada

| Verificação | Resultado |
| --- | --- |
| `GET /api/status` | **200** — registro ativado, verificação por e-mail ativada, captcha desativado, check-in desativado (`签到功能未启用`) |
| `POST /api/user/register` | **200** com o corpo próprio do fork (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| modelo de código | o painel envia `您的验证码为: <strong>e18f62</strong>` — um **hex misto de 6 caracteres**; a CLI lê exatamente essa âncora (uma varredura apenas de dígitos lê `e12118` como `12118` e o painel responde "Verification code is incorrect or has expired") |
| `POST /v1/chat/completions` — para **cada** chave antes de ser gravada | **200** com `content`/`reasoning_content` reais (`deepseek-v4-flash-0731`, fallback `deepseek-v4.1-flash`) |
| reverificação independente (pool de saída novo, amostra) | **6/6 chaves** 200 + conteúdo não vazio |
| nota sobre caixa de correio compartilhada | o pool de e-mail temporário é público: `Email address is already in use` → a CLI pega uma caixa de correio nova (até 3 por linha) em vez de queimar a tentativa |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

## 🧭 Como funciona

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Etapa | Chamada | Notas |
| --- | --- | --- |
| status | `GET /api/status` | os portões falham fechados (register/mail devem estar ativados, captcha desativado) |
| caixa de correio | Emailnator `POST /api/generate-email` | um @gmail.com novo por conta (forma sem pontos) |
| enviar código | `GET /api/verification?email=…` | 429 → espere os segundos indicados (`请等待 N 秒`) e tente novamente; endereço duplicado → caixa de correio nova |
| código | Emailnator `/api/message-list` → `/api/message/<id>` | a âncora do modelo vence; execuções apenas de dígitos são o último recurso |
| registrar | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}`; um código obsoleto tenta novamente o próximo candidato |
| login | `POST /api/user/login` | token de sessão |
| chave | `POST /api/token/` (+ revelar) | gravada uma vez |
| prova | `POST /v1/chat/completions` | percurso de modelos: `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ Opções da CLI

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Padrão | Significado |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | pool de saída, uma linha por proxy (opcional) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | modelo para a verificação ativa |

## 🛰️ Atrás de um proxy

**Opcional — recomendado em escala (limites por IP, banimentos).** A ferramenta funciona sem proxy também.

- **Com um pool:** passe `--pool <file>` (ou defina `TOKVI_PROXIES`) e cada requisição passa por uma saída dessa lista — uma linha morta falha nessa tentativa e a ferramenta passa para a próxima (falha fechada enquanto um pool é fornecido; o pool é re-sondado no uso).
- **Sem um pool:** a CLI imprime `running WITHOUT a proxy (direct)` e conecta diretamente. Nada sobre cadastro, geração de chaves ou verificação muda — apenas de onde os pacotes saem.
- Execute `--screen` em qualquer um dos modos: ele testa as saídas que você tem (ou a rota direta) contra o provedor antes de uma execução real.

## 📄 Arquivos

| Caminho | Conteúdo |
| --- | --- |
| `keys.txt` | uma chave verificada em produção por linha — ignorado pelo git |
| `accounts.json` | nome de usuário / e-mail / cota / código / grupo |
| `proxies.txt` | suas saídas — ignorado pelo git |
| `outputs/` | logs de execução — ignorado pelo git |

## 🔌 Conectar ao 9Router

[9Router](https://9router.com) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Os modelos são servidos como `tokvi/<model id>`.

## 🔌 Conectar ao OmniRoute

[OmniRoute](https://omniroute.online) é um roteador auto-hospedado e compatível com OpenAI:

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Padrões: `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Os modelos são servidos como `tokvi/<model id>`.
## 🔑 O que uma chave lhe dá

| | |
| --- | --- |
| 🏢 Provedor | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 O que o provedor dá | 342.466 unidades (≈ $0,68) no cadastro |
| 🔑 O que uma chave lhe dá | uma chave em sua própria conta, verificada por chat em `deepseek-v4-flash-0731` |
| 🌐 Formato da API | Compatível com OpenAI: `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Seus dados | apenas `tokvi.net` e seu backend de caixa de correio são contatados |

## 🐞 Problemas

Se a Tokvi mudar seu corpo de registro, modelo de código ou portões, a ferramenta para ali e avisa. **[Abra uma issue](../../issues)** com o comando e a saída exata.

## 📄 Licença

MIT © 2026 — veja [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>