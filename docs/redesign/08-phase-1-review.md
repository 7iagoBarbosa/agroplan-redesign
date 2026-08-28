# 08 — Revisão de fechamento da Fase 1

> ## ✅ RESOLVIDO — 28/08/2026
>
> Todos os **4 BLOCKER** e as **5 decisões pendentes** foram resolvidos no mesmo dia desta
> revisão. Registro no final, em [§ Resolução](#-resolução--28082026). O texto original dos
> achados foi preservado como registro do processo.
>
> **Situação atual: as 15 etapas da Fase 2 estão desbloqueadas.**

> **Data:** 28/08/2026 · **Escopo:** `docs/redesign/` (7 documentos, 4.279 linhas) +
> `.claude/skills/agroplan-design/` (4 arquivos, 979 linhas) + protótipos
> **Nenhum código da aplicação foi alterado.**

Revisão cruzada em busca de contradição, decisão pendente, regra ambígua, componente sem
especificação, requisito inexequível e referência de benchmark tratada como regra.

**Método:** verificação no texto dos documentos, não de memória. Cada achado abaixo tem
arquivo e linha, e foi confirmado por varredura cruzada.

**Resultado:** **4 BLOCKER · 5 IMPORTANT · 4 MINOR.** A documentação **não está pronta**
para a Fase 2 completa, mas **3 das 15 etapas** já são executáveis hoje sem nenhum
desbloqueio. Detalhe nas respostas finais.

Nota de honestidade: a maioria dos bloqueadores foi introduzida na reescrita do `05-`,
quando ele foi expandido de 14 para 25 componentes. Especificações que existiam na versão
anterior não sobreviveram, e os documentos que dependiam delas não foram reconciliados.

---

## 🔴 BLOCKER

### B1 — Colisão silenciosa da escala tipográfica

**Onde:** [`05-` §2.1](05-design-system.md) linhas 125–127.

O sistema redefine tokens que **já existem no Tailwind com outro valor**:

| Token | Tailwind padrão | Design system | Delta |
|---|---|---|---|
| `--text-sm` | 14px | **13px** | −1px |
| `--text-base` | 16px | **14px** | −2px |

O código atual usa `text-sm` **533 vezes** e `text-base` 15 vezes. No momento em que o
bloco `@theme` entrar, **todas essas ocorrências mudam de tamanho sem que ninguém tenha
editado uma linha de componente** — e o `text-base` encolhe 12,5%.

Isso pode até ser a intenção (o produto vive em 12–14px), mas **nenhum documento registra
que a troca de escala é retroativa**. A etapa 4 da ordem de construção diz "escala
tipográfica e de espaçamento" como se fosse uma migração dirigida, quando na prática é uma
mudança global implícita.

**Por que é blocker:** a etapa 4 não é planejável sem essa decisão, e o resultado visual de
todas as telas depende dela.

**Correção necessária:** decidir e registrar — (a) aceitar a redefinição retroativa e
documentar que `text-sm` passa a significar 13px em todo o código existente; ou (b) nomear
os degraus fora da escala padrão (`--text-body`, `--text-label`) para não colidir; ou
(c) manter 14/16 e mover a escala do produto um degrau acima.

---

### B2 — Cinco componentes citados como existentes, sem contrato

**Onde:** cruzamento entre [`05-`](05-design-system.md),
[`ui-patterns.md`](../../.claude/skills/agroplan-design/ui-patterns.md) e os protótipos.

O `05-` especifica componentes nas seções 10 a 33. Os abaixo são **referenciados como se
tivessem contrato**, mas não têm:

| Componente | Onde é citado | Gravidade |
|---|---|---|
| `FormSection` | `05-` §11 (padrão de formulário), `ui-patterns` §11 | **Load-bearing** — é a peça que resolve P8 (4 escalas de label) |
| `ConfirmDialog` | `ui-patterns` §20, `05-` §43 (gabarito CRUD) | **Load-bearing** — resolve P7 (28 `confirm()` nativos) |
| `DensityToggle` | `ui-patterns` §9, §17; gabarito CRUD; protótipo | Usado em 3 padrões |
| `StatusBadge` / `StatusRail` | `ui-patterns` §12; **0 menções no `05-`** | Carrega a assinatura visual do sistema |
| `Accordion` agrupado | Protótipo (2 telas), `06-` §5.6; **ausente do `05-` e dos 23 padrões** | Existe em 3 telas reais |

`FormField` tem contrato — mas em [`06-` §5.3](06-component-inventory.md), não no `05-`.
Contrato fora do lugar.

**Por que é blocker:** as etapas 6, 7, 9 e 12 da ordem de construção dependem desses
componentes. `FormSection` e `ConfirmDialog` são exatamente o que resolve dois dos dez
problemas prioritários do `00-` §4.

**Correção necessária:** adicionar as cinco especificações ao `05-` Parte II, e mover o
contrato de `FormField` do `06-` para lá.

---

### B3 — `planStatus.ts` sem especificação, no caminho crítico

**Onde:** citado apenas em [`00-` §8](00-redesign-brief.md) e
[`06-` §5.4](06-component-inventory.md) — **ausente de `04-`, `05-` e das três skills**.

`lib/planStatus.ts` define os 4 estados do **planejamento** (`rascunho`, `ativo`,
`concluido`, `arquivado`) — eixo distinto do `StatusEvento`. O estado `ativo` usa hoje
`bg-brand-100 text-brand-700`.

A etapa 5 (troca atômica) remove os tokens `brand-*`. **Sem especificação prévia, o badge
`ativo` fica sem cor no exato PR que troca a paleta.**

Existe uma proposta — badge de contorno para `planStatus`, preenchido + trilho para
`StatusEvento` — mas ela nasceu no protótipo, está registrada como "a validar" e **nunca
foi aprovada**.

**Por que é blocker:** bloqueia a etapa 5, que é o único passo de alto risco e o que exige
plano de reversão.

**Correção necessária:** especificar os 4 estados com valores verificados por WCAG e
decidir o tratamento visual, antes de tocar `index.css`.

---

### B4 — Mapeamento de prioridade de coluna desapareceu

**Onde:** [`05-` §20](05-design-system.md) linha 454 e 487.

O contrato de `Table` declara `priority: "always" | "wide" | "detail"` como **obrigatória**,
com máximo de 3 colunas `always`. Mas o **mapeamento por tela** — quais colunas sobrevivem
em cada uma das tabelas do produto — existia numa versão anterior do `05-` e **não
sobreviveu à reescrita**.

Restou a regra sem os dados. A etapa 8 ("`Table` + `Pagination` + prioridade de coluna") não
é executável: o desenvolvedor sabe que precisa declarar `priority`, mas não sabe o que
declarar em nenhuma das 28 tabelas.

**Por que é blocker:** é a peça que operacionaliza a Decisão 1 (responsivo) nas tabelas —
o item que o `00-` §13 lista como critério de sucesso.

**Correção necessária:** restaurar a tabela de prioridade por tela, no mínimo para as 7
telas de maior tráfego.

---

## 🟠 IMPORTANT

### I3 — Contradição de alvo de toque: 44px ou 36px?

| Documento | Valor |
|---|---|
| [`03-` P4](03-current-ui-audit.md) linha 388 | "**44px** como piso no desktop, 48px em toque" |
| [`05-` §8](05-design-system.md) linha 240 | "Alvo de toque mínimo — fino / grosseiro: **36** / 48px" |
| [`05-` §11](05-design-system.md) linha 316 | "Alvo **36px** (fino) / 48px (`pointer: coarse`)" |

A auditoria recomendou 44; o design system adotou 36 sem registrar a mudança. Oito pixels
por alvo, em toda ação de linha do produto.

**Correção:** escolher um e alinhar os três pontos. 36px casa com a altura de linha
compacta; 44px é o piso recomendado pelas diretrizes de toque em desktop híbrido.

### I4 — Itens **EXPERIMENTAR** do benchmark viraram especificação

O `02-` classifica padrões em ADOTAR / ADAPTAR / EVITAR / **EXPERIMENTAR**. Três itens
classificados como EXPERIMENTAR aparecem no `05-` e nos protótipos **como parte aprovada do
sistema**, sem decisão registrada no caminho:

| Item | `02-` | Onde virou spec |
|---|---|---|
| Alternador de densidade | **EXPERIMENTAR** (§1.5.8, linha 490) | `05-` §8 e §36 · `ui-patterns` §9/§17 · gabarito CRUD · protótipo |
| Miniatura de geometria do talhão | **EXPERIMENTAR** (linha 493) | Virou **enxerto B**, com escopo e contrato |
| Talhão colorido por status no mapa | **EXPERIMENTAR** (linha 415) | `05-` §39 como comportamento definido |

É exatamente o risco que você levantou: **referência de benchmark tratada como regra.**
Nenhum dos três é má ideia — o problema é que passaram de "testar" a "construir" sem
alguém decidir.

**Correção:** decidir explicitamente os três (aprovar, rebaixar a opcional, ou remover) e
registrar no `02-` como promoção consciente, como foi feito com os itens marcados ⬆.

### I5 — Padrão de acordeão ausente do catálogo

`ui-patterns.md` tem 23 padrões e **nenhum cobre a lista hierárquica agrupada**, apesar de
ela existir em **3 telas reais** (`PlanejamentoListPage`, `ApListPage`,
`ClientesAcessoModal`) e ser a estrutura central de **2 das 5 telas do protótipo**.

O `06-` §5.6 propõe um contrato, mas ele vive no inventário da tela piloto, não no catálogo
de padrões nem no design system.

**Correção:** promover a 24º padrão em `ui-patterns` e adicionar o contrato ao `05-`.

### I6 — Migração dos 14 modais sem regra de destino

O sistema diz que **formulário complexo nunca vai em modal** e que o `Modal` fica para
"diálogo curto e confirmação". Existem **14 modais escritos à mão**.

Não há regra que diga, para cada um, qual é o destino: virar `Modal`, virar expansão
inline, ou virar página seccionada. O critério "complexo" não está quantificado — o `05-`
§17 sugere "mais de ~5 campos" em um lugar, mas isso não é regra declarada.

**Correção:** definir o critério e classificar os 14. É trabalho pequeno e evita 14
decisões improvisadas.

### I7 — Regra de visibilidade da `ContextBar` é ambígua

O `05-` §30 diz que telas sem recorte aplicável "(Configurações, Usuários) **ocultam a
barra**". São 33 rotas; duas estão nomeadas.

Casos genuinamente ambíguos: `Produtos` (catálogo global tem cliente?), `Estoque` (por
almoxarifado ou por fazenda?), `Almoxarifados`, `Safras`, `Bicos`. E quando a barra aparece
parcialmente — safra sim, cliente não — não há regra.

**Correção:** mapear as 33 rotas contra os 3 seletores. É uma tabela, não uma decisão
difícil, mas sem ela cada tela decide sozinha e a barra deixa de ser previsível.

---

## 🟡 MINOR

### M8 — Contrato de `FormField` fora do lugar

Vive em [`06-` §5.3](06-component-inventory.md), o inventário da tela piloto. Todos os
outros contratos estão no `05-` Parte II. Quem procurar componente no design system não
encontra.

### M9 — `04-` mantém as três direções por extenso

O documento descreve A, B e C completos (cada uma com 14 especificações), embora só
**C + enxerto A em impressão + enxerto B em talhões** tenha sido aprovado. O §0 explica a
escolha, mas o leitor atravessa ~250 linhas de direções descartadas antes da spec vigente.

Não é erro — é custo de leitura. Vale um aviso no topo ou mover A e B para um anexo.

### M10 — Protótipo superado ainda publicado

`prototypes/planejamento.html` contém os erros identificados (tabela em vez de acordeão,
paleta de status errada, KPIs inventados). O README já o marca como superado, mas o
artifact publicado continua acessível e pode ser confundido com o vigente.

### M11 — Não existe `07-`

A série vai de `00-` a `06-` e salta para `08-`. Se o `07-` está reservado (plano de
implementação?), vale registrar; caso contrário, é lacuna de numeração que confunde quem
chega depois.

---

## Verificações que passaram ✅

Registrado para não serem reconferidas:

- **Ordem de construção** — as 15 etapas do `05-` §42 e os 3 blocos do `00-` §11 são
  consistentes item a item.
- **Contraste** — todos os pares prescritos foram verificados por WCAG 2.1. Nenhuma
  combinação do sistema reprova. A regra do dourado (nunca texto sobre claro) é aplicada
  de forma consistente nos 4 documentos que a citam.
- **Tipografia** — nenhum resíduo de `JetBrains Mono` (fonte da direção C original antes da
  consolidação). Barlow / Barlow Condensed / IBM Plex Mono são consistentes em todos os
  documentos.
- **Altura de linha** — 36/48px consistente entre `04-`, `05-` e skills. A menção anterior
  a 34px não sobreviveu à consolidação.
- **Paleta de status** — os 7 estados e a regra "prevista e desativada não têm trilho" são
  idênticos em `04-`, `05-`, `design-system.md` e `ui-patterns.md`.
- **Escopo dos enxertos** — A (só PDF) e B (só cadastro e seleção de talhões) declarados de
  forma idêntica em `04-`, `05-` e `00-`.
- **O que não muda** — lógica de negócio, contratos de API, schema e permissões aparecem
  como inegociáveis em `00-`, `ux-principles` e `05-`, sem contradição.
- **Requisitos inexequíveis** — nenhum encontrado. O mais ambicioso ("nenhuma `useQuery`
  fora de `QueryState`", 289 chamadas) é grande, mas mecânico e incremental. A
  virtualização está corretamente marcada como dependente de decisão sobre dependência.

---

## Respostas

### 1. A documentação está pronta para implementação?

**Não integralmente — mas o começo está desbloqueado.**

Das 15 etapas da Fase 2:

| Etapa | Situação |
|---|---|
| 1 — `ErrorBoundary` + `onError` global | ✅ **Executável hoje** |
| 2 — Contraste (`gray-400/300` → piso) | ✅ **Executável hoje** |
| 3 — `QueryState`, `EmptyState`, `Skeleton` | ✅ **Executável hoje** — os três têm contrato |
| 4 — Escala tipográfica e espaçamento | 🔴 Bloqueada por **B1** |
| 5 — Troca atômica de tokens | 🔴 Bloqueada por **B3** |
| 6, 7, 9, 12 — Componentes e adoção | 🔴 Bloqueadas por **B2** |
| 8 — `Table` + prioridade de coluna | 🔴 Bloqueada por **B4** |
| 10, 11, 13, 14, 15 | 🟠 Dependem das anteriores |

**Três etapas podem começar imediatamente** — e são justamente as que resolvem os dois
problemas críticos do `00-` §4 (P1 contraste e P2 estados) sem tocar em cor de marca.
Somadas ao PR de limpeza da tela piloto (`06-` §4, itens 1–5), há trabalho real e seguro
disponível enquanto os bloqueadores são resolvidos.

### 2. Quais decisões ainda precisam ser tomadas?

Sete, em ordem de urgência:

1. **Escala tipográfica retroativa** (B1) — aceitar que `text-sm` passa a ser 13px em todo
   o código, ou renomear os degraus? *Bloqueia a etapa 4.*
2. **Tratamento visual do `planStatus`** (B3) — aprovar a proposta de badge de contorno, ou
   outra? *Bloqueia a etapa 5.*
3. **Alvo de toque no desktop** (I3) — 36px ou 44px?
4. **Os três itens EXPERIMENTAR** (I4) — alternador de densidade, glifo de talhão e talhão
   colorido no mapa: aprovar, tornar opcional ou remover?
5. **Critério de "formulário complexo"** (I6) — qual dos 14 modais vira o quê?
6. **Visibilidade da `ContextBar`** (I7) — mapear as 33 rotas contra os 3 seletores.
7. **Existe um `07-`?** (M11) — reservado ou lacuna?

As decisões 1 e 2 são as únicas que travam trabalho. As outras cinco podem ser resolvidas
em paralelo às etapas 1–3.

### 3. O que precisa ser corrigido antes da Fase 2?

Em ordem, com esforço estimado em documentação:

| # | Correção | Esforço |
|---|---|---|
| 1 | **B3** — especificar `planStatus` com valores WCAG verificados | Pequeno |
| 2 | **B1** — decidir e documentar a escala tipográfica | Pequeno *(decisão, não redação)* |
| 3 | **B2** — cinco contratos faltantes no `05-` (`FormSection`, `ConfirmDialog`, `DensityToggle`, `StatusBadge`/`StatusRail`, `Accordion`) + mover `FormField` do `06-` | Médio |
| 4 | **B4** — restaurar o mapeamento de prioridade de coluna | Médio |
| 5 | **I3** — alinhar 36/44px entre `03-` e `05-` | Trivial |
| 6 | **I5** — promover o acordeão a 24º padrão | Pequeno |
| 7 | **I4** — registrar a decisão sobre os três EXPERIMENTAR | Trivial *(depois da decisão)* |
| 8 | **I6, I7** — critério de modal e mapa da `ContextBar` | Pequeno |

Nada exige rediscussão de direção. Os quatro bloqueadores são **lacunas e inconsistências
de documentação**, não erros de concepção — a identidade visual, os princípios e a ordem de
construção resistiram à revisão cruzada sem contradição.

**Recomendação:** corrigir B1 a B4 num único passe de documentação, começar as etapas 1–3
em paralelo (não dependem de nada), e só então abrir a etapa 4.

---

## ✅ Resolução — 28/08/2026

Decisões tomadas pelo dono do produto e correções aplicadas no mesmo dia.

### Decisões

| # | Decisão | Escolha |
|---|---|---|
| **B1** | Escala tipográfica | **Tokens nomeados, sem redefinir os do Tailwind.** `text-label`(12) · `text-body-sm`(13) · `text-body`(14) · `text-title`(16) · `text-heading`(20) · `text-display`(26). `text-sm` continua 14px; migração opt-in |
| **B3** | `planStatus` | **Badge de contorno**, peso menor que o `StatusEvento` preenchido + trilho |
| **I3** | Alvo de toque no desktop | **36px** (44px é critério AAA; AA exige 24px). `03-` P4 corrigido |
| **I4** | Itens EXPERIMENTAR | **Os três aprovados**: alternador de densidade · glifo de talhão · talhão colorido por status no mapa |

### Correções aplicadas

| Achado | O que foi feito | Onde |
|---|---|---|
| **B1** | Escala reescrita com nomes semânticos + nota explicando por que não se redefine `--text-sm`. Migração documentada | [`05-` §2](05-design-system.md) · skill |
| **B2** | **Seis contratos adicionados**: `FormSection`+`FormField`, `ConfirmDialog`, `StatusBadge`/`StatusRail`, `PlanStatusBadge`, `DensityToggle`, `GroupedAccordion` | [`05-` §33b–33f](05-design-system.md) |
| **B3** | Tokens `--color-ps-*` no `@theme` + contraste verificado (5,41–9,00:1) + rationale das bordas decorativas | [`05-` §1, §33d](05-design-system.md) · skill |
| **B4** | **Mapeamento de prioridade de coluna restaurado** para 8 telas | [`05-` §20](05-design-system.md) |
| **I3** | `03-` P4 corrigido com o novo valor e a justificativa WCAG | [`03-` P4](03-current-ui-audit.md) |
| **I4** | Promoções registradas com destino de cada item | [`02-` §6](02-market-benchmark.md) |
| **I5** | Acordeão promovido a **24º padrão** | [`ui-patterns` §24](../../.claude/skills/agroplan-design/ui-patterns.md) |
| **I6** | **Critério objetivo de destino de modal** (≤3 campos / 4–8 / >8) + os 14 classificados | [`05-` §17](05-design-system.md) |
| **I7** | **Mapa das 33 rotas × 3 seletores** da `ContextBar`, com o princípio "entidade nunca é contexto da tela que a lista" | [`05-` §30](05-design-system.md) |
| **M8** | Contrato de `FormField` movido do `06-` para o `05-` | [`05-` §33b](05-design-system.md) |
| **M11** | `07-` fica **reservado ao plano de implementação** da Fase 2 | — |

### Pendências que permanecem

Nenhuma bloqueia a Fase 2. Registradas para não se perderem:

- **M9** — o `04-` mantém as direções A e B por extenso, embora só C + enxertos tenha sido
  aprovado. Custo de leitura, não erro.
- **M10** — `prototypes/planejamento.html` continua publicado como artifact, com os erros
  já identificados. O README o marca como superado.
- **Virtualização de tabela** — segue dependendo de decisão sobre dependência nova
  (`@tanstack/react-virtual`). Teto atual: paginação no servidor.

### Situação após a resolução

| Etapa | Antes | Depois |
|---|---|---|
| 1–3 (ErrorBoundary, contraste, estados) | ✅ Executável | ✅ Executável |
| 4 (escala tipográfica) | 🔴 B1 | ✅ **Desbloqueada** |
| 5 (troca atômica) | 🔴 B3 | ✅ **Desbloqueada** |
| 6, 7, 9, 12 (componentes) | 🔴 B2 | ✅ **Desbloqueadas** |
| 8 (Table + prioridade) | 🔴 B4 | ✅ **Desbloqueada** |
| 10, 11, 13, 14, 15 | 🟠 Dependentes | ✅ **Desbloqueadas** |

**As 15 etapas estão desbloqueadas.** A Fase 1 está encerrada.
