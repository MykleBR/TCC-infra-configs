## Documento Técnico de Funcionalidades

Este documento consolida as funcionalidades expostas pelas APIs e o roteamento/funcionalidades do Frontend Angular do projeto.

### Convenções
- Bases das APIs:
  - Auth API: `/auth/v1`
  - Core API: `/core/v1`
  - Message API: `/message/v1`

---

## Auth API (`@api-auth/`)

Base: `/auth/v1`

### Rotas e Endpoints

- `/auth`
  - `GET /check-username` — Verifica disponibilidade de nome de usuário
  - `GET /clarify` — Endpoint utilitário para esclarecimentos (diagnóstico)
  - `POST /signin` — Login local (com CAPTCHA e estratégia local)
  - `POST /update` — Atualiza dados do usuário autenticado
  - `POST /password-recovery` — Inicia recuperação de senha (CAPTCHA)
  - `POST /change-password` — Troca de senha (CAPTCHA + autenticado)
  - `GET /me` — Retorna dados do usuário autenticado
  - Integração Google:
    - `POST /signin-google` — Login via Google
    - `GET /validate-google-token` — Valida token Google
    - `POST /set-google-account` — Associa conta Google ao usuário
    - `POST /unlink-google-account` — Desassocia conta Google

- `/companies`
  - `POST /` — Cria empresa (autenticado)
  - `GET /` — Lista empresas (autenticado)
  - `GET /:id_company` — Obtém empresa por id (autenticado)
  - `PUT /:id_company` — Atualiza empresa (autenticado)
  - `DELETE /:id_company` — Remove empresa (autenticado)

- `/general-tokens`
  - `POST /` — Cria token geral (autenticado)
  - `GET /` — Valida token geral (autenticado)

- `/groups`
  - `POST /` — Cria grupo (autenticado)
  - `GET /` — Lista grupos (autenticado)
  - `GET /:id` — Obtém grupo (autenticado)
  - `PUT /:id` — Atualiza grupo (autenticado)
  - `DELETE /:id` — Remove grupo (autenticado)

- `/menus`
  - `GET /` — Lista itens de menu por permissões (autenticado)

- `/permissions`
  - `GET /` — Lista permissões (autenticado)

- `/users`
  - `POST /` — Cria usuário
  - `GET /` — Lista usuários (autenticado)
  - `GET /permissions` — Lista permissões do usuário (autenticado)
  - `GET /:id` — Obtém usuário (autenticado)
  - `PUT /:id` — Atualiza usuário (autenticado)
  - `DELETE /:id` — Remove usuário (autenticado)

- `/email-confirmation`
  - `POST /` — Cria confirmação de e-mail
  - `POST /send-code` — Envia código de confirmação
  - `POST /resend-code` — Reenvia código de confirmação
  - `POST /check-code` — Valida código (autenticado)

- `/invite`
  - `POST /decode` — Decodifica convite
  - `GET /` — Lista convites (autenticado)
  - `POST /` — Cria convite (autenticado)
  - `POST /:id` — Reenvia convite (autenticado)
  - `DELETE /:id` — Remove convite (autenticado)

---

## Core API (`@api-core/`)

Base: `/core/v1`

### Rotas e Endpoints

- `/person`
  - `POST /` — Cria pessoa
  - `GET /` — Lista pessoas (autenticado)
  - `GET /:id` — Obtém pessoa (autenticado)
  - `PUT /:id` — Atualiza pessoa (autenticado)
  - `DELETE /:id` — Remove pessoa (autenticado)

- `/address`
  - `POST /` — Cria endereço
  - `GET /` — Lista endereços (autenticado)
  - `GET /:id` — Obtém endereço (autenticado)
  - `PUT /:id` — Atualiza endereço (autenticado)
  - `DELETE /:id` — Remove endereço (autenticado)

- `/city`
  - `POST /` — Cria cidade
  - `GET /` — Lista cidades (autenticado)
  - `GET /:id` — Obtém cidade (autenticado)
  - `PUT /:id` — Atualiza cidade (autenticado)
  - `DELETE /:id` — Remove cidade (autenticado)

- `/state`
  - `POST /` — Cria estado
  - `GET /` — Lista estados (autenticado)
  - `GET /:id` — Obtém estado (autenticado)
  - `PUT /:id` — Atualiza estado (autenticado)
  - `DELETE /:id` — Remove estado (autenticado)

---

## Message API (`@api-message/`)

Base: `/message/v1`

### Rotas e Endpoints

- `/general-tokens`
  - `POST /` — Cria token geral (autenticado)
  - `GET /` — Valida token geral (autenticado)

- `/email`
  - `POST /` — Envia e-mail (autenticado)

---

## Frontend Angular (`@frontend/`)

### Fluxo de Roteamento

- `auth` (lazy load `AuthModule`)
  - `auth/login` — Tela de login
  - `auth/register/:token` — Cadastro de usuário a partir de token
  - `auth/password-recovery/:token` — Recuperação de senha via token

- Raiz (protegida por `AuthGuardService.isLoggedIn()`): carrega módulo conforme `environment.mode`:
  - `AdminModule` (quando `mode = ADMIN`)
    - `` — Dashboard/Admin `IndexComponent`
    - `companies` — Lazy: `content/admin/modules/company/CompanyModule`
  - `CompanyModule` (quando `mode = COMPANY`)
    - `` — `IndexComponent`
    - `administrative` — Lazy `AdministrativeModule`
      - `details` — `CompanyDetailsComponent`
      - `access` — Lazy `AccessModule`
    - `human-resources` — Lazy `HumanResourcesModule`
    - `pedagogical` — Lazy `PedagogicalModule`
    - `finance` — Lazy `FinancialModule`
    - `marketing` — Lazy `MarketingModule`
    - `infrastructure` — Lazy `InfrastructureModule`

### Guards e Resolvers
- `AuthGuardService.isLoggedIn()` — Protege a área autenticada
- `AuthService.hasRole(UserRole.ADMIN)` — Protege rotas do Admin
- `CompanyGuardService.hasSelectedCompany()` — Garante empresa selecionada
- Resolver em `CompanyModule` root: carrega empresa corrente (`CompanyService.setCurrent('current')`) e despacha estado via NgRx (`SetStateAction`)

### Integrações e Interceptadores (alto nível)
- Interceptador HTTP (autenticação/cabeçalhos) em `core/interceptors/http-interceptor.service.ts`
- Serviços de autenticação em `core/services/auth/*` (login, token, usuário, empresa)
- Serviços de endereço em `core/services/address/*`
- Componentes de layout: `BaseLayoutComponent`, `EmptyLayoutComponent`
- Componentes compartilhados: breadcrumb, header, sidebar, user-topbar, etc.

---

## Health Checks
- Auth API: `GET /auth/v1/health` — OK
- Core API: `GET /core/v1/health` — OK
- Message API: `GET /message/v1/health` — OK

---

## Observações
- Todas as APIs utilizam CORS, Helmet, cookies e compressão. As rotas principais ficam versionadas e montadas em `/auth/v1`, `/core/v1` e `/message/v1`.
- Diversos endpoints exigem autenticação via middleware `isAuthenticated`; algumas funções de criação (POST) em Core não exigem autenticação conforme arquivos de rota.

---

## Padrões de API (AS-IS)

### Autenticação
- Header: `Authorization: Bearer <token>`
- Middleware: as APIs validam o header via `isAuthenticated`, que chama um serviço de clarificação (`/auth/clarify`).
- Frontend: um `HttpInterceptor` adiciona o header `Authorization` automaticamente quando há token em sessão.

### Erros
- Formato de erro (padronizado pelo middleware global):
```json
{
  "message": "auth.error.captcha_invalid",
  "error": { "stack": "..." } // presente fora de produção
}
```
- Status HTTP conforme contexto (ex.: 401 sem token, 404 NotFound, 500 exceção).

### CORS
- Pré-flight habilitado e controlado (Nginx e Express). Headers permitidos: `Authorization, Content-Type, X-Requested-With`.
- Em produção, origens permitidas são validadas; em desenvolvimento, mais permissivo.

### Segurança adicional
- reCAPTCHA no login e processos sensíveis (`/auth/signin`, `/auth/password-recovery`, `/auth/change-password`).
- TLS obrigatório no edge (Nginx) com certificados válidos.

---

## Exemplos de Requests/Responses (principais)

### Auth — Login local
Request
```bash
curl -X POST 'https://api.tcc-cesumar.com.br/auth/v1/auth/signin' \
  -H 'Content-Type: application/json' \
  -d '{
    "username": "jdoe",
    "password": "secret",
    "captcha": "<token-recaptcha>"
  }'
```
Response (exemplo)
```json
{
  "token": "<jwt>",
  "token_expiration": 1699999999,
  "user": { "id": "...", "username": "jdoe", "roles": ["admin"], "companies": [] }
}
```

### Auth — Dados do usuário autenticado
```bash
curl -X GET 'https://api.tcc-cesumar.com.br/auth/v1/auth/me' \
  -H 'Authorization: Bearer <jwt>'
```

### Users — Criar usuário
```bash
curl -X POST 'https://api.tcc-cesumar.com.br/auth/v1/users' \
  -H 'Content-Type: application/json' \
  -d '{
    "username": "jdoe",
    "password": "secret",
    "email": "john@example.com",
    "roles": ["company"],
    "person_id": null
  }'
```

### Core — Criar pessoa
Payload (IPerson)
```json
{
  "name": "João da Silva",
  "cpf": "12345678900",
  "phone": "+5541999999999",
  "birthday": "1990-01-01",
  "person_type": "employee"
}
```
Request
```bash
curl -X POST 'https://api.tcc-cesumar.com.br/core/v1/person' \
  -H 'Content-Type: application/json' \
  -d @payload.json
```

### Message — Enviar e-mail (com template)
Payload aceito (AS-IS)
```json
{
  "to": "destinatario@exemplo.com",
  "template_name": "user_invite",
  "subject": "Bem-vindo",
  "link": "https://app.tcc-cesumar.com.br/register/abcd",
  "name": "João"
}
```
Request
```bash
curl -X POST 'https://api.tcc-cesumar.com.br/message/v1/email' \
  -H 'Authorization: Bearer <jwt>' \
  -H 'Content-Type: application/json' \
  -d @payload.json
```
Response (sucesso)
```json
{ "id": "<uuid>", "status": "sent" }
```
Response (falha)
```json
{ "id": "<uuid>", "status": "failed", "error": "SMTP não configurado ..." }
```

### Message — Token genérico
- Criar
```bash
curl -X POST 'https://api.tcc-cesumar.com.br/message/v1/general-tokens' \
  -H 'Authorization: Bearer <jwt>' \
  -H 'Content-Type: application/json' \
  -d '{ "payload": { "inviteId": "..." }, "expires_in": 3600 }'
```
Response
```json
{ "token": "<token>" }
```
- Validar
```bash
curl -X GET 'https://api.tcc-cesumar.com.br/message/v1/general-tokens?token=<token>' \
  -H 'Authorization: Bearer <jwt>'
```

---

## Arquitetura e Deploy (AS-IS)

### Nginx (Edge)
- Domínio: `api.tcc-cesumar.com.br` com redirecionamento HTTP→HTTPS e TLS (Let’s Encrypt).
- Rotas de proxy:
  - `/auth/` → `auth-api:8000`
  - `/core/` → `core-api:8001`
  - `/message/` → `message-api:8002`
- Pré-flight CORS tratado no edge com headers e `max-age` de 600s.

### Docker Compose (EC2)
- Serviços: `auth-api` (8000), `core-api` (8001), `message-api` (8002), `nginx-api-proxy` (80/443).
- Certificados montados em `/etc/letsencrypt` (somente leitura).

### Frontend
- Angular entregue via S3/CloudFront nos domínios `app.tcc-cesumar.com.br` e `admin.tcc-cesumar.com.br`.
- O app seleciona módulo (`AdminModule`/`CompanyModule`) conforme `environment.mode`.

### Integrações
- Google reCAPTCHA (Auth).
- SMTP/SES (Message) via `nodemailer`.

---

## Variáveis de Ambiente (principais)

### Comuns
- `CORS_ORIGINS` — Lista de origens permitidas em produção.

### Auth/Core
- `GOOGLE_CLIENT_ID`, `GOOGLE_CLIENT_SECRET` — Integração Google.
- `CAPTCHA_KEY` (via config) — Chave secreta do reCAPTCHA.

### Message
- `SMTP_HOST`, `SMTP_PORT`, `SMTP_SECURE` — Provedor SMTP.
- `SMTP_USER`, `SMTP_PASSWORD` — Credenciais SMTP.
- `EMAIL_SENDER` — Remetente padrão (fallback para `SMTP_USER`).

---

## CI/CD (alto nível)
- Repositórios e pipelines no Azure DevOps (build/test/lint, build Docker, deploy).
- Agente self-hosted na EC2 executando os pipelines.
- Publicação do frontend em S3/CloudFront; APIs atualizadas via Docker/Compose.

---

## Recomendações (Próximos passos)
- Padrão de paginação e filtros nas listagens (ex.: `?page=1&limit=20&q=...`).
- Documentação OpenAPI/Swagger gerada para as três APIs.
- Rate limiting no Nginx para endpoints sensíveis.
- Padronizar respostas de sucesso (envelopes) e códigos de erro.



