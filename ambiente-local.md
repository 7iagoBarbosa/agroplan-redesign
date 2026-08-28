# Ambiente local — Agro-Plataform

> Montado em 23/08/2026. Tudo que este documento descreve é **local e
> gitignored** — o working tree do clone continua limpo (`git status` vazio).

## Acessos

| O quê | URL |
|---|---|
| **Aplicação (dev, com hot reload)** | http://localhost:5173 |
| API | http://localhost:8000 |
| Swagger / OpenAPI | http://localhost:8000/docs |
| Postgres (do host) | `127.0.0.1:5434` · db `app_planejamento` · user `app_user` |

**Conta de teste criada:**

- E-mail: `dev@agroplan.local.com`
- Senha: `devlocal123`
- Perfil: `admin` · consultoria "Terram (dev local)" em trial de 30 dias

Se preferir sua própria conta, a tela `/cadastro` faz onboarding self-service
(cria a consultoria + o admin dela e já loga).

## Subir e derrubar

```powershell
cd c:\Users\tiago.barbosa\Documents\AgroPlan\Agro-Plataform

# Backend (Postgres + Redis + API). Migrations rodam sozinhas no boot da API.
docker compose up -d postgres redis api

# Frontend com hot reload — é aqui que os ajustes aparecem na hora
cd frontend; npm run dev
```

Para parar: `docker compose stop` (preserva o banco).
`docker compose down -v` apaga o volume e recria tudo do zero na próxima subida.

Logs da API: `docker compose logs api -f`

## O que foi preciso fazer para funcionar

Três arquivos foram criados. **Nenhum deles vai para o git** — vale registrar
porque dois expõem lacunas reais do repositório.

### 1. `.env` (raiz) — gitignored pelo `.gitignore` do repo

Copiado do `.env.example` com dois ajustes necessários:

- **`POSTGRES_USER` / `POSTGRES_PASSWORD` / `POSTGRES_DB`** — o
  `docker-compose.yml` referencia essas três variáveis, mas o `.env.example`
  **não as documenta**. Sem elas o container do Postgres sobe sem credenciais.
- **`DATABASE_URL` aponta para `postgres:5432`**, não `127.0.0.1:54321`. A API
  roda dentro da rede do compose, então o host é o nome do serviço. O valor do
  `.env.example` (`127.0.0.1:54321`) é o de outro setup — e, coincidência útil,
  é exatamente a porta do stack `appplanejoficial` que já roda nesta máquina.
- `REDIS_URL=redis://redis:6379/0` pela mesma razão (o exemplo traz `localhost`).

### 2. `backend/docker/init-external-stubs.sql` — **bug real do repositório**

Este é o achado que importa.

O `docker-compose.yml:12` monta esse arquivo em
`/docker-entrypoint-initdb.d/01-external-stubs.sql`. Ele cria a tabela externa
stub `talhoes`, que as migrations 0001, 0003 e 0004 leem (`FROM talhoes`, para a
view `v_talhoes_para_planejamento` e o seed inicial de safras). Em produção essa
tabela pertence ao sistema legado single-tenant; em dev ela não existe.

**O arquivo não está no repositório.** Num clone novo, `docker compose up`
quebra na migration 0001 com `relation "talhoes" does not exist` — e, pior, o
bind-mount do Docker cria um **diretório vazio** no lugar do arquivo ausente.

O ponto: **isso é exatamente o FEAT-010**
(`.spec/fix-init-external-stubs-sql.spec.md`), que está marcado como
`concluido` em `project-state.yaml`. A correção foi feita na máquina do dono,
mas o commit nunca levou o arquivo — porque o `.gitignore:28` tem uma regra
abrangente:

```gitignore
# Connection strings / database dumps — nunca versionar (contêm credenciais/dados)
*.sql
```

O `git add` foi silenciosamente ignorado. Confirmado com:

```
$ git check-ignore -v backend/docker/init-external-stubs.sql
.gitignore:28:*.sql   backend/docker/init-external-stubs.sql
```

**Correção sugerida ao dono (1 linha no `.gitignore` + o arquivo):**

```gitignore
*.sql
!backend/docker/init-external-stubs.sql
```

A intenção original da regra (não versionar dumps e connection strings) continua
válida — só precisa da exceção para o arquivo de bootstrap, que não contém dado
nenhum, só um `CREATE TABLE`.

Esse é um forte candidato a primeiro PR: corrige um bug que impede qualquer
colaborador novo de rodar o projeto, é de escopo mínimo, e o repo já tem a spec
descrevendo o comportamento esperado.

### 3. `docker-compose.override.yml` — só desta máquina

Esta máquina já roda o stack `appplanejoficial`, que publica `6379` (redis) e
`54321` (postgres). O `docker-compose.yml` deste repo também tenta publicar
`6379` e falhava com *"port is already allocated"*.

O override remove a publicação da porta do Redis (a API o alcança pela rede
interna do compose — publicar no host nunca foi necessário). O stack
`appplanejoficial` ficou intacto.

Como esse arquivo **não** é coberto pelo `.gitignore` do repo, foi adicionado a
`.git/info/exclude` — que é local-only e nunca é versionado. O `.gitignore` do
repositório não foi tocado.

## Estado verificado

```
37 migrations (0001 → 0037)   ✅ todas aplicadas
GET  /health                  ✅ {"status":"ok"}
GET  /health/db               ✅ {"status":"ok","result":1}
POST /auth/registrar          ✅ 201, cookies access_token (httpOnly) + csrf_token
GET  /auth/me                 ✅ autenticado via cookie, pelo proxy do Vite
GET  http://localhost:5173/   ✅ 200
git status                    ✅ vazio
```

O fluxo completo — proxy do Vite → FastAPI → Postgres → cookie httpOnly + CSRF
double-submit — foi exercitado de ponta a ponta e funciona.
