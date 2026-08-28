# 03 — Auditoria de UI/UX do AgroPlan

> **Data:** 28/08/2026 · **Branch:** `feature/recomendacao-criar-e-finalizar`
> **Referências:** [`01-product-audit.md`](01-product-audit.md) (arquitetura e produto) ·
> [`02-market-benchmark.md`](02-market-benchmark.md) (padrões de mercado observados)
> **Nenhuma alteração foi feita no código.**

---

## 1. Metodologia e escopo

Auditoria de **interface e experiência**, complementar às duas anteriores: o `01-`
mapeou arquitetura e produto; o `02-` estabeleceu a régua externa; este documento avalia
a **execução visual e de interação** do que está no ar.

**Método:** medição direta sobre `Agro-Plataform/frontend/src/` (92 arquivos `.tsx`,
20.240 linhas). Onde havia número a apurar, ele foi apurado — inclusive os **contrastes,
calculados pela fórmula WCAG 2.1** a partir dos hex reais da paleta, não estimados a olho.

**O que não foi feito:** a aplicação não foi percorrida visualmente tela a tela com o
backend em pé, e não há capturas do AgroPlan como as que temos dos concorrentes. Para
dimensões mensuráveis no código — contraste, escala tipográfica, espaçamento, alvos de
toque, densidade — isso não limita nada; a medição é mais precisa que a inspeção visual.
Para **gestalt** (o quanto uma tela "parece" pesada ou confusa como um todo), as
observações são derivadas da estrutura, não da percepção. Onde isso importa, está dito.

**Escala de gravidade:**

| Nível | Critério |
|---|---|
| 🔴 **Crítico** | Induz o usuário a erro, impede uso, ou falha norma de acessibilidade em larga escala |
| 🟠 **Alto** | Prejudica o trabalho diário ou bloqueia uma decisão já tomada (ex.: responsividade) |
| 🟡 **Médio** | Custo de manutenção e ruído visual acumulado, sem impedir a tarefa |
| ⚪ **Baixo** | Higiene, código morto, detalhe isolado |

---

## 2. Avaliação por dimensão

### 2.1 Contraste — 🔴 a pior nota da auditoria

Contrastes calculados pela fórmula WCAG 2.1 sobre os hex reais:

| Classe | Usos | Sobre branco | Sobre `bg-canvas` | Veredito |
|---|---|---|---|---|
| `text-gray-400` | **328** | **2,54:1** | **2,37:1** | ❌ Falha grave |
| `text-gray-300` | **50** | **1,47:1** | **1,38:1** | ❌ Falha grave |
| `text-gray-500` | 228 | 4,83:1 | 4,52:1 | ✅ Passa AA (no limite) |
| `text-gray-600` | 145 | 7,56:1 | 7,06:1 | ✅ Passa |
| `text-gray-700` | 172 | 10,31:1 | 9,63:1 | ✅ Passa |
| `text-gray-800` | 121 | 14,68:1 | 13,71:1 | ✅ Passa |

**`text-gray-400` é a cor de texto mais usada do produto inteiro** — 328 ocorrências, mais
que qualquer outra — e falha o mínimo de 4,5:1 por larga margem. Somado ao `gray-300`,
são **378 usos de texto que não atendem WCAG AA**.

A paleta de marca, por contraste, é bem construída — **tudo passa**:

| Combinação | Razão | Veredito |
|---|---|---|
| Branco sobre `brand-500` (botão primário) | 6,02:1 | ✅ |
| Branco sobre `brand-700` (hover) | 10,09:1 | ✅ |
| `brand-500` sobre branco (link) | 6,02:1 | ✅ |
| `brand-600` sobre `brand-50` (PageHeader) | 6,69:1 | ✅ |
| Sidebar: label 72% sobre `--c-dark` | 8,65:1 | ✅ |
| Gold sobre `--c-dark` (trilho ativo) | 7,37:1 | ✅ |

Duas exceções pontuais:
- **Sidebar teal (`#2a9d8f`) sobre o fundo escuro: 4,49:1** — falha por 0,01. Usado nos
  cabeçalhos de grupo **e no e-mail do usuário**. Escurecer um degrau resolve.
- **Badge `desativada`: 2,43:1** — aqui é provavelmente deliberado (o comentário no
  código diz "neutra e apagada de propósito: não é urgência nem conquista, é ausência").
  Aceitável se o `line-through` carrega o significado, mas o texto em si é ilegível.

> **O problema não é a paleta — é o uso do cinza.** A marca foi pensada; os cinzas foram
> escolhidos por aparência, não por legibilidade.

**Agravante decisivo:** a [Decisão 1](01-product-audit.md#13-decisões-tomadas) definiu que
o produto **deve ser responsivo para uso em campo**. O `02-` registrou que app de campo
exige **alto contraste para luz solar direta**. Texto a 2,54:1 é difícil num monitor de
escritório; sob sol, é invisível. Esta dimensão passa de dívida de acessibilidade a
**bloqueio funcional da decisão já tomada**.

### 2.2 Tipografia — 🟠 escala colapsada com cauda longa

Distribuição real de tamanhos:

| Tamanho | Usos | % |
|---|---|---|
| `text-sm` (14px) | 533 | 45,6% |
| `text-xs` (12px) | 478 | 40,9% |
| `[11px]` *(arbitrário)* | 58 | 5,0% |
| `[10px]` *(arbitrário)* | 41 | 3,5% |
| `text-xl` (20px) | 21 | 1,8% |
| `text-base` (16px) | 15 | 1,3% |
| `text-lg` (18px) | 12 | 1,0% |
| `[9px]` *(arbitrário)* | 8 | 0,7% |
| `text-2xl` (24px) | 4 | 0,3% |

Dois problemas de sinais opostos:

**(a) A hierarquia colapsou no meio.** 86,5% de todo o texto é 12px ou 14px — dois
tamanhos separados por 2px. Não há degrau perceptível entre "rótulo", "conteúdo",
"secundário" e "meta". A hierarquia é feita quase toda por **cor** (e por isso os cinzas
foram sendo puxados para baixo até o `gray-400` ilegível) e por **peso** — `font-medium`
sozinho aparece 422 vezes, contra 14 de `font-normal`, o que significa que "médio" virou
o peso padrão e perdeu função distintiva.

**(b) Há uma cauda de 107 tamanhos arbitrários abaixo de 12px**, chegando a **9px**. Nove
pixels é menor que qualquer mínimo defensável, e a combinação `[10px]`/`[11px]` com
`text-gray-400` — que ocorre no código — produz texto simultaneamente minúsculo e de
baixíssimo contraste.

A face de exibição (`Fraunces`) aparece em 39 lugares, quase toda em `text-xl`/`text-2xl`
— ou seja, o único degrau tipográfico real do produto existe em 4% do texto.

### 2.3 Hierarquia visual — 🟡 achatada por consequência

Decorre direto de 2.1 e 2.2: com 86,5% do texto em dois tamanhos vizinhos e a distinção
delegada a cinzas que vão até o ilegível, a hierarquia depende de recursos frágeis.

Estrutura de heading também é irregular: **22 `<h1>`, 22 `<h2>`, 15 `<h3>`** no projeto,
mas o `PageHeader` — que produz o `<h1>` canônico — é usado em apenas **8 arquivos**. Os
outros 22 `<h1>` são escritos à mão, cada um com sua combinação de tamanho, peso e cor.
Para leitor de tela, o esqueleto do documento é imprevisível de tela para tela.

**Onde funciona bem:** o `DashboardPage` acerta — `text-2xl` + `font-display` +
`font-semibold` + `.tnum` para o número, `text-xs text-gray-500` para o rótulo. É o único
lugar com degrau tipográfico real, e não por acaso é a tela mais recente.

### 2.4 Espaçamento e densidade — 🟡 sem escala definida

Padding de célula/controle, 12 variantes:

| Padding | Usos |
|---|---|
| `px-3 py-2` | 134 |
| `px-4 py-2` | 130 |
| `px-4 py-3` | 129 |
| `px-3 py-1.5` | 51 |
| `px-1.5 py-0.5` | 44 |
| `px-3 py-2.5` | 40 |
| `px-4 py-2.5` | 39 |
| *(mais 5 variantes)* | 65 |

As três primeiras — praticamente idênticas, diferindo em 4px — somam 393 usos sem regra
discernível de quando usar cada uma. O resultado é que **tabelas vizinhas têm alturas de
linha diferentes**, e o olho percebe a inconsistência sem conseguir nomeá-la.

Mesmo padrão em raio e sombra:

- **Raio:** `rounded-lg` 285 · `rounded` 139 · `rounded-xl` 104 · `rounded-full` 54 ·
  `rounded-md` 3 · `rounded-2xl` 2 · `rounded-t` 1 → **7 valores**, três dominantes sem
  critério de superfície.
- **Sombra:** `shadow-sm` 81 · `shadow-xl` 15 · `shadow-md` 10 · `shadow-lg` 7 ·
  `shadow` 5 → **5 níveis** para um produto que precisa de dois (repouso e sobreposição).
- **Ícones:** **12 tamanhos** (11, 12, 13, 14, 15, 16, 18, 20, 22, 32, 36, 40) — sem
  escala nomeada. `13`, `14`, `15` e `16` coexistem em contextos equivalentes.

O `01-` já havia registrado a divergência de largura de página (6 valores, incluindo 19
telas sem `max-w` nenhum). Junto, isso forma o quadro: **não existe sistema de
espaçamento — existe escolha caso a caso**.

Sobre **densidade** propriamente: a densidade média é adequada para o público (o `02-`
concluiu que densidade é aliada do agrônomo, não inimiga). O problema não é ser denso
demais — é ser **inconsistentemente denso**.

### 2.5 Navegação — 🟠 contexto e filtro misturados

O `Layout` acerta o essencial: sidebar colapsável, grupos colapsáveis, ambos persistidos,
item ativo com trilho dourado. É código bom.

Três problemas, todos confirmados contra o `02-`:

1. **Não há separação entre contexto e filtro.** As duas referências do benchmark põem
   cliente/fazenda/safra num seletor persistente no topo (§1.5.1 do `02-`) e deixam os
   filtros de atributo junto da tabela. O AgroPlan mistura tudo numa barra só, por tela, e
   só 6 arquivos persistem a escolha (`useStickyState`) — **nenhum deles em `cadastros/`**.
2. **Configuração compete com trabalho diário.** `Bicos`, `Equipamentos`, `Tipos de
   Contrato`, `Perfis de Intervalo` estão no mesmo grupo "Cadastros" que `Clientes` e
   `Produtos`. O Farmbox isola configuração no rodapé da sidebar; o Cropwise usa
   engrenagem na barra superior.
3. **Sem breadcrumb**, com hierarquia de 3 níveis (`/planejamento/:id/talhao/:talhaoId`).
   O usuário depende do botão voltar do navegador.

### 2.6 Botões — 🟡 boa base, adoção parcial

`.btn-primary` (61 usos), `.btn-secondary` (9), `.icon-btn` (14) existem e são coerentes.
Mas há 10 usos de `bg-brand-500`/`bg-brand-600` aplicados à mão em botões, fora das
classes — divergência pequena, porém é exatamente por onde a inconsistência entra.

O desequilíbrio `btn-primary` 61 × `btn-secondary` 9 sugere que ações secundárias estão
sendo montadas à mão em vez de usar a classe.

**Foco de teclado:** melhor do que eu esperava. Inputs têm foco tratado globalmente em
`index.css` (`box-shadow` + borda de marca) e as ocorrências de `outline-none` vêm quase
todas pareadas com `focus:ring-*`. **Nenhum `outline-none` em `<button>` sem substituto.**
A lacuna real é menor: `.btn-primary`, `.btn-secondary` e `.icon-btn` **não definem
`:focus-visible`**, então dependem do anel padrão do navegador — presente, mas de
contraste imprevisível sobre o verde da marca.

### 2.7 Formulários — 🟠 quatro tipografias para o mesmo papel

Já registrado no `01-`, confirmado aqui: **173 `<label>` em 4 tratamentos concorrentes**
(`text-sm/gray-700` 57 · `text-xs/gray-500` 48 · `text-xs/gray-600` 22 · `[11px]` 9). Três
tamanhos e três tons para rotular campo, sem regra.

`FormField` existe e é usado em **2 arquivos**. Dois paradigmas convivem:
`react-hook-form`+`zod` em 10 arquivos, `useState` manual no resto (340 `useState` no
projeto, contra 36 `useMemo`/`useCallback`).

Erro de formulário é mostrado (14 arquivos com `mutation.isError`), mas quase sempre com
texto fixo `"Erro ao salvar."`, descartando o `detail` que o backend envia.

**Contra o benchmark (§1.5.9 do `02-`):** o Farmbox não usa label solto — cada seção tem
título **e uma linha explicando o que fazer ali**. Isso resolveria o problema das 4
escalas de forma estrutural: a hierarquia passa a ser *seção › campo*, não tamanho de
fonte.

### 2.8 Tabelas — 🟠 sem componente, sem resposta para tela estreita

28 tabelas em 26 arquivos, **nenhuma compartilhando componente**. Cada tela monta
`<table>`/`<thead>`/`<tbody>` com suas próprias classes — daí as 12 variantes de padding.

**20 das 28 não têm `overflow-x-auto`**; e nenhuma tabela de `cadastros/` tem. Com a
Decisão 1, isso deixa de ser detalhe: o `02-` (§2.10) concluiu que scroll horizontal
sozinho **não é resposta aceitável** — tabela larga em celular é ruim mesmo rolando.

**Acerto a preservar:** `.tnum` (números tabulares) em 53 lugares, 18 arquivos. É um dos
padrões mais bem executados do produto e deve virar regra para toda coluna numérica.

### 2.9 Filtros — 🟡 componente bom, padrão ausente

`SearchableSelect` (201 linhas) é sólido e tem boa adoção (12 arquivos). O problema é que
**não existe padrão de barra de filtro**: cada tela compõe a sua, algumas com largura
definida em `style={{ flex: "0 1 220px" }}` inline (`CalendarioHome`).

Persistência (`useStickyState`) em 6 arquivos, concentrada em `calendario`/`planejamento`/
`estoque`. As 14 telas de `cadastros/` — as de maior entra-e-sai — não persistem nada.

### 2.10 Estados — 🔴 o problema mais grave do produto

Reafirmado do `01-` porque continua sendo o item nº 1:

| Métrica | Valor |
|---|---|
| Arquivos com `useQuery` | 61 |
| Arquivos tratando `isError` **de query** | **2** |
| `ErrorBoundary` no projeto | **0** |
| Tratamentos visuais distintos de "Carregando..." | **12** |
| Variantes de copy de estado vazio | ~35 |
| Usos de skeleton | **1** |

Falha de rede renderiza **tabela vazia** — indistinguível de "não há registros". Para um
agrônomo triando pendências, é leitura errada de dado real.

**Contra o benchmark:** o `02-` (§1.5.4) identificou o melhor padrão de todo o
levantamento — estado vazio que **ensina a regra da seção** ("aparecerá aqui se tiver
progresso ou se estiver programada para começar hoje") e que se refere ao **contexto
ativo** ("nenhuma semeadura *nessa safra*"). O AgroPlan tem 35 variantes e nenhuma
ensina nada.

### 2.11 Feedback — 🟡 funciona, mas escapa do sistema

`lib/toast.ts` (58 linhas) manipula o DOM fora do React. Tem `role="status"` e
`aria-live="polite"` — correto. Mas **hardcoda `#1f6f5c` e `#dc2626`**: é o único ponto
do produto onde a cor de marca escapa dos tokens. E mostra um toast por vez (remove o
anterior) — o que vai atropelar quando houver tratamento global de erro de query.

`ConfirmDialog` existe e é bem feito (usa `Modal`, botão destrutivo vermelho, estado
`pending`), mas está em **2 arquivos**; **22 arquivos ainda usam `confirm()`/`alert()`
nativos** (28 ocorrências).

### 2.12 Consistência — 🟠 três gerações, confirmadas

O `01-` (§9) estabeleceu a estratificação; esta auditoria a confirma na camada visual.
Nenhuma exceção encontrada no bloco antigo: 14 rotas de `cadastros/` + `admin/` +
`almoxarifados/` com cabeçalho à mão, modal `fixed inset-0`, `confirm()` nativo, `<svg>`
inline e tabela sem scroll.

### 2.13 Responsividade — 🟠 bloqueia a Decisão 1

**13 de 92 arquivos** usam breakpoint (35 ocorrências: `sm:` 20 · `lg:` 11 · `md:` 3 ·
`xl:` 1 · `2xl:` 0). Sidebar sem menu mobile. 20 tabelas sem scroll. Modal com formulário
longo em tela estreita.

### 2.14 Acessibilidade — 🔴 nome acessível ausente em escala

| Métrica | Valor |
|---|---|
| `title=` | 151 |
| `aria-label=` | **15** |
| `aria-*` (qualquer) | 18 |
| `role=` | 2 |

Botões só-ícone (41 `Pencil`, 24 `Trash`) dependem de `title`, que não é lido de forma
confiável por leitor de tela. Some-se o contraste de §2.1 e os 9px de §2.2.

**Alvo de toque:** `.icon-btn` é **32×32px**. O mínimo para uso com luvas, registrado no
`02-` (§2.15), é **48×48dp**. Está 33% abaixo — e essa é a ação mais repetida do produto
(editar/inativar/remover em lista).

**Acertos:** `Modal.tsx` tem `role="dialog"`, `aria-modal`, `aria-labelledby` e devolve
foco ao fechar (corrigido nesta branch). Falta apenas focus trap. `toast` tem `aria-live`.

### 2.15 Legibilidade — 🔴 síntese das anteriores

Não é dimensão independente: é o produto de §2.1 + §2.2. Texto de 9–12px em `gray-400`
(2,54:1) é a combinação que aparece no código e representa o pior caso. Em escritório é
cansativo; sob sol, com luvas, é inutilizável — exatamente o cenário que a Decisão 1
tornou obrigatório.

### 2.16 Mapas, calendário e indicadores — 🟡 pontos altos, sem sistema

**Mapas** (`FazendaMapaPage` 465 linhas, 10 `useState`, 7 `useMemo`) são o código mais
bem estruturado do frontend, retrabalhados em FEAT-011…014.

**Calendário** é o subsistema mais maduro (22 arquivos, 3 visões, filtro compartilhado,
drawer de detalhe, legenda dedicada). O `02-` (§1.5.8) trouxe um padrão que ele não tem e
que lhe cai bem: **alternador de densidade** (`Resumido | Todos`).

**Indicadores:** 4 tratamentos distintos para o mesmo conceito (`StatCard`,
`PendenciaCard`, `ReportSummaryCards`, `ResumoVolumes`). O `PendenciaCard` é o melhor —
reusa `STATUS_DOT_CLASS`/`STATUS_LABEL` deliberadamente para o número do Início bater com
o do Calendário. Deveria ser a regra, não a exceção.

---

## 3. Catálogo de problemas

### 🔴 Críticos

---

**P1 — `text-gray-400` (2,54:1) é a cor de texto mais usada do produto**

- **Problema:** 328 usos de `text-gray-400` e 50 de `text-gray-300` (1,47:1) — 378
  ocorrências de texto que falham WCAG AA por larga margem.
- **Impacto:** Rótulos, metadados, valores secundários e estados vazios ficam ilegíveis
  para usuários com baixa visão e cansativos para todos. Sob luz solar — cenário exigido
  pela Decisão 1 — tornam-se invisíveis, **bloqueando a decisão já tomada**.
- **Gravidade:** Crítica.
- **Recomendação:** Rebaixar `gray-400` para `gray-500` (4,83:1) como piso de texto, e
  `gray-300` para uso exclusivamente decorativo (borda, divisor, ícone desativado) —
  nunca texto. Fixar isso como regra no design system e verificar na revisão. É uma
  substituição amplamente mecânica, de altíssimo retorno.

---

**P2 — Falha de carregamento renderiza como lista vazia**

- **Problema:** 61 arquivos usam `useQuery`; apenas 2 tratam `isError`. Sem
  `ErrorBoundary` no projeto.
- **Impacto:** O agrônomo lê "não há pendências" quando o dado não carregou. É indução a
  erro em decisão agronômica, não incômodo estético. Exceção de render derruba a
  aplicação para tela branca sem caminho de volta.
- **Gravidade:** Crítica.
- **Recomendação:** Três camadas, na ordem: (a) `ErrorBoundary` no `Layout`; (b)
  `onError` global no `QueryCache` de `main.tsx` — ~8 linhas cobrindo as 289 queries;
  (c) componente `<EstadoConsulta>` com os três estados, copiando o markup que já
  funciona em `ApListPage.tsx:197-212`, aplicado por tela conforme o redesign passar.

---

**P3 — Botão de ação sem nome acessível, em escala**

- **Problema:** 151 `title=` contra 15 `aria-label=`. As ações mais repetidas do produto
  (editar 41, remover 24) são só-ícone.
- **Impacto:** Produto inoperável por leitor de tela nas ações de linha. Também prejudica
  descoberta para usuário novo.
- **Gravidade:** Crítica.
- **Recomendação:** Adotar o padrão do benchmark (§1.5.7 do `02-`): **texto + ícone**
  (`EDITAR ✎`) nas ações de linha, que resolve acessibilidade e descoberta de uma vez,
  sem depender de `aria-label`. Onde o espaço exigir ícone puro, `aria-label` obrigatório.

---

### 🟠 Altos

---

**P4 — Alvo de toque 33% abaixo do mínimo para campo**

- **Problema:** `.icon-btn` é 32×32px; o mínimo para uso com luvas é 48×48dp.
- **Impacto:** A ação mais frequente do produto fica imprecisa em campo — erro de toque
  em botão que pode ser "remover". Conflita diretamente com a Decisão 1.
- **Gravidade:** Alta.
- **Recomendação:** ~~44px como piso no desktop~~ → **36px no desktop, 48px em toque**
  *(decidido em 28/08/2026, ver [`05-` §8](05-design-system.md))*. 36px é coerente com a
  linha compacta e passa WCAG 2.2 AA com folga — o mínimo AA é 24×24px; 44px é critério
  AAA. Combinar com P3: o botão texto+ícone naturalmente atinge o alvo.

---

**P5 — Escala tipográfica colapsada, com cauda até 9px**

- **Problema:** 86,5% do texto em dois tamanhos vizinhos (12px e 14px); 107 tamanhos
  arbitrários abaixo disso, chegando a `[9px]`. `font-medium` em 422 usos contra 14 de
  `font-normal`.
- **Impacto:** Sem hierarquia tipográfica, a distinção recai sobre cor — o que produziu
  P1. Texto de 9–10px é ilegível em campo e sofrível em escritório.
- **Gravidade:** Alta.
- **Recomendação:** Definir escala nomeada de 5–6 degraus com **12px como piso absoluto**,
  eliminar os 107 arbitrários, e devolver função ao peso (`normal` como padrão de corpo,
  `medium` para ênfase real). Ver §4 do `04-` quando existir.

---

**P6 — 14 modais escritos à mão**

- **Problema:** 14 modais com `fixed inset-0` ignoram o `<Modal>` compartilhado, perdendo
  `Esc`, trava de scroll, clique-fora protegido, `role="dialog"`, `aria-modal` e
  devolução de foco.
- **Impacto:** Comportamento inconsistente e inacessível em metade dos diálogos.
- **Gravidade:** Alta.
- **Recomendação:** ⚠️ O `02-` (§1.5.3) mudou o alvo: **nenhuma das referências usa modal
  para formulário complexo**. Antes de migrar os 14 para `<Modal>`, decidir o padrão —
  página seccionada (Farmbox), assistente em etapas (Cropwise) ou expansão inline
  (Farmbox). Migrar para `<Modal>` só os que forem genuinamente diálogos curtos.

---

**P7 — Ação destrutiva via `confirm()` nativo em 22 arquivos**

- **Problema:** 28 ocorrências de `confirm()`/`alert()` para inativar cliente, remover AP,
  excluir item de manejo. `ConfirmDialog` existe e está em 2 arquivos.
- **Impacto:** Quebra de identidade visual e — mais grave — o diálogo pode ser suprimido
  pelo navegador, e aí **a ação não acontece sem nenhum feedback**.
- **Gravidade:** Alta.
- **Recomendação:** Substituição mecânica por `ConfirmDialog`. Aproveitar para adotar o
  padrão de §1.5.10 do `02-`: dizer **a consequência**, não só pedir confirmação.

---

**P8 — Quatro tipografias de label para o mesmo papel**

- **Problema:** 173 `<label>` em 4 combinações de tamanho e cor, sem regra.
- **Impacto:** Inconsistência mais difusa do produto — aparece em toda tela com
  formulário.
- **Gravidade:** Alta.
- **Recomendação:** Uma única escala, via `FormField` (hoje em 2 arquivos). Adotar
  também o padrão do Farmbox: **seção com título + linha de explicação**, que resolve a
  hierarquia estruturalmente.

---

**P9 — 20 de 28 tabelas sem resposta para tela estreita**

- **Problema:** Nenhum componente de tabela compartilhado; 20 tabelas sem
  `overflow-x-auto`; nenhuma de `cadastros/` com scroll.
- **Impacto:** Layout estoura em tela estreita, bloqueando a Decisão 1 em 42% das rotas.
- **Gravidade:** Alta.
- **Recomendação:** Duas respostas conforme o trabalho (§2.10 do `02-`): **coluna
  seletiva** para tabelas de triagem (calendário, recomendações), **card empilhado** para
  cadastros. `overflow-x-auto` sozinho está classificado como EVITAR.

---

**P10 — Contexto e filtro misturados na mesma barra**

- **Problema:** Cliente/fazenda/safra são filtros por tela, não contexto persistente. Só
  6 arquivos persistem escolha; nenhum em `cadastros/`.
- **Impacto:** Usuário refaz seleção a cada navegação. Impede a visão "tudo sobre este
  cliente/talhão" que o `02-` identificou como padrão das duas referências.
- **Gravidade:** Alta.
- **Recomendação:** Separar as duas camadas — contexto persistente no topo (aceitando
  "todos"), filtros de atributo junto da tabela. É pré-requisito de outras decisões do
  `04-`.

---

### 🟡 Médios

---

**P11 — Sem sistema de espaçamento**

- **Problema:** 12 variantes de padding (três quase idênticas somando 393 usos), 7 raios,
  5 sombras, 12 tamanhos de ícone, 6 larguras de página.
- **Impacto:** Tabelas vizinhas com alturas de linha diferentes; inconsistência que o
  olho percebe sem nomear. Custo de decisão em cada tela nova.
- **Gravidade:** Média.
- **Recomendação:** Escala nomeada: 2 densidades de célula (compacta/confortável), 3
  raios (controle/cartão/modal), 2 sombras (repouso/sobreposição), 4 tamanhos de ícone,
  1 largura de página.

---

**P12 — Doze tratamentos visuais para "Carregando..."**

- **Problema:** 40 ocorrências do mesmo texto em 12 combinações de classe. 1 único
  skeleton no projeto.
- **Impacto:** Ruído acumulado; percepção de espera maior que o necessário em listas.
- **Gravidade:** Média.
- **Recomendação:** Componente único; skeleton para lista e tabela (confirmado no
  benchmark, §1.5.5 do `02-`), texto só para casos pontuais.

---

**P13 — Trinta e cinco variantes de estado vazio, nenhuma acionável**

- **Problema:** ~35 copies distintas, algumas duplicadas literalmente entre arquivos. Só
  o `DashboardPage` convida à ação.
- **Impacto:** Oportunidade desperdiçada de ensinar o produto no momento da dúvida.
- **Gravidade:** Média.
- **Recomendação:** Componente `<EstadoVazio>` com título + explicação da regra + ação,
  seguindo §1.5.4 do `02-`. Referir-se ao **contexto ativo** ("nessa safra"), não ao
  absoluto.

---

**P14 — Quatro componentes de indicador para o mesmo conceito**

- **Problema:** `StatCard`, `PendenciaCard`, `ReportSummaryCards`, `ResumoVolumes`.
- **Impacto:** Mesmo conceito com 4 aparências entre telas.
- **Gravidade:** Média.
- **Recomendação:** Unificar tomando o `PendenciaCard` como base — é o único que amarra
  a cor do indicador à paleta do dado que representa.

---

**P15 — `PageHeader` em 8 de 32 telas**

- **Problema:** 24 `*Page.tsx` montam cabeçalho à mão; 22 `<h1>` avulsos.
- **Impacto:** Ritmo visual diferente entre seções; esqueleto de heading imprevisível
  para leitor de tela.
- **Gravidade:** Média.
- **Recomendação:** Adoção universal. Considerar absorver o contexto de P10 no próprio
  `PageHeader`, como faz o Farmbox ("Aplicações **na** FAZENDA **na safra** X").

---

**P16 — Falta `:focus-visible` nas classes de botão**

- **Problema:** `.btn-primary`, `.btn-secondary` e `.icon-btn` não definem foco; dependem
  do anel padrão do navegador.
- **Impacto:** Contraste do foco imprevisível sobre o verde da marca. Menor do que
  parecia — inputs estão bem cobertos.
- **Gravidade:** Média.
- **Recomendação:** Um `:focus-visible` explícito nas três classes, reusando o anel que
  `index.css` já aplica aos inputs.

---

**P17 — Sidebar teal falha AA por 0,01**

- **Problema:** `#2a9d8f` sobre `#0f2c26` = 4,49:1 (mínimo 4,5:1). Usado em cabeçalho de
  grupo e no e-mail do usuário.
- **Impacto:** Marginal, mas é texto persistente em toda a aplicação.
- **Gravidade:** Média.
- **Recomendação:** Clarear o teal um degrau no contexto da sidebar. Ajuste de um token.

---

### ⚪ Baixos

---

**P18 — `toast` hardcoda cores fora dos tokens**

`lib/toast.ts` usa `#1f6f5c` e `#dc2626` literais — único ponto onde a marca escapa do
sistema. **Recomendação:** ler as CSS custom properties. Reavaliar a fila de um toast por
vez quando o `onError` global de P2 entrar.

**P19 — `.card-in` é código morto**

Animação de entrada definida em `index.css`, com `prefers-reduced-motion` tratado, **sem
nenhum uso**. **Recomendação:** adotar nas listas de card ou remover.

**P20 — Variáveis CSS legadas ainda em uso**

`--c-dark` e `--c-teal` usados inline em `Layout.tsx` apesar dos tokens `@theme`
equivalentes. **Recomendação:** migrar as duas ocorrências — PR mecânico isolado, não de
passagem.

**P21 — 14 arquivos com `<svg>` inline**

Todos na geração antiga, apesar de `lucide-react` ser dependência; nenhum com
`aria-hidden`. **Recomendação:** trocar por lucide ao tocar cada tela.

---

## 4. Padrões antigos identificados

Concentrados no bloco `cadastros/` + `admin/` + `almoxarifados/` (16 rotas), sem exceção:

| Padrão antigo | Substituto disponível hoje |
|---|---|
| Cabeçalho `<h1>` à mão | `PageHeader` |
| Modal `fixed inset-0` | `Modal` |
| `confirm()` / `alert()` nativos | `ConfirmDialog` |
| `<svg>` inline | `lucide-react` |
| String Tailwind de input repetida | `.input` |
| Tabela sem container de scroll | *(nenhum — precisa ser criado)* |
| Botão só-ícone com `title` | *(nenhum — adotar texto+ícone)* |
| Sem persistência de filtro | `useStickyState` |

Sete dos oito substitutos **já existem no projeto**. O problema não é ausência de
componente — é adoção.

## 5. Padrões genéricos de SaaS a evitar

O `02-` recomendou diferenciação; estes são os pontos onde o AgroPlan hoje se parece com
qualquer painel administrativo:

1. **A fileira de quatro `StatCard`** no topo do dashboard (ícone em caixa colorida +
   número grande + rótulo). É literalmente a resposta-template de dashboard SaaS. Os
   `PendenciaCard` logo abaixo são melhores justamente por **serem acionáveis e amarrados
   à paleta do dado** — a fileira de cima não diz o que fazer com os números.
2. **Card branco `rounded-xl shadow-sm` sobre fundo cinza** como recurso universal —
   aparece em toda tela, sem hierarquia entre "isto é primário" e "isto é apoio".
3. **"Olá, {primeiro nome}" + data por extenso** como cabeçalho do dashboard. Ocupa a
   posição mais valiosa da tela com informação que o usuário já tem.
4. **Cartão de "Atalhos"** com lista de links ícone+título+descrição — padrão de
   onboarding de SaaS, questionável numa ferramenta de uso diário onde o menu lateral já
   está permanentemente visível.
5. **Copy de vazio genérica** ("Nenhum resultado", "Nenhum produto.") — o oposto do
   padrão que o benchmark identificou como o melhor.

Nenhum destes é erro; todos são **oportunidades desperdiçadas de parecer um produto de
manejo agrícola** em vez de um admin template.

## 6. Duplicações

Do `01-` (§8), reconfirmadas — a duplicação é de **padrão**, não de identificador, e por
isso invisível ao compilador:

| O que se repete | Escala |
|---|---|
| Chamada HTTP direta em componente | 256 |
| `queryKey` literal | 267 |
| Estilo de label | 173 em 4 variantes |
| Tratamento de erro `?.detail ?? "..."` | 49 |
| Padding de célula/controle | 12 variantes |
| Tratamento de "Carregando..." | 40 em 12 variantes |
| Copy de estado vazio | ~35 variantes |
| Estrutura de modal | 14 |
| Tamanho de ícone | 12 variantes |
| Componente de indicador | 4 |

## 7. Oportunidades

Além da correção de problemas — onde o produto pode ganhar:

1. **A paleta de status é o maior ativo de design do produto.** 7 estados × 6
   representações contextuais; o `02-` não encontrou nada comparável nas referências.
   Estendê-la para o **mapa** (pintar contorno de talhão por status) transforma o mapa
   existente em ferramenta de triagem sem coletar nenhum dado novo. É o melhor
   custo/benefício disponível.
2. **Alternador de densidade** (`Resumido | Todos`) no calendário — resolve a tensão
   denso × legível oferecendo os dois (§1.5.8 do `02-`).
3. **Estado vazio que ensina a regra** — o padrão mais barato e de maior retorno do
   benchmark inteiro.
4. **A recomendação impressa** (`RecomendacaoPrintPage`) é o único artefato que sai da
   tela e vira documento assinado na mão do cliente. Nenhuma referência trata a saída
   impressa como peça de design. É onde cabe a ousadia visual do redesign.
5. **Contexto no `PageHeader`** — absorver cliente/fazenda/safra no cabeçalho resolve
   P10 e P15 juntos, com um componente que já existe.

## 8. Ordem sugerida de ataque

Não é plano de implementação (isso é o `04-`), é ordem de dependência:

| Onda | Itens | Por quê primeiro |
|---|---|---|
| **1 — Fundação invisível** | P1 (contraste), P2 (estados), P16 (foco) | Não exige decisão de design; alto retorno; P1 e P2 são mecânicos e destravam a Decisão 1 |
| **2 — Vocabulário visual** | P5 (tipografia), P11 (espaçamento), P8 (label) | Define a régua. Redesenhar tela antes disso obriga a redesenhar duas vezes |
| **3 — Componentes de estado** | P12, P13, P14, P15 | Dependem da onda 2 para ter escala definida |
| **4 — Padrões de interação** | P6 (modal), P9 (tabela), P10 (contexto), P3/P4 (ações) | Exigem decisão de design explícita no `04-` |
| **5 — Adoção no bloco antigo** | Aplicar 1–4 às 16 rotas antigas | É a Decisão 3; só faz sentido com o padrão fechado |

As ondas 1 e 2 não são redesign — são pré-requisito dele. Fazer o `04-` (sistema de
design) sem elas significa desenhar sobre fundação que ainda vai mudar.

---

## Anexo — reprodutibilidade

Contrastes calculados pela fórmula WCAG 2.1 (luminância relativa) sobre os hex de
`index.css`, `statusCores.ts` e a paleta padrão do Tailwind. Demais números por varredura
sobre `Agro-Plataform/frontend/src/`:

```bash
# escala tipográfica real (inclui arbitrários)
grep -rhoE "\btext-(xs|sm|base|lg|xl|2xl)\b|\[[0-9]+px\]" --include="*.tsx" src | sort | uniq -c | sort -rn

# cinzas de texto
grep -rhoE "\btext-gray-[0-9]{2,3}\b" --include="*.tsx" src | sort | uniq -c | sort -rn

# padding de célula
grep -rhoE "px-[0-9.]+ py-[0-9.]+" --include="*.tsx" src | sort | uniq -c | sort -rn

# raio, sombra, tamanho de ícone
grep -rhoE "\brounded(-[a-z0-9]+)?\b" --include="*.tsx" src | sort | uniq -c | sort -rn
grep -rhoE "\bshadow(-[a-z0-9/]+)?\b" --include="*.tsx" src | sort | uniq -c | sort -rn
grep -rhoE "size=\{?[0-9]+\}?" --include="*.tsx" src | grep -oE "[0-9]+" | sort -n | uniq -c
```
