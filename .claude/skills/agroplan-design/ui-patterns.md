# Padrões de UI do AgroPlan

Catálogo oficial. Cada padrão é uma **composição de componentes** com regra de uso.
Componentes e tokens em [`design-system.md`](design-system.md); a doutrina que governa as
escolhas em [`ux-principles.md`](ux-principles.md).

> **Estado:** padrões aprovados, majoritariamente **não implementados**. Ao alterar código
> existente, confirmar tokens na Parte D de [`design-system.md`](design-system.md).

**Índice** — Estrutura: [App Shell](#1-app-shell) · [Sidebar](#2-sidebar) ·
[Header](#3-header) · [Breadcrumb](#4-breadcrumb) · [Navegação](#5-navegação) ·
Contexto: [Cliente](#6-seleção-de-cliente) · [Fazenda](#7-seleção-de-fazenda) ·
[Safra](#8-seleção-de-safra) · [Filtros](#9-filtros) ·
Dados: [CRUD](#10-crud) · [Formulários](#11-formulários) · [Tabelas](#12-tabelas) ·
Sobreposição: [Modais](#13-modais) · [Drawers](#14-drawers) ·
Domínio: [Dashboards](#15-dashboards) · [Mapas](#16-mapas) · [Calendário](#17-calendário) ·
[Planejamento](#18-planejamento) ·
Feedback: [Alertas](#19-alertas) · [Confirmações](#20-confirmações) ·
[Loading](#21-loading) · [Empty](#22-empty-states) · [Error](#23-error-states) ·
Estrutura de lista: [Lista agrupada](#24-lista-agrupada-acordeão)

---

## 1. App Shell

**Objetivo** — Dar uma moldura única e previsível a toda tela autenticada, para que a
navegação nunca seja objeto de aprendizado.

**Estrutura**
```
┌──────────────┬──────────────────────────────────────┐
│ Sidebar 240  │ ContextBar          56px             │
│ navy-900     ├──────────────────────────────────────┤
│              │ PageHeader          72px             │
│              ├──────────────────────────────────────┤
│              │ Conteúdo · máx 1360px · 12col/24gap  │
└──────────────┴──────────────────────────────────────┘
```

**Comportamento** — Sidebar e contexto persistem entre rotas (só o conteúdo troca) ·
`ErrorBoundary` envolve o conteúdo, nunca a casca — erro de tela não derruba a navegação ·
Em `base`, sidebar vira navegação inferior de 64px e o contexto empilha em 2 linhas.

**Hierarquia** — Contexto › título › ação primária › conteúdo. O cromo escuro fica em
segundo plano por baixo contraste relativo, não por tamanho.

**Componentes** — `Sidebar` · `ContextBar` · `PageHeader` · `ErrorBoundary`

**Usar** em toda rota autenticada. **Evitar** em `/login`, `/cadastro` e
`RecomendacaoPrintPage` — fora do shell por natureza.

---

## 2. Sidebar

**Objetivo** — Navegação primária, agrupada por **fase de trabalho**, separando o que se
usa todo dia do que se configura uma vez.

**Estrutura** — Marca + colapso · itens soltos (Início, Estoque) · grupos com cabeçalho
`font-condensed` versalete · **rodapé isolado: Configurações + conta**.

**Comportamento** — Colapso 240↔64px persistido; grupos colapsáveis persistidos · Item
ativo: fundo `navy-800`, texto branco, **trilho `gold-500` de 3px** — único dourado da
navegação · Colapsada mostra só ícone, com `Tooltip`.

**Hierarquia** — Itens soltos › grupos de trabalho › configuração (rodapé, menor contraste).

**Componentes** — `Sidebar` · `Tooltip` · `lucide-react`

**Usar** em ≥1024px. **Evitar** empurrar configuração de baixa frequência (bicos,
equipamentos, tipos de contrato) para o mesmo nível de Clientes e Produtos; **evitar**
transformá-la em gaveta lateral no celular — vira navegação inferior.

---

## 3. Header

**Objetivo** — Identificar a tela e hospedar sua ação primária.

**Estrutura** — `[breadcrumb opcional]` · ícone 20 + `<h1>` `text-lg semibold` ·
subtítulo `text-sm ink-muted` · ações à direita.

**Comportamento** — Um `<h1>` por rota, garantido pelo componente · Máximo **1** botão
`primary` ou `accent`; demais em `secondary`/`ghost` · Não é fixo na rolagem (o cabeçalho
da tabela é).

**Hierarquia** — Título domina; ação primária é o segundo ponto de fixação.

**Componentes** — `PageHeader` · `Breadcrumb` · `Button`

**Usar** em toda tela de conteúdo. **Evitar** repetir no cabeçalho o contexto que já está
no `ContextBar`, e **evitar** empilhar mais de uma ação de alta consequência.

---

## 4. Breadcrumb

**Objetivo** — Mostrar a posição do **dado** dentro da hierarquia e permitir subir de
nível.

**Estrutura** — `Cliente › Fazenda › Talhão`, separador `ChevronRight` 14px em `slate-300`,
último item em `ink` sem link.

**Comportamento** — Todo nível anterior é navegável · Em `base`, colapsa para `‹ Voltar`
apontando ao pai · Nunca substitui o botão voltar do navegador, complementa.

**Hierarquia** — Subordinado ao título; vive acima dele em `text-sm`.

**Componentes** — `Breadcrumb`

**Usar** quando a rota tem ≥2 níveis de profundidade (`/planejamento/:id/talhao/:talhaoId`,
`/clientes/:id/fazendas`). **Evitar** em tela de nível único, e **evitar** confundir com
`ContextBar`: breadcrumb mostra *onde este dado está*; contexto mostra *o recorte ativo*.

---

## 5. Navegação

**Objetivo** — Levar o usuário ao trabalho em no máximo dois cliques.

**Estrutura** — Sidebar (primária) · ContextBar (recorte) · Breadcrumb (profundidade) ·
KPI e linha de tabela como atalhos diretos ao item.

**Comportamento** — Todo `KPI` leva à lista **já filtrada** · Linha de tabela é navegável
inteira quando há destino · Estado de aba e filtro na URL quando a visão for compartilhável;
caso contrário em `localStorage`.

**Hierarquia** — Do geral (panorama) ao específico (talhão), sempre com caminho de volta.

**Componentes** — `Sidebar` · `ContextBar` · `Breadcrumb` · `KPI` · `Tabs`

**Usar** o princípio: nenhuma informação é beco sem saída. **Evitar** navegação que exige
refazer o recorte ao voltar — o contexto persiste.

---

## 6. Seleção de cliente

**Objetivo** — Definir de quem é o trabalho. É o recorte de maior consequência do produto.

**Estrutura** — `Select searchable` no `ContextBar`, primeira posição, com `allLabel`
`"Todos os clientes"`.

**Comportamento** — Persiste entre telas e sessões · `"Todos"` é **valor legítimo**, não
placeholder — o panorama da consultoria depende dele · Trocar cliente **não reseta** safra;
reseta fazenda se ela não pertencer ao novo cliente · Busca liga sozinha acima de 8 opções.

**Hierarquia** — Primeiro seletor do contexto, à esquerda.

**Componentes** — `ContextBar` · `Select`

**Usar** em toda tela cujo dado pertence a um cliente. **Evitar** duplicar como filtro
dentro da tela, e **evitar** tratar como filtro obrigatório: sem cliente escolhido a tela
mostra o portfólio inteiro, que é justamente a pergunta central do usuário.

---

## 7. Seleção de fazenda

**Objetivo** — Estreitar o recorte dentro do cliente.

**Estrutura** — `Select searchable` no `ContextBar`, segunda posição, `allLabel`
`"Todas as fazendas"`.

**Comportamento** — Dependente do cliente: com cliente `"Todos"`, lista todas as fazendas
da consultoria · Trocar cliente revalida a fazenda · Desabilitada apenas se o cliente
escolhido não tiver fazendas — com `hint` explicando, nunca desabilitada em silêncio.

**Hierarquia** — Segundo seletor, subordinado a cliente.

**Componentes** — `ContextBar` · `Select`

**Usar** em telas de dado geográfico ou operacional. **Evitar** exigir fazenda para exibir
qualquer coisa; **evitar** cascata rígida que zera tudo a cada troca.

---

## 8. Seleção de safra

**Objetivo** — Recortar o ciclo produtivo. Quase todo dado do produto é datado por safra.

**Estrutura** — `Select` no `ContextBar`, terceira posição. Aceita **múltipla seleção** —
safras se sobrepõem no calendário real.

**Comportamento** — Padrão é a safra vigente, não `"Todas"` · Persiste entre telas · Quando
mais de uma está ativa, o rótulo mostra `"2025/26 e 2026/27"` — nunca `"2 selecionadas"` ·
Telas sem recorte de safra (Clientes, Produtos, Configurações) **ocultam o seletor**.

**Hierarquia** — Terceiro seletor. É o que menos muda no dia a dia e mais muda o conteúdo.

**Componentes** — `ContextBar` · `Select` (múltiplo)

**Usar** em planejamento, calendário, recomendações, estoque, relatórios. **Evitar** em
cadastros atemporais, e **evitar** ocultar qual safra está ativa — o vazio "não há nada"
quase sempre é "não há nada *nesta safra*".

---

## 9. Filtros

**Objetivo** — Recortar as linhas da lista por atributo.

**Estrutura** — `FilterBar` acima da tabela: busca · filtros compostos da esquerda para a
direita · `DensityToggle` · ações (`Exportar`, `Novo`).

**Comportamento** — Filtros leem como frase · Acima de 4, excedentes vão para
`"Mais filtros"` · Chips de filtro ativo com `×` e `"Limpar tudo"` · Estado persistido por
tela · Busca com `debounce` 300ms, no servidor acima de 500 linhas · Filtro **nunca**
recebe cliente/fazenda/safra.

**Hierarquia** — Subordinada à tabela; nunca compete com o cabeçalho.

**Componentes** — `FilterBar` · `Select` · `Input` · `Checkbox` · `DensityToggle` · `Badge`

**Usar** em qualquer lista com mais de ~20 linhas. **Evitar** esconder o filtro atrás de
disclosure em tela de triagem (calendário, recomendações) — ali filtrar é o modo de
trabalho, não exceção.

---

## 10. CRUD

**Objetivo** — Padrão único para as 14 telas de cadastro, que hoje são 14 implementações da
mesma coisa.

**Estrutura**
```
PageHeader        [ícone] Bicos              [ Novo bico ]
FilterBar         [buscar…]  □ Mostrar inativos  [Compacto|Completo]
Table             NOME                            AÇÕES
                  Cone vazio TX-8      Editar ✎  Inativar ⊘
```

**Comportamento** — Edição por **expansão inline** na linha; criação em painel no topo ·
Ciclo é criar → editar → **inativar/reativar**; não há exclusão física · `Novo X` é
`primary` — nenhum CRUD usa `accent` · Linha inativa em `ink-subtle` + `Badge "Inativo"`,
**nunca `opacity-50`**.

**Hierarquia** — Lista domina; formulário é subordinado e contextual.

**Componentes** — `PageHeader` · `FilterBar` · `Table` · `FormField` · `Input` · `Button` ·
`ConfirmDialog` · `QueryState` · `EmptyState`

**Pontos de extensão** (só três): `aboveList` (abas de `ProdutosPage`) · `rowExpansion`
(itens aninhados de `PerfisIntervaloPage`) · `extraActions`.

**Usar** em entidade simples com lista + formulário curto. **Evitar** quando a entidade
exigir um quarto slot — aí **não é um CRUD**, e deve sair do gabarito em vez de deformá-lo.

---

## 11. Formulários

**Objetivo** — Coletar dado técnico com precisão, explicando o que se pede.

**Estrutura** — `FormSection` (título `md` + **linha de explicação** `sm`) › `FormField`
(rótulo · controle · hint · erro) › barra de ação **fixa no rodapé**.

**Comportamento** — Complexo **nunca em modal**: página seccionada ou expansão inline ·
Marca o **opcional**, não o obrigatório · Erro no campo, com o `detail` do backend, nunca
texto fixo · Campo calculado é **somente-leitura com `hint` da fórmula** · Verbos distintos:
`Emitir recomendação` (`accent`) · `Salvar rascunho` (`secondary`) · `Cancelar` (`ghost`) ·
2 colunas em ≥1024px, 1 abaixo.

**Hierarquia** — Seção › campo. É a estrutura que substitui hierarquia por tamanho de
fonte, causa das 4 escalas de label atuais.

**Componentes** — `FormSection` · `FormField` · `Input` · `Select` · `Checkbox` · `Radio` ·
`Switch` · `Button` · `Alert`

**Usar** para qualquer entrada de dado. **Evitar** modal para mais de ~5 campos; **evitar**
input que finge ser cálculo; **evitar** desabilitar campo sem explicar o motivo.

---

## 12. Tabelas

**Objetivo** — O núcleo do produto. Permitir escanear muitas linhas e achar o que exige
ação.

**Estrutura** — Cabeçalho fixo `font-condensed` versalete · linha com **trilho de status**
3px à esquerda · divisor `rule`, **sem zebra** · numérica em mono à direita · ação
texto+ícone à direita.

**Comportamento** — Cada coluna declara `priority` (`always` ≤3 · `wide` · `detail`) ·
Ordenação e paginação **no servidor** · Linha inteira navegável quando há destino · Seleção
múltipla mantém escolha entre páginas · Em `base`: triagem mantém tabela só com `always`;
cadastro vira card empilhado.

**Hierarquia** — Identificador (1ª coluna) › status › data › resto.

**Componentes** — `Table` · `Pagination` · `Badge` · `StatusRail` · `QueryState` ·
`EmptyState` · `Button`

**Usar** sempre que houver ≥2 registros comparáveis. **Evitar** `overflow-x-auto` isolado
como resposta ao mobile · **evitar** `opacity-50` em linha inativa (destrói o contraste) ·
**evitar** mais de 3 colunas `always`.

---

## 13. Modais

**Objetivo** — Interromper para uma decisão curta.

**Estrutura** — Título · corpo · rodapé com ação primária à direita.

**Comportamento** — `Esc`, clique fora e `×` fecham · Trava scroll do body · **Focus trap**
+ devolução de foco · `role="dialog"` `aria-modal` `aria-labelledby` · Em `base`, tela
cheia · Nunca abre outro modal.

**Hierarquia** — Domina totalmente enquanto aberto.

**Componentes** — `Modal` · `Button` · `ConfirmDialog`

**Usar** para confirmação, escolha curta, detalhe pontual. **Evitar** para formulário
complexo — nenhuma referência de mercado faz isso, e é a origem dos 14 modais à mão do
produto atual. **Evitar** modal sobre modal.

---

## 14. Drawers

**Objetivo** — Mostrar o detalhe de um item **sem perder a lista**.

**Estrutura** — Painel de 420/560px à direita, com título, corpo rolável e rodapé de ações.

**Comportamento** — Lista permanece visível e navegável atrás · Trocar de item mantém o
drawer aberto e troca o conteúdo · `Esc` fecha · Em `base`, sobe de baixo ocupando 90vh ·
Mesma semântica de diálogo do modal.

**Hierarquia** — Coexiste com a lista; não domina.

**Componentes** — `Drawer` · `Badge` · `Button` · `QueryState`

**Usar** para detalhe de evento no calendário, de talhão, de aplicação — quando o usuário
vai inspecionar vários em sequência. **Evitar** para edição longa (vira página) e para
confirmação (vira modal).

---

## 15. Dashboards

**Objetivo** — Responder *"quais dos meus clientes precisam de mim esta semana?"* em uma
tela.

**Estrutura** — (1) Pendências: faixa de `KPI` por status em `STATUS_RESUMO_ORDEM` ·
(2) Panorama por cliente · (3) Atividade recente, máx. 6 itens.

**Comportamento** — **Todo KPI leva à lista já filtrada** · Métrica vem agregada do
servidor, **nunca derivada de lista paginada** · Carregando mostra `—`, nunca `0`.

**Hierarquia** — O que exige ação domina. Totais informativos são subordinados ou não
existem.

**Componentes** — `KPI` · `Card` · `Table` (compacta) · `QueryState` · `EmptyState`

**Usar** como porta de entrada. **Evitar** os clichês: 4 KPIs de contagem sem ação · cartão
de atalhos duplicando a sidebar · `"Olá, {nome}"` na posição mais valiosa · gráfico sem
pergunta associada.

---

## 16. Mapas

**Objetivo** — Localizar e **triar** espacialmente.

**Estrutura** — Base satélite dessaturada · contorno de talhão 2px `navy-800` (selecionado
`gold-500`) · **preenchimento pela cor de trilho do status a 25%** · rótulo
`font-condensed` com halo · legenda sempre visível · escala e norte.

**Comportamento** — Alterna com a lista pelo **mesmo filtro e contexto** · Clique no talhão
abre `Drawer` · Controles como `IconButton` de **48px** — é a tela mais provável de uso em
campo · Scrim de legibilidade é o **único gradiente permitido** no sistema.

**Hierarquia** — Geometria › status › rótulo.

**Componentes** — `Drawer` · `IconButton` · `Badge` · `EmptyState` · `TalhaoGlyph`

**Usar** para contorno, visualização e triagem espacial. **Evitar** mapa como navegação
principal (o trabalho do consultor é lista e formulário) e **evitar** padrões que dependem
de dado que o produto não coleta — mapa de calor de infestação exige monitoramento
georreferenciado, que está fora de escopo.

---

## 17. Calendário

**Objetivo** — Acompanhar o que está previsto, pendente e atrasado ao longo do tempo.

**Estrutura** — Três visões sobre os mesmos dados: **Kanban** (colunas por fase) ·
**Tabela** · **Agenda** (grade talhão × dias).

**Comportamento** — `Tabs segmented` alterna as visões, mantendo contexto e filtro ·
`DensityToggle` nas três, padrão Compacto · `Drawer` abre o detalhe · Legenda de status
sempre visível, **nunca em tooltip** · Kanban mantém a redução deliberada a 4 cores no
quadro — a distinção recomendação/aplicação já vem da coluna.

**Hierarquia** — Status › data › talhão › produto.

**Componentes** — `Tabs` · `Table` · `Card` · `Drawer` · `DensityToggle` · `FilterBar` ·
`Badge` · `KPI`

**Usar** como tela de acompanhamento diário. **Evitar** adicionar uma quarta visão
(as três já cobrem as perguntas) e **evitar** simplificar a paleta de status em nome de
limpeza — é regressão funcional.

---

## 18. Planejamento

**Objetivo** — Construir o manejo por safra, do plano ao item aplicado no talhão.

**Estrutura** — `Planejamento › Talhão › Item de manejo`, em páginas seccionadas com
`FormSection`; item de manejo por expansão inline.

**Comportamento** — **Nunca em modal** · Seleção de talhões usa `TalhaoGlyph` · Dose, área
e volume em `Input numeric` com unidade em `suffix` · Cálculo derivado é **somente-leitura
com `hint`** · Produto vem do catálogo Agrofit via `Select searchable`, com nº de registro
em mono como rastreabilidade regulatória · Ação: `Emitir recomendação` (`accent`) ·
`Salvar rascunho` · `Cancelar`.

**Hierarquia** — Talhão › atividade › produto › dose.

**Componentes** — `FormSection` · `FormField` · `Input` · `Select` · `Table` ·
`TalhaoGlyph` · `Button` · `ConfirmDialog`

**Usar** em todo o fluxo de manejo. **Evitar** alterar comportamento de cálculo em trabalho
de redesign — é regra de negócio; se parecer errado, reportar.

---

## 19. Alertas

**Objetivo** — Comunicar condição persistente que afeta o que está na tela.

**Estrutura** — Bloco no fluxo, borda esquerda 3px, ícone, título, corpo opcional, ação
opcional.

**Comportamento** — Persistente até resolvido ou dispensado (≠ Toast) · Fica **acima do
conteúdo afetado**, não no topo da página · `danger` e `warning` não são dispensáveis se
bloqueiam o trabalho.

**Hierarquia** — Acima do conteúdo que qualifica; abaixo do cabeçalho.

**Componentes** — `Alert` · `Button`

**Usar** para trial expirando, catálogo desatualizado, safra sem planejamento, dado
parcial. **Evitar** para sucesso de ação (é Toast) e **evitar** empilhar mais de um por
tela — dois alertas simultâneos anulam-se.

---

## 20. Confirmações

**Objetivo** — Impedir ação destrutiva acidental **explicando a consequência**.

**Estrutura** — `ConfirmDialog`: título com a ação e o alvo · corpo com a **consequência** ·
`Cancelar` (`ghost`) + ação (`danger`, sempre `lg`).

**Comportamento** — Nunca `confirm()` nativo — pode ser suprimido pelo navegador, e aí a
ação não acontece sem feedback · Foco inicial em `Cancelar` · Botão de ação mostra `pending`
· Toast confirma o resultado depois.

> **Inativar o talhão T-14?**
> Ele deixa de receber novas recomendações e continua nos relatórios das safras anteriores.

**Hierarquia** — Consequência é mais importante que a pergunta.

**Componentes** — `ConfirmDialog` · `Modal` · `Button` · `Toast`

**Usar** em inativar, remover, finalizar, descartar rascunho. **Evitar** para ação
reversível de baixo custo — confirmação em excesso treina o usuário a confirmar sem ler.

---

## 21. Loading

**Objetivo** — Comunicar espera sem esconder a estrutura do que vem.

**Estrutura** — `Skeleton` com a **forma do conteúdo**: linhas de tabela, cards, KPIs.

**Comportamento** — Skeleton é o padrão · Spinner **só dentro de botão** em ação ·
Não existe texto "Carregando..." solto · Botão em `loading` mantém a largura e troca o
rótulo · Pulso desligado em `prefers-reduced-motion` · Revalidação em segundo plano
**não** mostra skeleton — mantém o dado antigo visível.

**Hierarquia** — Skeleton ocupa exatamente o espaço final, para não haver salto.

**Componentes** — `Skeleton` · `Spinner` · `QueryState`

**Usar** em toda primeira carga. **Evitar** skeleton em revalidação (pisca) e **evitar**
spinner de página inteira.

---

## 22. Empty states

**Objetivo** — Transformar ausência de dado em orientação.

**Estrutura** — Três partes obrigatórias: **título** (o que não existe) · **regra** (por que
está vazio, no contexto ativo) · **ação**.

> **Nenhuma recomendação para os filtros desta safra**
> Recomendações aparecem aqui quando têm talhão e produto definidos. Rascunhos ficam em
> *Em edição*.
> `[ Nova recomendação ]`

**Comportamento** — A regra referencia o **contexto ativo** ("nesta safra"), não o absoluto
· Distingue "nunca houve" (convida a criar) de "o filtro não encontrou" (convida a limpar
filtro) · Ícone `icon-empty` em `slate-300` permitido; **sem ilustração**.

**Hierarquia** — Regra é mais importante que o título.

**Componentes** — `EmptyState` · `Button` · `QueryState`

**Usar** em toda lista, tabela e seção que pode ficar vazia — inclusive **dentro de
formulário** ("Nenhum insumo selecionado"). **Evitar** copy genérica e **evitar** confundir
vazio com erro.

---

## 23. Error states

**Objetivo** — Garantir que falha nunca se pareça com ausência de dado.

**Estrutura** — Três camadas: `ErrorBoundary` no layout · `onError` global no `QueryCache`
→ Toast · `ErrorState` por consulta, dentro de `QueryState`.

**Comportamento** — **`isError` tem precedência sobre `isEmpty`** — a inversão que corrige o
bug de "falha vira lista vazia" · Mostra o `detail` do backend, nunca texto fixo · Sempre
oferece repetir · Erro parcial não esconde o que já carregou: `Alert` no topo, dado abaixo ·
`ErrorBoundary` oferece caminho de volta, nunca tela branca.

**Hierarquia** — Erro domina a região afetada, não a tela inteira.

**Componentes** — `ErrorState` · `QueryState` · `ErrorBoundary` · `Alert` · `Toast` ·
`Button`

**Usar** em **toda** `useQuery` que alimenta região visível. **Evitar** cair no default
`[]` sem tratar erro, e **evitar** mensagem genérica quando o servidor já mandou o motivo.

---

## 24. Lista agrupada (acordeão)

**Objetivo** — Organizar uma lista longa pela hierarquia que o consultor usa para pensar:
**por cliente**, e dentro dele por safra ou fazenda.

**Estrutura**
```
▌ AGROPECUÁRIA VALE VERDE            5 planejamentos      ⌄
  ┌ SAFRA 2026/27 · 3 planos                              ⌄
  │▎ Soja precoce — Cascata      [Ativo]    há 2 dias  ✎ ⧉ ⊘
  │▎ Milho safrinha — Retirinho  [Ativo]    há 4 dias  ✎ ⧉ ⊘
  └ SAFRA 2025/26 · 2 planos                              ⌄
```
Cabeçalho de grupo com barra esquerda 3px `action`, nome em versalete e **contagem sempre
visível**. Sub-cabeçalho em `surface-sunken`. Itens com trilho de status.

**Comportamento** — Estado em `Set<string>`, persistido por `storageKey` · Cabeçalho é
`<button>` com `aria-expanded`/`aria-controls` · A **contagem aparece mesmo recolhido** —
é o que permite triar sem expandir · Padrão de abertura conforme a tela: primeiro grupo
aberto em telas de trabalho, todos recolhidos quando há muitos clientes.

**Hierarquia** — Cliente › agrupador secundário › item. O trilho de status do item é o
único elemento saturado; os cabeçalhos são neutros.

**Componentes** — `GroupedAccordion` · `StatusRail` · `Badge` · `Button` · `QueryState` ·
`EmptyState`

**Usar** quando a lista é naturalmente lida por cliente e o volume torna a lista plana
cansativa: Planejamentos (Cliente → Safra), Recomendações (Cliente → Fazenda), fazendas por
cliente. **Evitar** com menos de 3 grupos — o acordeão vira atrito puro; e **evitar** quando
comparar itens de grupos diferentes for a tarefa principal, porque o agrupamento esconde
justamente a comparação. Nesse caso é tabela plana ordenável.
