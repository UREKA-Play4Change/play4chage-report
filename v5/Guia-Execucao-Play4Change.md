# Guia de Execução — Play4Change

Guia para colocar a plataforma Play4Change a correr localmente (backend), aceder ao portal web já publicado e instalar a aplicação móvel Android.

---

## 1. Visão geral da arquitetura

| Componente | Tecnologia | Onde vive | Como se corre |
|---|---|---|---|
| **Backend (REST API)** | Kotlin + Spring Boot 3.2, Arrow Either | repo `play4change-core` | Docker Compose, localmente ou em servidor próprio |
| **Base de dados** | PostgreSQL 16 + pgvector, Flyway | repo `play4change-core` (container) | Sobe automaticamente via Docker Compose |
| **Storage de ficheiros** | MinIO (compatível com S3) | repo `play4change-core` (container) | Sobe automaticamente via Docker Compose |
| **Agente de IA** | LangChain4j 0.36 + Mistral (`mistral-small-latest`) | repo `play4change-core` (módulo `ai-agent`) | Corre dentro do container do servidor |
| **Web portal** (landing page + admin) | React, Vite, TypeScript | repo `play4change-web` | Já publicado — basta abrir o URL |
| **App móvel** | Kotlin Multiplatform + Compose Multiplatform (Android/iOS) | repo `play4change-core` (`composeApp`) | APK pronto, distribuído a partir do web portal |
| **Observabilidade** | Micrometer, Prometheus, Grafana | repo `play4change-core` (containers) | Sobe automaticamente via Docker Compose |

Resumindo o fluxo para um colega novo:
1. Corre o **backend** localmente com Docker Compose.
2. Usa o **web portal**, que já está publicado no Cloudflare Pages — não precisa de correr nada.
3. Instala a **app Android** a partir do APK disponibilizado no próprio site.

---

## 2. Repositórios

| Repositório | URL | Conteúdo |
|---|---|---|
| `play4change-core` | https://github.com/UREKA-Play4Change/play4change-core | Backend (Spring Boot), agente de IA, app mobile (Kotlin Multiplatform), infra (Docker, Nginx, Prometheus, Grafana) |
| `play4change-web` | https://github.com/UREKA-Play4Change/play4change-web | Portal web (React) — landing page pública + dashboard admin |

Clonar:

```bash
git clone https://github.com/UREKA-Play4Change/play4change-core.git
git clone https://github.com/UREKA-Play4Change/play4change-web.git
```

> Nota: só é preciso clonar `play4change-web` se quiseres correr o frontend localmente (ex: para desenvolvimento). Para apenas usar a aplicação, o site já publicado chega.

---

## 3. Tecnologias/software necessário na máquina

| Ferramenta | Necessário para | Notas |
|---|---|---|
| Docker + Docker Compose | Correr o backend | Obrigatório |
| JDK 21 | Builds locais de Gradle fora do Docker (opcional) | Só se fores mexer no código do servidor |
| Node.js 20+ | Correr o frontend localmente (opcional) | Só se fores mexer no código do web |
| MinIO Client (`mc`) | Criar o bucket do MinIO no primeiro arranque | `brew install minio/stable/mc` (Mac) |
| Android Studio / `adb` | Compilar o APK localmente ou instalar em dispositivo via USB | Opcional — o APK já vem pronto |
| Um telemóvel/emulador Android | Instalar a app móvel | — |

---

## 4. Configurar o backend antes de correr

No repositório `play4change-core`, copiar o ficheiro de exemplo e preencher:

```bash
cp .env.example .env
```

| Variável | Descrição | Onde obter |
|---|---|---|
| `JWT_SECRET` | Segredo de assinatura HS256 (mín. 32 caracteres) | Gerar uma string aleatória |
| `MISTRAL_API_KEY` | Chave da API Mistral para geração de conteúdo | https://console.mistral.ai |
| `RESEND_API_KEY` | Chave da API Resend para emails de magic link | https://resend.com |
| `RESEND_FROM` | Endereço remetente (ex: `noreply@teudominio.com`) | — |
| `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` | Credenciais do MinIO | Default: `minioadmin` / `minioadmin` |
| `DB_USER` / `DB_PASS` | Credenciais do Postgres | Default: `play4change` / `play4change` |
| `FRONTEND_ORIGIN` | Origem permitida por CORS (URL do frontend) | Ex: `https://play4change-web.pages.dev` |
| `CORS_ALLOWED_ORIGINS` | Lista de origens permitidas por CORS | — |
| `SPRING_PROFILES_ACTIVE` | Perfil ativo: `dev`, `test` ou `prod` | — |
| `GRAFANA_ADMIN_PASSWORD` | Password de admin do Grafana | — |
| `CLOUDFLARE_TUNNEL_TOKEN` / `CLOUDFLARE_TUNNEL_ID` | Só necessário se fores expor o backend à internet via Cloudflare Tunnel | Não é preciso para correr só localmente |

> Sem `MISTRAL_API_KEY` e `RESEND_API_KEY` válidas, a geração de conteúdo por IA e o envio de magic links não funcionam — o resto da aplicação corre na mesma.

---

## 5. Correr o backend

```bash
cd play4change-core

./scripts/setup.sh          # builda e sobe todos os containers (Postgres, MinIO, scraper, unstructured, server, nginx, prometheus, grafana)
./scripts/minio-init.sh     # cria o bucket do MinIO (só na primeira vez)
```

Verificar que o servidor está de pé:

```bash
curl http://localhost:8080/actuator/health
# {"status":"UP"}
```

### Serviços disponíveis depois do arranque

| Serviço | URL | Credenciais |
|---|---|---|
| REST API | http://localhost:8080 | — |
| Swagger UI (docs interativas) | http://localhost:8080/swagger-ui.html | — |
| Grafana | http://localhost:3000 | admin / (`GRAFANA_ADMIN_PASSWORD`) |
| Prometheus | http://localhost:9090 | — |
| Consola MinIO | http://localhost:9001 | valor de `MINIO_ROOT_USER` / `MINIO_ROOT_PASSWORD` |

### Scripts úteis

| Script | Para quê |
|---|---|
| `./scripts/setup.sh` | Rebuild completo — **apaga volumes** (Postgres/MinIO) |
| `./scripts/restart-server.sh` | Rebuild só do servidor, sem apagar dados — usar durante desenvolvimento |
| `./scripts/db-shell.sh` | Abre uma sessão `psql` dentro do container Postgres |
| `./scripts/promote-admin.sh <email>` | Promove um utilizador a ADMIN (ver secção 7) |
| `./scripts/build-android.sh [debug\|release]` | Compila o APK localmente |

---

## 6. Aceder ao web portal (já publicado)

Não é preciso correr nada — o frontend já está publicado no Cloudflare Pages:

**https://play4change-web.pages.dev/**

- `/` — landing page pública, sem autenticação.
- `/admin` — dashboard de administração, requer conta com role `ADMIN` (autenticação por magic link).

O deploy é automático via GitHub Actions (`deploy.yml`) sempre que há push para `main` do repositório `play4change-web`.

> Se precisares de correr o frontend localmente para desenvolvimento: `npm install && npm run dev` dentro de `play4change-web`, com um `.env.local` a apontar `VITE_API_BASE_URL` para o teu backend (ex: `http://localhost:8080`) e `VITE_USE_MOCK=false`.

---

## 7. Primeiro utilizador administrador

Não existe conta admin pré-criada. Depois de qualquer utilizador fazer login pelo menos uma vez (via magic link no portal):

```bash
./scripts/promote-admin.sh o-email-da-pessoa@exemplo.com
```

A mudança de role só tem efeito depois de a pessoa sair e voltar a entrar (logout/login).

---

## 8. Instalar a aplicação móvel (Android)

A app já vem compilada como APK e é distribuída diretamente a partir do web portal:

1. No telemóvel Android, abrir **https://play4change-web.pages.dev/play4change.apk** (ou o link de download disponibilizado no site).
2. Autorizar "instalar de fontes desconhecidas" quando pedido pelo Android.
3. Instalar e abrir a app.

Alternativa — compilar o APK a partir do código-fonte:

```bash
cd play4change-core
./scripts/build-android.sh release   # ou "debug"
adb install -r <caminho-do-apk-gerado>
```

Novas versões (tags `v*` no repositório) geram automaticamente um novo APK via GitHub Actions (`release.yml`), publicado como GitHub Release.

---

## 9. Verificação final (checklist)

- [ ] `docker compose ps` — todos os containers com estado `healthy`/`running`
- [ ] `curl http://localhost:8080/actuator/health` → `{"status":"UP"}`
- [ ] Swagger UI acessível em `http://localhost:8080/swagger-ui.html`
- [ ] Portal web acessível em `https://play4change-web.pages.dev/`
- [ ] Login por magic link funciona (recebido via Resend)
- [ ] Utilizador promovido a ADMIN consegue aceder a `/admin`
- [ ] APK instalado e app móvel abre sem erros

---

## 10. Problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| `server` não fica `healthy` | Postgres/MinIO ainda a arrancar, ou env vars em falta | Ver logs: `docker compose logs -f server` |
| Emails de magic link não chegam | `RESEND_API_KEY`/`RESEND_FROM` inválidos ou domínio não verificado no Resend | Confirmar configuração no dashboard da Resend |
| Erro de CORS no browser | `FRONTEND_ORIGIN`/`CORS_ALLOWED_ORIGINS` não incluem o URL usado | Ajustar no `.env` e correr `./scripts/restart-server.sh` |
| Geração de conteúdo por IA falha | `MISTRAL_API_KEY` inválida ou sem créditos | Confirmar chave na consola Mistral |
| Android bloqueia a instalação do APK | Play Protect / fontes desconhecidas desativadas | Ativar instalação de fontes desconhecidas nas definições |
