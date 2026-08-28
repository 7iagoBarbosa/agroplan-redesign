# 02 — Benchmark de UX/UI para o redesign

> **Data:** 28/08/2026 · **Base de comparação:** [`01-product-audit.md`](01-product-audit.md)
> **Referências principais:** Cropwise (Syngenta) · Farmbox (Checkplant/xFarm)
> **Nenhuma alteração foi feita no código.**

---

## 0. Nível de evidência

Três camadas de pesquisa alimentam este documento:

1. **Fontes públicas** — documentação de produto, imprensa, blog da Syngenta Digital,
   App Store, análises de mercado.
2. **Documentação técnica pública** — o [Cropwise Core Services API](https://open-platform.cropwise.com/docs/cropwise-core-services/api-reference/),
   aberto a desenvolvedores, que expõe o modelo de entidades da plataforma.
3. **Observação direta das interfaces** — 31 capturas de tela feitas pelo dono do produto
   em contas reais de Cropwise Protector e Farmbox, em 28/08/2026, arquivadas em
   [`assets/`](assets/). Esta camada substituiu a maior parte do que antes era inferência.

| Marcador | Significado |
|---|---|
| 🟢 **Observado** | Visto diretamente em captura de tela da plataforma |
| ✅ **Verificado** | Confirmado em documentação ou fonte pública direta |
| 🔸 **Inferido** | Deduzido de descrição funcional, sem observação direta |
| ⬜ **Padrão de mercado** | Prática consolidada em B2B SaaS, não específica destas plataformas |

**Cobertura por plataforma:** o Farmbox foi observado com profundidade (24 capturas:
home, navegação, talhões, aplicações, formulário de aplicação, agenda, planejamento,
estoque). O Cropwise Protector foi observado parcialmente (7 capturas: home, tarefas,
registro de aplicação, calendários) — permissões mais restritas na conta disponível.

**O que ainda não foi observado:** comportamento responsivo real (nenhuma captura em
tela estreita ou celular), estado de erro de carregamento, e as telas de configuração de
alertas do Cropwise. As recomendações nessas três frentes seguem apoiadas em literatura
de design (⬜), não em observação.

---

## 1. As referências

### Cropwise — Syngenta ✅

Plataforma modular de agricultura digital. Mais de 40.000 agricultores, dados de mais de
100 milhões de hectares. Funciona como **sistema operacional integrado**, onde soluções
específicas — proteção de cultivos, planejamento, gestão financeira — operam como
**módulos interconectados**, não como produtos separados.

Módulos: **Protector** (monitoramentos, prescrições, tarefas, análise de talhão),
**Operations** (centralizar e acompanhar operações agrícolas), **Interra Scan** (análise
de solo), **Armadilhas Digitais**. A Syngenta abriu a plataforma a desenvolvedores
externos via API.

O **Protector** é o módulo mais próximo do AgroPlan: gera mapas, gráficos e análises a
partir de dados de monitoramento; usa esses dados para montar prescrições de aplicação
via mapas de calor, permitindo tratamento localizado só na área afetada; e acompanha
custo e estoque de aplicação por talhão, tipo de produto ou variedade. Integra com o
John Deere Operations Center.

### Farmbox — Checkplant / xFarm ✅

Agtech brasileira, fundada em 2003 em Pelotas (RS), produto lançado em 2011 em Luís
Eduardo Magalhães (BA). Presente em Brasil, Bolívia, Paraguai, Colômbia e Venezuela,
apoiando mais de 4 milhões de hectares. Em março de 2025 uniu-se à xFarm Technologies
para liderar agricultura digital na América Latina.

Cobre planejamento de safra, registro de aplicações, controle de custos, pluviometria,
contagens de campo, semeadura, armadilhamento, mapas de infestação, agenda de aplicações,
estoque de insumos e previsão de colheita/custo/rentabilidade por talhão.

**A decisão de arquitetura que mais importa para nós:** o app funciona **offline** quando
o operador está em talhão sem sinal, com sincronização automática para a nuvem depois. O
app móvel é **otimizado para iPad** (tablet-first), com compatibilidade iPhone.

### Referências complementares

- **Aegro** ✅ — agtech brasileira com **produto dedicado a consultor agronômico**
  ("gerencie vários clientes em um só lugar"). É a referência mais próxima do nicho exato
  do AgroPlan. O app `Aegro Campo` funciona offline e, segundo a própria empresa, **com
  todas as funcionalidades da versão desktop**. Rateia custos por talhão e gera relatórios
  personalizados por cliente.
- **Climate FieldView** ✅ — parceiro de integração do próprio Farmbox (Farmbox Scout).
  Referência de visualização de mapa/camadas.
- **John Deere Operations Center** ✅ — destino de integração do Cropwise Protector.
- **Linear** ⬜ — citado em análises de 2026 como a maior densidade de informação do
  mercado com o menor ruído visual. Referência de densidade para produto técnico, fora do
  agro.

---

## 1.5 O que as capturas revelaram 🟢

Seção baseada exclusivamente em observação direta. Onde ela contradiz uma inferência
anterior deste documento, a contradição está marcada — a observação vence.

### 1.5.1 Contexto (cliente/fazenda/safra) vive no topo, não no filtro

**As duas plataformas fazem isso, com execuções diferentes.**

**Cropwise** põe o contexto na barra global superior, como cromo permanente:

```
📁 Terra Savana Agro          🌾 Safras
   Fazenda Cascata  ▾            Todas as safras  ▾
```

Organização em cinza pequeno, propriedade em negrito — dois níveis num só controle. A
safra aceita **"Todas as safras"** como valor legítimo.

**Farmbox** põe o contexto no *título da página*, em forma de frase:

```
Talhões  na [TIAGO MS_FORMOSO ▾]  na safra [2025/26 e 2026/26 ▾]
```

E na tela de aplicações, o nome da fazenda **é a manchete da página** (~40px), maior que
qualquer outro elemento:

```
Aplicações na
TIAGO MS_FORMOSO
na safra 2025/26 e 2026/26
```

A safra do Farmbox é **multi-seleção** (duas safras ativas ao mesmo tempo).

> **Isto promove uma classificação:** "seletor global de safra" estava como
> **EXPERIMENTAR** neste documento. Passa a **ADOTAR** — as duas plataformas de
> referência convergem, e ambas resolvem a ressalva que eu havia levantado (precisa
> aceitar "todos") oferecendo exatamente essa opção.

### 1.5.2 A hierarquia de entidades é a rota — evidência definitiva 🟢

A URL do Cropwise Protector, capturada diretamente:

```
protector.cropwise.com/company/45da9b0c…/property/927bb51d…/tasks/spray-registration
```

**`company → property → tasks`.** A hierarquia do modelo de dados (§2.3) não é só
conceitual: ela *é* a estrutura de navegação. O contexto está na URL; a funcionalidade
é a folha.

O AgroPlan roteia pelo inverso — por funcionalidade, com o contexto como parâmetro
interno ou filtro: `/planejamento/:id`, `/calendario/aps`, `/relatorio/manejo/:planId`.

Esta é a diferença estrutural mais concreta encontrada em todo o benchmark, e agora está
apoiada em observação, não em interpretação de material de marketing.

### 1.5.3 Nenhuma das duas usa modal para formulário complexo 🟢

**Contradiz uma recomendação anterior deste documento.** Eu havia classificado "modal
vira tela cheia em mobile" como ADAPTAR, presumindo que o modal seria mantido no desktop.
As capturas mostram que **nenhuma das referências usa modal para formulário de trabalho**.
São três respostas distintas ao mesmo problema:

| Produto | Padrão | Execução observada |
|---|---|---|
| **AgroPlan** | Modal | `ApFormModal` — 672 linhas, 16 `useState` |
| **Farmbox** | Página longa seccionada | Rota própria (`/front/applications/application_form`), seções com título + explicação, barra de ação fixa no rodapé |
| **Cropwise** | Assistente em 4 etapas | `① Informações básicas — ② Aplicação — ③ Informações opcionais — ④ Resumo`, com trilha de progresso no topo e "Próxima ›" fixo no rodapé direito |

O Farmbox também usa **expansão inline dentro da tabela** para edição: clicar num cultivo
abre um painel completo na própria linha, com "SALVAR ALTERAÇÕES" e "FECHAR ✕" — sem
sobreposição, sem perder o contexto da lista.

> **Isto promove outra classificação:** "edição inline" estava como **EXPERIMENTAR**.
> Passa a **ADOTAR** para casos simples, e a decisão modal-vs-página-vs-assistente vira
> item explícito do `03-`.

### 1.5.4 Estado vazio como documentação da regra do sistema 🟢

O melhor padrão observado em todo o benchmark, e o mais barato de copiar. O Farmbox
escreve, na seção vazia de "Aplicações em andamento":

> **Não há aplicações em andamento**
> Você pode adicionar uma aplicação clicando no botão *ADICIONAR APLICAÇÃO*, a partir
> disso ela aparecerá aqui **se tiver progresso ou se estiver programada para começar hoje**

O estado vazio não diz apenas "não há nada" — ele **ensina a regra que governa aquela
seção**. O usuário aprende, no momento em que a dúvida surge, por que a lista está vazia.

Outros observados, todos com a mesma estrutura de duas linhas (título + explicação):
- "Não há aplicações planejadas para a safra / Aplicações e rascunhos planejados para a safra aparecerão aqui"
- "Nenhuma semeadura. / Não há nenhuma semeadura nessa safra." — note o *"nessa safra"*:
  o vazio se refere ao contexto ativo, não ao absoluto
- "Sem chuva! / Não há registro de chuva nessa safra." — título em âmbar, não cinza

O Cropwise usa **ilustração + ação embutida**: "Nenhuma área selecionada", um desenho de
mapa com interrogações, e o botão "+ Selecionar áreas" logo abaixo.

E há estado vazio **dentro do formulário**: "Nenhum insumo selecionado para essa
aplicação", "Sem equipamentos selecionados para essa aplicação".

### 1.5.5 Skeleton de carregamento confirmado 🟢

A home do Farmbox mostra barras cinza de placeholder no card "Semeaduras" enquanto
carrega — skeleton real, não texto "Carregando...". Confirma a recomendação de §2.13 com
observação.

### 1.5.6 Sidebar: as duas divergem entre si

| | Farmbox 🟢 | Cropwise 🟢 | AgroPlan |
|---|---|---|---|
| Fundo | Escuro (azul-petróleo) | **Claro** | Escuro |
| Agrupamento | 3 grupos com cabeçalho em versalete | **Lista plana, 13 itens** | 3 grupos + 2 soltos |
| Configuração | **Separada, no rodapé** | Ícone ⚙ na barra superior | Misturada em "Cadastros" |
| Submenu | Expansível, indentado | Não observado | Expansível |
| Colapsar | Sim (ícone no topo) | Sim ("REDUZIR", rodapé) | Sim |

Os grupos do Farmbox são **fases de trabalho**, não tipos de dado:
`INDICADORES DE RESULTADOS` · `ESTRATÉGIA DA SAFRA` · `OPERAÇÕES DE CAMPO`.

> **Isto confirma a recomendação de §2.1** com observação direta: o Farmbox separa o que
> se usa todo dia do que se configura uma vez, colocando "Configurações" isolado no
> rodapé. É exatamente o que este documento recomendou antes de ver as telas.

O Cropwise mostra que uma lista plana de 13 itens também é viável — mas ele tem contexto
global no topo tirando peso da barra lateral, o que o AgroPlan não tem.

### 1.5.7 Ações em lista usam texto + ícone, não ícone puro 🟢

O Farmbox rotula ações de linha como **`EDITAR ✎`**, e as do painel expandido como
`EXPORTAR DESENHO` · `EXCLUIR CULTIVO` (vermelho) · `IMPRIMIR`, todas com ícone **e**
texto.

Relevante para o AgroPlan: temos 151 `title=` contra 15 `aria-label=`, quase todos em
botões só-ícone. O padrão das referências resolve o problema de acessibilidade **e** o de
descoberta, sem precisar de `aria-label` — porque o nome está visível.

### 1.5.8 Densidade: controle explícito de nível de detalhe 🟢

A Agenda do Farmbox tem um alternador **`Resumido | Todos`** no canto superior da grade.
O mesmo dado, dois níveis de densidade, à escolha do usuário.

É um padrão que eu não conhecia antes das capturas e que resolve elegantemente a tensão
"denso para o especialista × legível para o ocasional" — em vez de escolher um ponto
médio, oferece os dois. **Classificação: EXPERIMENTAR** (novo item).

Outros detalhes de densidade observados no Farmbox:
- **Truncamento com contador**: o card de AP lista 4 talhões e fecha com "+ 3 talhões";
  os insumos com "+ 1 insumos". Mostra o suficiente para reconhecer, sem estourar o card.
- **Filtro escondido atrás de disclosure**: `› FILTRAR` ao lado do título da seção — a
  barra de filtros só aparece quando pedida.
- **Miniatura de geometria**: ao lado do nome do talhão na lista, um pequeno polígono com
  a forma real da área. Identificação visual sem abrir o mapa.
- **Numeral tipográfico**: valores monetários com a parte inteira grande e os centavos
  pequenos (`**0**,00 R$/ha`).

### 1.5.9 Formulários: cada seção explica a si mesma 🟢

O formulário de aplicação do Farmbox não tem labels soltos — cada bloco tem **título e
uma linha de explicação**:

> **Tipo da Atividade**
> Selecione o tipo de atividade dessa aplicação.

> **Selecione os insumos que deseja adicionar à calda.**
> Você pode configurar pela dose ou pela quantidade total que será utilizada. Reordene os
> insumos e indique a ordem de aplicação.

> **Configure os detalhes desta aplicação.**
> Alguns campos não são obrigatórios.

E marca o **opcional**, não o obrigatório: `Misturador Opcional`, `Operador Opcional`,
`Observações Opcional`. Faz sentido quando a maioria dos campos é obrigatória — inverte o
ruído do asterisco.

A barra de ação fica fixa no rodapé com três verbos distintos:
`✓ SOLICITAR APLICAÇÃO` (primária) · `SALVAR` (rascunho) · `CANCELAR`.

> Note a proximidade com a FEAT-019 do AgroPlan (criar e finalizar recomendação): a
> distinção rascunho × submissão é a mesma. O vocabulário do Farmbox — "solicitar" vs.
> "salvar" — vale como referência de microcopy.

### 1.5.10 Badges de estado explicam a consequência 🟢

No Farmbox, um cultivo marcado `Colhido - Inativo` traz logo abaixo:

> × Não pode receber atividades.
> — Utilizado nos relatórios da safra.

O badge não diz só *o que* é o estado — diz **o que ele impede e o que ele ainda
permite**. Para o AgroPlan, que tem 7 estados de ciclo de vida e um `desativada`, é um
padrão diretamente aplicável.

---

## 2. Comparação por dimensão

Cada dimensão traz o padrão observado, a classificação e a justificativa **para o nosso
usuário específico** — um agrônomo de consultoria que atende múltiplos clientes.

---

### 2.1 Navegação e arquitetura de informação

**Cropwise** 🔸 organiza por **módulo** (Protector, Operations, Interra Scan): o usuário
escolhe primeiro *que tipo de trabalho* vai fazer. **Farmbox e Aegro** 🔸 organizam por
**ciclo produtivo** (planejamento → plantio → monitoramento → aplicação → colheita →
custo).

O AgroPlan hoje é um híbrido: menu agrupado por *Defensivos / Relatórios / Cadastros* —
o primeiro grupo é ciclo, o terceiro é natureza técnica do dado.

| Padrão | Classificação | Por quê |
|---|---|---|
| Arquitetura modular tipo "sistema operacional" (Cropwise) | **EVITAR** | Faz sentido para um portfólio de produtos vendidos separadamente. O AgroPlan é um produto só — modularizar criaria fronteira artificial onde hoje o fluxo é contínuo. |
| Navegação pelo ciclo produtivo (Farmbox/Aegro) | **ADAPTAR** | O grupo *Defensivos* já é isso. Vale estender: "Cadastros" com 10 itens é uma gaveta de natureza técnica, não uma etapa de trabalho. |
| Separar "o que eu faço todo dia" de "o que eu configuro uma vez" | **ADOTAR** | 🟢 **Confirmado por observação** (§1.5.6): o Farmbox isola "Configurações" no rodapé da sidebar; o Cropwise põe engrenagem na barra superior. Hoje `Bicos`, `Equipamentos`, `Tipos de Contrato` e `Perfis de Intervalo` competem por atenção com `Clientes` e `Produtos`. |
| Agrupar a sidebar por **fase de trabalho** | **ADOTAR** | 🟢 O Farmbox usa `INDICADORES DE RESULTADOS` / `ESTRATÉGIA DA SAFRA` / `OPERAÇÕES DE CAMPO` — verbos de trabalho, não tipos de dado. O AgroPlan tem "Defensivos / Relatórios / **Cadastros**" — o terceiro é natureza técnica. |
| Abrir a plataforma via API a terceiros (Cropwise) | **EVITAR** | Escala de Syngenta. Fora de propósito para uma consultoria. |

**Recomendação:** rebaixar configuração de baixa frequência para uma área de
*Configurações*, e promover o que é usado diariamente. Isso reduz o menu lateral de 15
itens visíveis para algo em torno de 8 — o que também é pré-requisito para o menu mobile
(§2.16).

---

### 2.2 Sidebar

| Padrão | Classificação | Por quê |
|---|---|---|
| Sidebar colapsável com estado persistido | **ADOTAR** *(já temos)* | `Layout.tsx` já faz isso, incluindo estado dos grupos. É um acerto do AgroPlan — manter. |
| Sidebar fixa como única navegação | **EVITAR** | Com a Decisão 1 (responsivo), sidebar de 224px consome metade do viewport no celular. Precisa de um modo mobile. |
| Navegação primária no terço inferior da tela em mobile | **ADOTAR** | ✅ Prática consolidada em app de campo: permite uso com uma mão enquanto se caminha. Sidebar lateral não atende isso. |
| Ícone + label sempre visível (vs. só ícone) | **ADOTAR** *(já temos)* | O modo colapsado só-ícone com `title` é aceitável como opção do usuário, mas não como padrão. |
| **Ação de linha com texto + ícone** (`EDITAR ✎`), não ícone puro | **ADOTAR** | 🟢 §1.5.7. Resolve de uma vez o problema de acessibilidade do AgroPlan (151 `title=` × 15 `aria-label=` em botões só-ícone) **e** o de descoberta — o nome fica visível, sem depender de `aria-label`. |

**Recomendação:** manter a sidebar no desktop; em tela estreita, converter para barra
inferior com as 4–5 rotas de maior frequência + "mais". Não tentar espremer a sidebar
atual em drawer lateral — isso resolve layout mas não ergonomia de uma mão.

---

### 2.3 Seleção de cliente / fazenda / talhão — **a maior lacuna estrutural**

Esta é a dimensão onde a diferença conceitual é maior, e ela merece destaque.

#### O modelo de dados do Cropwise valida o do AgroPlan ✅

A Syngenta abriu o Cropwise a desenvolvedores e a documentação do
[Cropwise Core Services](https://open-platform.cropwise.com/docs/cropwise-core-services/api-reference/)
é **pública**. Ela expõe a hierarquia de entidades da plataforma:

| Cropwise | Papel declarado na documentação | Equivalente no AgroPlan |
|---|---|---|
| **Workspace** | "top-level tenancy and licensing container" | `consultoria_id` |
| **Organization** | "business entities or farming operations" | `Cliente` |
| **Property** | "farming locations and land parcels… containers for fields" | `Fazenda` |
| **Field** | "individual cultivation areas… basic units for agricultural operations" | `Talhão` |

A documentação afirma que *"every Organization, Property, and Field is reached through a
Workspace, and Workspace-level permissions cascade down to every Organization within it"*
— exatamente o isolamento multi-tenant por consultoria que o AgroPlan implementa. Além
disso, o Cropwise modela `Crops`, `Seasons`, `Growth Scales`, `Agro Operations` e
`Products` — todos com correspondente no AgroPlan (cultura, safra, estágio, atividade,
produto).

Note também que apenas o **Field** carrega polígono: *"fields are the leaf of the region
tree and are the only entity which has an actual polygon"*. No AgroPlan, tanto fazenda
(contorno) quanto talhão têm geometria — uma diferença deliberada nossa, não um erro.

**Conclusão que isso trava:** a lacuna do AgroPlan **não é de modelagem de dados** — nosso
modelo coincide com o de uma plataforma global de 100 milhões de hectares. A lacuna é de
**navegação e rotas**: modelamos o talhão corretamente, mas não damos a ele uma página
própria. Isso rebaixa o custo da correção de "refatorar o domínio" para "criar rota e
tela", e deve ser dito assim no `03-`.

No **Cropwise Protector** ✅, ao clicar em um talhão o usuário vê **o quadro completo
daquela área**: resultados de monitoramento, estágios fenológicos, aplicações realizadas,
fotos e observações gerais. O talhão é um **objeto de primeira classe com página própria**.

Na **Aegro para consultores** ✅, o consultor "gerencia vários clientes em um só lugar" e
acessa "o histórico completo da lavoura" — o cliente é um **contexto de trabalho**.

No **AgroPlan hoje**, cliente é um **filtro** (`SearchableSelect` + `useStickyState` em 6
telas), não um contexto. Não existe página de cliente nem página de talhão: o talhão só
existe dentro de `/planejamento/:id/talhao/:talhaoId` — ou seja, **como filho de um
planejamento, não como entidade própria**. Para ver "tudo sobre este talhão" é preciso
saber de antemão em qual planejamento ele está.

| Padrão | Classificação | Por quê |
|---|---|---|
| Talhão como objeto de primeira classe, com visão unificada (Cropwise) | **ADOTAR** | É o padrão dos três concorrentes e resolve uma pergunta que o agrônomo faz o tempo todo — "o que já foi aplicado aqui?" — que hoje exige navegação indireta. |
| Cliente como **contexto** persistente, não filtro por tela (Aegro) | **ADAPTAR** | Adotar o conceito, não a execução: um seletor de cliente no topo que vale para toda a sessão. Mas o AgroPlan tem casos legítimos de visão cross-cliente (o panorama do calendário, o relatório de demanda de compra) — o contexto precisa poder ser "todos". |
| Breadcrumb em hierarquia profunda | **ADOTAR** | ⬜ O AgroPlan tem hierarquia de 3 níveis e **nenhum breadcrumb** (§6.2 da auditoria). O usuário depende do botão voltar. |
| Seletor global de safra | **ADOTAR** ⬆ | 🟢 Promovido de EXPERIMENTAR após observação: **as duas plataformas fazem isso** e ambas aceitam "todas as safras" como valor, resolvendo a ressalva que eu tinha. Ver §1.5.1. |
| Contexto no topo da página, em forma de frase (Farmbox) ou barra global (Cropwise) | **ADOTAR** | 🟢 Convergência das duas referências. Separa *contexto* (onde estou) de *filtro* (o que recorto) — distinção que o AgroPlan não faz hoje: mistura os dois na mesma barra. |
| Hierarquia de entidades refletida na URL (`/company/…/property/…/tarefa`) | **ADAPTAR** | 🟢 Evidência definitiva em §1.5.2. Adotar o conceito sem reescrever as 33 rotas: o mínimo é dar rota própria a talhão e cliente. |

---

### 2.4 Dashboards

| Padrão | Classificação | Por quê |
|---|---|---|
| Uma métrica primária por visão + secundárias, com drill-down sob demanda | **ADOTAR** | ⬜ O `DashboardPage` hoje mostra 4 `StatCard` + 4 `PendenciaCard` + panorama + recentes + atalhos — tudo no mesmo peso visual. Falta hierarquia. |
| KPI que leva à ação, não KPI decorativo | **ADOTAR** *(já temos parcialmente)* | Os `PendenciaCard` já são links para o calendário e reusam a paleta oficial para o número bater com o quadro. É o melhor padrão do AgroPlan hoje — estender aos demais. |
| Painel de controle "do plantio à colheita" (Farmbox) | **EVITAR** | O Farmbox cobre o ciclo inteiro incluindo colheita e rentabilidade. O AgroPlan é de manejo de defensivos — importar KPIs de colheita seria escopo que o produto não tem. |
| Executive summary com 3–5 KPIs para avaliador, não só power user | **EXPERIMENTAR** | ⬜ Relevante porque o dono da consultoria pode não ser o usuário diário. Mas só vale se houver esse segundo perfil de verdade — não inventar tela para usuário hipotético. |

**Alerta específico:** o dashboard atual deriva métricas no cliente a partir de uma lista
de até 200 planejamentos (§6.8 da auditoria). Passa a mentir silenciosamente acima
disso. Isso é bug latente, não questão de design.

---

### 2.5 Mapas

| Padrão | Classificação | Por quê |
|---|---|---|
| Mapa de calor / infestação para aplicação localizada (Cropwise, Farmbox) | **EVITAR** | Depende de dados de monitoramento georreferenciado que o AgroPlan **não coleta**. Adotar exigiria um módulo de monitoramento inteiro — é decisão de produto, não de redesign. |
| Talhão colorido por status no mapa | **EXPERIMENTAR** | Aqui sim: o AgroPlan já tem contorno de talhão e já tem status de aplicação por talhão. Pintar o contorno com `STATUS_*` transforma o mapa existente em ferramenta de triagem sem coletar nenhum dado novo. **É o melhor custo/benefício desta seção.** |
| Mapa como visualização, não como navegação principal | **ADOTAR** *(já temos)* | O AgroPlan trata mapa como tela específica (`/fazendas/:id/mapa`), não como shell. Correto para o nosso caso — o trabalho do consultor é lista e formulário, não exploração geográfica. |
| Integração com maquinário (John Deere) | **EVITAR** | Fora de escopo e fora do perfil do usuário. |

---

### 2.6 Planejamento

| Padrão | Classificação | Por quê |
|---|---|---|
| Planejamento de safra como espinha dorsal (Farmbox, Aegro) | **ADOTAR** *(já temos)* | É exatamente o que o AgroPlan faz. Conceitualmente estamos alinhados ao mercado. |
| Custo/estoque por talhão, tipo de produto ou variedade (Cropwise) | **ADAPTAR** | O AgroPlan tem estoque e almoxarifado, mas o cruzamento com talhão é indireto. Vale como evolução — mas é feature, não redesign. Registrar e não misturar com esta frente. |
| Rateio de custo indireto e mão de obra por talhão (Aegro) | **EVITAR** | É gestão financeira de fazenda. O AgroPlan é ferramenta de consultoria técnica — o custo que importa é o de insumo recomendado, não a folha da fazenda. |
| Comparar manejo aplicado × retorno financeiro (Aegro consultor) | **EXPERIMENTAR** | Alinhado ao papel do consultor (provar o valor da recomendação), mas exige dado de produtividade que o AgroPlan não tem hoje. Anotar como direção de produto. |

---

### 2.7 Calendário e operações

| Padrão | Classificação | Por quê |
|---|---|---|
| Agenda de aplicações como visão central (Farmbox) | **ADOTAR** *(já temos)* | O módulo `calendario/` é a área mais madura do AgroPlan (§6.10 da auditoria). Alinhado ao mercado. |
| Múltiplas visões sobre os mesmos dados (kanban / tabela / agenda) | **ADOTAR** *(já temos)* | O AgroPlan tem as três. É diferencial, não lacuna — ver §4. |
| Registro de operação em campo, offline (Farmbox, Aegro) | **ADAPTAR** | Ver §2.16. O caminho realista não é replicar o app nativo deles, mas garantir que o registro de aplicação funcione bem no navegador do celular. |
| "Operações" como módulo separado do planejamento (Cropwise Operations) | **EVITAR** | Duplicaria a fronteira que o AgroPlan já resolve melhor: no nosso fluxo, a aplicação nasce do planejamento e do calendário, não de um módulo à parte. |

---

### 2.8 Alertas — **o padrão mais valioso deste benchmark**

O **Sistema de Alertas do Cropwise Protector** ✅ funciona assim: alertas são gerados
automaticamente a partir dos dados de monitoramento e são **totalmente configuráveis pelo
usuário**. É possível **definir o limite de dias que um talhão pode ficar sem
monitoramento** — ultrapassado o prazo, uma notificação é enviada. Também dispara quando
um talhão piora em relação ao monitoramento anterior. As informações chegam no celular,
tablet ou computador.

O AgroPlan já tem o **conceito de status derivado de prazo** — `recomendacao_atrasada`,
`aplicacao_atrasada` — e, o que é mais importante, **já tem o modelo de dados de prazo
configurável**: `perfis-intervalo` / `PerfisIntervaloPage` / `perfis_intervalo.py`.

A diferença não é de dado. É de **entrega**: no AgroPlan o atraso é *exibido* para quem
abre a tela; no Cropwise ele é *empurrado* para quem precisa saber.

| Padrão | Classificação | Por quê |
|---|---|---|
| Alerta configurável por limiar de prazo | **ADOTAR** | Metade do trabalho já existe (`perfis-intervalo`). O que falta é transformar o status calculado em alerta com destinatário. |
| Notificação fora da aplicação (push / e-mail / WhatsApp) | **ADAPTAR** | ⚠️ **O AgroPlan já tem o canal**: a integração Z-API/n8n que hoje manda digest semanal. Estender esse canal para alerta de atraso é caminho muito mais curto do que construir push do zero. |
| Alerta por piora em relação à medição anterior | **EVITAR** | Exige série histórica de monitoramento que o AgroPlan não coleta. |
| Central de alertas dentro do app (ícone de sino + histórico) | **EXPERIMENTAR** | ⬜ Útil, mas o `PendenciaCard` do dashboard já cumpre parte do papel. Testar se agrega antes de construir. |

---

### 2.9 Filtros

| Padrão | Classificação | Por quê |
|---|---|---|
| Barra horizontal de filtros, cada controle = um atributo, empilháveis "como uma frase" | **ADOTAR** | ⬜ Padrão dominante em B2B. O AgroPlan já usa `SearchableSelect` bem, mas cada tela compõe sua própria barra, com larguras em `style` inline (`CalendarioHome`). Falta o padrão, não o componente. |
| Linha primária com filtros mais usados + menu "Adicionar filtro" | **ADAPTAR** | ⬜ Bom para telas de muitos atributos. No AgroPlan a maioria das telas tem 2–3 filtros — aplicar só onde houver mais que isso, senão é complexidade sem ganho. |
| Persistir filtro entre navegações | **ADOTAR** *(temos parcialmente)* | `useStickyState` existe e funciona, mas está em **6 arquivos** — nenhum deles em `cadastros/`, que são justamente as telas de entra-e-sai. Estender é barato. |
| Filtro refletido na URL (compartilhável) | **EXPERIMENTAR** | Hoje só `?cliente=` no calendário, e ele é limpo logo após ser aplicado. Um consultor mandar link de uma visão filtrada para o cliente tem valor real — mas conflita com `useStickyState`. Precisa de decisão de qual vence. |

---

### 2.10 Tabelas e densidade de informação

O benchmark de densidade em B2B é o Linear ⬜: **maior densidade de informação do mercado
com o menor ruído visual**. Em produtos técnicos, a tabela *é* o produto — os times
investem em densidade, edição inline e suporte a teclado.

| Padrão | Classificação | Por quê |
|---|---|---|
| Tabela densa, com pouco ruído (Linear) | **ADOTAR** | O AgroPlan tem 28 tabelas e é um produto de trabalho. Densidade é aliada do agrônomo que compara talhões — não um problema a "arejar". |
| Números tabulares em coluna numérica | **ADOTAR** *(já temos)* | `.tnum` em 53 lugares. Um dos padrões mais bem executados do AgroPlan. |
| Edição por expansão inline na linha | **ADOTAR** ⬆ | 🟢 Promovido de EXPERIMENTAR: o Farmbox abre um painel de edição completo dentro da própria linha, sem sobreposição e sem perder o contexto da lista (§1.5.3). Resolve parte dos 14 modais à mão do AgroPlan. |
| Alternador de densidade (`Resumido \| Todos`) | **EXPERIMENTAR** | 🟢 Padrão novo, observado na Agenda do Farmbox (§1.5.8). Em vez de escolher um ponto médio entre denso e legível, oferece os dois ao usuário. Candidato natural para o Calendário do AgroPlan. |
| Truncar lista longa com contador (`+ 3 talhões`) | **ADOTAR** | 🟢 Mantém o card informativo sem estourar. O AgroPlan tem o mesmo problema em cards de evento com muitos talhões. |
| Filtro atrás de disclosure (`› FILTRAR`) | **ADAPTAR** | 🟢 Boa economia de espaço, mas esconde affordance. Usar onde o filtro é ocasional; manter visível onde é o modo de trabalho (calendário, recomendações). |
| Miniatura da geometria do talhão ao lado do nome | **EXPERIMENTAR** | 🟢 O AgroPlan já tem o polígono de cada talhão. Identificação visual barata numa lista — mas confirmar se ajuda de fato o consultor a reconhecer a área. |
| Rolagem horizontal como resposta ao mobile | **EVITAR** | ✅ Boa para 3–5 colunas com primeira coluna fixa; ruim para as tabelas largas do AgroPlan. Ver decisão abaixo. |
| Linha vira card em tela estreita (stacked cards) | **ADAPTAR** | ✅ Sem rolagem horizontal e todas as colunas visíveis, mas cabem menos registros por tela. |
| Exibição seletiva de colunas em tela estreita | **ADOTAR** | ✅ Recomendado justamente para tabelas ricas em informação — que é o caso do AgroPlan. |

**Decisão de design recomendada para o `03-`:** não existe resposta única — a literatura é
explícita quanto a isso, e a escolha deve vir do trabalho que o usuário está fazendo.
Para o AgroPlan:

- **Tabelas de triagem** (calendário, recomendações — o usuário procura o que está
  atrasado): **exibição seletiva de colunas**. Mostrar em tela estreita só o que
  responde "isto precisa de mim?" — talhão, status, data — e abrir o resto no detalhe.
- **Tabelas de cadastro** (as 14 telas de `cadastros/` — listas curtas, poucos campos):
  **stacked cards**. Combina com a Decisão 3 e com alvos de toque grandes.
- **Nunca só `overflow-x-auto`.** Ele resolve o estouro de layout, não a usabilidade.

---

### 2.11 Indicadores

| Padrão | Classificação | Por quê |
|---|---|---|
| Indicador que carrega a mesma paleta do dado que representa | **ADOTAR** *(já temos)* | `PendenciaCard` reusa `STATUS_DOT_CLASS`/`STATUS_LABEL` para o número do Início bater com o do Calendário. Excelente decisão — deve virar regra, não exceção. |
| Componente único de indicador | **ADOTAR** | Hoje são 4 tratamentos distintos (`StatCard`, `PendenciaCard`, `ReportSummaryCards`, `ResumoVolumes`) para o mesmo conceito. Unificar. |
| Sparkline / tendência no indicador | **EXPERIMENTAR** | ⬜ Só faz sentido se houver série temporal que o usuário acompanhe. Hoje os indicadores são contagens no instante. Não forçar. |

---

### 2.12 Formulários

| Padrão | Classificação | Por quê |
|---|---|---|
| Uma única escala tipográfica de label | **ADOTAR** | ⬜ Não é padrão de mercado, é higiene. O AgroPlan tem **4 escalas concorrentes** em 136 labels (§6.3 da auditoria). |
| Validação e erro no campo, com mensagem do servidor | **ADOTAR** | O AgroPlan mostra `"Erro ao salvar."` fixo, descartando o `detail` que o FastAPI já envia. |
| Formulário complexo **fora** do modal | **ADOTAR** ⬆ | 🟢 Revisado após observação: eu presumia manter modal no desktop. **Nenhuma das duas referências usa modal para formulário de trabalho** — Farmbox usa página longa seccionada, Cropwise usa assistente de 4 etapas. Ver §1.5.3. |
| Seção de formulário com título + linha de explicação | **ADOTAR** | 🟢 Padrão do Farmbox (§1.5.9). Resolve de uma vez o problema das 4 escalas de label do AgroPlan: a hierarquia passa a ser seção › campo, não tamanho de fonte. |
| Marcar o **opcional** em vez do obrigatório | **ADAPTAR** | 🟢 Faz sentido quando a maioria dos campos é obrigatória — que é o caso na maior parte dos formulários do AgroPlan. Verificar caso a caso. |
| Barra de ação fixa no rodapé, com verbos distintos para rascunho e submissão | **ADOTAR** | 🟢 `SOLICITAR APLICAÇÃO` / `SALVAR` / `CANCELAR`. Diretamente aplicável à FEAT-019 (criar × finalizar recomendação). |
| Um paradigma único de formulário | **ADOTAR** | `react-hook-form`+`zod` já é dependência e está em 10 arquivos; o resto usa `useState` manual (340 no projeto). Escolher um. |

---

### 2.13 Estados (carregando / vazio / erro)

| Padrão | Classificação | Por quê |
|---|---|---|
| Estado vazio que **ensina a regra da seção** | **ADOTAR** | 🟢 O melhor padrão do benchmark e o mais barato de copiar (§1.5.4). O Farmbox explica *por que* a lista está vazia — "aparecerá aqui se tiver progresso ou se estiver programada para começar hoje". O AgroPlan tem ~35 variantes de copy e nenhuma ensina nada. |
| Estado vazio referente ao **contexto ativo**, não ao absoluto | **ADOTAR** | 🟢 "Não há nenhuma semeadura *nessa safra*" — evita que o usuário conclua que o dado não existe quando ele só está fora do recorte. Diretamente aplicável às telas filtradas do AgroPlan. |
| Estado vazio dentro do próprio formulário | **ADOTAR** | 🟢 "Nenhum insumo selecionado para essa aplicação". O `ItensManejoModal` do AgroPlan tem exatamente esse caso. |
| Ilustração no estado vazio | **EVITAR** | 🟢 O Cropwise usa; o Farmbox não. Custo de produção e manutenção de ilustração não se paga num produto de trabalho diário — o texto que explica a regra vale mais. |
| Skeleton em vez de texto "Carregando..." | **ADOTAR** ⬆ | 🟢 Confirmado por observação na home do Farmbox (§1.5.5), não mais só literatura. O AgroPlan tem 12 tratamentos visuais para o mesmo texto e 1 único skeleton. |
| Erro de carregamento visualmente distinto de vazio | **ADOTAR** | Já discutido e priorizado: 2 de 61 arquivos tratam erro de query. O padrão-alvo já existe em `ApListPage`. |

---

### 2.14 Feedback

| Padrão | Classificação | Por quê |
|---|---|---|
| Toast para confirmação de ação | **ADOTAR** *(já temos)* | `lib/toast.ts` funciona e tem `role="status"`/`aria-live`. Falta usar os tokens em vez de hex hardcoded. |
| Confirmação in-app para ação destrutiva | **ADOTAR** *(temos, sem adoção)* | `ConfirmDialog` existe; 22 arquivos ainda usam `confirm()` nativo. |
| Fila de toasts (vários simultâneos) | **EXPERIMENTAR** | Hoje mostra um por vez (remove o anterior). Com o `onError` global de query, várias falhas simultâneas podem se atropelar — vale reavaliar junto daquela mudança. |
| Feedback otimista (UI atualiza antes da resposta) | **EVITAR** | Atraente, mas o AgroPlan lida com recomendação técnica com responsabilidade agronômica. Mostrar como salvo algo que falhou é pior que esperar. |

---

### 2.15 Responsividade e uso em campo

Aqui o mercado é unânime e o AgroPlan está claramente atrás.

**Farmbox** ✅: funciona offline quando o operador está em talhão sem sinal, sincroniza
depois; app otimizado para iPad. **Aegro** ✅: `Aegro Campo` funciona sem internet e, pela
descrição da empresa, com todas as funcionalidades da versão desktop. **Cropwise** ✅:
informação chega no celular, tablet ou computador.

Princípios de app de campo ✅: alto contraste para luz solar direta, alvos de toque
grandes (≥48×48dp) para uso com luvas, funcionamento offline, e navegação primária no
terço inferior para uso com uma mão.

| Padrão | Classificação | Por quê |
|---|---|---|
| Alvo de toque ≥48×48dp e alto contraste | **ADOTAR** | Direto e barato. O `.icon-btn` do AgroPlan tem 32×32px — **abaixo do mínimo**. |
| Navegação primária no terço inferior em mobile | **ADOTAR** | Ver §2.2. |
| Offline-first com sincronização (Farmbox, Aegro) | **EVITAR** *(nesta frente)* | ⚠️ Ambos entregam isso via **app nativo**, não web. Para o AgroPlan — SPA React com cookie httpOnly e CSRF — offline real exigiria fila de escrita, resolução de conflito e repensar autenticação. É **um projeto próprio, não um item de redesign**. Confundir os dois é o maior risco de escopo deste documento. |
| Tablet como alvo principal de campo (Farmbox: iPad-first) | **EXPERIMENTAR** | Insight relevante: talvez o alvo real de campo não seja celular, e sim tablet — onde a tabela densa ainda funciona. Vale confirmar como o consultor trabalha em campo hoje. |

**Recomendação de escopo:** a Decisão 1 (responsivo) deve ser lida como **"a aplicação web
funciona bem em tela pequena"**, não como "o AgroPlan vira app offline". A segunda é
legítima como direção de produto, mas precisa ser um `NN-` próprio.

---

## 3. Onde o AgroPlan está atrás

Em ordem de distância até o mercado:

1. **Talhão e cliente não são objetos de primeira classe.** Cropwise dá página completa ao
   talhão; Aegro trata cliente como contexto de trabalho. No AgroPlan, talhão só existe
   dentro de um planejamento, e cliente é filtro por tela. É a maior lacuna estrutural.
2. **Uso em campo.** Os três concorrentes têm resposta para o campo; o AgroPlan é
   desktop-only (13 de 92 arquivos com breakpoint, `.icon-btn` de 32px, sem menu mobile).
3. **Alertas não saem da tela.** O dado de atraso existe e é calculado corretamente, mas
   só é visto por quem abre a aplicação. Cropwise empurra; nós exibimos.
4. **Estados de carregamento/erro.** Não é comparação com concorrente — é falha absoluta:
   2 de 61 arquivos tratam erro de query.
5. **Consistência visual.** Três gerações de código convivendo (§9 da auditoria). Nenhum
   concorrente maduro tem 4 escalas de label e 12 tratamentos de "Carregando...".

## 4. Onde o AgroPlan já é melhor

Isto não é consolo — são ativos a proteger no redesign:

1. **Modelagem de status muito superior.** 7 estados no ciclo de vida da aplicação, com 6
   representações visuais conforme o contexto (tabela, kanban, legenda, card), e o status
   de AP derivando da mesma paleta. Não encontrei nada nas referências com esse nível de
   granularidade — elas trabalham com "monitorado / não monitorado / alerta". **É o ativo
   de design mais forte do produto.**
2. **Três visões sobre os mesmos dados** (kanban, tabela, agenda) no calendário, com
   filtro compartilhado e drawer de detalhe. Maduro e coerente.
3. **Catálogo oficial integrado.** Sync automático com Agrofit/Bioinsumos da Embrapa —
   o produto recomendado vem de fonte regulatória, não de cadastro manual. Nenhuma
   referência pesquisada anuncia isso.
4. **Multi-tenant de consultoria nativo.** A Aegro tem produto *para* consultor, mas o
   núcleo é do produtor. O AgroPlan é consultoria-first desde o modelo de dados.
5. **Coerência de números entre telas.** O `PendenciaCard` reusa deliberadamente a paleta e
   a query do calendário para o número bater. É maturidade de design que muitos produtos
   maiores não têm.

## 5. Onde podemos nos diferenciar

Quatro apostas, ancoradas no que já temos e no que os outros não fazem:

1. **Ser a melhor ferramenta de *recomendação técnica*, não de gestão de fazenda.**
   Farmbox e Aegro vão de plantio a rentabilidade; Cropwise cobre um portfólio inteiro.
   Nenhum é especialista no ato de recomendar. O AgroPlan já tem AP, finalização e
   impressão — esse é o território a dominar.
2. **A recomendação como documento com identidade.** `RecomendacaoPrintPage` já existe. É
   o artefato que o consultor entrega ao cliente e assina tecnicamente — o único ponto do
   produto que sai da tela e vira papel/PDF na mão de outra pessoa. Nenhuma referência
   trata a saída impressa como peça de design. Aqui cabe a ousadia visual do redesign.
3. **Rastreabilidade regulatória do produto recomendado.** O vínculo com Agrofit pode ir
   além do catálogo: mostrar na própria recomendação que o produto é registrado para
   aquela cultura/alvo é valor que nenhum concorrente pesquisado expõe.
4. **Painel do portfólio da consultoria.** Todos são bons em "esta fazenda"; o problema
   real do nosso usuário é "quais dos meus 30 clientes precisam de mim esta semana". O
   `PanoramaClientes` já é o embrião disso — é o ponto onde o AgroPlan pode ser melhor
   que empresas muito maiores, porque elas resolvem outro problema.

## 6. Padrões a incorporar — lista consolidada

**ADOTAR** (13): separar uso diário de configuração · sidebar com nav inferior em mobile ·
talhão como objeto de primeira classe · breadcrumb · uma métrica primária por visão · KPI
acionável · mapa como visualização · alerta configurável por prazo · barra de filtro
horizontal · persistir filtro em todas as telas · tabela densa com baixo ruído · coluna
seletiva em tela estreita · alvo de toque ≥48dp · componente único de indicador · escala
única de label · paradigma único de formulário · estado vazio acionável · erro distinto de
vazio · confirmação in-app.

**ADAPTAR** (7): navegação por ciclo produtivo · cliente como contexto (com opção "todos")
· custo por talhão *(registrar como feature futura)* · registro de operação no navegador ·
notificação via canal Z-API/n8n que já existe · linha vira card nos cadastros · skeleton
como parte da padronização de loading · modal vira tela cheia em mobile.

### ⬆ Promoções decididas em 28/08/2026

Três itens que estavam como EXPERIMENTAR foram **aprovados pelo dono do produto** e passam
a **ADOTAR**, integrando o design system:

| Item | Onde vive agora |
|---|---|
| **Alternador de densidade** (`Compacto \| Completo`) | `05-` §33e · `ui-patterns` §9, §17 · gabarito de CRUD |
| **Glifo de talhão** (micro-polígono na lista) | Enxerto B — `05-` §39, escopo fechado em cadastro e seleção de talhões |
| **Talhão colorido por status no mapa** | `05-` §39 — preenchimento pelo trilho do status a 25% |

A lista abaixo é o estado **anterior** à decisão, mantida como registro do processo.

**EXPERIMENTAR** (8): seletor global de safra · executive summary · talhão colorido por
status no mapa · comparar manejo × retorno · filtro na URL · edição inline · central de
alertas · fila de toasts · tablet como alvo de campo.

**EVITAR** (9): arquitetura modular tipo SO · API pública · sidebar fixa como única
navegação · KPI de colheita/rentabilidade · mapa de calor de infestação · integração com
maquinário · rateio de custo indireto · "Operações" como módulo separado · alerta por
piora histórica · rolagem horizontal como resposta ao mobile · feedback otimista ·
**offline-first nesta frente de trabalho**.

## 7. O que não faz sentido para o nosso usuário

O agrônomo de consultoria não é o produtor rural, e boa parte do que essas plataformas
fazem existe para o segundo:

- **Gestão financeira da fazenda** (rateio de custo indireto, mão de obra, fluxo de caixa,
  NF-e). O consultor recomenda insumo; ele não fecha o caixa do cliente.
- **Ciclo produtivo completo** (semeadura, pluviometria, previsão de colheita,
  rentabilidade por talhão). O AgroPlan entra em manejo de defensivos — ampliar para o
  ciclo inteiro é virar outro produto e competir de frente com quem tem 4 milhões de
  hectares.
- **Monitoramento georreferenciado de pragas** (contagens em campo, mapas de infestação,
  armadilhas digitais, NDVI). É um módulo inteiro de coleta que o AgroPlan não tem — e
  sem ele, todos os padrões de mapa de calor ficam sem dado.
- **Integração com maquinário** (John Deere Operations Center). O usuário não opera a
  máquina.
- **Marketplace / rede de distribuição.** Cropwise chega ao agricultor pela distribuição
  Syngenta; é modelo de negócio, não de produto.

---

## 8. Consequências para o `03-`

O que este benchmark trava como premissa para o documento de design:

1. **Responsividade = a aplicação web funciona bem em tela pequena.** Offline-first é
   outro projeto — se entrar aqui, o redesign não termina.
2. **A tabela responsiva tem duas respostas, não uma:** coluna seletiva para triagem,
   card empilhado para cadastro. Nunca só `overflow-x-auto`.
3. **A sidebar não vira drawer no mobile** — vira navegação inferior, e isso exige antes
   reduzir o menu de 15 para ~8 itens de primeiro nível. 🟢 As capturas mostram dois
   caminhos para isso: agrupar por fase de trabalho (Farmbox) ou tirar o contexto da
   sidebar e pô-lo no topo (Cropwise). **Os dois combinados resolvem com folga.**
4. **A paleta de status é ativo, não dívida.** O redesign deve preservá-la e estendê-la
   (mapa, indicadores), nunca simplificá-la em nome de "limpeza visual".
5. **Talhão e cliente como objetos de primeira classe** é a maior mudança de arquitetura
   de informação em jogo — precisa de decisão explícita no `03-`, porque muda o mapa de
   rotas, não só o visual. 🟢 O custo é menor do que parecia: o modelo de dados do
   AgroPlan já está correto (§2.3), e a rota do Cropwise mostra o alvo (§1.5.2).
6. **Contexto ≠ filtro.** 🟢 As duas referências separam explicitamente "onde estou"
   (cliente/fazenda/safra, no topo, persistente) de "o que recorto" (atributos, junto da
   tabela). O AgroPlan mistura os dois na mesma barra. Essa separação é pré-requisito
   das decisões 3 e 5, e deve ser resolvida antes de desenhar qualquer tela.
7. **Decisão em aberto para o `03-`: qual padrão para formulário complexo.** 🟢 Três
   respostas válidas observadas — página longa seccionada (Farmbox), assistente em
   etapas (Cropwise), expansão inline na linha (Farmbox, para casos simples). O modal
   atual do AgroPlan não é nenhuma das três. Escolher exige olhar caso a caso:
   `ApFormModal` (672 linhas) provavelmente pede assistente; um CRUD de bicos pede
   expansão inline.

### O que ainda falta observar

Três lacunas que as capturas não cobriram e que valem uma segunda rodada, se houver
oportunidade:

- **Comportamento responsivo real** — nenhuma captura em tela estreita. É a Decisão 1 do
  projeto e continua apoiada só em literatura.
- **Estado de erro de carregamento** — nenhuma captura com falha de rede. É o problema
  nº 1 do AgroPlan e seria muito útil ver como duas plataformas maduras o resolvem.
- **Configuração de alertas do Cropwise** — o padrão classificado como ADOTAR em §2.8
  segue conhecido só por descrição textual.

---

## Fontes

- [Syngenta — lançamento da plataforma Cropwise](https://www.syngenta.es/noticias/syngenta-lanza-su-nueva-plataforma-de-agricultura-digital-cropwiser)
- [Syngenta Argentina — Cropwise abre plataforma a desenvolvedores](https://www.syngenta.com.ar/syngenta-abre-su-plataforma-digital-cropwise-desarrolladores-para-impulsar-la-innovacion-y-reducir)
- [IT Forum — Syngenta Digital e a plataforma no Brasil](https://itforum.com.br/noticias/syngenta-digital-agricultura-brasil/)
- [Syngenta Digital — Sistema de Alertas do Cropwise Protector](https://blog.syngentadigital.ag/sistema-de-alertas/)
- [Syngenta Digital — Cropwise Protector](https://blog.syngentadigital.ag/cropwise-protector-3/)
- [Gazeta Digital — Cropwise Protector, tomada de decisão no campo](https://www.gazetadigital.com.br/editorias/informe-publicitario/soluo-digital-cropwise-protector-facilita-tomada-de-deciso-e-otimiza-produo-no-campo/644051)
- [Farmbox — sobre a empresa](https://www.farmbox.com.br/about-1)
- [MundoGEO — Farmbox e xFarm Technologies na América Latina](https://mundogeo.com/2025/03/14/farmbox-e-xfarm-technologies-unidas-para-liderar-atuacao-em-agricultura-digital-na-america-latina/)
- [App Store — Farmbox](https://apps.apple.com/br/app/farmbox/id1641359260)
- [PwC AgTech Innovation — crescimento e novo app do Farmbox](https://www.pwc.com.br/pt/consultoria/agtech-innovation/agtech-innovation-news/materias/2023/apos-registrar-crescimento-de-40-na-base-de-usuarios-farmbox-vai-lancar-novo-app.html)
- [Aegro — software para consultor agronômico](https://aegro.com.br/para-voce/software-para-consultor-agronomico/)
- [Aegro — aplicativo Aegro Campo](https://aegro.com.br/produtos/aplicativo-aegro-campo/)
- [FieldView — parceria Farmbox Scout](https://climatefieldview.com.br/parceiros/farmbox)
- [SaaSUI — padrões de filtro e ordenação em SaaS (2026)](https://www.saasui.design/blog/saas-filtering-sorting-ux-patterns)
- [SaaSFrame — anatomia de dashboards SaaS de alta performance (2026)](https://www.saasframe.io/blog/the-anatomy-of-high-performance-saas-dashboard-design-2026-trends-patterns)
- [Morningstar Design System — tabelas responsivas](https://designsystem.morningstar.com/legacy/v/2.29.0/ux-patterns/responsive-data-tables.html)
- [Design Bootcamp — tabelas de dados para mobile](https://medium.com/design-bootcamp/designing-user-friendly-data-tables-for-mobile-devices-c470c82403ad)
- [Agriculture App UI Design — princípios testados em campo](https://medium.com/@sneh_sagar/agriculture-app-ui-design-7-field-tested-principles-that-drive-real-farmer-adoption-3fbbbbb24cea)
