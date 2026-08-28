# 05 — Design System oficial do AgroPlan

> **Data:** 28/08/2026 · **Base:** [`04-visual-directions.md`](04-visual-directions.md)
> **Stack:** React 19 · TypeScript (`strict`) · Tailwind CSS 4 (`@theme`, CSS-first) · `lucide-react`
> **Nenhum código foi implementado.** Nenhuma tela foi construída.

O `04-` define *o que o produto parece*. Este documento define *o que se constrói*: tokens
em forma de código, contrato de cada componente e os padrões de domínio agrícola.

**Todo par de cores prescrito aqui foi verificado por WCAG 2.1.** Nenhuma combinação
desta especificação reprova.

---

# PARTE I — TOKENS

## 1. Cores

Substituição integral do bloco `@theme` de `index.css`.

```css
@import "tailwindcss";

@theme {
  /* ═══ Azul — marca, ação, cromo ═══════════════════════════════ */
  --color-navy-950: #0e1727;   /* sobreposição, profundidade      */
  --color-navy-900: #131e34;   /* CROMO de navegação              */
  --color-navy-800: #1e2e4f;   /* MARCA · ação primária           */
  --color-navy-700: #2a3f6a;   /* hover da ação                   */
  --color-navy-600: #35507f;   /* link · borda ativa · foco       */
  --color-navy-500: #456497;   /* último degrau legível como texto*/
  --color-navy-400: #6480ad;   /* ✗ NUNCA texto — borda/gráfico   */
  --color-navy-300: #93a7c6;   /* ✗ NUNCA texto                   */
  --color-navy-200: #c3cedf;   /* ✗ NUNCA texto                   */
  --color-navy-100: #e1e7f0;   /* fundo de seleção                */
  --color-navy-50:  #f2f5f9;   /* realce de linha / hover         */

  /* ═══ Dourado — acento. NUNCA texto sobre claro. ══════════════ */
  --color-gold-800: #6b4c0f;   /* texto sobre gold-100 · 7,12:1   */
  --color-gold-700: #8a6414;   /* único legível s/ branco · 5,37:1*/
  --color-gold-600: #a87d1c;   /* ✗ NUNCA texto — borda/gráfico   */
  --color-gold-500: #daa52d;   /* ACENTO · superfície c/ navy-800 */
  --color-gold-400: #e4ba5c;   /* hover de superfície dourada     */
  --color-gold-200: #f5e4bf;
  --color-gold-100: #fbf3df;

  /* ═══ Neutros frios ═══════════════════════════════════════════ */
  --color-slate-900: #0f1620;
  --color-slate-800: #1a2331;  /* texto primário · 15,81:1        */
  --color-slate-700: #2e3949;
  --color-slate-600: #465366;  /* texto secundário · 7,81:1       */
  --color-slate-500: #5d6b80;  /* PISO ABSOLUTO DE TEXTO · 5,41:1 */
  --color-slate-400: #8b97a8;  /* ✗ NUNCA texto — borda de campo  */
  --color-slate-300: #bec7d3;  /* ícone desativado                */
  --color-slate-200: #dce2ea;  /* borda de card                   */
  --color-slate-100: #eaeef3;  /* divisor                         */
  --color-slate-50:  #f3f5f9;  /* fundo da aplicação              */

  /* ═══ Status — ciclo de vida da aplicação ═════════════════════ */
  /* Recomendação = violeta → magenta · Aplicação = teal → vermelho */
  --color-st-prevista-fg:   #3d4a5c;  --color-st-prevista-bg:   #edf0f4;
  --color-st-recpend-fg:    #5b21b6;  --color-st-recpend-bg:    #f3eefc;
  --color-st-recpend-rail:  #7c3aed;
  --color-st-recatr-fg:     #9d174d;  --color-st-recatr-bg:     #fce9f1;
  --color-st-recatr-rail:   #db2777;
  --color-st-aplpend-fg:    #115e59;  --color-st-aplpend-bg:    #e4f4f2;
  --color-st-aplpend-rail:  #0d9488;
  --color-st-aplatr-fg:     #991b1b;  --color-st-aplatr-bg:     #fce9e9;
  --color-st-aplatr-rail:   #dc2626;
  --color-st-completa-fg:   #166534;  --color-st-completa-bg:   #e7f4eb;
  --color-st-completa-rail: #16a34a;
  --color-st-desativada-fg: #5d6b80;  --color-st-desativada-bg: #f1f3f6;
  /* prevista e desativada NÃO têm rail — a ausência é o sinal */

  /* ═══ planStatus — ciclo de vida do PLANEJAMENTO ══════════════ */
  /* Eixo distinto do status de aplicação. Badge de CONTORNO,      */
  /* peso menor, para não competir com o trilho de StatusEvento.   */
  --color-ps-rascunho-fg:  #3d4a5c;  --color-ps-rascunho-bd:  #c8d0da;
  --color-ps-ativo-fg:     #35507f;  --color-ps-ativo-bd:     #b6c4dc;
  --color-ps-concluido-fg: #166534;  --color-ps-concluido-bd: #a9d3ba;
  --color-ps-arquivado-fg: #5d6b80;  --color-ps-arquivado-bd: #d3dae2;

  /* ═══ Feedback ════════════════════════════════════════════════ */
  --color-danger-fg:  #991b1b;  --color-danger-bg:  #fce9e9;
  --color-success-fg: #166534;  --color-success-bg: #e7f4eb;
  --color-warning-fg: #6b4c0f;  --color-warning-bg: #fbf3df;
  --color-info-fg:    #1e2e4f;  --color-info-bg:    #f2f5f9;
}
```

### Aliases semânticos — **use estes no código**

```css
:root {
  --color-canvas:         var(--color-slate-50);
  --color-surface:        #ffffff;
  --color-surface-sunken: var(--color-slate-100);
  --color-chrome:         var(--color-navy-900);
  --color-chrome-deep:    var(--color-navy-950);
  --color-ink:            var(--color-slate-800);
  --color-ink-muted:      var(--color-slate-600);
  --color-ink-subtle:     var(--color-slate-500);   /* piso */
  --color-action:         var(--color-navy-800);
  --color-action-hover:   var(--color-navy-700);
  --color-link:           var(--color-navy-600);
  --color-accent:         var(--color-gold-500);
  --color-rule:           var(--color-slate-100);   /* divisor  */
  --color-border:         var(--color-slate-200);   /* card     */
  --color-border-control: var(--color-slate-400);   /* campo    */
  --color-focus:          var(--color-navy-600);
  --chrome-label:         #cbd5e4;   /* 10,02:1 s/ chrome */
  --chrome-label-dim:     #8fa0bc;   /*  5,03:1 s/ chrome */
}
```

### Regras invioláveis de cor

1. **Nada abaixo de `slate-500` é texto.** Se ficou ilegível, ou sobe de tom ou não se escreve.
2. **Dourado nunca é texto sobre claro e nunca recebe texto branco** (2,23:1 nos dois sentidos). É superfície com `navy-800` (6,03:1) ou acento sobre cromo (7,44:1).
3. **Um elemento `accent` por tela**, no máximo.
4. **Divisor ≠ borda de campo.** Divisor é decorativo (`slate-100`); borda de campo é componente e exige 3:1 (`slate-400`).
5. **Nenhum gradiente**, exceto scrim de legibilidade sobre imagem de satélite.

## 2. Tipografia

> **Decisão (28/08/2026) — a escala não redefine tokens do Tailwind.** Uma versão anterior
> deste documento sobrescrevia `--text-sm` (14→13px) e `--text-base` (16→14px). Como o
> código usa `text-sm` **533 vezes**, isso encolheria o corpo de texto de todo o produto no
> PR do `@theme`, sem nenhum componente ter sido editado — e texto menor contraria a
> decisão de uso em campo. A escala passa a usar **nomes semânticos próprios**; `text-sm`
> continua significando 14px e a migração é opt-in, componente a componente.

```css
@theme {
  --font-sans:      "Barlow", ui-sans-serif, system-ui, sans-serif;
  --font-condensed: "Barlow Condensed", "Barlow", ui-sans-serif, sans-serif;
  --font-mono:      "IBM Plex Mono", ui-monospace, monospace;

  /* Escala semântica — NÃO redefine --text-xs/sm/base/lg/xl do Tailwind */
  --text-label:   0.75rem;   --text-label--line-height:   1rem;      /* 12/16 */
  --text-body-sm: 0.8125rem; --text-body-sm--line-height: 1.125rem;  /* 13/18 */
  --text-body:    0.875rem;  --text-body--line-height:    1.25rem;   /* 14/20 */
  --text-title:   1rem;      --text-title--line-height:   1.375rem;  /* 16/22 */
  --text-heading: 1.25rem;   --text-heading--line-height: 1.625rem;  /* 20/26 */
  --text-display: 1.625rem;  --text-display--line-height: 1.875rem;  /* 26/30 */
}
```

Gera `text-label`, `text-body-sm`, `text-body`, `text-title`, `text-heading`,
`text-display`. Os utilitários padrão (`text-xs`, `text-sm`…) continuam existindo com os
valores do Tailwind — **não usar em código novo**, mas o código antigo não quebra.

| Token | px | Papel |
|---|---|---|
| `text-label` | **12** | Rótulo, meta, versalete. **Piso absoluto** |
| `text-body-sm` | 13 | Corpo denso, célula de tabela compacta |
| `text-body` | 14 | Corpo padrão, campo de formulário |
| `text-title` | 16 | Título de seção |
| `text-heading` | 20 | Título de página (`<h1>`) |
| `text-display` | 26 | Indicador numérico |

| Papel | Família | Onde |
|---|---|---|
| Interface | `Barlow` | Corpo, rótulo, botão, título |
| Rótulo denso | `Barlow Condensed` | Cabeçalho de tabela, versalete, rótulo de mapa |
| Dado | `IBM Plex Mono` | Todo número: dose, área, volume, contagem, nº Agrofit, hora |

**Pesos:** `400` regular (padrão de corpo) · `500` medium (ênfase, rótulo de campo) ·
`600` semibold (título, cabeçalho, botão). Três, fim.

**Piso de 12px.** Nenhum valor arbitrário. Todo número com `tabular-nums`.

**Migração:** substituir `text-xs`→`text-label`, `text-sm`→`text-body`,
`[11px]`/`[10px]`/`[9px]`→`text-label`. Mecânica, e pode acontecer tela a tela — nenhuma
tela muda de aparência até ser tocada.

## 3. Espaçamento

Sete valores. A escala padrão do Tailwind já corresponde — **não declarar tokens próprios**.

| Token | px | Uso |
|---|---|---|
| `1` | 4 | Intra-componente |
| `2` | 8 | Rótulo↔campo, padding vertical de célula |
| `3` | 12 | Padding horizontal de célula |
| `4` | 16 | Padding de card, gap entre campos |
| `6` | 24 | Gap entre seções |
| `8` | 32 | Margem de bloco |
| `12` | 48 | Margem de topo de página |

## 4. Raio

```css
@theme {
  --radius-control: 6px;    /* botão, campo, chip, badge */
  --radius-card:    10px;   /* card, painel, tabela      */
  --radius-overlay: 14px;   /* modal, drawer, popover    */
}
```
Mais `rounded-full` para ponto de status, avatar e contador. **Quatro valores, fim.**

## 5. Bordas

| Token | Valor | Uso |
|---|---|---|
| `--color-rule` | 1px `slate-100` | Divisor entre linhas |
| `--color-border` | 1px `slate-200` | Contorno de card, painel, tabela |
| `--color-border-control` | 1px `slate-400` | Campo de formulário — **3:1 obrigatório** |
| Ativa | 1px `navy-600` | Campo em foco, item selecionado |
| Trilho de status | **3px** | Borda esquerda de linha/card com estado |
| Anel de foco | 3px `navy-600` @30%, offset 1px | Todo elemento focável |

## 6. Sombras

**Dois níveis. Fim.**

```css
@theme {
  --shadow-overlay: 0 8px 24px -4px rgb(14 23 39 / .18),
                    0 2px 6px -2px rgb(14 23 39 / .10);
}
```

| Nível | Definição | Uso |
|---|---|---|
| `rest` | **Sem sombra** — borda 1px | Card, painel, tabela, tudo em repouso |
| `overlay` | `--shadow-overlay` | Modal, drawer, popover, dropdown, toast |

Elevação em repouso é **borda**, não sombra. Produz interface mais nítida em tela de baixa
qualidade — relevante para uso em campo.

## 7. Iconografia

`lucide-react`, traço 1,5px.

| Token | px | Uso |
|---|---|---|
| `icon-dense` | 14 | Célula de tabela compacta |
| `icon-base` | 16 | Padrão: botão, nav, ação de linha |
| `icon-header` | 20 | Cabeçalho de página, título de seção |
| `icon-empty` | 24 | Estado vazio, ilustração |

**Quatro valores, fim.** Todo ícone decorativo leva `aria-hidden="true"`. **Nenhum ícone é
o único portador de significado.**

Vocabulário fixo — o mesmo conceito usa sempre o mesmo ícone:

| Conceito | Ícone | Conceito | Ícone |
|---|---|---|---|
| Editar | `Pencil` | Inativar | `Ban` |
| Reativar | `CircleCheck` | Excluir | `Trash2` |
| Adicionar | `Plus` | Buscar | `Search` |
| Filtrar | `SlidersHorizontal` | Exportar | `Download` |
| Imprimir | `Printer` | Fechar | `X` |
| Talhão | `LandPlot` | Fazenda | `Building2` |
| Cliente | `Users` | Produto | `Package` |
| Recomendação | `ClipboardList` | Calendário | `CalendarRange` |
| Aplicação | `SprayCan` | Estoque | `ShoppingCart` |

## 8. Dimensões

| Elemento | Valor |
|---|---|
| Altura de controle `sm` / `md` / `lg` | 36 / 40 / 48px |
| Altura de linha de tabela — compacta / completa | 36 / 48px |
| Sidebar expandida / colapsada | 240 / 64px |
| Barra de contexto | 56px |
| Cabeçalho de página | 72px |
| Navegação inferior (mobile) | 64px |
| Largura de modal `sm`…`2xl` | 420 / 560 / 720 / 960 / 1200px |
| Largura de drawer | 420px (`base`: 100%) |
| Largura máxima de conteúdo | **1360px** |
| Alvo de toque mínimo — fino / grosseiro | **36** / **48px** |

> **Decisão (28/08/2026) — 36px no ponteiro fino.** A auditoria [`03-`](03-current-ui-audit.md)
> P4 recomendava 44px. Prevalece 36px: é coerente com a linha compacta de 36px (44px não
> caberia nela) e passa WCAG 2.2 AA com folga — o mínimo AA (2.5.8) é 24×24px; 44px é
> critério AAA (2.5.5). Em `pointer: coarse` o modo compacto é desativado e os alvos vão
> para 48px.

## 9. Layout

```
┌──────────────┬──────────────────────────────────────────────┐
│ SIDEBAR      │ CONTEXT BAR                          56px    │
│ 240px        ├──────────────────────────────────────────────┤
│ navy-900     │ PAGE HEADER                          72px    │
│              ├──────────────────────────────────────────────┤
│  marca       │                                              │
│  ─────       │  CONTEÚDO · máx 1360px · grade 12col/24gap   │
│  grupo       │                                              │
│  ▎ativo      │                                              │
│  item        │                                              │
│  ─────       │                                              │
│  ⚙ config    │                                              │
│  👤 conta    │                                              │
└──────────────┴──────────────────────────────────────────────┘
```

**Pontos de quebra**

| Nome | Largura | Comportamento |
|---|---|---|
| `base` | <640 | Sidebar → **navegação inferior** (5 itens + "mais"). Densidade travada em Completo. Modal em tela cheia. Formulário 1 coluna |
| `sm` | ≥640 | Barra de contexto em 2 linhas |
| `lg` | ≥1024 | Sidebar lateral. Formulário 2 colunas |
| `xl` | ≥1280 | Layout pleno |

---

# PARTE II — COMPONENTES

Contrato de cada um. `React 19` + `TypeScript strict`. Todos aceitam `className` para
ajuste pontual e encaminham `ref`.

## 10. Button

```ts
type ButtonProps = {
  variant?: "primary" | "accent" | "secondary" | "ghost" | "danger";
  size?: "sm" | "md" | "lg";
  icon?: LucideIcon;
  iconPosition?: "left" | "right";
  loading?: boolean;
  fullWidth?: boolean;
} & React.ButtonHTMLAttributes<HTMLButtonElement>;
```

| Variante | Fundo | Texto | Borda | Contraste |
|---|---|---|---|---|
| `primary` | `navy-800` | branco | — | 13,47:1 |
| `accent` | `gold-500` | **`navy-800`** | — | 6,03:1 |
| `secondary` | branco | `slate-800` | `slate-400` | 15,81:1 |
| `ghost` | transparente | `navy-600` | — | 8,06:1 |
| `danger` | `#991b1b` | branco | — | 7,11:1 |

Tamanhos: `sm` 36px/`text-sm`/ícone 14 · `md` 40px/`text-base`/16 · `lg` 48px/`text-base`/16.

**Regras:** `accent` no máximo 1 por tela · `danger` sempre `lg` · `loading` mantém a
largura e troca o rótulo ("Salvando…"), nunca vira spinner solto · `:focus-visible`
obrigatório · botão sem rótulo textual não é `Button`, é `IconButton`.

## 11. IconButton

```ts
type IconButtonProps = {
  icon: LucideIcon;
  label: string;            // obrigatório → vira aria-label + tooltip
  variant?: "ghost" | "danger";
  size?: "sm" | "md";
} & Omit<React.ButtonHTMLAttributes<HTMLButtonElement>, "children">;
```

`label` é **prop obrigatória no tipo**, não opcional — impede por construção o problema de
151 `title=` contra 15 `aria-label=`. Alvo 36px (fino) / **48px** (`pointer: coarse`).
Nunca para ação destrutiva — essa sempre leva rótulo visível.

## 12. Input

```ts
type InputProps = {
  size?: "sm" | "md";
  prefix?: React.ReactNode;   // ícone ou unidade à esquerda
  suffix?: React.ReactNode;   // unidade: "ha", "L/ha"
  invalid?: boolean;
  numeric?: boolean;          // aplica font-mono + tabular-nums + align right
} & React.InputHTMLAttributes<HTMLInputElement>;
```

Altura 36/40px · borda `--color-border-control` · raio `--radius-control` · fundo branco.
Foco: borda `navy-600` + anel 3px @30%. Inválido: borda `#991b1b` + anel vermelho.
Desabilitado: fundo `slate-50`, texto `slate-500`, `cursor: not-allowed`.

**`numeric` é obrigatório** em dose, área, volume e quantidade.

## 13. Select

```ts
type SelectProps<T> = {
  options: Array<{ value: T; label: string; group?: string; disabled?: boolean }>;
  value: T | null;
  onChange: (v: T | null) => void;
  searchable?: boolean;
  clearable?: boolean;
  allLabel?: string;          // "Todos" — valor legítimo, não placeholder
  size?: "sm" | "md";
  invalid?: boolean;
};
```

Sucede `SearchableSelect` (hoje bom, 12 arquivos) generalizando-o. Lista em `overlay`,
`role="listbox"`, navegação por teclado (↑↓, Enter, Esc, digitação incremental). Acima de
8 opções, `searchable` liga sozinho. `allLabel` distingue "todos" (recorte válido) de
"nenhum" (vazio) — distinção que a barra de contexto exige.

## 14. Checkbox · Radio · Switch

```ts
type CheckboxProps = { label: string; description?: string; indeterminate?: boolean } & ...;
type RadioGroupProps<T> = { options: {value:T; label:string; description?:string}[]; ... };
type SwitchProps = { label: string; description?: string } & ...;
```

| | Tamanho | Marcado | Uso |
|---|---|---|---|
| Checkbox | 16px, raio 4px | `navy-800` + ✓ branco | Seleção múltipla, "Mostrar inativos" |
| Radio | 16px, círculo | `navy-800` | Escolha única entre ≤5 opções |
| Switch | 36×20px | Trilho `navy-800`, botão branco | **Só para efeito imediato**, sem confirmação |

Alvo clicável (incluindo rótulo) ≥36px / 48px em toque. Radio com >5 opções vira `Select`.
Switch que exige "Salvar" está errado — nesse caso é Checkbox.

## 15. Badge

```ts
type BadgeProps = {
  variant?: "neutral" | "info" | "success" | "warning" | "danger";
  status?: StatusEvento | StatusAp;   // tem precedência sobre variant
  size?: "sm" | "md";
  dot?: boolean;
};
```

`text-xs`, `font-medium`, padding 2/6, `--radius-control`. Quando recebe `status`, lê de
`statusCores.ts` — nunca cor codificada à mão. Badge **não é clicável**; se precisar
clique, é `Chip` ou `Button ghost`.

## 16. Card

```ts
type CardProps = {
  title?: string;
  actions?: React.ReactNode;
  status?: StatusEvento;      // adiciona o trilho de 3px à esquerda
  padding?: "none" | "sm" | "md";
  interactive?: boolean;
};
```

Superfície branca · borda 1px `--color-border` · `--radius-card` · **sem sombra**.
Cabeçalho opcional com filete inferior. `interactive` adiciona hover `navy-50` e foco.

## 17. Modal

```ts
type ModalProps = {
  open: boolean; onClose: () => void; title: string;
  size?: "sm" | "md" | "lg" | "xl" | "2xl";
  footer?: React.ReactNode;
  dismissible?: boolean;      // default true
};
```

Evolui o `Modal` atual — que já tem `role="dialog"`, `aria-modal`, `aria-labelledby` e
devolução de foco. **Adiciona focus trap** (única lacuna).

**Escopo reduzido:** só diálogo curto e confirmação. Formulário complexo sai do modal
(`04-` §9.5) — vira página seccionada ou expansão inline. Em `base`, ocupa tela cheia.

### Critério de destino — os 14 modais escritos à mão

Regra objetiva, para não haver 14 decisões improvisadas:

| Condição | Destino |
|---|---|
| ≤ 3 campos, sem lista aninhada | **`Modal`** (`sm`/`md`) |
| 4–8 campos, edita **uma linha** de uma lista visível | **Expansão inline** na linha |
| > 8 campos, ou tem lista aninhada, ou tem etapas | **Página seccionada** (rota própria) |
| Só pergunta e confirma | **`ConfirmDialog`** |

Classificação dos 14 por essa regra:

- **`Modal`** — `AlmoxarifadoFormModal`, `UsuarioFormModal`, `EventoCustomModal`
- **Expansão inline** — `BicosPage`, `EquipamentosPage`, `TipoAtividadesPage`,
  `TiposContratoClientePage`, `UsuariosPage` (modal de senha)
- **Página seccionada** — `PlanejamentoListPage` (novo/duplicar), `AdicionarProdutoModal`,
  `PlanejamentoEstoqueModal`, `PerfisIntervaloPage` (itens aninhados), `HistoricoTab`
- **`Drawer`** — `EventoDetalheDrawer` (já é drawer, só falta o componente),
  `ClientesAcessoModal` (lista + permissões, não é formulário)

## 18. Drawer

```ts
type DrawerProps = {
  open: boolean; onClose: () => void; title: string;
  side?: "right";             // apenas direita
  width?: "md" | "lg";        // 420 / 560px
  footer?: React.ReactNode;
};
```

Para **detalhe contextual sem perder a lista** — sucede `EventoDetalheDrawer`, hoje escrito
à mão. Mesma semântica de diálogo do Modal. Em `base`, sobe de baixo ocupando 90vh.

## 19. Tooltip

```ts
type TooltipProps = { content: string; side?: "top"|"right"|"bottom"|"left"; children: React.ReactElement };
```

Fundo `navy-900`, texto branco (16,63:1), `text-xs`, raio `--radius-control`, atraso 400ms.

**Nunca é o único portador de informação** — substitui o `title=` nativo, mas o
`IconButton` continua exigindo `label`. Não aparece em `pointer: coarse`.

## 20. Table

Ver §26 para volume alto e §9 do `04-` para o comportamento visual.

```ts
type Column<T> = {
  key: string;
  header: string;
  priority: "always" | "wide" | "detail";   // obrigatório
  align?: "left" | "right";
  numeric?: boolean;
  width?: number;
  render?: (row: T) => React.ReactNode;
  sortable?: boolean;
};

type TableProps<T> = {
  columns: Column<T>[];
  rows: T[];
  rowKey: (row: T) => string | number;
  density?: "compact" | "comfortable";
  statusOf?: (row: T) => StatusEvento | undefined;   // aplica o trilho
  onRowClick?: (row: T) => void;
  selectable?: boolean;
  bulkActions?: React.ReactNode;
  sort?: { key: string; dir: "asc" | "desc" };
  onSortChange?: (s: TableProps<T>["sort"]) => void;
  stickyHeader?: boolean;
};
```

| Parte | Especificação |
|---|---|
| Cabeçalho | `font-condensed`, `text-xs`, versalete, `ink-muted`, fundo `surface-sunken`, fixo |
| Trilho | 3px `--color-st-*-rail`; **ausente** em `prevista`/`desativada` |
| Divisor | 1px `--color-rule`; **sem zebra** — o trilho dá o ritmo |
| Numérica | `font-mono`, `tabular-nums`, à direita |
| Ação | **Texto + ícone**, à direita; nunca ícone puro |
| Hover | `navy-50` |
| Inativa | `ink-subtle` + badge "Inativo". **Nunca `opacity-50`** — destruiria o contraste verificado |

**`priority` é obrigatória.** Máximo **3 colunas `always`** por tabela — são as que
respondem *"isto é meu e precisa de mim?"*. Abaixo de `sm`: triagem mantém tabela só com
`always`; cadastro vira card empilhado. **`overflow-x-auto` isolado é proibido.**

### Mapeamento por tela

Regra: se a coluna não ajuda a decidir **se aquela linha exige ação**, não é `always`.

| Tela | `always` (máx. 3) | `wide` (≥lg) | `detail` |
|---|---|---|---|
| Recomendações (`ApListPage`) | Nº AP · Status · Aplicados/Total | Fazenda · Data prevista | Insumos · Área · Responsável |
| Calendário — tabela | Talhão · Status · Data | Atividade · Cultura | Produtos · Dose · Equipamento |
| Planejamentos | Nome · Status | Fazenda · Cultura · Atualizado | Autor · Área · Talhões |
| Estoque | Produto · Saldo | Almoxarifado · Unidade | Mínimo · Última movimentação |
| Talhões (fazenda) | Glifo+Nome · Área | Cultura · Cultivar | Plantio · Colheita · Solo |
| Produtos | Nome comercial · Tipo | Ingrediente ativo · Fabricante | Dose · Nº Agrofit |
| Relatório de demanda | Produto · Quantidade | Unidade · Custo | Fazendas · Talhões |
| CRUDs simples (bicos, safras, atividades…) | Nome | *(nenhuma)* | Criado em |

Tela não listada segue a regra: identificador + estado + a data que indica atraso.

## 21. Pagination

```ts
type PaginationProps = {
  page: number; pageSize: number; total: number;
  onPageChange: (p: number) => void;
  onPageSizeChange?: (s: number) => void;
  pageSizes?: number[];       // [25, 50, 100]
};
```

O backend já expõe `skip`/`limit` — **paginação é sempre no servidor**, nunca fatiando
array no cliente. Rodapé da tabela: `"1–25 de 312"` em `font-mono` + navegação. Em `base`,
só `‹ Anterior / Próxima ›` com o contador entre eles.

⚠️ Corrige um bug latente: o dashboard hoje deriva métricas de uma lista limitada a 200
planejamentos e **passa a mentir silenciosamente acima disso** (`03-` §6.8).

## 22. Tabs

```ts
type TabsProps = {
  items: { key: string; label: string; count?: number; disabled?: boolean }[];
  value: string; onChange: (k: string) => void;
  variant?: "underline" | "segmented";
};
```

`underline`: navegação entre seções de uma tela (Configuração/Plantio do calendário).
`segmented`: alternância entre visões dos **mesmos dados** (Visão geral/Por cliente,
Compacto/Completo). `count` em `font-mono`. `role="tablist"`, setas navegam. Estado na URL
quando compartilhável.

## 23. Breadcrumb

```ts
type BreadcrumbProps = { items: { label: string; href?: string }[] };
```

Resolve a ausência total de breadcrumb com hierarquia de 3 níveis. `text-sm`, separador
`ChevronRight` 14px em `slate-300`, último item `ink` sem link. Em `base`, colapsa para
`‹ Voltar` apontando ao pai.

**Distinção:** breadcrumb mostra a hierarquia **do dado** (Cliente › Fazenda › Talhão); a
barra de contexto mostra **o recorte ativo**. São coisas diferentes e coexistem.

## 24. Dropdown

```ts
type DropdownProps = {
  trigger: React.ReactNode;
  items: Array<
    | { type: "item"; label: string; icon?: LucideIcon; onSelect: () => void; danger?: boolean; disabled?: boolean }
    | { type: "separator" }
    | { type: "label"; label: string }
  >;
  align?: "start" | "end";
};
```

Superfície branca, `--radius-overlay`, `--shadow-overlay`. `role="menu"`, teclado completo.
Item `danger` em `#991b1b`, sempre por último, após separador. **Nunca esconde a ação
primária da tela** — dropdown é para ação secundária.

## 25. Alert

```ts
type AlertProps = {
  variant: "info" | "success" | "warning" | "danger";
  title: string;
  children?: React.ReactNode;
  action?: React.ReactNode;
  onDismiss?: () => void;
};
```

| Variante | Texto | Fundo | Ícone | Contraste |
|---|---|---|---|---|
| `info` | `navy-800` | `navy-50` | `Info` | 12,3:1 |
| `success` | `#166534` | `#e7f4eb` | `CircleCheck` | 6,29:1 |
| `warning` | `#6b4c0f` | `#fbf3df` | `TriangleAlert` | 7,12:1 |
| `danger` | `#991b1b` | `#fce9e9` | `CircleAlert` | 7,11:1 |

Persistente e no fluxo (≠ Toast, que é transitório e sobreposto). Borda esquerda 3px na cor
do texto.

## 26. Toast

```ts
toast(message: string, opts?: { variant?: "success"|"danger"|"info"; action?: {label:string; onClick:()=>void} }): void;
```

Substitui `lib/toast.ts`, que manipula DOM fora do React e **codifica cores à mão**. Passa
a ser um `ToastProvider` React lendo tokens.

Canto inferior direito, fundo `navy-900`, texto branco (16,63:1), ícone na cor da variante,
`--shadow-overlay`, 4s. **Fila de até 3** — hoje remove o anterior, o que vai atropelar
quando o `onError` global entrar. `role="status"` / `aria-live="polite"`; `danger` usa
`assertive`.

**Mesmo verbo do botão que disparou:** "Emitir recomendação" → "Recomendação emitida".

## 27. KPI

```ts
type KpiProps = {
  value: number | string;
  label: string;
  status?: StatusEvento;
  href: string;               // OBRIGATÓRIO
  loading?: boolean;
  hint?: string;
};
```

Unifica os 4 componentes de indicador atuais. Valor `text-xl` `font-mono` + ponto de status
+ rótulo `text-xs` `font-condensed` versalete.

**Sem caixa de ícone colorida** — é exatamente o clichê SaaS apontado em `03-` §5.
**`href` é obrigatório:** todo indicador leva à lista já filtrada. Indicador que não leva a
lugar nenhum não entra no sistema. Carregando mostra `—`, nunca `0`.

## 28. FilterBar

```ts
type FilterBarProps = {
  filters: React.ReactNode;      // atributos: status, cultura, tipo, data
  search?: { value: string; onChange: (v: string) => void; placeholder?: string };
  density?: { value: Density; onChange: (d: Density) => void };
  actions?: React.ReactNode;     // Exportar, Nova...
  collapsible?: boolean;
};
```

**Filtro de atributo apenas.** Cliente/fazenda/safra são contexto e vivem no `ContextBar`
(§30) — a regra que elimina a ambiguidade de "onde ponho este seletor?".

Filtros compõem-se da esquerda para a direita como uma frase. Acima de 4, os excedentes vão
para "Mais filtros". Estado persistido por tela (`useStickyState`, hoje em 6 arquivos —
passa a ser universal). Chips de filtro ativo com `×`, mais "Limpar tudo".

## 29. Sidebar

```ts
type SidebarProps = {
  groups: { title: string; items: NavItem[] }[];
  loose?: NavItem[];              // Início, Estoque
  settings: NavItem[];            // rodapé, isolado
  user: { nome: string; email: string; perfil: string };
  collapsed: boolean; onToggle: () => void;
};
```

Fundo `--color-chrome`, 240/64px, estado persistido. Grupos por **fase de trabalho** com
cabeçalho `font-condensed` versalete em `--chrome-label-dim` (5,03:1). Item: `--chrome-label`
(10,02:1); ativo: fundo `navy-800`, texto branco, **trilho `gold-500` 3px** — o único
dourado da navegação, continuidade deliberada com o trilho que o produto já tem.

**Configuração isolada no rodapé**, separada por filete. Em `base`, vira navegação inferior
de 64px com 5 itens + "Mais".

## 30. Header + ContextBar

```ts
type PageHeaderProps = { title: string; subtitle?: React.ReactNode; icon?: LucideIcon;
                         breadcrumb?: BreadcrumbProps["items"]; actions?: React.ReactNode };

type ContextBarProps = { cliente: ContextSelect; fazenda: ContextSelect; safra: ContextSelect };
```

`PageHeader` (72px): breadcrumb opcional, `<h1>` `text-lg` `semibold` garantido único por
rota, subtítulo `text-sm` `ink-muted`, ações à direita (máx. 1 `primary`/`accent`).

`ContextBar` (56px): faixa persistente, com `Cliente · Fazenda · Safra`, cada um aceitando
"Todos". Persiste entre telas.

### Mapa das rotas × seletores

Regra: um seletor aparece se **recorta o conteúdo daquela tela**. Se não recorta, some —
nunca aparece desabilitado ou vazio. Barra sem nenhum seletor não é renderizada.

| Rota | Cliente | Fazenda | Safra |
|---|---|---|---|
| `/inicio` | ✅ | ✅ | ✅ |
| `/planejamento` · `/planejamento/:id` · `/…/talhao/:id` | ✅ | ✅ | ✅ |
| `/calendario` · `/calendario/gerenciar` · `/calendario/:id` | ✅ | ✅ | ✅ |
| `/calendario/aps` | ✅ | ✅ | ✅ |
| `/relatorio` · `/relatorio/manejo*` | ✅ | ✅ | ✅ |
| `/estoque` | ✅ | ✅ | — *(saldo é por almoxarifado, não por safra)* |
| `/almoxarifados` | ✅ | ✅ | — |
| `/clientes` · `/clientes/:id/fazendas` | — *(é a própria lista)* | — | — |
| `/fazendas` · `/fazendas/:id/*` | ✅ | — *(é a própria lista)* | — |
| `/cultivares` · `/produtos` | ✅ *(aba "Do cliente")* | — | — |
| `/safras` | — | — | — *(é a própria lista)* |
| `/tipo-atividades` · `/tipos-contrato-cliente` · `/bicos` · `/equipamentos` · `/perfis-intervalo` | — | — | — |
| `/usuarios` · Configurações | — | — | — |
| `/login` · `/cadastro` · `RecomendacaoPrintPage` | *(fora do shell)* | | |

**Princípio por trás:** uma entidade **nunca** é contexto da tela que a lista. `/clientes`
não tem seletor de cliente; `/safras` não tem seletor de safra.

## 31. Loading

```ts
type SkeletonProps = { variant: "text" | "block" | "row" | "card" | "kpi"; lines?: number };
type SpinnerProps  = { size?: 14 | 16 | 20 };
```

**Skeleton é o padrão** — blocos `slate-100`, `--radius-control`, com a forma do conteúdo
que vai chegar. Pulso desligado em `prefers-reduced-motion`.

Spinner **só** dentro de botão em ação. Não existe mais texto "Carregando..." solto — hoje
são 40 ocorrências em 12 tratamentos visuais.

## 32. EmptyState

```ts
type EmptyStateProps = {
  title: string;      // obrigatório — o que não existe
  rule: string;       // obrigatório — POR QUE está vazio, no contexto ativo
  action?: React.ReactNode;
  icon?: LucideIcon;
};
```

`rule` ser **obrigatória no tipo** é o que impede a volta de "Nenhum resultado" seco —
hoje há ~35 variantes e nenhuma ensina nada.

> **Nenhuma recomendação para os filtros desta safra**
> Recomendações aparecem aqui quando têm talhão e produto definidos. Rascunhos ficam em *Em edição*.
> `[ Nova recomendação ]`

Sem ilustração. Ícone `icon-empty` em `slate-300` é permitido.

## 33. ErrorState + QueryState

```ts
type ErrorStateProps = { title?: string; detail?: string; onRetry?: () => void; compact?: boolean };

type QueryStateProps = {
  isLoading: boolean; isError: boolean; error?: unknown; isEmpty?: boolean;
  skeleton: React.ReactNode; empty: React.ReactNode; children: React.ReactNode;
};
```

`QueryState` é **envelope obrigatório** de toda `useQuery` que alimenta região visível.
Ordem de decisão: **`isError` → `isLoading` → `isEmpty` → `children`**. Erro tem
precedência sobre vazio — é essa inversão que corrige o bug de "falha de rede vira lista
vazia", hoje presente em 59 de 61 arquivos.

`detail` vem do backend, nunca texto fixo. Três camadas obrigatórias no sistema:
`ErrorBoundary` no layout · `onError` global no `QueryCache` · `QueryState` por consulta.

---

# PARTE III — PADRÕES DE DOMÍNIO

## 33b. FormSection + FormField

Resolvem P8 (173 `<label>` em 4 escalas tipográficas concorrentes). A hierarquia passa a
ser **seção › campo** — não tamanho de fonte.

```ts
type FormSectionProps = {
  title: string;                 // text-title, semibold
  description?: string;          // text-body-sm, ink-muted — o que fazer nesta seção
  columns?: 1 | 2;               // 2 em ≥lg, sempre 1 abaixo
  actions?: React.ReactNode;     // "+ Adicionar insumo"
  children: React.ReactNode;
};

type FormFieldProps = {
  label: string;
  htmlFor?: string;
  optional?: boolean;            // marca o OPCIONAL, não o obrigatório
  hint?: string;                 // regra ou fórmula, text-label
  error?: string;                // detail do backend, nunca texto fixo
  children: React.ReactNode;
};
```

**Regras:** `description` é o que substitui rótulos maiores — use-a em vez de aumentar o
label · `label` sempre `text-body-sm` `medium` `ink-muted`, **sem exceção** · erro em
`aria-describedby` · campo desabilitado sempre acompanhado do motivo em `hint`.

## 33c. ConfirmDialog

Resolve P7 (28 `confirm()` nativos em 22 arquivos).

```ts
type ConfirmDialogProps = {
  open: boolean;
  title: string;                 // a ação e o alvo: "Inativar o talhão T-14?"
  consequence: string;           // OBRIGATÓRIO — o que muda e o que permanece
  confirmLabel: string;          // o mesmo verbo do título
  cancelLabel?: string;          // "Cancelar"
  destructive?: boolean;         // Button variant danger + lg
  pending?: boolean;
  onConfirm: () => void;
  onCancel: () => void;
};
```

`consequence` é **prop obrigatória no tipo** — é o que impede a volta de "Tem certeza?".
Foco inicial em `Cancelar`. Sobre `Modal` size `sm`. Toast confirma o resultado depois,
com o mesmo verbo.

> **Inativar o talhão T-14?**
> Ele deixa de receber novas recomendações e continua nos relatórios das safras anteriores.

## 33d. StatusBadge + StatusRail

Encapsulam a escolha de família por contexto, hoje feita à mão em cada tela. A API de
`lib/statusCores.ts` **não muda**; só os valores.

```ts
type StatusBadgeProps = {
  status: StatusEvento | StatusAp;
  variant?: "cell" | "board" | "legend";   // qual das 6 famílias usar
  size?: "sm" | "md";
  showDot?: boolean;
};

type StatusRailProps = { status: StatusEvento };   // 3px, borda esquerda
```

`StatusRail` **não renderiza** para `prevista` e `desativada` — a ausência é o sinal.
Nenhum consumidor escolhe cor: quem precisa de status importa o componente.

### PlanStatusBadge — eixo do planejamento

```ts
type PlanStatusBadgeProps = { status: "rascunho"|"ativo"|"concluido"|"arquivado"; size?: "sm"|"md" };
```

**Contorno, não preenchido** — peso visual menor, para o ciclo do planejamento não competir
com a urgência da aplicação quando os dois aparecem na mesma linha.

| Estado | Texto | Borda | Texto s/ branco |
|---|---|---|---|
| Rascunho | `#3d4a5c` | `#c8d0da` | 9,00:1 ✅ |
| Ativo | `#35507f` | `#b6c4dc` | 8,06:1 ✅ |
| Concluído | `#166534` | `#a9d3ba` | 7,13:1 ✅ |
| Arquivado | `#5d6b80` | `#d3dae2` | 5,41:1 ✅ |

> **Sobre as bordas (~1,5:1):** são **decorativas** e não precisam de 3:1. O badge é
> identificado pelo seu **texto**, que passa 4,5:1 em todos os estados — a borda apenas
> delimita. Vale aqui a mesma distinção da §5: divisor decorativo ≠ borda de controle.

## 33e. DensityToggle

```ts
type Density = "compact" | "comfortable";
type DensityToggleProps = { value: Density; onChange: (d: Density) => void; storageKey: string };
```

Segmentado `Compacto | Completo`, persistido por `storageKey`. **Não renderiza em
`@media (pointer: coarse)`** — lá só existe Completo, porque 36px fica abaixo do mínimo de
toque. Padrão: triagem abre em Compacto; cadastro em Completo.

## 33f. GroupedAccordion

Padrão hoje **triplicado** no código (`PlanejamentoListPage`, `ApListPage`,
`ClientesAcessoModal`), cada um com seu `Set<string>` e sua função de toggle.

```ts
type GroupedAccordionProps<T> = {
  groups: Array<{
    key: string; label: string; count: number;
    children: Array<{ key: string; label: string; items: T[] }>;
  }>;
  renderItem: (item: T) => React.ReactNode;
  levels?: 1 | 2;                          // Cliente → Safra = 2
  defaultExpanded?: "none" | "first" | "all";
  storageKey?: string;
  emptyGroupLabel?: string;
};
```

**Anatomia** — Cabeçalho de grupo: `<button>` com `aria-expanded`/`aria-controls`, borda
esquerda 3px `action`, nome em `font-condensed` versalete, **contagem sempre visível mesmo
recolhido** (é o que permite triar sem expandir), chevron à direita. Sub-cabeçalho: fundo
`surface-sunken`, `text-label` versalete. Itens: linha padrão com trilho de status.

**Usar** em Planejamento (Cliente → Safra), Recomendações (Cliente → Fazenda) e fazendas
por cliente. **Evitar** com menos de 3 grupos (vira atrito) ou quando comparar itens de
grupos diferentes for a tarefa principal — aí é tabela plana ordenável.

## 34. Tabelas de grande volume

O produto tem tabelas que crescem sem teto (calendário por safra, histórico de aplicações,
catálogo Agrofit com milhares de produtos).

| Faixa | Estratégia |
|---|---|
| <100 linhas | Render direto |
| 100–500 | Paginação no servidor (`skip`/`limit`, já disponível) |
| >500 | Paginação + busca no servidor. Cabeçalho fixo obrigatório |
| Rolagem contínua necessária | **Virtualização** |

⚠️ **Virtualização exige dependência nova** (`@tanstack/react-virtual` seria a coerente,
dado que TanStack Query já está no projeto). O `CLAUDE.md` exige aprovação antes de
adicionar dependência — **não decidir isto sozinho**. Enquanto não houver decisão, o teto é
paginação.

Regras para volume: coluna `sortable` ordena **no servidor** · busca com `debounce` de
300ms · seleção múltipla mantém escolha entre páginas e mostra "N selecionados" ·
exportação sempre no servidor, nunca montando CSV no cliente.

## 35. Dashboards

Estrutura fixa, de cima para baixo:

1. **Pendências** — faixa de `KPI` por status, na ordem de `STATUS_RESUMO_ORDEM` (atrasado
   › pendente › previsto). Todo KPI leva à lista filtrada.
2. **Panorama por cliente** — a resposta a *"quais dos meus 30 clientes precisam de mim
   esta semana"*, que o `02-` identificou como a diferenciação do produto.
3. **Atividade recente** — lista compacta, no máximo 6 itens.

**Proibido:** fileira de 4 KPIs de contagem total (planejamentos, clientes, fazendas) sem
ação associada — é o clichê SaaS de `03-` §5 · cartão de "Atalhos" duplicando a sidebar ·
saudação "Olá, {nome}" ocupando a posição mais valiosa · gráfico sem pergunta associada.

Toda métrica vem do servidor agregada. **Nunca derivar no cliente a partir de lista
paginada** — é o bug latente de §21.

## 36. Calendários

Três visões sobre os mesmos dados, com filtro e contexto compartilhados (padrão que o
produto já tem e o benchmark não encontrou nas referências — **é ativo a preservar**):

| Visão | Estrutura | Densidade padrão |
|---|---|---|
| Kanban | Colunas por fase, card com trilho de status | Compacta |
| Tabela | `Table` com `statusOf` | Compacta |
| Agenda | Grade talhão × dias, cabeçalho fixo nos dois eixos | Compacta |

`Tabs variant="segmented"` alterna as visões · `DensityToggle` presente nas três ·
`Drawer` abre o detalhe sem perder a lista · legenda de status sempre visível, nunca em
tooltip. O kanban mantém a redução deliberada a 4 cores no quadro (a distinção
recomendação/aplicação já vem da coluna).

## 37. Planejamento agrícola

Fluxo `Planejamento → Talhão → Item de manejo`, hoje concentrado nos maiores arquivos do
frontend (807, 672, 620 linhas).

- **Nunca em modal.** Página seccionada com `FormSection`; item de manejo por expansão
  inline na linha.
- **Seleção de talhões** usa `TalhaoGlyph` (§39) — é uma das duas telas do enxerto B.
- Dose, área e volume sempre `Input numeric` com unidade em `suffix`.
- Cálculo derivado (volume de calda, quantidade total) é **somente-leitura**, com `hint`
  explicando a fórmula — nunca campo editável que finge ser calculado.
- Barra de ação fixa no rodapé com verbos distintos: `Emitir recomendação` (`accent`) ·
  `Salvar rascunho` (`secondary`) · `Cancelar` (`ghost`).
- Produto vem do catálogo Agrofit/Bioinsumos via `Select searchable`; o nº de registro
  aparece em `font-mono` como reforço de rastreabilidade regulatória.

⚠️ **Regra de negócio:** cálculo de dose/área/volume e as regras de status são regra de
negócio do produto (`CLAUDE.md`). Redesign **não altera comportamento de cálculo** — se
algo parecer errado, reportar, não corrigir de passagem.

## 38. Indicadores

Todo indicador do produto é `KPI` (§27). Três regras:

1. **Leva a algum lugar** — `href` obrigatório.
2. **Herda a cor do dado** que representa (`statusCores.ts`), garantindo que o número do
   Início bata com o do Calendário. É o acerto do `PendenciaCard` atual, promovido a regra.
3. **Vem agregado do servidor.**

Sem sparkline nem tendência enquanto não houver série temporal real — não forçar.

## 39. Mapas

- Base satélite dessaturada (`saturate(0.7)`); scrim de legibilidade é o **único gradiente
  permitido** no sistema.
- Contorno de talhão 2px `navy-800`; selecionado 2px `gold-500`.
- **Preenchimento pela cor de trilho do status a 25%** — transforma o mapa em ferramenta de
  triagem sem coletar nenhum dado novo. É a maior oportunidade identificada no `03-` §7.
- Rótulo `font-condensed` `text-xs` com halo branco. Legenda sempre visível. Escala gráfica
  e norte presentes.
- Controles como `IconButton` de **48px** — mapa é a tela mais provável de uso em campo.
- Sem geometria: `EmptyState` com ação de importar KML/shapefile.

**`TalhaoGlyph`** — escopo fechado: **cadastro de talhões e seleção de talhões em
recomendações. Nada mais.** Quadrado 20px (lista) / 40px (detalhe), polígono normalizado,
traço 1px `navy-400`, preenchimento pelo rail do status a 25%, `aria-hidden`. Nunca
identificador único — o nome está sempre ao lado. Sem geometria: quadrado tracejado
`slate-300`.

## 40. Relatório em PDF

Enxerto A — **escopo: `RecomendacaoPrintPage` e o PDF gerado. Nada mais.**

É o único artefato que sai da tela e vira documento assinado nas mãos do cliente. Recebe
tratamento de documento, não de tela:

| Aspecto | Regra |
|---|---|
| Formato | A4 retrato, margens 18mm; largura de papel, não a grade de 1360px |
| Corpo | `slate-900` sobre branco — custo de impressão e legibilidade em fotocópia |
| Dado regulado | **`IBM Plex Mono`**: dose, área, volume, nº Agrofit, carência |
| Cabeçalho | Institucional `navy-800` com filete `gold-500` 2px — **a única aparição do dourado** |
| Estrutura | Tabular com filetes finos. Sem card, sem sombra, sem raio |
| Assinatura | Bloco com identificação do responsável técnico |
| Quebra | `break-inside: avoid` em linha de item; cabeçalho de tabela repete por página |
| Rodapé | Numeração `n/N`, identificação da recomendação, data de emissão |

Geração no navegador (`window.print()` + `@media print`) — **não há geração de PDF no
servidor** e este documento não propõe criar. `.no-print` oculta o cromo.

---

# PARTE IV — ADOÇÃO

## 41. Mapa de substituição

| Hoje | Alvo |
|---|---|
| `bg-brand-500`, `.btn-primary` | `<Button variant="primary">` |
| `.btn-secondary` | `<Button variant="secondary">` |
| `.icon-btn` (32px) | `<IconButton>` (36/48px, `label` obrigatório) |
| `text-gray-400` (328×) | `text-slate-500` — **piso** |
| `text-gray-300` (50×) | Não é texto |
| `font-display` (Fraunces) | `font-sans` `semibold` |
| `.tnum` | `font-mono` + `tabular-nums` |
| `[9px]`/`[10px]`/`[11px]` (107×) | `text-xs` |
| `confirm()` (28×) | `<ConfirmDialog>` com consequência |
| `fixed inset-0` à mão (14×) | Expansão inline ou `<Modal>` |
| `<h1>` à mão (22×) | `<PageHeader>` |
| `<table>` à mão (28×) | `<Table>` |
| `"Carregando..."` (40×) | `<QueryState>` + `<Skeleton>` |
| Vazio ad hoc (~35 variantes) | `<EmptyState>` |
| `<svg>` inline (14 arquivos) | `lucide-react` |
| `err: any` (38×) | `mensagemErro(err: unknown, fallback)` |
| `.card-in` | Removida |
| `lib/toast.ts` (DOM direto) | `ToastProvider` |

## 42. Ordem de construção

Cada etapa é um PR. Respeita a dependência do [`03-` §8](03-current-ui-audit.md).

| # | Entrega | Depende | Risco |
|---|---|---|---|
| 1 | `ErrorBoundary` + `onError` global no `QueryCache` | — | Baixo |
| 2 | `text-gray-400/300` → piso legível, **paleta atual** | — | Baixo, mecânico |
| 3 | `QueryState`, `EmptyState`, `Skeleton` nas telas de maior tráfego | 1 | Baixo |
| 4 | Escala tipográfica e de espaçamento, **paleta atual** | — | Médio, diff amplo |
| 5 | **Troca atômica de tokens** — só `index.css` + `statusCores.ts` | 2, 4 | **Alto** |
| 6 | `Button`, `IconButton`, `Input`, `Select`, `Checkbox`, `Radio`, `Switch` | 5 | Médio |
| 7 | `PageHeader`, `Breadcrumb`, `Card`, `Badge`, `Alert`, `Toast`, `Tooltip` | 6 | Médio |
| 8 | `Table` + `Pagination` + prioridade de coluna | 6 | Médio |
| 9 | `Sidebar` + `ContextBar` + navegação inferior | 7 | Médio |
| 10 | `Modal`, `Drawer`, `Dropdown`, `Tabs` | 7 | Baixo |
| 11 | `KPI` + `FilterBar` + dashboard | 8, 9 | Médio |
| 12 | Gabarito de CRUD provado em `BicosPage` | 6–8 | Baixo |
| 13 | Demais 13 CRUDs | 12 | Baixo, repetitivo |
| 14 | Calendário e planejamento sob o novo sistema | 8, 10 | Alto — maior densidade |
| 15 | Enxerto A (PDF) e B (`TalhaoGlyph`) | 5, 8 | Baixo, isolados |

Etapas 1–4 rodam **na paleta atual** e entregam a maior parte do ganho de usabilidade antes
de qualquer risco cromático. A 5 é a única atômica e a única que exige plano de reversão
explícito (`git revert` do PR isolado).

## 43. Gabarito do CRUD único

Decisão 3: 14 rotas, um padrão. As telas já compartilham a mesma anatomia — 1 query com
`include_inactive`, mutação de salvar, mutação de ativar/inativar, `showInactive` no
cabeçalho. **Não há exclusão física.**

```
┌─ PageHeader ─────────────────────────────────────────────┐
│ [ícone] Bicos                          [ Novo bico ]     │
├─ FilterBar ──────────────────────────────────────────────┤
│ [buscar…]      □ Mostrar inativos     [Compacto|Completo]│
├─ Table ──────────────────────────────────────────────────┤
│ NOME                                          AÇÕES      │
│ Cone vazio TX-8                    Editar ✎  Inativar ⊘  │
└──────────────────────────────────────────────────────────┘
```

Edição por **expansão inline**; criação em painel no topo da lista. `Novo X` é `primary` —
nenhum CRUD usa `accent`.

**Pontos de extensão** (só três; um quarto significa que a tela não é um CRUD):

| Slot | Para quê | Quem usa |
|---|---|---|
| `aboveList` | Conteúdo antes da tabela | `ProdutosPage` (abas Global/Cliente) |
| `rowExpansion` | Substitui a edição inline padrão | `PerfisIntervaloPage` (itens aninhados) |
| `extraActions` | Ações de linha além de editar/inativar | `ProdutosPage` |

**Prova em `BicosPage`** — 150 linhas, a menor das 14, e concentra quase todos os problemas
catalogados:

| Linha | Problema | Resolve com |
|---|---|---|
| 66 | `<h1>` à mão | `PageHeader` |
| 79–81 | Loading solto; **sem `isError`** | `QueryState` |
| 82–85 | Vazio genérico | `EmptyState` com `rule` |
| 97 | `opacity-50` em linha inativa | `ink-subtle` + `Badge` |
| 103–110 | `text-gray-400`; botão só-ícone com `title` | Ação texto+ícone |
| 107 | `confirm()` nativo | `ConfirmDialog` |
| 113–114 | Dois `<svg>` inline | `lucide` (`Ban`, `CircleCheck`) |
| 126–127 | Modal `fixed inset-0` | Expansão inline |
| 131–134 | Label e input à mão | `FormField` + `Input` |
| 36 | `err: any` | `mensagemErro(err: unknown)` |

Se o padrão fechar nela, fecha nas outras 13.

## 44. Verificação

- **Contraste:** nenhum texto <4,5:1; nenhuma borda de controle <3:1.
- **Tipografia:** `grep -rE "\[[0-9]+px\]" src --include="*.tsx"` → vazio.
- **Cinzas:** `grep -r "text-gray-400\|text-gray-300" src` → vazio.
- **Toque:** nada <36px; nada <48px em `pointer: coarse`.
- **Estados:** nenhuma `useQuery` visível fora de `QueryState`.
- **Acessibilidade:** todo `IconButton` com `label`; um `<h1>` por rota; foco visível em
  todo focável.
- **Build:** `npm run build` limpo — **único gate automático do projeto** (não há lint nem
  teste no frontend).

## 45. Fora de escopo

- **Modo escuro** — a base existe (neutros frios + cromo), mas exige passe próprio.
- **Logotipo** — a identidade cromática mudou; a marca gráfica "AgroConsultoria" não foi tratada.
- **Virtualização** — depende de decisão sobre dependência nova (§34).
- **`tipoCores.ts`** — inalterado por [Decisão 2](01-product-audit.md#13-decisões-tomadas).
- **Camada `lib/queries/`** — dívida real do `01-`, mas é arquitetura, não design.
- **Geração de PDF no servidor** — hoje é impressão do navegador; não se propõe mudar.
