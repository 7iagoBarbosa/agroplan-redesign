# Design System do AgroPlan

Sistema oficial. Especificação completa em
[`docs/redesign/05-design-system.md`](../../../docs/redesign/05-design-system.md).

> ## ⚠️ LEIA ANTES DE USAR QUALQUER VALOR
>
> **O sistema abaixo está especificado e aprovado, mas NÃO está implementado.** O código em
> `Agro-Plataform/frontend/` ainda usa a paleta verde antiga.
>
> | | No código HOJE | Sistema oficial (alvo) |
> |---|---|---|
> | Marca | Verde `#1f6f5c` (`brand-*`) | Azul `#1E2E4F` + dourado `#DAA52D` |
> | Fontes | Fraunces + Hanken Grotesk | Barlow · Barlow Condensed · IBM Plex Mono |
> | Status | Âmbar/laranja/sky/vermelho/esmeralda | Violeta→magenta · teal→vermelho |
>
> **Escrever `bg-navy-800` hoje não funciona** — o Tailwind 4 não gera classe para token não
> declarado em `@theme`, e a falha é silenciosa. Ao **alterar código existente**, use os
> tokens da Parte D. Ao **projetar ou revisar design**, use este sistema. Confirme sempre em
> `Agro-Plataform/frontend/src/index.css`.

---

## A. Tokens

### Cores

```css
/* Azul — marca, ação, cromo */
navy-950 #0e1727  navy-900 #131e34 (cromo)  navy-800 #1e2e4f (MARCA/ação)
navy-700 #2a3f6a (hover)  navy-600 #35507f (link/foco)  navy-500 #456497 (último legível)
navy-400 #6480ad  navy-300 #93a7c6  navy-200 #c3cedf  navy-100 #e1e7f0  navy-50 #f2f5f9
                  └────────────── ✗ NUNCA texto ──────────────┘

/* Dourado — acento */
gold-800 #6b4c0f  gold-700 #8a6414 (único legível s/ branco)  gold-600 #a87d1c
gold-500 #daa52d (ACENTO)  gold-400 #e4ba5c  gold-200 #f5e4bf  gold-100 #fbf3df

/* Neutros frios */
slate-900 #0f1620  slate-800 #1a2331 (texto)  slate-700 #2e3949
slate-600 #465366 (secundário)  slate-500 #5d6b80 (PISO DE TEXTO)
slate-400 #8b97a8 (borda de campo)  slate-300 #bec7d3  slate-200 #dce2ea (borda card)
slate-100 #eaeef3 (divisor)  slate-50 #f3f5f9 (fundo app)
```

**Aliases semânticos — use estes:** `canvas` `surface` `surface-sunken` `chrome`
`chrome-deep` `ink` `ink-muted` `ink-subtle` `action` `action-hover` `link` `accent`
`rule` `border` `border-control` `focus`.

### Status — recomendação = violeta→magenta · aplicação = teal→vermelho

| Estado | Texto | Fundo | Trilho |
|---|---|---|---|
| prevista | `#3d4a5c` | `#edf0f4` | **nenhum** |
| recomendação pendente | `#5b21b6` | `#f3eefc` | `#7c3aed` |
| recomendação atrasada | `#9d174d` | `#fce9f1` | `#db2777` |
| aplicação pendente | `#115e59` | `#e4f4f2` | `#0d9488` |
| aplicação atrasada | `#991b1b` | `#fce9e9` | `#dc2626` |
| completa | `#166534` | `#e7f4eb` | `#16a34a` |
| desativada | `#5d6b80` | `#f1f3f6` | **nenhum** |

`prevista` e `desativada` não têm trilho — a ausência é o sinal. A estrutura das 6 famílias
de classe e a API de `statusCores.ts` **não mudam**; só os valores.

### planStatus — ciclo de vida do PLANEJAMENTO (eixo distinto)

Badge de **contorno**, peso menor, para não competir com o trilho do `StatusEvento`.

| Estado | Texto | Borda | Texto s/ branco |
|---|---|---|---|
| Rascunho | `#3d4a5c` | `#c8d0da` | 9,00:1 ✅ |
| Ativo | `#35507f` | `#b6c4dc` | 8,06:1 ✅ |
| Concluído | `#166534` | `#a9d3ba` | 7,13:1 ✅ |
| Arquivado | `#5d6b80` | `#d3dae2` | 5,41:1 ✅ |

As bordas (~1,5:1) são **decorativas** — o badge é identificado pelo texto, que passa
4,5:1. Substitui `bg-brand-100 text-brand-700`, que some na troca de paleta.

### Feedback

`danger` `#991b1b`/`#fce9e9` · `success` `#166534`/`#e7f4eb` · `warning` `#6b4c0f`/`#fbf3df`
· `info` `#1e2e4f`/`#f2f5f9`.

### Tipografia

`Barlow` (interface) · `Barlow Condensed` (cabeçalho de tabela, versalete, rótulo de mapa)
· `IBM Plex Mono` (**todo número**).

Escala com **nomes próprios — não redefine os tokens do Tailwind**:
`text-label` 12/16 · `text-body-sm` 13/18 · `text-body` 14/20 · `text-title` 16/22 ·
`text-heading` 20/26 · `text-display` 26/30.

⚠️ `text-sm` continua sendo **14px** (padrão do Tailwind). Uma versão anterior redefinia
`--text-sm` para 13px, o que encolheria os 533 usos existentes de uma vez. **Não fazer.**
A migração é opt-in: `text-xs`→`text-label`, `text-sm`→`text-body`.

**Piso 12px, nenhum valor arbitrário.** Pesos 400/500/600 — `regular` é o padrão de corpo.

### Escalas

- **Espaço:** 4 · 8 · 12 · 16 · 24 · 32 · 48 (escala padrão do Tailwind, sem token próprio)
- **Raio:** `control` 6 · `card` 10 · `overlay` 14 · `full`
- **Elevação: 2 níveis** — repouso = borda 1px **sem sombra**; sobreposição = `--shadow-overlay`
- **Ícone:** 14 · 16 · 20 · 24 (lucide, traço 1,5px)
- **Controle:** 36 / 40 / 48px · **linha de tabela:** 36 (compacta) / 48 (completa)
- **Layout:** sidebar 240/64 · contexto 56 · header 72 · conteúdo máx **1360px**
- **Toque:** 36px (ponteiro fino) / **48px** (`pointer: coarse`)

---

## B. Regras invioláveis

1. **Nada abaixo de `slate-500` é texto.** Se ficou ilegível, sobe de tom ou não se escreve.
2. **Dourado nunca é texto sobre claro e nunca recebe texto branco** (2,23:1 nos dois
   sentidos). É superfície com `navy-800` (6,03:1) ou acento sobre cromo (7,44:1).
3. **Um elemento `accent` por tela**, no máximo.
4. **Divisor ≠ borda de campo.** Divisor é decorativo (`slate-100`); borda de campo é
   componente e exige 3:1 (`slate-400`).
5. **Nenhum gradiente**, exceto scrim sobre imagem de satélite.
6. **Sombra só em sobreposição.** Repouso usa borda.
7. **Trilho de status = "isto precisa de você".**
8. **Contexto** (cliente/fazenda/safra) no `ContextBar`; **filtro de atributo** na
   `FilterBar`. Nunca se misturam.
9. **Formulário complexo nunca em modal** — página seccionada ou expansão inline.
10. **Ação de linha sempre texto + ícone.** Ícone puro só em `IconButton`, com `label`
    obrigatório.
11. **Todo número em `IBM Plex Mono` + `tabular-nums`** — sem exceção em tabela, indicador ou formulário.
12. **`EmptyState` sempre com três partes:** o quê · a regra · a ação.
13. **Erro de carregamento nunca se parece com lista vazia** — `isError` tem precedência
    sobre `isEmpty`.
14. **Ícone nunca é o único portador de significado.**
15. **Mono só para dado. `TalhaoGlyph` só em cadastro de talhões e seleção de talhões em
    recomendações. PDF só em `RecomendacaoPrintPage`.** Fora disso, não.

---

## C. Componentes

Contrato completo em [`05-design-system.md`](../../../docs/redesign/05-design-system.md)
Parte II. Resumo:

| Componente | Nota essencial |
|---|---|
| `Button` | 5 variantes: `primary` `accent` `secondary` `ghost` `danger`. `accent` 1×/tela; `danger` sempre `lg` |
| `IconButton` | `label` **obrigatório no tipo** → `aria-label` + tooltip. Nunca destrutivo |
| `Input` | `numeric` obriga mono + tabular + alinhamento à direita |
| `Select` | Sucede `SearchableSelect`. `allLabel` ("Todos") é valor legítimo, não placeholder |
| `Checkbox`/`Radio`/`Switch` | Switch **só** para efeito imediato; se exige "Salvar", é Checkbox |
| `Badge` | Com `status`, lê de `statusCores.ts`. Não é clicável |
| `Card` | Borda, **sem sombra**. `status` adiciona o trilho |
| `Modal` | Já tem semântica de diálogo; falta focus trap. **Só diálogo curto** |
| `Drawer` | Detalhe sem perder a lista. Sucede `EventoDetalheDrawer` |
| `Tooltip` | Substitui `title=`, mas nunca é único portador |
| `Table` | `priority` obrigatória por coluna; **máx. 3 `always`**. Sem zebra. `opacity-50` proibido |
| `Pagination` | **Sempre no servidor** (`skip`/`limit`) |
| `Tabs` | `underline` = seções · `segmented` = visões dos mesmos dados |
| `Breadcrumb` | Hierarquia do **dado**; ≠ `ContextBar` (recorte ativo) |
| `Dropdown` | Só ação secundária. `danger` por último |
| `Alert` | Persistente e no fluxo (≠ Toast) |
| `Toast` | Fila de 3. Mesmo verbo do botão que disparou |
| `KPI` | **`href` obrigatório.** Sem caixa de ícone colorida |
| `FilterBar` | Só atributos. >4 filtros → "Mais filtros" |
| `Sidebar` | Grupos por fase de trabalho; configuração isolada no rodapé; trilho `gold-500` no ativo |
| `PageHeader`/`ContextBar` | `<h1>` único por rota; contexto persistente aceita "Todos" |
| `Skeleton` | Padrão de loading. Spinner só dentro de botão |
| `EmptyState` | `rule` **obrigatória no tipo** |
| `QueryState`/`ErrorState` | Envelope obrigatório: `isError` → `isLoading` → `isEmpty` → `children` |
| `FormSection`+`FormField` | Hierarquia é **seção › campo**, não tamanho de fonte. `description` substitui rótulo maior |
| `ConfirmDialog` | `consequence` **obrigatória no tipo** — diz o que muda e o que permanece |
| `StatusBadge`/`StatusRail` | Encapsulam a escolha de família. Rail não renderiza em `prevista`/`desativada` |
| `PlanStatusBadge` | **Contorno**, peso menor — eixo do planejamento não compete com o da aplicação |
| `DensityToggle` | Não renderiza em `pointer: coarse` |
| `GroupedAccordion` | Lista por cliente; contagem visível mesmo recolhido |

### Padrões de domínio

- **Tabelas de volume:** <100 render direto · 100–500 paginação servidor · >500 + busca
  servidor. Virtualização **exige decisão sobre dependência nova**.
- **Dashboard:** pendências (KPI acionável) → panorama por cliente → atividade recente.
  Proibido: 4 KPIs de contagem sem ação, cartão de atalhos, "Olá, {nome}".
- **Calendário:** 3 visões (kanban/tabela/agenda) com contexto e filtro compartilhados —
  ativo a preservar. `DensityToggle` nas três.
- **Planejamento:** nunca em modal. Cálculo derivado é somente-leitura com `hint`.
  **Redesign não altera comportamento de cálculo.**
- **Mapas:** talhão preenchido pelo trilho de status a 25% — maior oportunidade do produto.
  Controles de 48px.
- **PDF:** A4, mono no dado regulado, filete `gold-500` no cabeçalho — única aparição do
  dourado no documento.

---

## D. O que existe no código hoje

Necessário para ler e alterar o código atual.

**Marca:** `brand-50 #eaf4f1` · `brand-500 #1f6f5c` (primária) · `brand-600 #1b5f4f` ·
`brand-700 #154a3e` (hover) · `brand-900 #0f2c26` (sidebar) · `accent #2a9d8f` ·
`gold #f2a65a` · `canvas #f4f8f7`. Legadas em `:root`: `--c-dark` `--c-green` `--c-teal`
`--c-gold` `--c-muted` (usadas inline em `Layout.tsx` — não usar em código novo).

**Fontes:** `--font-display` Fraunces (h1–h3, `.font-display`) · `--font-sans` Hanken
Grotesk. `.tnum` para números.

**Status atual:** prevista cinza · rec. pendente âmbar · rec. atrasada laranja · aplic.
pendente sky · aplic. atrasada vermelho · completa esmeralda · desativada cinza riscado.
Seis famílias de classe em `lib/statusCores.ts`; `StatusAp` deriva via
`statusApParaEvento()`.

**Classes:** `.input` `.btn-primary` `.btn-secondary` `.icon-btn` (32px) `.sidebar-link`
`.card-in` (**órfã**) `.no-print` `.tnum`.

**Sem sistema:** 12 variantes de padding · 7 raios · 5 sombras · 12 tamanhos de ícone ·
6 larguras de página · 9 tamanhos de fonte (107 arbitrários, até 9px).

**`tipoCores.ts`** — chip por tipo de produto. **Não muda no redesign**
([Decisão 2](../../../docs/redesign/01-product-audit.md)).

**Scrollbar:** 10px, thumb `#c3ccc9`→`#8fa39d`. O código registra que 5px era fino demais
para acertar com o mouse — **não reduzir**.

### Mapa de migração

| Hoje | Alvo |
|---|---|
| `bg-brand-500` / `.btn-primary` | `<Button variant="primary">` · `navy-800` |
| `brand-700` (hover) | `navy-700` |
| `brand-900` / `--c-dark` | `navy-900` |
| `accent` / `--c-teal` | *(sem equivalente — era teal decorativo)* |
| `gold` `#f2a65a` | `gold-500 #daa52d` |
| `canvas` `#f4f8f7` | `slate-50 #f3f5f9` |
| `text-gray-400` (328×) | `slate-500` — **nunca mais claro** |
| `text-gray-300` (50×) | Não é texto |
| `text-gray-500/600/700/800` | `slate-500/600/700/800` |
| `font-display` | `font-sans` `semibold` |
| `text-xs` (478×) | `text-label` |
| `text-sm` (533×) | `text-body` — **`text-sm` NÃO muda de valor** |
| `bg-brand-100 text-brand-700` (planStatus `ativo`) | `PlanStatusBadge` contorno |
| `.tnum` | `font-mono` + `tabular-nums` |
| `[9px]`/`[10px]`/`[11px]` (107×) | `text-xs` |
| Borda de input `#d1d5db` (1,47:1) | `slate-400` (3:1) |
| `.card-in` | Removida |
| `lib/toast.ts` (DOM direto) | `ToastProvider` |

**Sequência obrigatória:** (1) contraste, estados, tipografia e espaçamento **na paleta
atual**; (2) **troca atômica** num PR só tocando `index.css` + `statusCores.ts`; (3) adoção
por tela. Não existe caminho incremental para a cor — ou as duas paletas convivem, ou a
troca é atômica. Detalhe em [`05-` §42](../../../docs/redesign/05-design-system.md).
