# HANDOFF — estado do trabalho no AgroPlan

> **Última atualização:** 28/08/2026
> Documento de **estado**, não de decisões. As decisões estão em
> [`docs/redesign/`](docs/redesign/); aqui está o que uma sessão nova precisa saber para
> continuar sem repetir trabalho.

**Leia nesta ordem:** este arquivo → [`CLAUDE.md`](CLAUDE.md) →
[`docs/redesign/00-redesign-brief.md`](docs/redesign/00-redesign-brief.md).

---

## 1. O que é este diretório

```
AgroPlan/                        ← NÃO é repositório git
├── CLAUDE.md                    ← regras do projeto (lidas automaticamente)
├── HANDOFF.md                   ← este arquivo
├── ambiente-local.md            ← como subir o ambiente
├── analise-frontend.md          ← análise de qualidade de código (23/08)
├── .claude/                     ← skills e comandos
├── docs/redesign/               ← a documentação do redesign (série 00–08)
└── Agro-Plataform/              ← ESTE é o repositório git (produto)
```

**A documentação vive fora do repositório de propósito.** O `Agro-Plataform` é um
repositório de terceiro (`hidekimva`) e o working tree dele deve permanecer limpo. Nada
do que está nesta raiz foi commitado lá.

## 2. Estado do repositório do produto ⚠️

```
Agro-Plataform/  ·  remote: hidekimva/Agro-Plataform  ·  branch: feature/recomendacao-criar-e-finalizar
```

**Existem 20 entradas não commitadas — trabalho em andamento da FEAT-019, que não é
nosso.** 14 modificados + 6 novos, cobrindo backend de calendário, a migration
`0038_recomendacao_aberta.py`, `ApFinalizarModal` e `RecomendacaoPrintPage`.

> 🚨 **Nunca rodar `git add .` nesse repositório.** Varreria a FEAT-019 inteira para
> dentro de um commit. Qualquer `git add` ali precisa ser explicitamente escopado.

Não há branch `develop` local nem remota — só `main` e a feature atual. O `CLAUDE.md`
manda criar branches a partir de `develop`; confirmar com o dono antes.

## 3. Ambiente local

Detalhe completo em [`ambiente-local.md`](ambiente-local.md). Resumo:

```bash
cd Agro-Plataform
docker compose up -d postgres redis api   # migrations rodam sozinhas
cd frontend && npm ci && npm run dev
```

| | |
|---|---|
| App (Vite, hot reload) | http://localhost:5173 |
| API / Swagger | http://localhost:8000 · `/docs` |
| Postgres (do host) | `127.0.0.1:5434` · db `app_planejamento` |
| Conta de teste | `dev@agroplan.local.com` / `devlocal123` (admin) |

**Três arquivos locais e gitignored** que precisam existir e **não vêm no clone**: `.env`
na raiz do repo, `backend/docker/init-external-stubs.sql`, e o
`docker-compose.override.yml` desta máquina.

### Dois bugs de ambiente conhecidos

**1. Clone novo não sobe** — `backend/docker/init-external-stubs.sql` é exigido pelo
`docker-compose.yml` e pelas migrations 0001/0003/0004, mas **nunca foi commitado**: o
`.gitignore:28` tem um `*.sql` abrangente que engole o arquivo silenciosamente. Isso é
exatamente a FEAT-010, marcada como `concluido` no `project-state.yaml` mas cuja correção
nunca chegou ao remoto. Fix: `!backend/docker/init-external-stubs.sql` no `.gitignore` +
commitar o arquivo. **Candidato natural a primeiro PR.**

**2. Esta máquina roda o stack `appplanejoficial`**, que ocupa as portas 6379 e 54321.
Por isso existe o `docker-compose.override.yml` local (listado em `.git/info/exclude`, não
no `.gitignore` do repo) que remove a publicação da porta do Redis. **Não parar aquele
stack.**

## 4. O que foi feito (28/08/2026)

Fase 1 do redesign, **inteiramente documental — nenhuma linha de código da aplicação foi
alterada**. Produzimos:

| Entrega | Onde |
|---|---|
| Série de 8 documentos (~4.400 linhas) | [`docs/redesign/`](docs/redesign/) |
| 4 skills + 3 comandos | [`.claude/skills/`](.claude/skills/) · [`.claude/commands/`](.claude/commands/) |
| `CLAUDE.md` expandido (219 → 252 linhas) | [`CLAUDE.md`](CLAUDE.md) |
| 2 protótipos HTML | [`docs/redesign/prototypes/`](docs/redesign/prototypes/) |

**Fase 1 está encerrada.** A revisão cruzada ([`08-`](docs/redesign/08-phase-1-review.md))
encontrou 4 blockers e 5 decisões pendentes; **todos foram resolvidos no mesmo dia**. As 15
etapas da Fase 2 estão desbloqueadas.

### Decisões aprovadas pelo dono do produto

| Decisão | Escolha |
|---|---|
| Responsividade | **Sim** — uso em campo é requisito, não cortesia |
| Colisão paleta status × tipo de produto | **Não incomoda** — fora de escopo |
| 14 CRUDs de `cadastros/` | **Padrão único** |
| Identidade cromática | **Azul-marinho `#1E2E4F` + dourado `#DAA52D`** (deixa de ser verde) |
| Direção visual | **Painel de Operação**, + enxerto A no PDF e B em talhões |
| Escala tipográfica | **Tokens nomeados** — não redefine os do Tailwind |
| `planStatus` | **Badge de contorno**, peso menor que `StatusEvento` |
| Alvo de toque no desktop | **36px** (48px em toque) |
| 3 itens EXPERIMENTAR do benchmark | **Todos aprovados** |

## 5. Armadilhas que já custaram tempo

**Os tokens `navy-*` e `gold-*` NÃO existem no código.** O design system descreve o produto
aprovado; o código ainda é verde. Escrever `bg-navy-800` hoje **falha em silêncio** — o
Tailwind 4 não gera classe para token não declarado em `@theme`. Ao mexer em código
existente, usar a **Parte D** de `.claude/skills/agroplan-design/design-system.md`.

**A troca de paleta não tem caminho incremental.** Ou as duas paletas convivem (feio), ou a
troca é atômica num PR só tocando `index.css` + `statusCores.ts` + `planStatus.ts`. É a
etapa 5 e a única de alto risco.

**As capturas em `docs/redesign/assets/` contêm dados reais de clientes** (Cropwise e
Farmbox, contas do usuário). 17 MB, 31 arquivos. **Não versionar** — o `02-` já extraiu
tudo o que importava em texto. Estão no `.gitignore` deste repositório.

**A tela de Planejamento não é uma tabela** — é um acordeão Cliente → Safra. Um protótipo
anterior errou isso; `prototypes/planejamento.html` está superado e mantido só como
registro. O vigente é `telas-redesign.html`.

## 6. Pendências

**Nada bloqueia a Fase 2.** O que segue em aberto:

- **`docs/redesign/07-implementation-plan.md`** — reservado, ainda não escrito. É o próximo
  passo natural: o plano de execução das 15 etapas, com o que entra em cada PR.
- **Virtualização de tabela** — depende de decisão sobre dependência nova
  (`@tanstack/react-virtual`). Enquanto não houver, o teto é paginação no servidor.
- **Modo escuro, logotipo** — fora de escopo declarado.
- **`04-`** ainda descreve as direções A e B por extenso, embora só C tenha sido aprovada.
  Custo de leitura, não erro.

### Primeiro passo recomendado da Fase 2

Um PR pequeno de limpeza da tela piloto — itens 1–5 do
[`06-` §4](docs/redesign/06-component-inventory.md). Arquivo único cada, mecânicos, sem
decisão de design pendente. Um deles corrige uma **divergência visível ao usuário**:
`relativeDate()` existe em 3 arquivos com **2 comportamentos** — a mesma data aparece como
`15/02` no Planejamento e `15/02/2026` no Calendário.

## 7. Ao continuar em outra máquina

**Memória do Claude** — 5 arquivos que **não vêm neste repositório**:
```
~/.claude/projects/c--Users-tiago-barbosa-GRUPOTERRAM-Documents-AgroPlan/memory/
```
⚠️ O nome dessa pasta é **derivado do caminho do projeto**. Se o projeto ficar em outro
caminho na nova máquina, o Claude procura numa pasta diferente e não encontra nada — ou
replique o caminho, ou renomeie a pasta de memória para bater com o novo.

**Plugins** — 7 instalados, precisam ser reinstalados:
```bash
for p in typescript-lsp pyright-lsp pr-review-toolkit frontend-design feature-dev commit-commands skill-creator; do
  claude plugin install "$p@claude-plugins-official" -s user -y
done
```

**Não vem no clone:** as capturas de `assets/`, o `.env`, o
`init-external-stubs.sql`, o `docker-compose.override.yml`, e o próprio
`Agro-Plataform/` (clonar à parte de `hidekimva/Agro-Plataform`).

**Artifacts publicados** — acessíveis de qualquer máquina pela sua conta Claude
(`/artifacts` no terminal, ou claude.ai/code/artifacts):
- Cinco telas redesenhadas — `telas-redesign.html`
- Protótipo inicial de Planejamento *(superado)*
