# 01 — Auditoria de produto do frontend

> **Data:** 28/08/2026 · **Branch:** `feature/recomendacao-criar-e-finalizar`
> **Escopo:** `Agro-Plataform/frontend/src/` — 92 arquivos `.tsx`, 20.240 linhas, 33 rotas
> **Nenhuma alteração foi feita no código.** Documento diagnóstico.

---

## 1. Sumário executivo

O frontend do AgroPlan **não tem inconsistência espalhada aleatoriamente**. Tem três
gerações de código claramente estratificadas, cada uma internamente coerente, escritas
em momentos diferentes com padrões diferentes. Essa é a descoberta que deve organizar
todo o redesign.

| Geração | Onde vive | Rotas | Característica |
|---|---|---|---|
| **Antiga** | `cadastros/`, `admin/`, `almoxarifados/` | 16 | Cabeçalho à mão, modal `fixed inset-0` à mão, `confirm()` nativo, `<svg>` inline, tabela sem scroll, zero `aria-label` |
| **Intermediária** | `planejamento/`, `estoque/`, `relatorio/` | 7 | Mistura os dois mundos, arquivo por arquivo |
| **Nova** | `calendario/`, `dashboard/` | 6 | `PageHeader`, `<Modal>`, `ConfigDialog`, lucide, `aria-label` começando a aparecer |

A geração antiga é **100% uniforme na sua divergência** — não há uma única exceção
dentro dela — e é a maior: 28 arquivos, 5.426 linhas, **14 das 33 rotas (42% das telas
do produto)**.

Isso é uma boa notícia para o redesign. O trabalho não é "corrigir 92 arquivos um a um";
é **trazer duas gerações até o padrão da terceira**, que já existe, já está em produção
e já provou funcionar. O alvo do redesign não precisa ser inventado — precisa ser
estendido.

### Os três riscos de maior impacto no usuário final

**1. Falha de carregamento é invisível — e mente para o usuário.**
61 arquivos usam `useQuery`; **apenas 2 tratam o erro dessa query**. Nos outros 59, se a
rede cai ou o backend responde 500, `isLoading` vira `false`, `data` cai no default `[]`,
e a tela renderiza **uma tabela vazia** — visualmente idêntica a "não há registros".
Um agrônomo abrindo o calendário para triar pendências vê "nada pendente" quando na
verdade o dado não carregou. Este é o problema mais grave do frontend hoje, e não é
estético: é uma leitura errada de dado real, no fluxo de decisão do produto.

**2. Não existe `ErrorBoundary` em lugar nenhum.**
Qualquer exceção de render derruba a aplicação inteira para tela branca, sem mensagem e
sem caminho de volta. Zero ocorrências de `ErrorBoundary`, `componentDidCatch` ou
`getDerivedStateFromError` no projeto.

**3. Ações destrutivas usam o diálogo nativo do navegador.**
22 arquivos ainda chamam `confirm()`/`alert()` (28 ocorrências) para inativar cliente,
remover AP, excluir item de manejo. Além de quebrar a identidade visual, esses diálogos
podem ser suprimidos pelo navegador — e quando são, **a ação simplesmente não acontece,
sem nenhum feedback**. O `ConfirmDialog` que resolveria isso já existe, mas só em 2
arquivos.

---

## 2. Metodologia e escopo

Análise **estática**: leitura do código-fonte e varreduras (`grep`/`find`) sobre
`Agro-Plataform/frontend/src/` na branch atual. Todo número deste documento é resultado
de uma varredura verificável, não de estimativa.

**O que não foi feito:**
- A aplicação não foi executada com o backend em pé para navegação real de ponta a ponta.
- Nenhuma auditoria automatizada de acessibilidade (axe, Lighthouse).
- Nenhuma medição de performance/bundle nesta passagem (exigiria `npm run build`, que
  escreve em `dist/` — fora do escopo "não altere nada").
- Nenhum teste com usuário real. Toda inferência sobre fluxo vem das rotas, da navegação
  e dos nomes/labels no código.

**Relação com `analise-frontend.md`:** aquele documento (23/08/2026, commit `f071e5c`) é
uma análise de **qualidade de código** — duplicação de fetch, bundle, `any`, dívida
técnica. Este aqui é de **produto e interface**. Onde há sobreposição, os números foram
recontados nesta data.

**Correção a um item da análise anterior:** `analise-frontend.md` registrava que
`Modal.tsx` não tinha `role="dialog"`, `aria-modal`, `aria-labelledby` nem devolução de
foco. **Isso foi corrigido nesta branch** — o componente hoje tem os quatro. O item não
deve ser reaberto. (Falta apenas focus trap.)

---

## 3. Arquitetura

**Camadas:** SPA React 19 + Vite 8 + TypeScript `strict`, Tailwind 4 com tokens
declarados via `@theme` em `index.css` (sem `tailwind.config.js`). Estado de servidor via
TanStack Query 5; HTTP via axios com cookie httpOnly + CSRF double-submit.

**Shell único:** `App.tsx` → `PrivateRoute` → `Layout` → `<Outlet />`. Não há variação de
layout por seção — toda a aplicação logada usa a mesma casca. Isso é uma força: um único
ponto para mudar navegação, e um único lugar onde faltaria envolver um `ErrorBoundary`.

**Roteamento:** 33 rotas, **todas com import estático**. Zero `React.lazy` / `Suspense`.
Quem abre a tela de login baixa junto o Google Maps, o kanban do calendário e todas as
telas de relatório.

**Camada de acesso a dados: inexistente.** `lib/api.ts` expõe apenas a instância axios
crua (32 linhas). Cada componente monta suas próprias URLs e chaves de cache:

- **256** chamadas `api.get/post/patch/put/delete` diretas dentro de componentes
- **267** `queryKey` escritos como literais, sem fonte única
- **49** repetições literais de `err?.response?.data?.detail ?? "Erro ao ..."`
- **38** usos de `any` — quase todos exatamente nesse tratamento de erro, furando o
  `strict: true` no ponto onde o formato de erro do backend importa

**Consequência prática:** invalidação de cache é frágil por construção. Acertar qual
chave invalidar depende de lembrar o formato exato escrito em outro arquivo.

---

## 4. Mapa de rotas e features

| Feature | Arquivos | Linhas | Rotas | Geração |
|---|---|---|---|---|
| `cadastros/` | 28 | 5.426 | **14** | Antiga |
| `calendario/` | 22 | 4.422 | 5 | Nova |
| `planejamento/` | 10 | 3.901 | 3 | Intermediária |
| `relatorio/` | 10 | 1.508 | 3 | Intermediária |
| `admin/` | 3 | 819 | 1 | Antiga |
| `estoque/` | 3 | 665 | 1 | Intermediária |
| `almoxarifados/` | 2 | 443 | 1 | Antiga |
| `dashboard/` | 1 | 292 | 1 | Nova |
| `auth/` | 2 | 261 | 2 | (fora do shell) |

Note a desproporção: `cadastros/` tem 42% das rotas mas apenas 27% das linhas —
são muitas telas pequenas e repetitivas (CRUD de bicos, equipamentos, safras, tipos de
atividade, tipos de contrato…). `planejamento/` é o inverso: 3 rotas e 3.901 linhas —
poucas telas, muito densas.

---

## 5. Principais fluxos do usuário

Derivados das rotas, da navegação entre telas e dos agrupamentos do menu lateral
(`Layout.tsx`: *Defensivos*, *Relatórios*, *Cadastros*, mais Início e Estoque soltos).

### Fluxo 1 — Planejar manejo *(fluxo central do produto)*
```
/planejamento  →  /planejamento/:id  →  /planejamento/:id/talhao/:talhaoId
   (lista)          (edição do plano)      (manejo do talhão)
                          ↓
              ItensManejoModal (807 linhas) → ItemManejoFormModal (414 linhas, 16 useState)
```
É o coração do produto e o código mais pesado do frontend. `PlanejamentoListPage` (620
linhas, 13 `useState`) tem modal escrito à mão e `confirm()` nativo. `PlanejamentoEditPage`
(571 linhas) tem tabela sem `overflow-x-auto`.

### Fluxo 2 — Recomendação técnica (AP)
```
/calendario/aps  →  ApFormModal (672 linhas, 16 useState)  →  ApFinalizarModal
       ↓
/calendario/aps/:calendarioId/:apId/impressao  (RecomendacaoPrintPage)
```
É o fluxo que está sendo trabalhado agora (FEAT-019). Já é geração nova: usa
`PageHeader`, `ConfirmDialog`, trata `isError` de query — é **uma das duas telas do
projeto inteiro que trata**. Serve como referência do padrão-alvo.

### Fluxo 3 — Acompanhamento diário no calendário
```
/calendario (CalendarioHome: alternador Visão geral | Por cliente)
   ├── AgendaMiniCalendario + PanoramaClientes  (indicadores)
   ├── VisaoGeralView → KanbanBoard/KanbanColuna → EventoCard
   ├── PorClienteView → TabelaView → EventoLinha
   └── EventoDetalheDrawer → "Registrar AP" → ApFormModal
/calendario/gerenciar (CalendarioListPage) · /calendario/:id (ConfigTab, PlantioTab)
```
Maior densidade de informação do produto e a tela de uso mais frequente. É onde a
paleta de 7 status trabalha mais duro — e onde qualquer regressão de escaneabilidade
custa caro.

### Fluxo 4 — Cadastros (hierarquia cliente → fazenda → talhão)
```
/clientes → /clientes/:id/fazendas → /fazendas/:id/talhoes
                                          ├── /fazendas/:id/contorno  (desenho no mapa)
                                          └── /fazendas/:id/mapa      (visualização)
+ CRUDs planos: /produtos /cultivares /safras /tipo-atividades
                /tipos-contrato-cliente /bicos /equipamentos /perfis-intervalo
```
14 rotas, toda a geração antiga. `/fazendas/:id/talhoes` é o destino mais linkado do
app (6 links apontam para ele) — é um nó de navegação real, não uma tela secundária.

### Fluxo 5 — Relatórios
```
/relatorio  (RelatorioPage — demanda de compra, 10 useState)
/relatorio/manejo → /relatorio/manejo/:planId  (manejo por talhão)
```
Composto por `ReportHeader`/`ReportMetadata`/`ReportSummaryCards`/`ReportArea`/
`ReportFooter`/`DemandByProductTable` — a única parte do frontend com componentização
por papel bem separada. Tem impressão como caso de uso real (`.no-print`).

### Fluxo 6 — Estoque e almoxarifado
```
/almoxarifados  →  AlmoxarifadoFormModal (modal à mão)
/estoque        →  EstoqueLinhaRow · AdicionarProdutoModal (modal à mão)
```
`EstoquePage` é a **única tela da geração antiga/intermediária que acerta o
`overflow-x-auto`**.

### Onde os fluxos quebram hoje
- **Todos os seis**: falha de carregamento vira lista vazia silenciosa (59 de 61 arquivos).
- **Fluxos 1, 4, 6**: confirmação destrutiva via `confirm()` nativo, suprimível.
- **Fluxo 4**: nenhuma das 14 telas persiste filtro — o usuário refaz a seleção a cada
  navegação. Nos fluxos 1, 2, 3 e 6 o filtro é persistido (`useStickyState`).
- **Fluxo 5**: `RelatorioPage` monta cabeçalho à mão e não usa `PageHeader`.

---

## 6. Inventário de UI por categoria

### 6.1 Layouts
Um único layout (`Layout.tsx`, 260 linhas). Sidebar escura (`--c-dark`, 224px / 64px
colapsada), estado persistido em `localStorage`, grupos colapsáveis também persistidos.
Rodapé com avatar de iniciais, nome, e-mail e link de Admin condicional ao perfil.

**Container de página divergente** — não há shell padronizado abaixo do `Layout`:

| Largura | Ocorrências |
|---|---|
| `p-6` sem `max-w` | **19** |
| `p-6 max-w-6xl mx-auto` | 6 |
| `p-6 max-w-4xl mx-auto` | 3 |
| `p-6 max-w-[1400px] mx-auto` | 1 |
| `p-6 max-w-7xl mx-auto` | 1 |
| `p-6 max-w-5xl mx-auto` | 1 |

Em monitor largo, 19 telas esticam o conteúdo até a borda enquanto as vizinhas param em
1.152px. É a inconsistência mais visível a olho nu ao navegar entre seções.

### 6.2 Navegação
Menu lateral com 2 itens soltos (Início, Estoque) + 3 grupos colapsáveis (Defensivos 3,
Relatórios 2, Cadastros 10) + Usuários (admin). Item ativo marcado por fundo `brand-500`
e trilho dourado (`--color-gold`) — única aplicação do acento gold no produto.

Sem breadcrumb em nenhuma tela, apesar de existirem hierarquias de 3 níveis
(`/planejamento/:id/talhao/:talhaoId`, `/clientes/:id/fazendas` → `/fazendas/:id/talhoes`).
O usuário depende do botão voltar do navegador.

Sem menu mobile: em tela estreita a sidebar consome metade do viewport.

### 6.3 Formulários
**Dois paradigmas convivendo.** `react-hook-form` + `zod` são dependências do projeto e
aparecem em **10 arquivos**; os demais formulários usam `useState` manual — 340
`useState` no projeto contra 36 `useMemo`/`useCallback`.

`FormField` (label + conteúdo + erro) existe e é usado em **2 arquivos**, apesar de seu
próprio docstring dizer que "substitui o `Field` que era redefinido em cada modal". Contra
isso, **173 `<label>`** no projeto, em **4 tratamentos tipográficos concorrentes para o
mesmo papel**:

| Classe de label | Ocorrências |
|---|---|
| `block text-sm font-medium text-gray-700 mb-1` | 57 |
| `block text-xs text-gray-500 mb-1 font-medium` | 48 |
| `block text-xs font-medium text-gray-600 mb-1` | 22 |
| `block text-[11px] text-gray-500 mb-1` | 9 |

Três tamanhos de fonte (14px, 12px, 11px) e três tons de cinza para rotular campos —
sem regra discernível que explique qual usar onde.

Erro de formulário é razoavelmente tratado: 14 arquivos mostram `mutation.isError`. Mas a
mensagem é quase sempre fixa (`"Erro ao salvar."`), descartando o `detail` que o FastAPI
já envia.

### 6.4 Tabelas
28 tabelas em 26 arquivos. **20 sem `overflow-x-auto`** — estouram a largura em tela
estreita em vez de rolar. As 8 que acertam estão em `estoque/`, `relatorio/`,
`calendario/` e `planejamento/`; **nenhuma tabela de `cadastros/` tem scroll horizontal**.

Não existe componente de tabela compartilhado: cada tela monta `<table>`/`<thead>`/`<tbody>`
com suas próprias classes. Colunas numéricas usam `.tnum` (53 ocorrências em 18 arquivos)
— este é um dos padrões com **boa** adoção.

### 6.5 Filtros
`SearchableSelect` (201 linhas, componente compartilhado sólido) usado em 12 arquivos.
22 arquivos têm input de busca com `placeholder="Buscar…"`.

`useStickyState` — persistência do filtro em `localStorage` — está em **6 arquivos**:
`ApListPage`, `CalendarioHome`, `CalendarioListPage`, `TabelaView`, `EstoquePage`,
`PlanejamentoListPage`. **Nenhuma tela de `cadastros/`** persiste filtro, embora sejam
justamente as telas onde o usuário mais entra e sai.

Não há barra de filtro padronizada: cada tela compõe sua própria combinação de
`SearchableSelect` + input + checkbox, com larguras definidas por `style={{ flex: ... }}`
inline em alguns casos (`CalendarioHome`).

### 6.6 Modais
`Modal.tsx` (80 linhas) resolve: `Esc` para fechar, trava de scroll do body, clique no
backdrop com proteção contra fechar ao soltar o mouse após selecionar texto,
`role="dialog"` + `aria-modal` + `aria-labelledby`, devolução de foco ao fechar, 5
tamanhos (`sm` a `2xl`). Falta apenas focus trap.

**18 arquivos usam. 14 modais são escritos à mão** com `fixed inset-0`, perdendo tudo
acima:

`ClientesAcessoModal` · `UsuarioFormModal` · `UsuariosPage` · `AlmoxarifadoFormModal` ·
`BicosPage` · `EquipamentosPage` · `PerfisIntervaloPage` · `TipoAtividadesPage` ·
`TiposContratoClientePage` · `EventoDetalheDrawer` · `AdicionarProdutoModal` ·
`HistoricoTab` · `PlanejamentoEstoqueModal` · `PlanejamentoListPage`

`EventoDetalheDrawer` é o caso defensável da lista — é um drawer lateral, não um modal
centrado, e `Modal` não oferece essa variante hoje.

### 6.7 Cards
Sem componente de card compartilhado. O padrão `bg-white rounded-xl shadow-sm` é repetido
inline. Cards especializados existem e são bons dentro do seu escopo: `EventoCard`
(kanban), `DistribuicaoCulturaCard`, `StatCard` e `PendenciaCard` (ambos locais ao
`DashboardPage`), `ReportSummaryCards`.

A classe `.card-in` (fade-in escalonável, com `prefers-reduced-motion` respeitado) está
definida em `index.css` e **não é usada em lugar nenhum** — código morto.

### 6.8 Dashboards
`DashboardPage` (292 linhas) é geração nova e bem construída: saudação com primeiro nome,
data por extenso, 4 `StatCard` de totais, 4 `PendenciaCard` ligados ao calendário
(reusando `STATUS_DOT_CLASS`/`STATUS_LABEL` — a mesma paleta, para o número bater com o
quadro), `PanoramaClientes` compartilhado com o calendário, planejamentos recentes e
atalhos.

Ponto de atenção de conteúdo: o atalho para `/calendario/aps` tem label **"Rec"** —
abreviação opaca ao lado de rótulos por extenso ("Planejar Manejo", "Calendário",
"Relatórios"). No menu lateral a mesma rota se chama "Recomendação".

Métricas são derivadas no cliente a partir de uma lista de até 200 planejamentos
(`totalClientes`, `totalFazendas` via `new Set`). Funciona hoje; passa a mentir quando
uma consultoria ultrapassar 200 planejamentos, sem nenhum aviso na tela.

### 6.9 Mapas
Três arquivos: `FazendaContornoPage` (311), `FazendaMapaPage` (465, 10 `useState`,
7 `useMemo`), `MapaControles` (144). Mais `TalhaoKmlImportModal`, `DividirTalhaoModal`,
`SelecionarArquivoContorno`, `TalhaoEdicaoInlinePainel`.

É a área mais recentemente trabalhada (FEAT-011 a FEAT-014) e se nota: `FazendaMapaPage`
é o arquivo com melhor uso de `useMemo` no projeto. Ambas as telas de mapa usam `<Modal>`
corretamente e não usam `confirm()` nativo.

`@react-google-maps/api` é carregado estaticamente junto com todo o bundle — quem nunca
abre um mapa paga por ele em toda visita.

### 6.10 Calendários
Maior subsistema do produto: 22 arquivos, 4.422 linhas. `CalendarioHome` é um shell com
alternador *Visão geral | Por cliente*, filtros compartilhados e hospedagem do drawer e
do modal de AP. Duas visualizações completas (`KanbanBoard`/`KanbanColuna`/`EventoCard`
e `TabelaView`/`EventoLinha`), mini-calendário de agenda, painel de panorama por cliente,
legenda de status dedicada (`StatusLegend`), e o hook `useKanban` compartilhado com o
dashboard.

É o subsistema mais maduro e o melhor candidato a servir de referência do padrão-alvo.

### 6.11 Indicadores
Cinco lugares produzem indicadores numéricos: `StatCard` e `PendenciaCard`
(`DashboardPage`), `PanoramaClientes`, `ReportSummaryCards`, `ResumoVolumes`
(`PlanejamentoEditPage`). São quatro tratamentos visuais distintos para o mesmo conceito
"número grande + rótulo pequeno", sem componente comum.

O acerto: `PendenciaCard` reusa a paleta de status oficial, garantindo que o número do
Início bata com o do Calendário — e o comentário no código diz explicitamente que essa
foi a intenção.

### 6.12 Estados de carregamento
40 ocorrências do texto `"Carregando..."`, em **12 tratamentos visuais diferentes**:

| Classe | Ocorrências |
|---|---|
| `text-gray-500 text-sm` | 14 |
| `text-sm text-gray-400 text-center py-10` | 6 |
| `text-sm text-gray-400 py-8 text-center` | 3 |
| `text-xs text-gray-400 text-center py-8` | 2 |
| `text-gray-500 text-sm py-10 text-center` | 2 |
| `p-6 text-sm text-gray-400` | 2 |
| `p-6 text-gray-500 text-sm` | 2 |
| `h-full flex items-center justify-center text-gray-400 text-sm` | 2 |
| *(mais 4 variantes de 1 ocorrência)* | 4 |

Mesmo texto, mesmo papel, 12 aparências. **Um único lugar no projeto usa skeleton /
`animate-pulse`** — em todo o resto, o usuário vê um parágrafo cinza.

### 6.13 Estados vazios
Cerca de 35 variantes de copy, sem componente comum. Vão de mensagens completas com
ícone e chamada para ação (`DashboardPage`: ícone `Leaf`, "Nenhum planejamento ainda.",
link "Criar o primeiro") até strings soltas ("Nenhum produto.", "Nenhum resultado").

Há duplicação literal de copy entre arquivos ("Nenhuma fazenda cadastrada para este
cliente." aparece 3×, "Nenhum talhão encontrado." 3×), sinal de copiar-e-colar entre
telas irmãs.

Estado vazio raramente convida à ação: o padrão do `DashboardPage` (ícone + frase +
link para criar) é a exceção, não a regra.

### 6.14 Mensagens de erro
**A maior lacuna do frontend.**

| Métrica | Valor |
|---|---|
| Arquivos usando `useQuery` | **61** |
| Arquivos tratando `isError` **de query** | **2** (`ApListPage`, `RecomendacaoPrintPage`) |
| Arquivos tratando `mutation.isError` (erro de formulário) | 14 |
| `ErrorBoundary` no projeto | **0** |

Os 14 arquivos com `mutation.isError` cobrem o *salvamento*, não o *carregamento* — são
coisas diferentes, e é o carregamento que está descoberto.

`ApListPage` mostra o padrão correto e já implementado no projeto:
```
{isError && (…)}
{!isLoading && !isError && grupos.length === 0 && (…)}
```
Três estados visualmente distintos. É o modelo a replicar; não precisa ser inventado.

Feedback positivo/negativo pontual usa `toast()` (`lib/toast.ts`) — implementação própria
de 58 linhas que manipula o DOM diretamente, fora do React. Funciona e tem
`role="status"`/`aria-live="polite"`, mas **hardcoda `#1f6f5c` e `#dc2626`** em vez de ler
os tokens: é o único ponto do produto onde a cor de marca escapa do design system. Mostra
um toast por vez (remove o anterior).

### 6.15 Responsividade
**13 de 92 arquivos** usam algum breakpoint Tailwind — 35 ocorrências no total:

| Breakpoint | Ocorrências |
|---|---|
| `sm:` | 20 |
| `lg:` | 11 |
| `md:` | 3 |
| `xl:` | 1 |
| `2xl:` | 0 |

Somado à sidebar sem menu mobile e às 20 tabelas sem scroll horizontal, a aplicação é
**desktop-only na prática**. → **Decidido (§13.1): o produto deve ser responsivo.** Isto
deixa de ser observação e passa a ser o eixo estruturante do redesign.

### 6.16 Acessibilidade
| Métrica | Valor |
|---|---|
| `title=` | **151** |
| `aria-label=` | **15** |
| `aria-*` (qualquer) | 18 |
| `role=` | 2 |

Botões só-com-ícone (editar, inativar, remover — 41 `Pencil`, 24 `Trash`) dependem de
`title` como único rótulo, que não é lido de forma confiável por leitor de tela. O
`icon-btn` (classe já pronta, alvo de toque 32×32) é usado em 14 lugares.

**14 arquivos desenham `<svg>` inline** apesar de `lucide-react` ser dependência do
projeto — todos na geração antiga, e nenhum com `aria-hidden`.

---

## 7. Componentes compartilhados

| Componente | Linhas | Adoção | Avaliação |
|---|---|---|---|
| `SearchableSelect` | 201 | 12 arquivos | ✅ Boa adoção, componente sólido |
| `Modal` | 80 | 18 arquivos | ⚠️ 14 modais à mão ignoram |
| `ProdutoSearchSelect` | 199 | novo (FEAT-019) | ✅ Especialização legítima |
| `CultivarSelect` | 108 | — | ✅ Especialização legítima |
| `Layout` | 260 | shell único | ⚠️ Sem menu mobile |
| `ConfirmDialog` | 49 | **2 arquivos** | ❌ 22 arquivos ainda em `confirm()` nativo |
| `PageHeader` | 30 | **8 arquivos** | ❌ 24 `*Page.tsx` montam cabeçalho à mão |
| `FormField` | 19 | **2 arquivos** | ❌ 173 `<label>` em 4 estilos concorrentes |
| `PrivateRoute` | — | shell único | ℹ️ Otimista por design (ver §10) |

O padrão é claro: **quanto mais recente e mais especializado, melhor a adoção**. Os três
componentes de menor adoção (`ConfirmDialog`, `PageHeader`, `FormField`) são exatamente
os mais genéricos e os que mais economizariam repetição.

Também: não existe componente `<Input>` — o padrão é `className="input"` (45 usos
corretos) sobre elemento nativo. Cerca de 10 arquivos ainda repetem a string Tailwind
equivalente à mão.

---

## 8. Duplicação estrutural

**Não há colisão de nomes.** A varredura de declarações de componente não encontrou um
único nome repetido entre arquivos — cada componente local tem nome próprio. A duplicação
do projeto é de **padrão**, não de identificador, o que a torna invisível para ferramentas
e para o compilador:

| O que se repete | Escala |
|---|---|
| Estrutura de modal (`fixed inset-0` + painel + header + botão fechar) | 14× |
| Tratamento de erro `err?.response?.data?.detail ?? "..."` | 49× |
| Estilo de label de formulário | 136× em 4 variantes |
| Tratamento visual de "Carregando..." | 40× em 12 variantes |
| Chamada HTTP direta em componente | 256× |
| `queryKey` literal | 267× |
| Shell de container de página | 31× em 6 larguras |
| Copy de estado vazio | ~35 variantes, algumas literalmente idênticas |

---

## 9. Inconsistências — a matriz das três gerações

Varredura de conformidade em todas as 30 `*Page.tsx`. `n/a` = a tela não tem esse
elemento.

| Página | `PageHeader` | Modal | Confirmação | Ícones | Scroll tabela | `aria-label` |
|---|---|---|---|---|---|---|
| `admin/UsuariosPage` | ✗ | à mão | nativo | ok | ✗ | ✗ |
| `almoxarifados/AlmoxarifadosPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/BicosPage` | ✗ | à mão | nativo | svg | ✗ | ✗ |
| `cadastros/ClienteFazendasPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/ClientesPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/CultivaresPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/EquipamentosPage` | ✗ | à mão | nativo | svg | ✗ | ✗ |
| `cadastros/FazendasPage` | ✗ | n/a | nativo | ok | ✗ | ✗ |
| `cadastros/FazendaTalhoesPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/PerfisIntervaloPage` | ✗ | à mão | nativo | svg | ✗ | ✗ |
| `cadastros/ProdutosPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/SafrasPage` | ✗ | n/a | nativo | svg | ✗ | ✗ |
| `cadastros/TipoAtividadesPage` | ✗ | à mão | nativo | svg | ✗ | ✗ |
| `cadastros/TiposContratoClientePage` | ✗ | à mão | nativo | svg | ✗ | ✗ |
| `cadastros/FazendaContornoPage` | ✗ | n/a | ok | ok | n/a | ✗ |
| `cadastros/FazendaMapaPage` | ✗ | n/a | ok | ok | n/a | ✗ |
| `estoque/EstoquePage` | ✗ | n/a | ok | ok | **ok** | ✗ |
| `planejamento/PlanejamentoListPage` | **ok** | à mão | nativo | ok | n/a | **ok** |
| `planejamento/PlanejamentoEditPage` | ✗ | n/a | ok | ok | ✗ | **ok** |
| `planejamento/TalhaoManejoPage` | ✗ | n/a | ok | ok | ✗ | ✗ |
| `relatorio/RelatorioPage` | ✗ | n/a | ok | ok | n/a | ✗ |
| `relatorio/RelatorioManejoPage` | ✗ | n/a | ok | ok | n/a | ✗ |
| `relatorio/RelatorioManejoSelecaoPage` | **ok** | n/a | ok | ok | n/a | ✗ |
| `calendario/ApListPage` | **ok** | n/a | ok | ok | n/a | **ok** |
| `calendario/CalendarioListPage` | **ok** | n/a | ok | ok | n/a | ✗ |
| `calendario/CalendarioPage` | **ok** | n/a | ok | ok | n/a | ✗ |
| `calendario/RecomendacaoPrintPage` | ✗ | n/a | ok | ok | ✗ | ✗ |
| `dashboard/DashboardPage` | **ok** | n/a | ok | ok | n/a | ✗ |
| `auth/LoginPage` | ✗ | n/a | ok | ok | n/a | ✗ |
| `auth/CadastroConsultoriaPage` | ✗ | n/a | ok | ok | n/a | ✗ |

A estratificação salta aos olhos: as 14 primeiras linhas são idênticas em todas as
colunas. Não é decadência gradual — são dois momentos distintos de escrita do código.

### Inconsistência de paleta a registrar
`lib/tipoCores.ts` (chip de tipo de produto) e `lib/statusCores.ts` (status de evento)
convivem na mesma tela em vários lugares, com tons colidentes: `inseticida` é vermelho,
igual a `aplicacao_atrasada`; `biológico` é esmeralda, igual a `completa`; `adjuvante` é
`sky`, igual a `aplicacao_pendente`. Duas escalas semânticas diferentes usando as mesmas
cores no mesmo campo de visão. Não há evidência no código de que isso já tenha causado
confusão real. → **Decidido (§13.2): não incomoda; as duas paletas ficam como estão.**
Fica registrado como observação histórica, fora do escopo do redesign.

---

## 10. Problemas arquiteturais

1. **Sem `ErrorBoundary`** — exceção de render = tela branca, em qualquer rota.
2. **Sem code-splitting** — 33 rotas em import estático; Google Maps e todo o calendário
   entram no bundle de quem só abre o login. Relevante para uso com conexão ruim.
3. **Sem camada de acesso a dados** — 256 chamadas soltas, 267 `queryKey` literais;
   invalidação de cache frágil por construção.
4. **Dois paradigmas de formulário** — `react-hook-form`+`zod` em 10 arquivos, `useState`
   manual no resto (340 `useState` no total). É a inconsistência de fundo dos formulários,
   abaixo da inconsistência visual dos labels.
5. **`toast` fora do React** — manipula o DOM diretamente e hardcoda cores fora do sistema
   de tokens.
6. **`PrivateRoute` otimista** — decide por flag em `localStorage` (`apv2_authed`); o
   backend é quem valida de fato, então **não é falha de segurança** e o comentário em
   `lib/auth.ts` deixa a intenção explícita. O efeito é de UX: com sessão expirada a tela
   monta inteira, dispara as queries, e só então o usuário é expulso — pisca conteúdo
   antes de sumir. Além disso `api.ts` usa `window.location.href`, que força reload
   completo e descarta o cache do React Query.
7. **Sem lint, sem formatter, sem teste no frontend** — `package.json` tem apenas `dev`,
   `build`, `preview`. O único gate é `tsc && vite build`. O backend tem 27 arquivos de
   teste; o frontend, zero. Para um redesign amplo, isso significa **nenhuma rede de
   segurança automatizada** — a verificação será visual.
8. **`types/index.ts` monolítico** — 956 linhas, maior arquivo do frontend, escrito à mão
   para espelhar os schemas Pydantic. Divergência com o backend não gera erro de build,
   só bug em runtime.

---

## 11. Classificação das telas

Critério: **divergência do sistema × centralidade no fluxo diário × raio de impacto**.
Uma tela muito divergente mas pouco usada não é prioridade alta; uma tela já moderna mas
central também não.

### 🔴 Prioridade alta

| Alvo | Rotas | Por quê |
|---|---|---|
| **Transversal: estados de carregamento / erro / vazio** | *todas as 33* | 59 de 61 arquivos podem mostrar dado ausente como lista vazia. Maior risco de decisão errada do usuário. Padrão-alvo já existe em `ApListPage`. |
| **`planejamento/`** — `PlanejamentoListPage`, `PlanejamentoEditPage`, `ItensManejoModal`, `ItemManejoFormModal`, `TalhaoManejoPage` | 3 | Coração do produto. Componentes mais pesados (807/672/620/571/414 linhas, até 16 `useState`). Modal à mão + `confirm()` nativo na tela de entrada do fluxo. |
| **Bloco `cadastros/`** — 14 CRUDs + hierarquia cliente→fazenda→talhão | 14 | 42% das rotas, geração antiga sem uma única exceção. Maior ganho por unidade de esforço: são telas pequenas e repetitivas, um padrão único resolve todas. |
| **`calendario/CalendarioHome`** + `VisaoGeralView` / `PorClienteView` / `TabelaView` / `EventoDetalheDrawer` | 1 (+ sub-views) | Tela de uso mais frequente e maior densidade de informação. Já é geração nova — aqui o redesign é de *refinamento e densidade*, não de correção. |

### 🟡 Prioridade média

| Alvo | Rotas | Por quê |
|---|---|---|
| `estoque/` + `almoxarifados/` | 2 | Modais à mão, mas escopo pequeno e contido. `EstoquePage` já acerta o scroll de tabela. |
| `relatorio/` | 3 | Melhor componentização do projeto, mas nenhuma das 3 telas principais usa `PageHeader`. Uso periódico, não diário. |
| `dashboard/DashboardPage` | 1 | Alta visibilidade (porta de entrada de toda sessão) mas já é geração nova. Ajustes pontuais: label "Rec", limite de 200 planejamentos nas métricas. |
| `admin/UsuariosPage` | 1 | Geração antiga completa, mas acessada só por admin e com baixa frequência. |

### 🟢 Prioridade baixa

| Alvo | Rotas | Por quê |
|---|---|---|
| `auth/LoginPage`, `auth/CadastroConsultoriaPage` | 2 | Fora do shell, telas simples, já tratam erro corretamente. Uso pontual. |
| `calendario/RecomendacaoPrintPage` | 1 | Saída para impressão — tem restrições próprias (`.no-print`, layout de papel) que não se beneficiam do sistema de tela. Já trata `isError`. |
| `cadastros/FazendaContornoPage`, `FazendaMapaPage` | 2 | Retrabalhadas recentemente (FEAT-011 a 014). Já usam `<Modal>`, lucide, sem `confirm()` nativo. Melhor código de `useMemo` do projeto. |

---

## 12. Áreas mais críticas para o redesign

Em ordem de retorno sobre esforço. **Requisito transversal às quatro frentes
(§13.1): tudo é projetado responsivo** — nenhuma delas é considerada pronta sem
comportamento definido em tela estreita.

**1. Padronizar os três estados de consulta (carregando / erro / vazio).**
Transversal às 33 rotas, resolve o risco nº 1 do produto, e o padrão-alvo já está
implementado e em produção em `ApListPage`. Combina com um `ErrorBoundary` no `Layout`.
É o item de maior retorno percebido pelo usuário final — e o único desta lista que é
mais correção funcional que redesign.

**2. Padrão único de CRUD para o bloco `cadastros/` (§13.3), mobile-first.**
`PageHeader` + `Modal` + `FormField` + `ConfirmDialog` + shell de página com largura única
+ lista responsiva. Os cinco componentes já existem; falta adoção. Aplicado às 14 rotas,
elimina de uma vez os 14 modais à mão, os 22 `confirm()` nativos, os 14 `<svg>` inline e
as 20 tabelas sem scroll — e cobre 42% das rotas do produto em responsividade.

Atenção: **`overflow-x-auto` não é a resposta completa.** Tabela larga em celular é
sofrível mesmo rolando; a lista precisa de representação alternativa em tela estreita
(linha vira card). E o padrão precisa de um ponto de extensão para `ProdutosPage` (abas)
e `PerfisIntervaloPage` (modal aninhado) — ver ressalva em §13.3.

É a peça central do redesign e onde o "antes e depois" será mais visível.

**3. Unificar a tipografia de formulário e o vocabulário de microcopy.**
As 4 escalas concorrentes de label são a inconsistência mais difusa do produto — aparecem
em toda tela com formulário. Junto: padronizar copy de estado vazio e fazer o toast usar
os tokens em vez de hex hardcoded.

**4. Refinar a densidade de informação do `calendario/` e do `planejamento/`.**
As duas áreas mais densas e mais usadas. Aqui o trabalho é genuinamente de design — não
de conformidade — e deve ser feito **depois** que o vocabulário visual estiver estável,
para não redesenhar duas vezes.

Fora deste eixo, mas habilitadores: `React.lazy` nas rotas (baixo risco, ganho imediato
de carga, especialmente para os mapas) e ESLint + Prettier (o frontend não tem nenhuma
rede de segurança automatizada, e um redesign amplo é exatamente quando ela faz falta).

---

## 13. Decisões tomadas

As três perguntas que este documento levantou foram respondidas pelo dono do produto em
**28/08/2026**. Ficam registradas aqui porque delimitam o escopo de todos os documentos
seguintes da série.

### ✅ Decisão 1 — O produto **deve ser responsivo**

Confirmado o uso em campo. Isso **promove responsividade de item de checklist a eixo
estruturante do redesign**: nenhuma tela nova ou redesenhada é considerada pronta sem
comportamento definido em tela estreita.

Consequências diretas sobre os achados deste documento:
- §6.15 (13 de 92 arquivos com breakpoint) deixa de ser observação e vira trabalho obrigatório.
- §6.4 — as **20 tabelas sem `overflow-x-auto`** não se resolvem só com scroll horizontal:
  tabela larga em celular é sofrível mesmo rolando. O padrão-alvo precisa de uma
  **representação alternativa em tela estreita** (linha vira card), decidida no `02-`.
- §6.1 — `Layout.tsx` precisa de menu mobile; a sidebar fixa de 224px consome metade do
  viewport hoje. É a única peça de navegação do produto, então é mudança de raio total.
- §6.2 — sem breadcrumb, e o botão voltar do navegador é ainda mais crítico no celular.
  Vale reavaliar em conjunto com o menu mobile.

### ❌ Decisão 2 — A colisão de paletas **não incomoda: fica como está**

Confirmado que a ambiguidade entre `lib/tipoCores.ts` e `lib/statusCores.ts` (vermelho =
*aplicação atrasada* e também *inseticida*) **não gera confusão real** — o contexto
desambigua na prática.

**Fora do escopo do redesign.** As duas paletas permanecem como estão. O achado de §9 fica
registrado como observação histórica, não como dívida a pagar: mexer nelas custaria tocar
duas escalas em produção para resolver um problema que não existe para o usuário.

### ✅ Decisão 3 — Os 14 CRUDs de `cadastros/` **convergem para um padrão único**

Confirmado. Combinado com a Decisão 1, isso torna o bloco `cadastros/` **a peça central do
redesign**, não um item de limpeza: projetar um padrão único já mobile-first resolve
responsividade em **14 das 33 rotas (42% do produto)** de uma vez.

**Ressalva a carregar para o `02-`:** as 14 telas não são idênticas. Duas têm
particularidades que vão brigar com um padrão rígido:
- `ProdutosPage` — abas globais/cliente (FEAT-001).
- `PerfisIntervaloPage` — modal de itens aninhado (`PerfilItensModal`, 352 linhas).

O padrão precisa prever **pelo menos um ponto de extensão** (slot de conteúdo acima da
lista, e suporte a ação de linha que abre um segundo nível), senão essas duas serão
tratadas como exceção e a convergência perde metade do valor.

### Fora de escopo, por decisão

| Item | Origem | Motivo |
|---|---|---|
| Unificar paleta de status e paleta de tipo de produto | §9 | Decisão 2 — sem incômodo real |

---

## Anexo — como reproduzir os números

Todos os dados deste documento vêm de varreduras sobre `Agro-Plataform/frontend/src/`.
Exemplos dos comandos usados:

```bash
# rotas
grep -c "<Route path" src/App.tsx

# arquivos com useQuery vs. que tratam isError de query
grep -rl "useQuery" --include="*.tsx" src | wc -l
grep -rn "isError" --include="*.tsx" src   # separar mutation.isError de query

# modais escritos à mão
for f in $(grep -rl "fixed inset-0" --include="*.tsx" src); do
  grep -q "components/Modal" "$f" || echo "$f"; done

# tabelas sem scroll horizontal
for f in $(grep -rl "<table" --include="*.tsx" src); do
  grep -q "overflow-x-auto" "$f" || echo "$f"; done

# variantes de classe de label
grep -rhoE '<label className="[^"]*"' --include="*.tsx" src | sort | uniq -c | sort -rn

# responsividade
grep -rl "sm:\|md:\|lg:\|xl:" --include="*.tsx" src | wc -l
```
