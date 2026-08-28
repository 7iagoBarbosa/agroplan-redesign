
## Prioridades (nesta ordem)

1. **Estabilidade** — nada pode quebrar o que já funciona em produção.
2. **Compatibilidade** — não quebrar contratos existentes: APIs, schema de banco, formatos de dados, comportamento de telas já em uso.
3. **Mudanças mínimas** — o menor diff possível que resolve o problema pedido.
4. **Seguir a arquitetura existente** — imitar os padrões já presentes no código, mesmo que existam abordagens que você considere melhores.

Quando essas prioridades entrarem em conflito com "código mais elegante", **as prioridades acima vencem**.

---

## Fluxo de trabalho

- Cada tarefa vira uma branch própria criada a partir de `develop` (`feat/...` ou `fix/...`),
  com PR para `main`. Não dar push em `develop` nem criar `origin/develop`.
- **Nunca** commitar, fazer merge ou push direto em `main`/`master`.
- Antes de começar: confirmar em qual branch estamos (`git status`, `git branch`).
- Cada pacote de modificação vira um **Pull Request** para revisão e aprovação do dono do repositório.
- Commits pequenos, com mensagens descritivas do "porquê", não só do "o quê".
- Não executar `git push --force`, `git rebase` em branch compartilhada, nem reescrever histórico.

---

## Regras de alteração de código

**Faça:**
- Ler o código existente ao redor antes de editar, para seguir o estilo e os padrões locais.
- Alterar apenas os arquivos necessários para a tarefa pedida.
- Explicar o impacto da mudança antes de aplicá-la, quando ela tocar algo compartilhado.
- Apontar riscos ou efeitos colaterais que perceber, mesmo que eu não tenha perguntado.

**Não faça sem me perguntar antes:**
- Refatorações, renomeações ou reorganização de arquivos não solicitadas.
- Adicionar, remover ou atualizar dependências.
- Alterar schema de banco, migrations ou dados de produção.
- Mexer em configuração de build, deploy, CI/CD, variáveis de ambiente ou secrets.
- Alterar arquivos de autenticação, permissões ou qualquer coisa ligada a segurança.
- "Corrigir" código não relacionado à tarefa atual, mesmo que pareça errado — me avise em vez de corrigir.
- Criar arquivos novos quando dá para resolver dentro dos existentes.

**Nunca:**
- Remover código sem entender para que serve.
- Introduzir mudanças que exijam migração de dados sem plano de rollback discutido comigo.
- Presumir que testes/lint existem — verifique antes de afirmar que algo foi validado.

---

## Arquitetura do projeto

`AgroPlan/` (esta raiz, **não é repositório git**) contém `Agro-Plataform/` — o
repositório git de terceiro (`hidekimva/Agro-Plataform`) que é o produto de verdade.
Todo trabalho de código acontece dentro de `Agro-Plataform/`; a raiz do `AgroPlan` é
só onde vivem anotações locais (`ambiente-local.md`, `analise-frontend.md`) e o
toolkit de ferramentas em `.claude/` e `docs/redesign/` — nada disso é versionado
junto com o produto.

Dentro de `Agro-Plataform/`:

- **`frontend/`** — SPA React que fala com a API via REST/JSON (axios), autenticado
  por cookie httpOnly + CSRF double-submit.
- **`backend/`** — API FastAPI em camadas: `api/v1/` (routers HTTP, um arquivo por
  recurso) → `services/` (lógica de negócio) → `models/` (SQLAlchemy) persistidos em
  Postgres. `schemas/` (Pydantic) define o contrato de entrada/saída de cada rota —
  é esse contrato que o frontend consome via `types/index.ts` (mantido à mão, não
  gerado do OpenAPI).
- **Multi-tenant por consultoria**: toda entidade de negócio pertence a uma
  `consultoria_id`. O isolamento é garantido no backend; telas novas não devem
  recriar esse filtro na UI.
- **Integrações externas**: AgroAPI/Embrapa (sync do catálogo Agrofit + Bioinsumos,
  roda no startup da API), Z-API/WhatsApp (só o botão de teste — o disparo real do
  digest semanal é feito por um workflow n8n externo via `integration_api_key`),
  Google Maps (contorno de fazenda/talhão).
- **Deploy**: containers Docker para API/Postgres/Redis; o frontend vira build
  estático servido por Caddy (não roda em container em produção). Há specs em
  `.spec/` cobrindo variações (mesma origem via Caddy vs. EC2+CloudFront+Supabase
  cross-origin) — checar `.spec/` antes de mexer em algo relacionado a deploy/CORS.

---

## Stack

| Camada | Tecnologias |
|---|---|
| Frontend | React 19 · Vite 8 · TypeScript ~6 (`strict: true`) · Tailwind CSS 4 (tokens via `@theme` em `index.css`, sem `tailwind.config.js`) · TanStack Query 5 · axios · React Router 7 · react-hook-form + zod · `@react-google-maps/api` · lucide-react |
| Backend | FastAPI · Python ≥ 3.11 (gerenciado por `uv`, `uv.lock`) · SQLAlchemy 2 + Alembic · psycopg 3 · Pydantic 2 + pydantic-settings · `python-jose` + bcrypt (JWT) · slowapi + Redis (rate limit) · shapely/pyproj/pyshp (geo) · ruff (lint) · pytest |
| Banco / infra | PostgreSQL 16 · Redis 7 · Docker Compose (dev com `--reload`, prod sem bind-mount) |

Não há gerador de tipos entre backend e frontend — `types/index.ts` é escrito à mão e
pode divergir do schema Pydantic real se um dos dois lados mudar sem o outro.

---

## Organização por features

**Frontend** (`frontend/src/`): pastas por domínio em `features/<dominio>/`
(`admin`, `almoxarifados`, `auth`, `cadastros`, `calendario`, `dashboard`, `estoque`,
`planejamento`, `relatorio`). Compartilhado fica fora disso: `components/` (UI
reutilizável entre features — ver `.claude/skills/agroplan-design/ui-patterns.md`),
`lib/` (hooks e helpers, ex. cálculo de calendário, cores de status/tipo), e
`types/index.ts` (tipos monolítico — grande, mas é o padrão atual; não fatiar por
domínio sem que isso seja o próprio pedido da tarefa).

**Backend** (`backend/app/`): `api/v1/<recurso>.py` (um router por recurso REST,
registrado em `api/v1/router.py`, separado em `public_router`/`protected_router`/
`integration_router` conforme o nível de autenticação exigido), `services/<dominio>.py`
(regra de negócio — cálculo, calendário, notificações), `models/` (agrupado por
domínio: `calendario.py`, `planejamento.py`, `existing.py`), `schemas/` (Pydantic,
espelha os models para I/O), `core/` (config, segurança, CSRF, rate limit,
integrações), `scripts/` (jobs standalone, ex. sync de produtos).

**Regra**: uma feature nova entra na pasta/domínio existente mais próxima dos dois
lados. Criar uma convenção de organização paralela (nova estrutura de pastas, novo
padrão de nomenclatura) não é decisão a tomar sozinho — é refatoração/reorganização,
já coberta pelo "Não faça sem perguntar" acima.

---

## Regras de TypeScript

- `strict: true` está ativo no `tsconfig.json` — não desabilitar, e não usar `any`
  para contornar um erro de tipo. O projeto já tem dívida real nesse ponto (uso de
  `any` em tratamento de erro de API) — não aumentar essa dívida em código novo.
- `noUnusedLocals`/`noUnusedParameters` estão **desligados** de propósito — não é
  para "corrigir" variável não usada como se o projeto pedisse rigor que não pede.
- Não há ESLint nem Prettier configurados. O único gate automático hoje é
  `tsc && vite build` (`npm run build`) — não presumir lint que não existe (isso já é
  regra geral acima, reforçando aqui porque é comum supor que todo projeto TS tem
  ESLint).
- `types/index.ts` é a fonte manual dos tipos que espelham os schemas Pydantic do
  backend. Ao mudar um schema no backend que afeta um endpoint consumido pelo
  frontend, atualizar o tipo correspondente no mesmo PR — divergência aqui não gera
  erro de build, só bug em runtime.

---

## Regras para não quebrar backend/API

- **Migrations Alembic são append-only na prática**: nunca editar uma migration já
  commitada/aplicada — criar uma nova migration para qualquer alteração de schema
  (isso já está coberto por "Alterar schema de banco, migrations..." acima; aqui é o
  mecanismo concreto).
- **`schemas/` (Pydantic) é o contrato com o frontend.** Renomear campo, mudar
  obrigatoriedade ou tipo de um schema quebra o frontend silenciosamente — não há
  checagem de tipo cruzando as duas linguagens. Qualquer mudança de schema exige
  checar (e, se preciso, atualizar) o `types/index.ts` e os componentes que consomem
  aquele endpoint.
- **Rotas são segregadas por nível de acesso** (`public_router` / `protected_router` /
  `integration_router`, ver `api/v1/router.py`) — uma rota nova entra no grupo certo;
  mover uma rota entre grupos muda quem consegue chamá-la, é mudança de segurança
  (já coberto pelo "Não faça sem perguntar" acima).
- **`/docs`, `/redoc`, `/openapi.json` são desabilitados em produção de propósito**
  (`main.py`, guarda por `app_env == "prod"`) — não reativar sem perguntar, é decisão
  de segurança (evita expor a estrutura da API).
- **O backend tem suíte de testes real** (`backend/tests/`, pytest, 27 arquivos,
  cobrindo multi-tenant, IDOR, CSRF, cálculo de calendário) — o frontend não tem
  nenhuma. Qualquer mudança de backend que toque autenticação, multi-tenant ou
  cálculo deve rodar essa suíte antes de ser considerada validada; não presumir que
  passa sem rodar.
- **Rate limiting (slowapi + Redis) e headers de segurança** (`SecurityHeadersMiddleware`,
  `OriginVerifyMiddleware`) protegem contra abuso e bypass do CloudFront — mudanças aí
  são mudança de segurança, já cobertas pelo "Não faça sem perguntar" acima.

---

## Preservação funcional

Durante o redesign, **não alterar sem solicitação explícita**:

- regras de negócio
- contratos de API (schemas Pydantic — são o contrato com o frontend, sem checagem cruzando as duas linguagens)
- modelos de dados e schema do banco
- autenticação e autorização
- permissões e isolamento multi-tenant
- funcionalidades existentes — **não remover nada**
- comportamento funcional **apenas por preferência visual**

**Mudança funcional é escopo separado.** Se durante um trabalho de UI aparecer a
necessidade de mudar comportamento, isso vira uma tarefa própria — não entra de carona no
PR de redesign.

Detalhamento do que isso significa na prática:

- Cálculo de dose/área/volume, regras de status (ciclo de vida de evento/aplicação em
  `lib/statusCores.ts` no frontend e a lógica correspondente em `services/calculo.py`/
  `services/calendario.py` no backend) e as regras de multi-tenant **não são detalhe de
  implementação — são a regra de negócio do produto**. Uma tarefa de estilo, redesign
  ou refatoração nunca deve alterar esse comportamento como efeito colateral.
- A lógica de negócio vive no backend (`services/`). O frontend não deve duplicar ou
  reimplementar um cálculo que a API já expõe só para exibir mais rápido ou evitar uma
  requisição — se falta um valor derivado na tela, buscar do backend, não recalcular
  no cliente.
- Se, ao mexer em UI ou em qualquer outra coisa, um comportamento de negócio parecer
  errado ou inconsistente, **reportar em vez de corrigir por conta própria** — mesma
  regra já existente acima ("Corrigir código não relacionado... me avise em vez de
  corrigir"), reforçada aqui porque lógica de negócio é onde um "ajuste" acidental
  causa o maior dano silencioso (ex.: mudar a ordem de prioridade de status muda o que
  o agrônomo vê como urgente).

---

## Redesign

O redesign do AgroPlan deve seguir **obrigatoriamente**:

1. [`docs/redesign/00-redesign-brief.md`](docs/redesign/00-redesign-brief.md) — objetivo, escopo, o que não muda
2. [`docs/redesign/05-design-system.md`](docs/redesign/05-design-system.md) — tokens e contrato dos componentes
3. [`.claude/skills/agroplan-design/`](.claude/skills/agroplan-design/) — doutrina, padrões e consulta rápida
4. [`docs/redesign/06-component-inventory.md`](docs/redesign/06-component-inventory.md) — o que existe, duplica e refatorar

Esses documentos representam as **decisões aprovadas** para o redesign.

**Quando houver conflito entre uma solicitação pontual e o Design System, sinalizar o
conflito antes de criar uma nova solução visual.** Não resolver por conta própria nem
inventar um token, componente ou padrão fora do sistema — apontar o conflito, propor a
alternativa dentro do sistema, e esperar decisão.

Regras operacionais que continuam valendo:

- Auditoria (`/redesign-audit`, skill `agroplan-ui-review`) é **somente leitura** por
  padrão — produz relatório em `docs/redesign/`, não edita `Agro-Plataform/`.
- ⚠️ **O design system descreve o produto aprovado, não o que está no código.** Os tokens
  `navy-*` e `gold-*` **ainda não existem** — escrevê-los hoje falha em silêncio, porque o
  Tailwind 4 não gera classe para token não declarado. Ao alterar código existente, usar a
  Parte D de `.claude/skills/agroplan-design/design-system.md` e confirmar em
  `Agro-Plataform/frontend/src/index.css`.
- Qualquer mudança que vá além de documentação (`.claude/`, `docs/`) e toque código em
  `Agro-Plataform/` segue o **mesmo fluxo de branch/PR** definido acima — proposta e
  confirmação antes de aplicar, nunca commit direto a partir de uma auditoria ou de uma
  proposta de tela.
- A ordem de implementação é a de [`05-` §42](docs/redesign/05-design-system.md). Não
  antecipar etapas: as ondas posteriores dependem de componentes que as anteriores criam.

---

## Referência às skills

Skills e comandos do toolkit de redesign, em `.claude/skills/` e `.claude/commands/`
(detalhe completo em `.claude/CLAUDE.md`):

| Skill/comando | Para quê |
|---|---|
| `skills/agroplan-design/` | **Fonte de verdade do design system.** `design-system.md` (tokens + 25 componentes, com a Parte D documentando o que existe no código hoje), `ux-principles.md` (doutrina, resolve ambiguidade), `ui-patterns.md` (24 padrões oficiais). |
| `skills/frontend-design/` | Critério estético geral (tipografia, hierarquia, quando arriscar) — usado em conjunto com `agroplan-design`, nunca sozinho para decidir tokens. |
| `skills/agroplan-ui-review/` | Checklist de conformidade de uma tela/diff contra o design system real — usar antes de dar um redesign por pronto. |
| `skills/agroplan-benchmark/` | Comparação com concorrentes — conteúdo inicial não verificado, validar antes de usar como base de decisão. |
| `commands/redesign-audit.md` | Audita o frontend (tudo ou uma área), produz relatório em `docs/redesign/`. |
| `commands/redesign-system.md` | Sincroniza a documentação do design system com o código real, ou propõe evolução de token consciente. |
| `commands/redesign-screen.md` | Propõe (e, só com confirmação, aplica) o redesign de uma tela específica. |

A documentação completa do redesign está em [`docs/redesign/`](docs/redesign/) —
comece pelo [`00-redesign-brief.md`](docs/redesign/00-redesign-brief.md).
