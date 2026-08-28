# AgroPlan — Redesign Brief

> **Data:** 28/08/2026 · **Status:** direção aprovada, implementação não iniciada
> **Documento de entrada da série.** Consolida `01-` a `06-`; cada seção aponta a fonte.
> Escrito por último, numerado primeiro — é por aqui que se começa a ler.

| # | Documento | O que responde |
|---|---|---|
| [`01-`](01-product-audit.md) | Auditoria de produto | Como o produto está estruturado e onde falha |
| [`02-`](02-market-benchmark.md) | Benchmark de mercado | O que Cropwise e Farmbox fazem, e o que copiar |
| [`03-`](03-current-ui-audit.md) | Auditoria de UI/UX | 21 problemas medidos, com gravidade |
| [`04-`](04-visual-directions.md) | Especificação visual | A identidade escolhida |
| [`05-`](05-design-system.md) | Design system | Tokens, 25 componentes, padrões de domínio |
| [`06-`](06-component-inventory.md) | Inventário da tela piloto | O que existe, o que duplica, o que refatorar |

Skills operacionais: [`.claude/skills/agroplan-design/`](../../.claude/skills/agroplan-design/)

---

## 1. Objetivo

O AgroPlan funciona. Não há tela quebrada, o `tsc` passa limpo e o backend tem suíte de
testes real. O problema não é qualidade pontual — é que **cada mudança futura custa mais
que a anterior**, e três fatos tornaram isso urgente:

**O frontend tem três gerações de código convivendo.** Não é inconsistência aleatória: é
estratificação. O bloco `cadastros/` + `admin/` + `almoxarifados/` (16 rotas, **42% das
telas**) é 100% uniforme na sua divergência — cabeçalho à mão, modal `fixed inset-0`,
`confirm()` nativo, `<svg>` inline, tabela sem scroll, zero `aria-label`. O `calendario/`
é uma geração à frente. Todo trabalho novo precisa decidir qual geração seguir.

**Há um problema que induz o usuário a erro.** 61 arquivos usam `useQuery`; **2 tratam o
erro dessa consulta**. Nos outros 59, falha de rede renderiza tabela vazia —
indistinguível de "não há registros". Um agrônomo abrindo o calendário lê "nada pendente"
quando o dado não carregou. Isso é decisão agronômica sobre informação falsa.

**O uso em campo passou a ser requisito.** Com essa decisão, o contraste deixa de ser
dívida de acessibilidade e vira bloqueio funcional: a cor de texto mais usada do produto
(`text-gray-400`, 328 ocorrências) tem **2,54:1** — sob sol direto, é invisível.

O redesign existe para resolver esses três pontos e, no caminho, dar ao produto uma
identidade que hoje não tem.

## 2. Objetivos do redesign

| Objetivo | Como se verifica |
|---|---|
| **Modernizar a interface** | Uma geração de código, não três |
| **Melhorar usabilidade** | Erro, vazio e carregando visualmente distintos em 100% das telas |
| **Aumentar consistência** | Escala única de tipografia, espaçamento, raio, sombra e ícone |
| **Melhorar leitura de dados** | Densidade escolhida pelo usuário; números tabulares em mono; status legível de relance |
| **Criar identidade própria** | Paleta e tipografia que não se confundem com admin template |
| **Viabilizar uso em campo** | Responsivo real, contraste ≥4,5:1, alvo de toque ≥48px |
| **Reduzir custo de mudança** | Componentes compartilhados adotados, não apenas existentes |

## 3. Público e contexto de uso

**Usuário principal:** agrônomo de **consultoria**, atendendo dezenas de clientes.

Não é o produtor rural — não fecha o caixa da fazenda, não opera máquina, não faz
monitoramento georreferenciado. Isso descarta boa parte do que as plataformas de mercado
oferecem (§5).

A pergunta que ele faz ao abrir o produto não é *"como está esta fazenda?"*. É:

> **"Quais dos meus clientes precisam de mim esta semana?"**

**Contexto operacional:** trabalho de escritório com muitas linhas comparáveis (densidade
é aliada, não inimiga) **e** consulta em campo — celular ou tablet, luz solar direta,
possivelmente com luvas, conexão instável.

**Estrutura de dados:** multi-tenant por consultoria. Cliente → Fazenda → Talhão → Safra.
O isolamento é garantido no backend; nenhuma tela recria esse filtro.

## 4. Problemas prioritários

Do [`03-`](03-current-ui-audit.md) — 21 problemas catalogados, todos medidos. Os críticos
e altos:

| | Problema | Escala |
|---|---|---|
| 🔴 P1 | `text-gray-400` a **2,54:1** é a cor de texto mais usada | 328 usos + 50 de `gray-300` (1,47:1) = **378 falhas WCAG** |
| 🔴 P2 | Falha de carregamento renderiza como lista vazia | 2 de 61 arquivos tratam `isError`; **0 `ErrorBoundary`** |
| 🔴 P3 | Botão de ação sem nome acessível | 151 `title=` contra **15 `aria-label=`** |
| 🟠 P4 | Alvo de toque 33% abaixo do mínimo de campo | `.icon-btn` 32px (mínimo 48px) |
| 🟠 P5 | Escala tipográfica colapsada | 86,5% do texto em 12–14px; **107 tamanhos arbitrários**, até 9px |
| 🟠 P6 | Modais escritos à mão | **14**, perdendo `Esc`, foco, semântica de diálogo |
| 🟠 P7 | Ação destrutiva via `confirm()` nativo | 28 ocorrências em 22 arquivos — suprimível pelo navegador |
| 🟠 P8 | Quatro tipografias de label para o mesmo papel | 173 `<label>` em 4 escalas |
| 🟠 P9 | Tabelas sem resposta para tela estreita | **20 de 28** |
| 🟠 P10 | Contexto e filtro misturados | Só 6 arquivos persistem filtro; nenhum em `cadastros/` |

Ausência de sistema (P11): **12 variantes de padding · 7 raios · 5 sombras · 12 tamanhos
de ícone · 6 larguras de página**.

## 5. Referências de mercado

Do [`02-`](02-market-benchmark.md). **Nível de evidência:** 31 capturas de tela em contas
reais (24 do Farmbox, 7 do Cropwise Protector), mais a documentação pública da API do
Cropwise. O que não foi observado está marcado no documento — comportamento responsivo
real, estado de erro e a tela de alertas do Cropwise.

**Cropwise (Syngenta)** — plataforma modular, 40 mil agricultores, 100 M ha. O achado
decisivo veio da API pública: a hierarquia `Workspace → Organization → Property → Field`
mapeia **exatamente** o `consultoria → cliente → fazenda → talhão` do AgroPlan. E a URL
(`/company/…/property/…/tasks`) mostra que essa hierarquia **é a rota**. Nosso modelo de
dados está certo; o que falta é rotear por ele.

**Farmbox (Checkplant/xFarm)** — agtech brasileira, 4 M ha, offline via app nativo. Trouxe
os melhores padrões observados: **estado vazio que ensina a regra da seção**, contexto em
forma de frase no título da página, **alternador de densidade**, e ação de linha com texto
+ ícone em vez de ícone puro.

**Aegro** — referência mais próxima do nicho: tem produto dedicado a consultor
multi-cliente.

**O que o benchmark descartou:** gestão financeira da fazenda, ciclo produtivo completo,
monitoramento georreferenciado, integração com maquinário. São do produtor, não do
consultor. E **offline-first**: as duas referências entregam via app nativo; para uma SPA
com cookie httpOnly e CSRF, exigiria fila de escrita e resolução de conflito — é projeto
próprio, não item de redesign.

**Onde o AgroPlan já é superior:** modelagem de status (7 estados × 6 representações
contextuais — nada comparável nas referências), três visões sobre os mesmos dados no
calendário, catálogo Agrofit/Bioinsumos integrado, e multi-tenant de consultoria nativo.

## 6. Princípios de UX

Doutrina completa em [`ux-principles.md`](../../.claude/skills/agroplan-design/ux-principles.md).
Os que mais decidem:

1. **Status é o eixo da informação, não decoração.** Diluir esse sinal é regressão
   funcional. O trilho de status significa exatamente *"isto precisa de você"* — por isso
   `prevista` e `desativada` não têm trilho.
2. **Confiabilidade do dado vence densidade visual.** `isError` tem precedência sobre
   `isEmpty`, sempre.
3. **Densidade é aliada, mas é escolha do usuário.** Compacto/Completo, com Compacto
   desativado em `pointer: coarse`.
4. **Contexto ≠ filtro.** Recorta o mundo (cliente/fazenda/safra) → barra de contexto,
   persistente. Recorta a lista (status/cultura/data) → junto da tabela.
5. **Hierarquia vem de estrutura, não de cor:** posição › tamanho › peso › cor. Se algo só
   se distingue por ser mais claro, a hierarquia está errada.
6. **Cor tem significado, não humor.** Densa em significado, escassa em área.
7. **Regra de negócio não é detalhe de implementação.** Cálculo e status são o produto;
   redesign não os altera como efeito colateral.
8. **Microcopy é material de design.** Mesmo verbo do início ao fim; erro usa o `detail` do
   backend; vazio ensina a regra; confirmação diz a consequência.

## 7. Direção visual aprovada

**Painel de Operação** como sistema base, com dois enxertos de escopo fechado:

| Origem | Onde | O que traz |
|---|---|---|
| **C — Painel de Operação** | Todo o produto | Cromo escuro, trilho de status, densidade alternável, tabela como núcleo |
| **A — Receituário** | `RecomendacaoPrintPage` **apenas** | Dado regulado em mono, desenho de documento |
| **B — Carta Agronômica** | Cadastro e seleção de talhões **apenas** | Glifo de polígono como identificador |

**Identidade cromática:** a marca deixa de ser verde. Passa a **azul-marinho `#1E2E4F` +
dourado `#DAA52D`** — instituição e responsabilidade técnica, mais colheita e valor.

Duas restrições saíram da verificação WCAG e moldaram o sistema:

- **O dourado reprova como texto** (2,23:1 sobre branco e com branco). É superfície com
  texto azul (6,03:1) ou acento sobre cromo escuro (7,44:1). **Um por tela, no máximo** —
  reservado à ação de maior consequência.
- **A paleta de status precisou ser reconstruída**: o âmbar colidia com o dourado e o
  azul-céu com o marinho. Agora **recomendação = violeta → magenta**, **aplicação = teal →
  vermelho**. A família diz qual trabalho; a temperatura diz quão urgente.

**Tipografia:** Barlow (interface) · Barlow Condensed (cabeçalho de tabela, versalete) ·
IBM Plex Mono (todo número). Fraunces e Hanken Grotesk saem.

## 8. Design System

Oficial em [`05-design-system.md`](05-design-system.md); versão operacional em
[`design-system.md`](../../.claude/skills/agroplan-design/design-system.md).

**Todo par de cores prescrito foi verificado por WCAG 2.1.** Nenhuma combinação reprova.

- **Cor** — escalas navy, gold e neutros frios. Piso absoluto de texto: `slate-500`
  (5,41:1). Não existe equivalente ao `text-gray-400`.
- **Tipografia** — 6 degraus, **piso de 12px**, nenhum valor arbitrário. 3 pesos, com
  `regular` como padrão de corpo.
- **Espaço** — 7 valores. **Raio** — 4. **Elevação** — **2 níveis**: repouso é borda, sem
  sombra; sombra só em sobreposição. **Ícone** — 4 tamanhos. **Largura de página** — 1.
- **Nenhum gradiente**, exceto scrim sobre imagem de satélite.
- **25 componentes** com contrato TypeScript, mais padrões de domínio para mapas,
  dashboards, calendário, planejamento, tabelas de volume, indicadores e PDF.

Dois contratos usam **o tipo para impedir o erro por construção**: `IconButton` exige
`label`, e `EmptyState` exige `rule`.

**Escala tipográfica:** usa **nomes semânticos próprios** (`text-label` · `text-body-sm` ·
`text-body` · `text-title` · `text-heading` · `text-display`) e **não redefine os tokens do
Tailwind** — `text-sm` continua sendo 14px, e a migração é opt-in, tela a tela. Isso evita
que os 533 usos existentes mudem de tamanho no PR do `@theme`.

**`planStatus`** (rascunho/ativo/concluído/arquivado) usa **badge de contorno**, peso menor
que o `StatusEvento` preenchido + trilho — os dois eixos convivem na mesma linha sem
competir. Contrastes verificados (5,41–9,00:1).

## 9. Componentes

Do [`06-`](06-component-inventory.md), sobre a tela piloto `PlanejamentoListPage`:
3 globais · 4 reutilizáveis · 4 específicos · 3 legados · **6 duplicados**.

**O achado mais grave é uma divergência, não uma duplicação:** `relativeDate()` existe em
três arquivos. Dashboard e Planejamento são byte-idênticas; a do Calendário **inclui o
ano**. A mesma data aparece como `15/02` numa tela e `15/02/2026` noutra.

Outras duplicações que contrariam a intenção declarada no próprio código: `STATUS_OPTIONS`
redeclara os rótulos apesar de `planStatus.ts` se anunciar "fonte única de verdade"; `FF`
reimplementa `FormField`, cujo docstring diz que ele veio justamente para acabar com isso.

E um padrão **triplicado que ninguém tinha notado**: o acordeão agrupado por cliente, em
`PlanejamentoListPage`, `ApListPage` e `ClientesAcessoModal` — três implementações
independentes. É peça que **falta no design system**.

**Diagnóstico de fundo:** a tela piloto usa `PageHeader` e `useStickyState`, mas mantém
modal à mão e `confirm()` nativo. Adotou parte do que existe e parou. Dos 8 padrões antigos
catalogados, **7 já têm substituto pronto no projeto** — o problema quase nunca é ausência
de componente, é adoção.

## 10. O que NÃO deve mudar

Inegociável. Alinhado ao [`CLAUDE.md`](../../CLAUDE.md) da raiz.

- **Lógica de negócio** — cálculo de dose, área, volume e calda. Se parecer errado,
  **reportar, não corrigir de passagem**.
- **Regras de status** — o ciclo de vida de evento e aplicação, e a ordem de prioridade.
  Muda a cor, **nunca a semântica**.
- **Contratos de API** — os schemas Pydantic são o contrato com o frontend, e não há
  checagem cruzando as duas linguagens.
- **Banco de dados** — nenhuma migration, nenhuma alteração de schema.
- **Permissões e multi-tenant** — isolamento por `consultoria_id`, segregação de rotas
  (`public`/`protected`/`integration`), autenticação por cookie httpOnly + CSRF.
- **Fluxo funcional** — as 33 rotas e o que cada tela faz, salvo mudanças explicitamente
  planejadas (a barra de contexto e a rota própria de talhão são as únicas previstas).
- **Integrações** — sync Agrofit/Bioinsumos, Z-API/n8n, Google Maps.
- **`lib/tipoCores.ts`** — inalterado por decisão do dono.

## 11. Escopo da Fase 2

Ordem de dependência do [`05-` §42](05-design-system.md). Cada etapa é um PR.

**Bloco A — fundação, na paleta atual.** Não depende de decisão de design e entrega a maior
parte do ganho de usabilidade **antes** de qualquer risco cromático.

1. `ErrorBoundary` + `onError` global no `QueryCache`
2. `text-gray-400`/`300` → piso legível *(mecânico, ~378 ocorrências)*
3. `QueryState`, `EmptyState`, `Skeleton` nas telas de maior tráfego
4. Escala tipográfica e de espaçamento

**Bloco B — a troca de identidade.**

5. **Troca atômica de tokens** — um PR só, tocando `index.css` + `statusCores.ts` (+
   `planStatus.ts`, ver §8). Único passo de alto risco, e o único que exige plano de
   reversão explícito.

**Bloco C — componentes e adoção.**

6. `Button`, `IconButton`, `Input`, `Select`, `Checkbox`, `Radio`, `Switch`
7. `PageHeader`, `Breadcrumb`, `Card`, `Badge`, `Alert`, `Toast`, `Tooltip`
8. `Table` + `Pagination` + prioridade de coluna
9. `Sidebar` + `ContextBar` + navegação inferior
10. `Modal`, `Drawer`, `Dropdown`, `Tabs`
11. `KPI` + `FilterBar` + dashboard
12. **Gabarito de CRUD provado em `BicosPage`** — 150 linhas, a menor das 14, e concentra
    quase todos os problemas catalogados. Se fecha nela, fecha nas outras 13
13. Demais 13 CRUDs
14. Calendário e planejamento sob o novo sistema
15. Enxertos A (PDF) e B (`TalhaoGlyph`)

**Antes de tudo isso**, cabe um PR de limpeza da tela piloto (itens 1–5 do
[`06-` §4](06-component-inventory.md)): arquivo único cada, mecânicos, e um deles corrige a
divergência de `relativeDate` visível ao usuário.

## 12. Fora do escopo

Deliberado. Cada item tem motivo registrado.

| Item | Motivo |
|---|---|
| **Offline-first** | As referências entregam via app nativo. Para nossa SPA exigiria fila de escrita e resolução de conflito — projeto próprio |
| **Modo escuro** | A base existe (neutros frios + cromo), mas exige passe próprio |
| **Logotipo e marca gráfica** | A identidade cromática mudou; a marca "AgroConsultoria" não foi tratada |
| **Unificar paletas de status e de tipo de produto** | Decisão do dono: não incomoda na prática |
| **Virtualização de tabela** | Exige dependência nova — decisão pendente do dono |
| **Camada `lib/queries/`** | Dívida arquitetural real (256 chamadas soltas, 267 `queryKey` literais), mas é arquitetura, não design |
| **Geração de PDF no servidor** | Hoje é impressão do navegador; não se propõe mudar |
| **Monitoramento georreferenciado** | Módulo de coleta que o produto não tem — sem ele, mapa de calor não tem dado |
| **ESLint / Prettier / testes de frontend** | Recomendável, mas é ferramental, não redesign |

## 13. Critérios de sucesso

Verificáveis, não subjetivos. Nenhuma etapa é considerada pronta sem passar.

**Acessibilidade e legibilidade**
- Nenhum texto abaixo de **4,5:1**; nenhuma borda de controle abaixo de **3:1**
- `grep -r "text-gray-400\|text-gray-300" src` → vazio
- `grep -rE "\[[0-9]+px\]" src --include="*.tsx"` → vazio
- Todo `IconButton` com `label`; um `<h1>` por rota; foco visível em todo focável

**Responsividade**
- Nenhum alvo interativo abaixo de 36px; nenhum abaixo de **48px** em `pointer: coarse`
- Nenhuma tabela dependendo só de `overflow-x-auto`
- Navegação utilizável com uma mão em tela estreita

**Consistência**
- Uma escala de tipografia, espaço, raio, sombra e ícone — sem exceção
- Zero modais escritos à mão; zero `confirm()` nativo
- Zero `<svg>` inline onde `lucide` serve

**Preservação funcional** *(o mais importante)*
- Nenhuma mudança de comportamento de cálculo
- Nenhuma mudança de schema, migration ou contrato de API
- As 33 rotas continuam existindo e fazendo o que faziam
- Suíte do backend (27 arquivos) passando
- `npm run build` limpo — **único gate automático do projeto**

**Confiabilidade percebida**
- Nenhuma `useQuery` visível fora de `QueryState`
- Falha de rede nunca se parece com lista vazia
- Todo estado vazio explica a regra da seção

---

## Como usar este documento

- **Entrando no projeto agora?** Leia este brief inteiro, depois [`03-`](03-current-ui-audit.md)
  (o que está errado) e [`05-`](05-design-system.md) (o que construir).
- **Vai implementar?** [`05-` §42](05-design-system.md) é a ordem; a skill
  [`agroplan-design`](../../.claude/skills/agroplan-design/) é a referência de consulta.
- **Vai propor algo novo?** [`ux-principles.md`](../../.claude/skills/agroplan-design/ux-principles.md)
  resolve a maioria das ambiguidades antes de virar discussão.

⚠️ **Lembrete permanente:** o design system descreve o produto **aprovado**, não o que está
no código. Os tokens `navy-*` e `gold-*` **ainda não existem** — escrevê-los hoje falha em
silêncio, porque o Tailwind 4 não gera classe para token não declarado. Ao alterar código
existente, confirme em `Agro-Plataform/frontend/src/index.css`.
