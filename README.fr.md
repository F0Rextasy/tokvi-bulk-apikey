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

**Créez en masse des comptes Tokvi avec des clés API générées automatiquement — inférence compatible OpenAI + connecteur 9Router.**

[![Node](https://img.shields.io/badge/Node-%E2%89%A518-3DA639?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org)
[![Dependencies](https://img.shields.io/badge/dependencies-0-blue?style=for-the-badge)](#)
[![License](https://img.shields.io/badge/license-MIT-6ea8fe?style=for-the-badge)](LICENSE)
[![Models](https://img.shields.io/badge/models-21%20served-412991?style=for-the-badge)](https://tokvi.net)
[![9Router](https://img.shields.io/badge/9Router-connector-8B5CF6?style=for-the-badge)](#-connect-to-9router)
[![OmniRoute](https://img.shields.io/badge/OmniRoute-connector-8B5CF6?style=for-the-badge)](#-connect-to-omniroute)
[![X](https://img.shields.io/badge/X-%40bigbullapp-000000?style=for-the-badge&logo=x&logoColor=white)](https://x.com/bigbullapp)

[🛠 Démarrage rapide](#-quick-start) · [🎁 Offre gratuite](#-free-tier--how-much-you-get) · [🤖 Modèles](#-models--all-21-of-them) · [🧪 Utiliser l'API](#-how-to-use-the-api) · [✅ Vérifié](#-verified-working--every-key-is-tested) · [🧭 Comment ça marche](#-how-it-works) · [🔌 9Router](#-connect-to-9router) · [🔌 OmniRoute](#-connect-to-omniroute) · [⚙️ Options](#️-cli-options) · [🛰️ Proxys](#️-behind-a-proxy) · [🐞 Problèmes](#-issues)

</div>

<div align="center">

**English** · [Türkçe](README.tr.md) · [中文](README.cn.md) · [日本語](README.ja.md) · [한국어](README.ko.md) · [Español](README.es.md) · [Français](README.fr.md) · [Deutsch](README.de.md) · [Русский](README.ru.md) · [Português](README.pt.md)

</div>

---

## ✨ Pourquoi ça existe

Tokvi offre **342 466 unités de quota (≈ 0,68 $ à qpu=500 000) à chaque nouveau compte** — pas de captcha, pas de carte, vérification par e-mail uniquement — et les lignes de clés actives sont les deux identifiants flash DeepSeek dans leurs propres canaux : **`deepseek-v4-flash-0731`** et **`deepseek-v4.1-flash`**. La voie l'a mis à l'échelle sur chaque clé ; une revérification indépendante de 6 clés échantillons a répondu 200 avec du contenu réel, et le CLI fourni revérifie chaque clé qu'il écrit.

## 🎁 Offre gratuite — ce que vous obtenez

| Quoi | Valeur |
| --- | --- |
| Crédit d'inscription (`quotaStart`) | **342 466 unités ≈ 0,68 $** (qpu=500 000 ; mesuré sur chaque compte) |
| Pointage quotidien | **désactivé** — le panneau répond `签到功能未启用` (le CLI garde l'étape derrière `--checkin` pour le jour où elle sera activée) |
| Inscription | e-mail + code de 6 caractères — **pas de captcha** (`turnstile_check: false`) |
| Catalogue | 21 entrées de modèles sur la grille tarifaire en direct |
| Limites de débit | sendCode répond `429 发送过于频繁，请等待 N 秒后再试` (le CLI attend le temps indiqué et réessaie) ; limitation register/login `429` en cas de rafales |
| L'unité qui passe à l'échelle | **un compte = une clé** |

> 💡 L'offre gratuite est définie par Tokvi et peut changer. Vérifiez les chiffres en direct sur [tokvi.net](https://tokvi.net) — et laissez la propre barrière de l'outil décider :

```bash
node src/cli.js --verify-only     # every key in keys.txt is re-tested with a real chat call
```

## 🤖 Modèles — les 21 au complet

La grille tarifaire en direct comporte **21 entrées de modèles**. Les deux lignes de clés vérifiées :

| ID du modèle | Vérifié |
| --- | --- |
| `deepseek-v4-flash-0731` | **chat-200** avec du contenu réel — la barrière principale de la fabrique |
| `deepseek-v4.1-flash` | **chat-200** — l'alternative vers laquelle le CLI se rabat quand le canal principal est occupé |

```bash
curl https://tokvi.net/v1/chat/completions \
  -H "Authorization: Bearer 6ZMXaW…" -H "Content-Type: application/json" \
  -d '{"model":"deepseek-v4-flash-0731","messages":[{"role":"user","content":"hi"}],"max_tokens":8}'
```

## 🧪 Comment utiliser l'API

URL de base **`https://tokvi.net/v1`** — compatible OpenAI.

```python
from openai import OpenAI
c = OpenAI(base_url="https://tokvi.net/v1", api_key="6ZMXaW…")
print(c.chat.completions.create(model="deepseek-v4-flash-0731",
      messages=[{"role": "user", "content": "hi"}]).choices[0].message.content)
```

## 🛠 Démarrage rapide

```bash
cd tokvi-bulk-apikey
printf 'socks5://host:port\n' > proxies.txt   # optional — without it the tool runs direct
node src/launcher.js        # interactive: mint / verify / screen / settings

# or: node src/cli.js --count 30
```

Une clé n'est écrite dans `keys.txt` **qu'après** que `/v1/chat/completions` ait répondu 200 avec du contenu réel ; `accounts.json` conserve le nom d'utilisateur / l'e-mail / le quota / le code / le groupe.

### 🖱️ Le lanceur

Le lanceur demande la liste de proxys et le nombre, les mémorise, et exécute le CLI — fabriquer, vérifier, tester les sorties, paramètres, aide, Connect to 9Router.

## ✅ Vérifié fonctionnel — chaque clé est testée

| Vérification | Résultat |
| --- | --- |
| `GET /api/status` | **200** — inscription activée, vérification par e-mail activée, captcha désactivé, pointage désactivé (`签到功能未启用`) |
| `POST /api/user/register` | **200** avec le corps propre au fork (`{aff_code: null, acquisition_source: 'manual', …, verification_code}`) |
| modèle de code | le panneau envoie `您的验证码为: <strong>e18f62</strong>` — un **hexadécimal mixte de 6 caractères** ; le CLI lit exactement cette ancre (une analyse à chiffres uniquement lit `e12118` comme `12118` et le panneau répond « Verification code is incorrect or has expired ») |
| `POST /v1/chat/completions` — pour **chaque** clé avant son écriture | **200** avec du `content`/`reasoning_content` réel (`deepseek-v4-flash-0731`, repli `deepseek-v4.1-flash`) |
| revérification indépendante (pool de sortie frais, échantillon) | chaque clé 200 + contenu non vide |
| note sur la boîte mail partagée | le pool de temp-mail est public : `Email address is already in use` → le CLI prend une boîte mail fraîche (jusqu'à 3 par rail) au lieu de brûler la tentative |

```bash
node src/cli.js --verify-only
node src/cli.js --screen
```

> **Barrières de qualité.** Chaque clé ici a passé un `chat-200` en direct avant d'être écrite dans `keys.txt`
> (les clés mortes ne sont jamais livrées). Les combinaisons de routeur suivent la même règle au niveau du modèle : une entrée n'est
> ajoutée **que si le modèle répond `chat-200` et peut appeler des outils** — un modèle sans prise en charge des outils,
> une clé morte ou un fournisseur mort est écarté au lieu d'être ajouté. Relancez l'étape de vérification à tout moment ;
> les échecs sont signalés, jamais cachés.

## 🧭 Comment ça marche

```
inbox (one address per account)                                  a fresh address per account
   │
   ├─▶ sign-up: Tokvi page → e-mail address → code / link from the inbox
   ├─▶ session: the account's cookies or access token in hand
   ├─▶ key mint: the account's key endpoint → plaintext key returned once (written to disk immediately)
   └─▶ live proof: POST https://tokvi.net/v1/chat/completions → real content = the key is live
```

| Étape | Appel | Notes |
| --- | --- | --- |
| statut | `GET /api/status` | les barrières échouent en mode fermé (register/mail doivent être activés, captcha désactivé) |
| boîte mail | Emailnator `POST /api/generate-email` | un @gmail.com frais par compte (forme sans point) |
| envoyer le code | `GET /api/verification?email=…` | 429 → attendre le nombre de secondes indiqué (`请等待 N 秒`) et réessayer ; adresse en double → boîte mail fraîche |
| code | Emailnator `/api/message-list` → `/api/message/<id>` | l'ancre du modèle l'emporte ; les suites de chiffres uniquement sont un dernier recours |
| inscription | `POST /api/user/register` | `{aff_code: null, acquisition_source: 'manual', …}` ; un code périmé réessaie le candidat suivant |
| connexion | `POST /api/user/login` | jeton de session |
| clé | `POST /api/token/` (+ reveal) | écrite une fois |
| preuve | `POST /v1/chat/completions` | parcours des modèles : `deepseek-v4-flash-0731` → `deepseek-v4.1-flash` |

## ⚙️ Options du CLI

```bash
node src/cli.js --count N        # mint N keys (chat gate per account)
node src/cli.js --model deepseek-v4-flash-0731 --alt-models deepseek-v4.1-flash
node src/cli.js --checkin        # opt in if the panel ever turns check-in on
node src/cli.js --verify-only    # re-verify every key in keys.txt
node src/cli.js --screen         # exit pool self-test
node src/cli.js --connect-9router
node src/cli.js --help
```

| Env | Défaut | Signification |
| --- | --- | --- |
| `TOKVI_PROXIES` | `proxies.txt` | pool de sortie, une ligne par proxy (facultatif) |
| `TOKVI_MODEL` | `deepseek-v4-flash-0731` | modèle pour la vérification en direct |

## 🛰️ Derrière un proxy

**Facultatif — recommandé à grande échelle (limites par IP, bannissements).** L'outil fonctionne aussi sans proxy.

- **Avec un pool :** passez `--pool <file>` (ou définissez `TOKVI_PROXIES`) et chaque requête passe par une sortie de cette liste — une ligne morte fait échouer cette tentative et l'outil passe à la suivante (échec en mode fermé tant qu'un pool est fourni ; le pool est re-testé à l'usage).
- **Sans pool :** le CLI affiche `running WITHOUT a proxy (direct)` et se connecte directement. Rien ne change concernant l'inscription, la création de clés ou la vérification — seul le point de sortie des paquets change.
- Lancez `--screen` dans l'un ou l'autre mode : il teste les sorties dont vous disposez (ou la route directe) contre le fournisseur avant une exécution réelle.

## 📄 Fichiers

| Chemin | Contenu |
| --- | --- |
| `keys.txt` | une clé vérifiée en direct par ligne — ignoré par git |
| `accounts.json` | nom d'utilisateur / e-mail / quota / code / groupe |
| `proxies.txt` | vos sorties — ignoré par git |
| `outputs/` | journaux d'exécution — ignorés par git |

## 🔌 Se connecter à 9Router

[9Router](https://9router.com) est un routeur auto-hébergé compatible OpenAI :

```bash
node src/connect-9router.js --base http://localhost:20128 --password 123456
```

Valeurs par défaut : `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Les modèles sont servis sous `tokvi/<model id>`.

## 🔌 Se connecter à OmniRoute

[OmniRoute](https://omniroute.online) est un routeur auto-hébergé compatible OpenAI :

```bash
node src/connect-omniroute.js --base http://localhost:20128 --password 123456
```

Valeurs par défaut : `--prefix tokvi`, `--base-url https://tokvi.net/v1`, `--model deepseek-v4-flash-0731`. Les modèles sont servis sous `tokvi/<model id>`.

## 🔑 Ce qu'une clé vous apporte

| | |
| --- | --- |
| 🏢 Fournisseur | **Tokvi** — [tokvi.net](https://tokvi.net) |
| 🎁 Ce que le fournisseur offre | 342 466 unités (≈ 0,68 $) à l'inscription |
| 🔑 Ce qu'une clé vous apporte | une clé sur son propre compte, vérifiée par chat sur `deepseek-v4-flash-0731` |
| 🌐 Forme de l'API | Compatible OpenAI : `POST https://tokvi.net/v1/chat/completions` |
| 🔒 Vos données | seuls `tokvi.net` et votre backend de boîte mail sont contactés |

## 🐞 Problèmes

Si Tokvi modifie le corps de son inscription, son modèle de code ou ses barrières, l'outil s'arrête là et le signale. **[Ouvrez un ticket](../../issues)** avec la commande et la sortie exacte.

## 📄 Licence

MIT © 2026 — voir [LICENSE](LICENSE).

<div align="center">

**[MIT](LICENSE)** © 2026 tokvi-bulk-apikey contributors

</div>