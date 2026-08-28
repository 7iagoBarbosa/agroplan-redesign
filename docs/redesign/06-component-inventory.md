# 06 — Inventário de componentes da tela piloto

> **Data:** 28/08/2026 · **Tela piloto:** `features/planejamento/PlanejamentoListPage.tsx` (620 linhas)
> **Base:** [`05-design-system.md`](05-design-system.md) · [`.claude/skills/agroplan-design/`](../../.claude/skills/agroplan-design/)
> **Nenhuma refatoração aplicada.** Documento de diagnóstico e especificação.

**Leitura de escopo:** "tela piloto" tratada como a implementação **real** de
`PlanejamentoListPage` — a tela que foi prototipada no `04-`/protótipo. As categorias
pedidas (*legado*, *duplicado*) só existem sobre o código atual; sobre o protótipo não
haveria o que classificar.

---

## 1. Inventário

Tudo o que a tela piloto usa hoje, na ordem em que aparece no arquivo.

| # | Componente / utilitário | Origem | Classificação |
|---|---|---|---|
| 1 | `PageHeader` | `components/PageHeader.tsx` | 🟦 **Global** |
| 2 | `SearchableSelect` | `components/SearchableSelect.tsx` | 🟩 **Reutilizável** |
| 3 | `api` (axios) | `lib/api.ts` | 🟦 **Global** |
| 4 | `toast()` | `lib/toast.ts` | 🟦 **Global** · legado por implementação |
| 5 | `useStickyState` | `lib/useStickyFilters.ts` | 🟩 **Reutilizável** |
| 6 | `planStatusColor` / `planStatusLabel` | `lib/planStatus.ts` | 🟩 **Reutilizável** |
| 7 | Ícones `lucide-react` (9) | dependência | 🟦 **Global** |
| 8 | `STATUS_OPTIONS` | local, linha 31 | 🟥 **Duplicado** |
| 9 | `groupPlans()` | local, linha 52 | 🟨 **Específico da feature** |
| 10 | `relativeDate()` | local, linha 76 | 🟥 **Duplicado (3×)** |
| 11 | Acordeão Cliente → Safra | inline no JSX | 🟨 **Específico** → candidato a reutilizável |
| 12 | Linha de planejamento | inline no JSX | 🟨 **Específico** |
| 13 | Badge de status | inline no JSX | 🟥 **Duplicado** |
| 14 | Botões de ação por ícone | inline, classe `.icon-btn` | 🟧 **Legado** |
| 15 | `ModalOverlay` | local, linha 586 | 🟧 **Legado** + 🟥 duplicado |
| 16 | `FF` (campo de formulário) | local, linha 600 | 🟥 **Duplicado** |
| 17 | `ModalActions` | local, linha 609 | 🟨 **Específico** → candidato a reutilizável |
| 18 | Estado vazio | inline no JSX | 🟥 **Duplicado (padrão)** |
| 19 | Estado de carregamento | inline no JSX | 🟥 **Duplicado (padrão)** |
| 20 | `confirm()` nativo | API do navegador | 🟧 **Legado** |

**Resumo:** 3 globais · 4 reutilizáveis · 4 específicos da feature · 3 legados ·
**6 duplicados**.

---

## 2. Achados por categoria

### 🟦 Componente global

Infraestrutura presente em toda a aplicação.

| Componente | Situação |
|---|---|
| `PageHeader` | ✅ Uso correto. A tela piloto é uma das **8** que adotam (de 32) |
| `api` | ⚠️ Instância axios crua. A tela monta 11 `queryKey` literais — sem camada de dados |
| `toast()` | ⚠️ Funciona, mas manipula DOM fora do React e codifica `#1f6f5c`/`#dc2626` à mão |
| `lucide-react` | ⚠️ 9 ícones em **3 tamanhos** (14, 15, 16) sem escala nomeada |

### 🟩 Componente reutilizável — bem aproveitado

| Componente | Situação |
|---|---|
| `SearchableSelect` | ✅ 201 linhas, sólido, 12 arquivos. O melhor componente do produto |
| `useStickyState` | ✅ 5 filtros persistidos. A tela piloto é uma das **6** que usam |
| `planStatus.ts` | ⚠️ Importa `planStatusColor`/`planStatusLabel`, mas **redeclara os rótulos** (ver §3.1) |

### 🟨 Específico da feature — legítimo

| Componente | Avaliação |
|---|---|
| `groupPlans()` | Agrupa `PlanejamentoListItem[]` em Cliente → Safra. Lógica genuína desta tela |
| Linha de planejamento | Composição própria: nome, fazenda·cultura, status, data, autor, 3 ações |
| `ModalActions` | Rodapé Cancelar + ação. Específico hoje, **deveria ser reutilizável** |
| Acordeão Cliente → Safra | Específico hoje, mas **o padrão se repete em 3 telas** (ver §3.4) |

### 🟧 Legado — padrão antigo com substituto disponível

| Componente | Problema | Substituto |
|---|---|---|
| `ModalOverlay` (l. 586) | `fixed inset-0` à mão. **Perde** `Esc`, trava de scroll, clique-fora protegido, `role="dialog"`, `aria-modal`, devolução de foco. Largura fixa `max-w-md` | `components/Modal.tsx` |
| Botões de ação por ícone | `.icon-btn` de **32px** (mínimo é 36/48). Rotulados só por `title` + `aria-label` | `IconButton` / ação texto+ícone |
| `confirm()` nativo (l. 447) | Suprimível pelo navegador — quando é, **a ação não acontece sem feedback** | `ConfirmDialog` (já existe) |

### 🟥 Duplicado

Detalhado em §3. É a categoria mais grave: 6 ocorrências, e uma delas **diverge em
comportamento**.

---

## 3. Duplicações

### 3.1 `STATUS_OPTIONS` reescreve a fonte única de verdade

`lib/planStatus.ts` abre com o comentário *"Rótulos e cores de status de planejamento —
fonte única de verdade"* e exporta `PLAN_STATUS_OPTIONS` e `PLAN_STATUS_LABEL`.

A tela piloto **importa apenas as funções de cor e rótulo** e redeclara a lista na
linha 31, repetindo os quatro rótulos à mão. Adicionar um quinto status exigiria editar
dois arquivos, e a intenção declarada no módulo é silenciosamente contrariada.

### 3.2 `relativeDate()` — três cópias, **dois comportamentos** ⚠️

| Arquivo | Linha | Assinatura |
|---|---|---|
| `dashboard/DashboardPage.tsx` | 27 | idêntica à do Planejamento (mesmo hash) |
| `planejamento/PlanejamentoListPage.tsx` | 76 | idêntica à do Dashboard |
| `calendario/CalendarioListPage.tsx` | 13 | **divergente** |

A divergência:

```diff
- return d.toLocaleDateString("pt-BR", { day: "2-digit", month: "2-digit" });          // Dashboard, Planejamento
+ return d.toLocaleDateString("pt-BR", { day: "2-digit", month: "2-digit", year: "numeric" }); // Calendário
```

Acima de 7 dias, o Calendário mostra o ano; as outras duas não. **A mesma data aparece
como `15/02` numa tela e `15/02/2026` noutra.** É pior que duplicação pura: mesmo nome,
mesmo propósito, comportamento diferente — e o correto é provavelmente o do Calendário,
já que sem o ano uma data antiga é ambígua.

### 3.3 `FF` reimplementa `FormField`

```
FF (local, l. 600)                    FormField (components/, 19 linhas)
label + children                      label + children + erro
```

Markup **idêntico** (`block text-sm font-medium text-gray-700 mb-1`); a versão local
apenas omite o slot de erro. O docstring de `FormField` diz que ele *"substitui o
componente `Field` que era redefinido em cada modal"* — a substituição não aconteceu aqui.
`FormField` é usado em **2** arquivos no projeto inteiro.

### 3.4 Acordeão agrupado por cliente — 3 telas

`PlanejamentoListPage` (`expandedClients`/`expandedSafras`), `ApListPage`
(`expandidos`/`toggleGrupo`) e `ClientesAcessoModal` implementam o mesmo padrão:
`Set<string>` de chaves expandidas, função de toggle, cabeçalho clicável com chevron e
contagem. Três implementações independentes do mesmo comportamento.

Além disso, `groupPlans()` e `groupAps()` (`ApListPage:33`) têm a mesma forma: reduzir uma
lista plana a `[{ clienteNome, total, filhos[] }]`.

### 3.5 Badge de status e estados vazio/carregando

Badge montado inline (`inline-flex items-center gap-1 px-2 py-0.5 rounded-full…`), com o
mesmo padrão repetido em dezenas de telas. Estado vazio e "Carregando..." também inline —
o projeto tem ~35 variantes de vazio e 12 tratamentos visuais de carregamento.

---

## 4. Oportunidades de refatoração

Ordenadas por (valor ÷ risco). **Nenhuma aplicada.**

| # | Ação | Toca | Risco | Ganho |
|---|---|---|---|---|
| 1 | `STATUS_OPTIONS` → derivar de `PLAN_STATUS_OPTIONS`/`PLAN_STATUS_LABEL` | 1 arquivo, ~6 linhas | Muito baixo | Restaura a fonte única |
| 2 | `relativeDate` → `lib/formatDate.ts` | 3 arquivos | Baixo | Corrige a **divergência de comportamento** |
| 3 | `FF` → `FormField` | 1 arquivo | Muito baixo | −10 linhas, ganha slot de erro |
| 4 | `ModalOverlay` → `Modal` | 1 arquivo | Baixo | Ganha `Esc`, foco, scroll lock, semântica |
| 5 | `confirm()` → `ConfirmDialog` | 1 chamada | Baixo | Ação destrutiva deixa de ser suprimível |
| 6 | `ModalActions` → `components/` | 1 arquivo + adoção | Baixo | Rodapé padronizado |
| 7 | Extrair `<Accordion>` agrupado por cliente | 3 arquivos | **Médio** | Unifica o padrão de 3 telas |
| 8 | Extrair `<StatusBadge>` | muitos | Médio | Prepara a troca de paleta |
| 9 | `<QueryState>` + `<EmptyState>` | muitos | Médio | Resolve o problema nº 1 do `03-` |

**Itens 1 a 5 são de arquivo único e mecânicos** — cabem num PR pequeno e revisável, e não
dependem de nenhuma decisão de design pendente.

Os itens 7 a 9 dependem dos componentes do `05-` e devem esperar a onda 6 da ordem de
construção. **Não antecipar.**

### O que a tela piloto revela sobre as outras

`PlanejamentoListPage` usa `PageHeader` e `useStickyState` (adoção boa) mas ainda tem modal
à mão, `confirm()` nativo e campo duplicado. É **geração intermediária**: adotou parte do
que existe e parou. O bloco `cadastros/` não adotou nada; o `calendario/` adotou quase tudo.

---

## 5. Especificação dos componentes reutilizáveis

Contrato dos que a tela piloto usa ou deveria usar. Alinhado ao [`05-`](05-design-system.md)
Parte II, com o recorte desta tela.

### 5.1 `SearchableSelect` → evolui para `Select`

**Finalidade** — Seleção única com busca, para listas onde rolar não é viável (clientes,
fazendas, safras, culturas).

```ts
type SelectProps<T> = {
  options: Array<{ value: T; label: string; group?: string; disabled?: boolean }>;
  value: T | null;
  onChange: (v: T | null) => void;
  searchable?: boolean;      // liga sozinho acima de 8 opções
  clearable?: boolean;
  allLabel?: string;         // "Todos os clientes" — valor legítimo, não placeholder
  size?: "sm" | "md";        // 36 / 40px
  invalid?: boolean;
  disabled?: boolean;
  placeholder?: string;
};
```

**Variantes** — `sm` (barra de filtro) · `md` (formulário).
**Estados** — repouso · hover (borda `navy-400`) · foco (anel 3px) · aberto · com valor ·
inválido · desabilitado · vazio ("Nenhum resultado").
**Comportamento** — Lista em `overlay`; `role="listbox"`; teclado completo (↑↓, Enter, Esc,
digitação incremental); fecha ao selecionar ou clicar fora; `allLabel` sempre no topo.
**Uso recomendado** — Filtros e formulários. Acima de 5 opções, preferir a `Radio`. Para
seleção múltipla, usar o modo multi (safra) e nunca renderizar "N selecionados" — listar os
valores.

### 5.2 `useStickyState`

**Finalidade** — Persistir escolha do usuário (filtro, aba, densidade) entre navegações.

```ts
function useStickyState<T>(key: string, initial: T): [T, Dispatch<SetStateAction<T>>];
```

**Comportamento** — Lê `localStorage` na montagem com fallback silencioso; grava a cada
mudança; `try/catch` nos dois lados (storage pode estar indisponível).
**Uso recomendado** — Filtro de atributo e preferência de visualização. **Não usar** para
contexto (cliente/fazenda/safra) quando o `ContextBar` existir — lá a persistência é
responsabilidade dele. **Não usar** para dado de servidor.
**Convenção de chave** — `<tela>-<coisa>` (`plan-filtro-cliente`). Já seguida.

### 5.3 `FormField`

**Finalidade** — Padronizar rótulo, campo e erro. Substitui `FF` e os outros `Field` locais.

```ts
type FormFieldProps = {
  label: string;
  htmlFor?: string;
  optional?: boolean;   // marca o OPCIONAL, não o obrigatório
  hint?: string;
  error?: string;       // recebe o detail do backend, nunca texto fixo
  children: React.ReactNode;
};
```

**Variantes** — nenhuma. Deliberadamente fino.
**Estados** — normal · com hint · com erro (rótulo mantém a cor; borda e mensagem em
`#991b1b`) · desabilitado (com motivo, nunca silencioso).
**Comportamento** — `label` vinculado por `htmlFor`; erro em `aria-describedby`; ocupa a
largura do container.
**Uso recomendado** — Todo campo de formulário. Dentro de `FormSection`, que carrega o
título e a linha de explicação — **é a seção que dá hierarquia, não o tamanho do rótulo**.

### 5.4 `planStatus` → `StatusBadge` (planejamento)

**Finalidade** — Exibir o ciclo de vida do **planejamento** (`rascunho` · `ativo` ·
`concluido` · `arquivado`), eixo distinto do `StatusEvento` (ciclo da aplicação).

```ts
type PlanStatusBadgeProps = { status: PlanStatus; size?: "sm" | "md" };
```

**Variantes** — uma por status.
**Comportamento** — Lê rótulo e cor de `planStatus.ts`; **nunca** cor codificada no
componente que a usa.
**Uso recomendado** — Lista e detalhe de planejamento.

> ✅ **Resolvido em 28/08/2026.** `planStatus` agora tem contrato em
> [`05-` §33d](05-design-system.md) com tokens `--color-ps-*` e contrastes verificados
> (5,41–9,00:1). Decisão: **badge de contorno** para `planStatus`, **preenchido + trilho**
> para `StatusEvento` — peso visual diferente para eixos diferentes, para o ciclo do
> planejamento não competir com a urgência da aplicação. O contrato completo de
> `FormField` também migrou para o `05-` §33b.

### 5.5 `ModalActions` → `FormActions`

**Finalidade** — Rodapé de ações de formulário e diálogo, com hierarquia fixa.

```ts
type FormActionsProps = {
  onCancel: () => void;
  cancelLabel?: string;               // "Cancelar"
  primary: { label: string; onClick: () => void; disabled?: boolean; loading?: boolean;
             variant?: "primary" | "accent" | "danger" };
  secondary?: { label: string; onClick: () => void };  // "Salvar rascunho"
  sticky?: boolean;                   // fixo no rodapé em página longa
};
```

**Variantes** — 2 ações (padrão) · 3 ações (emitir / rascunho / cancelar) · `sticky`.
**Estados** — normal · primária desabilitada (com motivo) · `loading` (mantém a largura e
troca o rótulo).
**Comportamento** — Ordem sempre `Cancelar` → `[secundária]` → primária, alinhadas à
direita; empilham em `base`; `Esc` equivale a cancelar.
**Uso recomendado** — Todo formulário e diálogo. Em página seccionada, `sticky`.

### 5.6 `Accordion` agrupado (proposto)

**Finalidade** — Lista hierárquica de 2 níveis agrupada por cliente. Unifica o padrão hoje
triplicado.

```ts
type GroupedAccordionProps<T> = {
  groups: Array<{ key: string; label: string; count: number;
                  children: Array<{ key: string; label: string; items: T[] }> }>;
  renderItem: (item: T) => React.ReactNode;
  defaultExpanded?: "none" | "first" | "all";
  storageKey?: string;            // persiste o que está aberto
  emptyGroupLabel?: string;
};
```

**Variantes** — 2 níveis (Cliente → Safra, Cliente → Fazenda) · 1 nível.
**Estados** — recolhido · expandido · grupo vazio · carregando (skeleton com a forma dos
cabeçalhos).
**Comportamento** — Estado em `Set<string>`, persistido por `storageKey`; cabeçalho é
`<button>` com `aria-expanded` e `aria-controls`; contagem sempre visível mesmo recolhido —
**é o que permite triar sem expandir**.
**Uso recomendado** — Planejamento, Recomendações, e a listagem de fazendas por cliente.
**Evitar** com menos de 3 grupos (o acordeão vira atrito) ou quando a comparação entre
itens de grupos diferentes for a tarefa principal — aí é tabela plana ordenável.

---

## 6. O que não fazer agora

Conforme pedido, nenhuma refatoração grande. Especificamente **não antecipar**:

- Extrair `Table`, `QueryState`, `EmptyState` — dependem da onda 6 do `05-` §42.
- Trocar tokens de cor — é a troca atômica da onda 5, e `planStatus` (§5.4) precisa ser
  especificado antes.
- Migrar o acordeão para componente compartilhado — o desenho do `05-` ainda não cobre
  esse padrão; entra como item novo do design system, não como refatoração de passagem.
- Criar `lib/queries/` — é dívida arquitetural real do `01-`, mas não é design.

### Próximo passo sugerido

Um PR único com os itens **1 a 5** da §4: arquivo único cada, mecânicos, sem decisão de
design pendente, e o item 2 corrige uma divergência de comportamento visível ao usuário.
Serve também para validar o gabarito antes de tocar as outras 31 telas.
