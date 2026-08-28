# 04 — Especificação visual do AgroPlan

> **Data:** 28/08/2026 · **Status:** direção escolhida e consolidada
> **Base:** [`01-`](01-product-audit.md) · [`02-`](02-market-benchmark.md) · [`03-`](03-current-ui-audit.md)
> **Nenhum código foi implementado.**

---

## 0. Direção escolhida

**Painel de Operação** como sistema base, com dois enxertos de escopo delimitado:

| Origem | Onde se aplica | O que traz |
|---|---|---|
| **C — Painel de Operação** | Todo o produto | Sistema base: cromo escuro, trilho de status, densidade com alternador, tabela como núcleo |
| **A — Receituário** | `RecomendacaoPrintPage` **apenas** | Dado regulado em monoespaçada e desenho de documento real |
| **B — Carta Agronômica** | Cadastro de talhões · seleção de talhões em recomendações **apenas** | Glifo de polígono como identificador do talhão |

Fora dessas três fronteiras, não há mistura. Qualquer dúvida do tipo "isto usa mono?" ou
"isto leva glifo?" se resolve pela tabela acima: **se a tela não está listada, a resposta
é não.**

### Mudança de identidade cromática

A marca deixa de ser verde. Passa a **azul-marinho `#1E2E4F` + dourado `#DAA52D`**.

Isto substitui os tokens `--color-brand-*`, `--color-accent`, `--color-gold` e
`--color-canvas` de `index.css`, e as variáveis legadas `--c-dark`, `--c-green`,
`--c-teal`, `--c-gold`. **É a mudança de maior raio de todo o redesign** — toca 100% das
telas. Ver §14 (impacto e migração).

Leitura da nova identidade: o azul-marinho carrega instituição, responsabilidade técnica e
robustez; o dourado carrega colheita e valor. A dupla é mais próxima de um instrumento
profissional de decisão do que o verde-agro genérico que qualquer produto do setor usa.

---

## 1. Cores

Todos os valores abaixo foram **verificados pela fórmula WCAG 2.1**. Nenhuma combinação
prescrita nesta especificação reprova.

### 1.1 Primitivas — Azul (marca, cromo, ação)

| Token | Hex | s/ branco | Uso permitido |
|---|---|---|---|
| `navy-950` | `#0E1727` | 17,94:1 | Profundidade do cromo, sombra de sobreposição |
| `navy-900` | `#131E34` | 16,63:1 | **Cromo de navegação** |
| `navy-800` | `#1E2E4F` | 13,47:1 | **Cor de marca.** Ação primária, texto de destaque |
| `navy-700` | `#2A3F6A` | 10,41:1 | Hover da ação primária |
| `navy-600` | `#35507F` | 8,06:1 | Link, texto interativo |
| `navy-500` | `#456497` | 5,96:1 | Último degrau legível como texto |
| `navy-400` | `#6480AD` | 4,01:1 | ❌ **Nunca texto.** Borda ativa, gráfico |
| `navy-300` | `#93A7C6` | 2,45:1 | ❌ Nunca texto. Borda |
| `navy-200` | `#C3CEDF` | 1,59:1 | ❌ Nunca texto. Divisor, preenchimento |
| `navy-100` | `#E1E7F0` | 1,24:1 | ❌ Nunca texto. Fundo de seleção |
| `navy-50` | `#F2F5F9` | 1,09:1 | ❌ Nunca texto. Fundo de realce |

### 1.2 Primitivas — Dourado (acento)

⚠️ **Regra absoluta do dourado.** `gold-500 #DAA52D` sobre branco dá **2,23:1** e branco
sobre ele dá **2,23:1** — reprova nos dois sentidos. Portanto:

> **O dourado nunca é texto sobre fundo claro, e nunca recebe texto branco.**
> Ele é superfície com texto azul, ou acento sobre o cromo escuro.

| Token | Hex | Uso permitido | Verificação |
|---|---|---|---|
| `gold-800` | `#6B4C0F` | Texto sobre `gold-100` | 7,12:1 ✅ |
| `gold-700` | `#8A6414` | Texto sobre branco (único dourado que pode) | 5,37:1 ✅ |
| `gold-600` | `#A87D1C` | Borda, gráfico (3,74:1) | ❌ nunca texto |
| `gold-500` | `#DAA52D` | **Acento de marca.** Superfície de botão (com texto `navy-800`), trilho no cromo | 6,03:1 com `navy-800` ✅ · 7,44:1 s/ `navy-900` ✅ |
| `gold-400` | `#E4BA5C` | Hover de superfície dourada | ❌ nunca texto |
| `gold-200` | `#F5E4BF` | Realce de linha | ❌ nunca texto |
| `gold-100` | `#FBF3DF` | Fundo de destaque | ❌ nunca texto |

### 1.3 Primitivas — Neutros frios

Afinados ao azul (levemente dessaturados em direção ao marinho), substituindo os cinzas
neutros do Tailwind.

| Token | Hex | s/ branco | s/ `canvas` | Uso |
|---|---|---|---|---|
| `slate-900` | `#0F1620` | 18,17:1 | 16,64:1 | Texto máximo |
| `slate-800` | `#1A2331` | 15,81:1 | 14,48:1 | **Texto primário** |
| `slate-700` | `#2E3949` | 11,68:1 | 10,70:1 | Título de seção |
| `slate-600` | `#465366` | 7,81:1 | 7,15:1 | **Texto secundário** |
| `slate-500` | `#5D6B80` | 5,41:1 | 4,96:1 | **Piso absoluto de texto** |
| `slate-400` | `#8B97A8` | 2,96:1 | 2,71:1 | ❌ **Nunca texto.** Borda de controle |
| `slate-300` | `#BEC7D3` | 1,71:1 | — | ❌ Nunca texto. Ícone desativado |
| `slate-200` | `#DCE2EA` | 1,30:1 | — | ❌ Nunca texto. **Borda de card** |
| `slate-100` | `#EAEEF3` | 1,17:1 | — | ❌ Nunca texto. **Divisor** |
| `slate-50` | `#F3F5F9` | 1,09:1 | — | ❌ Nunca texto. **Fundo da aplicação** |

> **Correção direta do problema nº 1 do `03-`:** `slate-500` é o piso. Não existe
> equivalente ao `text-gray-400` (2,54:1) que hoje aparece 328 vezes. O degrau abaixo do
> piso não é uma cor de texto mais clara — é **não escrever aquilo**.

### 1.4 Semânticas

Estes são os nomes a usar no código. As primitivas acima só aparecem na definição.

| Token semântico | Valor | Papel |
|---|---|---|
| `--color-canvas` | `slate-50` | Fundo da aplicação |
| `--color-surface` | `#FFFFFF` | Superfície de conteúdo |
| `--color-surface-sunken` | `slate-100` | Cabeçalho de tabela, área recuada |
| `--color-chrome` | `navy-900` | Navegação |
| `--color-chrome-deep` | `navy-950` | Rodapé do cromo, sobreposição |
| `--color-ink` | `slate-800` | Texto primário |
| `--color-ink-muted` | `slate-600` | Texto secundário |
| `--color-ink-subtle` | `slate-500` | Texto terciário — **piso** |
| `--color-action` | `navy-800` | Ação primária |
| `--color-action-hover` | `navy-700` | Hover |
| `--color-link` | `navy-600` | Link em corpo de texto |
| `--color-accent` | `gold-500` | Acento de marca |
| `--color-rule` | `slate-100` | Divisor (decorativo) |
| `--color-border` | `slate-200` | Borda de card |
| `--color-border-control` | `slate-400` | **Borda de campo de formulário** |
| `--color-focus` | `navy-600` | Anel de foco |

> **Divisor não é borda.** `--color-rule` é decorativo e pode ser claro.
> `--color-border-control` é componente de interface e precisa de 3:1 — por isso é
> `slate-400`, não `slate-200`. A borda atual dos inputs (`#d1d5db`, 1,47:1) reprova.

### 1.5 Cromo escuro

| Papel | Valor | s/ `navy-900` |
|---|---|---|
| Fundo | `navy-900 #131E34` | — |
| Rótulo de item | `#C9D6D2` → **`#CBD5E4`** | 10,02:1 ✅ |
| Rótulo secundário / grupo | `#8FA0BC` | 5,03:1 ✅ |
| Item ativo: fundo | `navy-800` | — |
| Item ativo: texto | `#FFFFFF` | 16,63:1 ✅ |
| **Item ativo: trilho** | `gold-500` | 7,44:1 ✅ |
| Divisor do cromo | `rgba(255,255,255,0.10)` | decorativo |

O trilho dourado no item ativo **é o único dourado da navegação**. É a continuidade
deliberada com o produto atual, que já faz exatamente isso — muda só a cor da barra e do
fundo.

### 1.6 Paleta de status — reconstruída

A paleta antiga tinha duas colisões com a nova marca: o âmbar de `recomendação pendente`
colidia com o dourado (43°) e o azul-céu de `aplicação pendente` colidia com o marinho
(220°). A reconstrução usa hues que a marca não ocupa, e melhora a lógica: **cada
atividade tem uma família, e o atraso escala em direção ao vermelho dentro dela.**

| Estado | Família | Texto | Fundo | Trilho / ponto | Texto/fundo | Trilho s/ branco |
|---|---|---|---|---|---|---|
| Prevista | slate | `#3D4A5C` | `#EDF0F4` | **sem trilho** | 7,88:1 ✅ | — |
| Recomendação pendente | violeta | `#5B21B6` | `#F3EEFC` | `#7C3AED` | 7,89:1 ✅ | 5,70:1 ✅ |
| Recomendação atrasada | magenta | `#9D174D` | `#FCE9F1` | `#DB2777` | 6,78:1 ✅ | 4,60:1 ✅ |
| Aplicação pendente | teal | `#115E59` | `#E4F4F2` | `#0D9488` | 6,69:1 ✅ | 3,74:1 ✅ |
| Aplicação atrasada | vermelho | `#991B1B` | `#FCE9E9` | `#DC2626` | 7,11:1 ✅ | 4,83:1 ✅ |
| Completa | verde | `#166534` | `#E7F4EB` | `#16A34A` | 6,29:1 ✅ | 3,30:1 ✅ |
| Desativada | cinza | `#5D6B80` | `#F1F3F6` | **sem trilho** | 4,87:1 ✅ | — |

**Recomendação = violeta → magenta. Aplicação = teal → vermelho.** A família diz *qual
trabalho*; a temperatura diz *quão urgente*. Os dois estados "atrasada" caem na metade
quente do círculo, então urgência continua legível de relance.

**`prevista` e `desativada` não recebem trilho.** A ausência de trilho é o sinal: não
exige ação. Isso resolve o contraste (nenhuma cor apagada precisa atingir 3:1) e afia a
assinatura — **o trilho passa a significar exatamente "isto precisa de você".**

As 6 famílias de classe por contexto (célula, ponto, quadro, borda de card, cabeçalho de
quadro, badge) do `statusCores.ts` atual permanecem. Só os valores mudam.

### 1.7 Cor de produto (`tipoCores.ts`)

Mantida como está. A auditoria `03-` registrou a colisão com a paleta de status, e a
[Decisão 2](01-product-audit.md#13-decisões-tomadas) definiu que não incomoda. **Fora de
escopo.** Única restrição nova: os chips de tipo de produto nunca aparecem na mesma coluna
que um trilho de status.

---

## 2. Tipografia

Três famílias, papéis exclusivos. Todas no Google Fonts.

| Família | Papel | Onde |
|---|---|---|
| **Barlow** | Interface | Todo corpo, rótulo, botão, título |
| **Barlow Condensed** | Rótulo denso | Cabeçalho de tabela, versalete de grupo, rótulo sobre mapa |
| **IBM Plex Mono** | Dado | Dose, área, volume, contagem, nº Agrofit, carência, hora |

**Fraunces e Hanken Grotesk saem.** Barlow tem origem em sinalização — geometria levemente
condensada, boa a 12–13px, que é onde este produto vive. IBM Plex Mono é institucional (foi
desenhada para documentação técnica), o que a torna adequada tanto à tela quanto ao
documento impresso da §11.

### 2.1 Escala

Seis degraus. **Nenhum valor arbitrário é permitido** — corrige os 107 de hoje.

| Nome | Tamanho | Entrelinha | Uso |
|---|---|---|---|
| `xs` | **12px** | 16px | Rótulo, meta, versalete. **Piso absoluto** |
| `sm` | 13px | 18px | Corpo denso, célula de tabela compacta |
| `base` | 14px | 20px | Corpo padrão, campo de formulário |
| `md` | 16px | 22px | Título de seção, rótulo de destaque |
| `lg` | 20px | 26px | Título de página |
| `xl` | 26px | 30px | Indicador numérico |

Nada abaixo de 12px existe. Onde hoje há `[9px]`, `[10px]` e `[11px]` (107 ocorrências), a
substituição é `xs` — e se não couber em 12px, o problema é de layout, não de tipografia.

### 2.2 Pesos

| Peso | Valor | Uso |
|---|---|---|
| `regular` | 400 | **Padrão de corpo** |
| `medium` | 500 | Ênfase real, rótulo de campo |
| `semibold` | 600 | Título, cabeçalho de tabela, botão |

Três pesos, fim. Hoje `font-medium` aparece 422 vezes contra 14 de `font-normal` — "médio"
virou padrão e perdeu função. **`regular` volta a ser o padrão.**

### 2.3 Números

Todo número em `IBM Plex Mono` com `font-variant-numeric: tabular-nums`. Sem exceção em
tabela, indicador ou formulário. Substitui e generaliza o `.tnum` atual (hoje em 53
lugares, o que estava certo — passa a ser regra).

---

## 3. Espaçamento

Escala de 7 valores. Nenhum outro é permitido.

| Token | px | Uso típico |
|---|---|---|
| `space-1` | 4 | Separação intra-componente |
| `space-2` | 8 | Gap entre rótulo e campo |
| `space-3` | 12 | Padding horizontal de célula |
| `space-4` | 16 | Padding de card, gap entre campos |
| `space-6` | 24 | Gap entre seções |
| `space-8` | 32 | Margem de bloco |
| `space-12` | 48 | Margem de topo de página |

**Padding canônico** — substitui as 12 variantes atuais:

| Contexto | Padding |
|---|---|
| Célula de tabela, densidade compacta | `space-2 space-3` (8/12) |
| Célula de tabela, densidade completa | `space-3 space-4` (12/16) |
| Campo de formulário | `space-2 space-3` (8/12) |
| Card | `space-4` (16) |
| Cabeçalho de card | `space-3 space-4` (12/16) |
| Modal / painel | `space-6` (24) |

---

## 4. Raio

Quatro valores. Fim.

| Token | px | Uso |
|---|---|---|
| `radius-control` | 6 | Botão, campo, chip, badge |
| `radius-card` | 10 | Card, painel, bloco de conteúdo |
| `radius-overlay` | 14 | Modal, drawer, popover |
| `radius-pill` | 999 | Ponto de status, avatar, contador |

Hoje há 7 valores sem critério (`rounded-lg` 285 · `rounded` 139 · `rounded-xl` 104 ·
`rounded-full` 54 · `rounded-md` 3 · `rounded-2xl` 2 · `rounded-t` 1).

---

## 5. Sombras e elevação

**Dois níveis. Fim.** Hoje há 5.

| Nível | Definição | Uso |
|---|---|---|
| `rest` | **Sem sombra.** Borda 1px `--color-border` | Card, painel, tabela — tudo em repouso |
| `overlay` | `0 8px 24px -4px rgba(14,23,39,.18), 0 2px 6px -2px rgba(14,23,39,.10)` | Modal, drawer, popover, dropdown |

Elevação em repouso é feita por **borda**, não por sombra. Isso atende diretamente ao
"evitar excesso de sombras" e produz um produto mais nítido em tela de baixa qualidade —
relevante para uso em campo.

Nenhum gradiente em nenhum lugar da interface. A única exceção permitida é o *scrim* de
legibilidade sobre imagem de satélite no mapa.

---

## 6. Bordas

| Token | Valor | Uso |
|---|---|---|
| `--color-rule` | 1px `slate-100` | Divisor entre linhas, dentro de card |
| `--color-border` | 1px `slate-200` | Contorno de card, painel, tabela |
| `--color-border-control` | 1px `slate-400` | **Campo de formulário** (3:1 obrigatório) |
| Borda ativa | 1px `navy-600` | Campo em foco, item selecionado |
| Trilho de status | **3px** cor do status | Borda esquerda de linha/card com estado |
| Anel de foco | 3px `navy-600` a 30% de opacidade, deslocado 1px | Todo elemento focável |

---

## 7. Iconografia

**`lucide-react`**, já dependência. Os 14 arquivos com `<svg>` inline migram.

| Tamanho | px | Uso |
|---|---|---|
| `icon-dense` | 14 | Dentro de célula de tabela compacta |
| `icon-base` | 16 | Padrão — botão, item de navegação, ação de linha |
| `icon-header` | 20 | Cabeçalho de página, título de seção |
| `icon-empty` | 24 | Estado vazio |

Quatro valores, fim. Hoje há 12 (11, 12, 13, 14, 15, 16, 18, 20, 22, 32, 36, 40).

Traço 1,5px (padrão do lucide). Todo ícone decorativo leva `aria-hidden="true"`.
**Nenhum ícone é o único portador de significado** — ver §12.

---

## 8. Densidade

Duas densidades, alternáveis pelo usuário nas telas de lista (`Compacto | Completo`,
padrão observado no Farmbox, [`02-` §1.5.8](02-market-benchmark.md)).

| | Compacto | Completo |
|---|---|---|
| Altura de linha | 36px | 48px |
| Padding de célula | 8/12 | 12/16 |
| Corpo | `sm` (13px) | `base` (14px) |
| Ícone | 14 | 16 |
| Alvo de toque efetivo | 36px ⚠️ | 48px ✅ |

**Regra de segurança:** o modo Compacto é **desativado automaticamente em ponteiro
grosseiro** (`@media (pointer: coarse)`), porque 36px fica abaixo do mínimo de toque. Em
celular e tablet só existe o modo Completo. Isso reconcilia densidade máxima no escritório
com a [Decisão 1](01-product-audit.md#13-decisões-tomadas) em campo.

Padrão por tipo de tela: **triagem** (calendário, recomendações, panorama) abre em
Compacto; **cadastro** abre em Completo. A escolha do usuário é persistida por tela.

### Alvos de toque

| Contexto | Mínimo |
|---|---|
| Ponteiro fino (mouse) | 36px |
| Ponteiro grosseiro (toque) | **48px** |
| Ação destrutiva, qualquer ponteiro | **48px** |

Hoje o `.icon-btn` é 32px — abaixo em todos os casos.

---

## 9. Layouts

### 9.1 Casca

```
┌─────────────────────────────────────────────────────────────┐
│ CROMO (navy-900, 240px) │  BARRA DE CONTEXTO (56px)         │
│                         ├───────────────────────────────────┤
│  marca + colapso        │  CABEÇALHO DE PÁGINA              │
│  ─────────────────      │  título · ação primária           │
│  grupo (Condensed)      ├───────────────────────────────────┤
│   ▎item ativo           │                                   │
│    item                 │  CONTEÚDO                         │
│  ─────────────────      │  largura máx. 1360px              │
│  grupo                  │                                   │
│    item                 │                                   │
│                         │                                   │
│  ─────────────────      │                                   │
│  ⚙ Configurações        │                                   │
│  👤 conta               │                                   │
└─────────────────────────┴───────────────────────────────────┘
```

**Uma única largura de página: 1360px**, centralizada. Substitui as 6 larguras atuais (e as
19 telas sem largura nenhuma). Exceção declarada: `RecomendacaoPrintPage` usa largura de
papel (§11).

### 9.2 Barra de contexto — obrigatória, persistente

Faixa de 56px abaixo do topo, presente em toda tela autenticada. Resolve o problema P10 do
`03-` (contexto misturado com filtro):

```
Cliente [ Todos ▾ ]   Fazenda [ Todas ▾ ]   Safra [ 2026/27 ▾ ]
```

- **Contexto** (cliente · fazenda · safra) vive **aqui**, é persistente entre telas e
  aceita "Todos" como valor legítimo — padrão verificado nas duas plataformas de
  referência ([`02-` §1.5.1](02-market-benchmark.md)).
- **Filtro de atributo** (status, cultura, tipo, data) vive **junto da tabela**, nunca aqui.

Essa separação é a regra que elimina a ambiguidade de "onde ponho este seletor?": se
recorta *qual recorte do mundo estou vendo*, é contexto; se recorta *quais linhas da
lista*, é filtro.

### 9.3 Grade

12 colunas, gap `space-6` (24px). Formulário usa 2 colunas em ≥1024px, 1 coluna abaixo.

### 9.4 Pontos de quebra

| Nome | Largura | Comportamento |
|---|---|---|
| `base` | <640px | Cromo vira **navegação inferior** (5 itens + "mais"). Tabela em coluna seletiva ou card. Densidade travada em Completo. Formulário em 1 coluna. Modal em tela cheia |
| `sm` | ≥640px | Barra de contexto empilha em 2 linhas |
| `lg` | ≥1024px | Cromo lateral aparece. Formulário em 2 colunas |
| `xl` | ≥1280px | Layout pleno |

**A sidebar não vira gaveta lateral no celular** — vira barra inferior, para uso com uma
mão ([`02-` §2.2](02-market-benchmark.md)).

### 9.5 Componentes

**Card** — superfície branca, borda 1px `--color-border`, raio `radius-card`, sem sombra.
Cabeçalho opcional com filete inferior `--color-rule`. Trilho de status de 3px na borda
esquerda quando carrega estado.

**Botões**

| Variante | Fundo | Texto | Borda | Verificação |
|---|---|---|---|---|
| Primário | `navy-800` | branco | — | 13,47:1 ✅ |
| **Acento** | `gold-500` | **`navy-800`** | — | 6,03:1 ✅ |
| Secundário | branco | `slate-800` | `slate-400` | 15,81:1 ✅ |
| Terciário | transparente | `navy-600` | — | 8,06:1 ✅ |
| Destrutivo | `#991B1B` | branco | — | 7,11:1 ✅ |

Altura 36px (compacto) / 40px (padrão) / 48px (toque). Raio `radius-control`. Peso
`semibold`, tamanho `base`. **`:focus-visible` obrigatório** nas cinco variantes — hoje
nenhuma define.

O botão acento (dourado) é reservado à **ação de maior consequência da tela** — no máximo
um por tela. Emitir recomendação, finalizar aplicação. Não é "botão bonito"; é o gesto que
compromete.

**Tabelas** — o núcleo do produto.

- Cabeçalho: `Barlow Condensed`, `xs`, versalete, `slate-600`, fundo
  `--color-surface-sunken`, fixo no scroll vertical.
- Linha: trilho de status 3px à esquerda quando aplicável; divisor `--color-rule`; sem
  zebra (o trilho já dá o ritmo).
- Numérica: `IBM Plex Mono`, alinhada à direita, `tabular-nums`.
- Ação: **texto + ícone** (`Editar ✎`), alinhada à direita, nunca ícone puro
  ([`02-` §1.5.7](02-market-benchmark.md)).
- Linha inteira navegável; hover `navy-50`.
- Seleção múltipla com barra de ação em lote.
- Truncamento de lista longa com contador (`+ 3 talhões`).
- **Tela estreita:** triagem → coluna seletiva (identificador, status, data); cadastro →
  card empilhado. `overflow-x-auto` sozinho é proibido.

**Formulários**

- **Nunca em modal quando complexo.** Página seccionada (padrão) ou expansão inline na
  linha (edição simples). Modal fica só para diálogo curto e confirmação.
- Cada seção: título `md` + **uma linha de explicação** `sm` em `--color-ink-muted`. É a
  hierarquia que substitui as 4 escalas de label atuais.
- Rótulo: `sm`, `medium`, `--color-ink-muted`, acima do campo.
- Campo: altura 40px, borda `--color-border-control`, raio `radius-control`, foco com anel.
- **Marca o opcional, não o obrigatório** — `Observações · opcional`.
- Erro: abaixo do campo, `xs`, `#991B1B`, com o `detail` do backend, nunca texto fixo.
- Barra de ação **fixa no rodapé**, com verbos distintos:
  `Emitir recomendação` · `Salvar rascunho` · `Cancelar`.

**Indicadores**

Número `xl` em `IBM Plex Mono` + ponto de status + rótulo `xs` em `Barlow Condensed`
versalete. **Sem caixa de ícone colorida** — é o clichê SaaS que o `03-` §5 apontou. **Todo
indicador é clicável e leva à lista já filtrada.**

**Mapas**

- Base satélite dessaturada (filtro `saturate(0.7)`).
- Contorno de talhão: 2px `navy-800`; selecionado: 2px `gold-500`.
- Preenchimento pela **cor de trilho do status** a 25% de opacidade — o mapa vira
  ferramenta de triagem sem coletar nenhum dado novo.
- Rótulo em `Barlow Condensed` `xs` com halo branco.
- Legenda sempre visível.

---

## 10. Estados

### 10.1 Carregando

**Skeleton**, não texto. Blocos em `slate-100`, raio `radius-control`, com a forma do
conteúdo que vai chegar. Sem animação de pulso em `prefers-reduced-motion`. Texto
"Carregando..." só para ação pontual dentro de botão.

Hoje: 40 ocorrências do mesmo texto em 12 tratamentos visuais, 1 único skeleton.

### 10.2 Vazio

Três partes obrigatórias, sempre nesta ordem:

1. **Título** `md` `semibold` — o que não existe
2. **Explicação da regra** `sm` `--color-ink-muted` — *por que* está vazio, referindo-se
   ao **contexto ativo**
3. **Ação** — botão terciário ou acento

> **Nenhuma recomendação para os filtros desta safra**
> Recomendações aparecem aqui quando têm talhão e produto definidos. Rascunhos ficam em
> *Em edição*.
> `[ Nova recomendação ]`

Sem ilustração ([`02-` §2.13](02-market-benchmark.md): custo de manutenção não se paga num
produto de trabalho diário). Ícone `icon-empty` em `slate-300` é permitido.

### 10.3 Erro de carregamento

Distinto de vazio, sempre. Bloco com borda `#991B1B`, título, o `detail` do backend e ação
de repetir. **Nunca renderizar lista vazia quando a consulta falhou.**

Três camadas obrigatórias:
1. `ErrorBoundary` envolvendo o conteúdo do layout
2. `onError` global no `QueryCache` → toast
3. Componente de estado por consulta

### 10.4 Erro de formulário

No campo, com o `detail` do backend. Resumo no topo só se houver mais de 3 erros.

### 10.5 Sucesso

Toast, canto inferior direito, `navy-800` com texto branco, `role="status"`. **Mesmo verbo
do botão que disparou** — "Emitir recomendação" → "Recomendação emitida". Fila de até 3;
não remove o anterior.

### 10.6 Confirmação destrutiva

`ConfirmDialog`, nunca `confirm()` nativo. Diz **a consequência**, não só pergunta:

> **Inativar o talhão T-14?**
> Ele deixa de receber novas recomendações e continua nos relatórios das safras anteriores.

### 10.7 Desativado e somente-leitura

Desativado: `slate-300` sobre `slate-50`, `cursor: not-allowed`, `aria-disabled`. Sempre
acompanhado do motivo em texto — nunca desabilitar sem explicar.

### 10.8 Foco

Anel de 3px `navy-600` a 30%, deslocado 1px, em **todo** elemento focável. Nunca
`outline: none` sem substituto.

### 10.9 Microinterações

| Evento | Duração | Curva |
|---|---|---|
| Cor (hover, foco) | 100ms | `ease-out` |
| Altura de linha (troca de densidade) | 160ms | `ease-out` |
| Expansão inline | 180ms | `ease-out` |
| Sobreposição (modal, drawer) | 200ms | `ease-out` |

**Nada anima na carga inicial.** Nenhuma animação decorativa, nenhum efeito de entrada
escalonado. Todo movimento respeita `prefers-reduced-motion`. A classe `.card-in` atual —
hoje código morto — é removida.

---

## 11. Enxerto A — a recomendação impressa

**Escopo: `RecomendacaoPrintPage` e o PDF gerado. Nada mais.**

É o único artefato do produto que sai da tela e vira documento assinado nas mãos do
cliente. Recebe tratamento de documento, não de tela:

- Largura de papel (A4), margens tipográficas, não a grade de 1360px.
- **Todo dado regulado em `IBM Plex Mono`**: dose, área, volume, nº de registro Agrofit,
  período de carência. A monoespaçada afirma que aquilo é um registro, não uma prosa.
- Cabeçalho institucional em `navy-800` com filete `gold-500` de 2px — a única aparição do
  dourado no documento.
- Corpo tabular com filetes finos; sem card, sem sombra, sem raio.
- Bloco de assinatura com identificação do responsável técnico.
- Preto sobre branco no corpo (`slate-900`), para custo de impressão e legibilidade em
  fotocópia.

## 12. Enxerto B — o glifo de talhão

**Escopo: cadastro de talhões e seleção de talhões dentro de recomendações. Nada mais.**

Nessas telas, cada talhão aparece acompanhado de um **micro-glifo com sua geometria real**:

- Quadrado de 20px (lista) ou 40px (card de detalhe).
- Polígono normalizado ao quadro, traço 1px `navy-400`.
- Preenchimento pela cor de trilho do status atual, 25% de opacidade.
- `aria-hidden` — é reforço visual, **nunca o único identificador**. O nome do talhão está
  sempre presente ao lado.
- Renderizado do mesmo contorno que alimenta o mapa. Sem geometria cadastrada, exibe
  quadrado tracejado `slate-300`.

Justificativa do escopo: nessas duas telas o usuário precisa **reconhecer qual área é**, e
o nome (`T_ACIMA CURRAL`) não ajuda. Em telas de triagem, o que importa é o estado — e ali
o glifo seria ruído.

---

## 13. Regras que eliminam ambiguidade

Quando houver dúvida, decidir por esta lista:

1. Texto abaixo de `slate-500` **não existe**. Se está ilegível, ou sobe de tom ou não é escrito.
2. Nada abaixo de 12px.
3. Dourado nunca é texto sobre claro, nunca recebe texto branco. Superfície com texto azul, ou acento sobre cromo escuro.
4. **Um botão acento por tela**, no máximo.
5. Sombra só em sobreposição. Repouso usa borda.
6. Nenhum gradiente, exceto scrim sobre imagem de satélite.
7. Trilho de status = "isto precisa de você". `prevista` e `desativada` não têm trilho.
8. Contexto (cliente/fazenda/safra) na barra de contexto. Filtro de atributo junto da tabela.
9. Formulário complexo nunca em modal.
10. Ação de linha sempre texto + ícone.
11. Todo número em `IBM Plex Mono` tabular.
12. Estado vazio sempre com três partes: o quê, a regra, a ação.
13. Erro de carregamento nunca se parece com lista vazia.
14. Ícone nunca é o único portador de significado.
15. Mono só para dado; glifo só nas duas telas da §12. Fora disso, não.

---

## 14. Impacto e migração

Esta especificação **substitui a identidade cromática inteira**. É a mudança de maior raio
do redesign e precisa ser tratada como tal, dadas as prioridades do `CLAUDE.md`
(estabilidade e mudanças mínimas).

**O que muda em cada lugar:**

| Arquivo | Mudança |
|---|---|
| `index.css` — bloco `@theme` | Substituição completa dos tokens `brand-*`, `accent`, `gold`, `canvas` |
| `index.css` — `:root` legado | `--c-dark`, `--c-green`, `--c-teal`, `--c-gold` removidos após migrar os 2 usos inline em `Layout.tsx` |
| `index.css` — classes utilitárias | `.input`, `.btn-primary`, `.btn-secondary`, `.icon-btn`, `.sidebar-link` reescritas; `.card-in` removida |
| `lib/statusCores.ts` | Novos valores nas 6 famílias de classe; **a estrutura e a API não mudam** |
| `lib/tipoCores.ts` | Sem mudança |
| `lib/toast.ts` | Passa a ler tokens em vez de `#1f6f5c`/`#dc2626` |
| Fontes | `Fraunces` e `Hanken Grotesk` saem; entram `Barlow`, `Barlow Condensed`, `IBM Plex Mono` |

**Risco a declarar:** como `brand-*` é usado em praticamente toda tela, uma troca de tokens
muda 100% da aparência de uma vez. Não existe caminho incremental para a cor — ou a paleta
antiga e a nova convivem (feio e confuso), ou a troca é atômica.

**Sequência recomendada**, respeitando a ordem de dependência do [`03-` §8](03-current-ui-audit.md):

1. **Ondas 1 e 2 na paleta atual** — contraste (`gray-400` → piso legível), estados de
   erro, escala tipográfica, espaçamento. Não dependem da cor e já entregam a maior parte
   do ganho real de usabilidade.
2. **Troca atômica de tokens** — um PR só, que altera `index.css` e `statusCores.ts` e
   nada mais. Diff pequeno, revisável, e reversível por `git revert` se algo escapar.
3. **Adoção por tela**, seguindo as ondas 3 a 5: componentes de estado, padrões de
   interação, e por fim o bloco antigo de `cadastros/` com o padrão único de CRUD.

O passo 2 é o único que exige coragem. Os passos 1 e 3 são incrementais e seguros.

---

## 15. O que ainda não está especificado

Honestidade de escopo — o que este documento **não** resolve e precisa do `05-`:

- **Padrão único de CRUD** para as 14 rotas de `cadastros/` (Decisão 3): esta spec dá o
  vocabulário, não o gabarito da tela.
- **Comportamento de coluna seletiva**: quais colunas sobrevivem em tela estreita, tabela
  por tabela.
- **Layout do calendário/kanban** sob a nova densidade.
- **Ícone e marca**: a identidade cromática mudou, mas o logotipo "AgroConsultoria" da
  sidebar não foi tratado.
- **Modo escuro**: não considerado. Se for desejado, a paleta de neutros frios e o cromo
  já dão a base, mas exige passe próprio.
