---
name: agroplan-benchmark
description: Como o AgroPlan se compara a outras plataformas de planejamento/manejo agrícola. Use antes de uma iniciativa de redesign maior, ou quando precisar checar se um padrão de UI proposto é comum na categoria ou é realmente novo. Conteúdo inicial é conhecimento geral não verificado — tratar como ponto de partida, sempre validar antes de usar como base de decisão.
---

# AgroPlan Benchmark

## Estado deste conteúdo — leia antes de usar

[`competitors.md`](competitors.md) e [`benchmark-matrix.md`](benchmark-matrix.md) foram
criados a partir de conhecimento geral sobre o setor de agtech, **sem pesquisa ao vivo**
(sem navegar nos produtos concorrentes, sem capturas de tela, sem checar se as empresas
listadas ainda existem com esse nome/produto). É um esqueleto para organizar uma
comparação futura, não uma análise competitiva concluída.

Antes de basear qualquer decisão de produto nisso: usar `WebSearch`/`WebFetch` para
validar cada concorrente listado (o produto ainda existe? o UX descrito ainda é assim?),
ou pedir ao usuário confirmação de quais concorrentes realmente importam para a
consultoria dona do AgroPlan. **Nunca apresentar os dados destes arquivos como
verificados** sem essa validação — se for citar algo de lá numa auditoria, marcar
explicitamente como "não verificado" a menos que tenha sido confirmado nesta sessão.

## Como usar

1. **[competitors.md](competitors.md)** — lista de candidatos a concorrente/referência,
   por que cada um é relevante (ou não) para o nicho específico do AgroPlan
   (planejamento de manejo por consultoria agronômica, não agricultura de precisão via
   sensor/imagem).
2. **[benchmark-matrix.md](benchmark-matrix.md)** — matriz de comparação por dimensão de
   UX (navegação, visualização de status/fluxo, uso em campo, catálogo de produto,
   fluxo recomendação→aplicação, multi-tenant, relatórios). Coluna do AgroPlan já
   preenchida com base no código real (`agroplan-design`); colunas de concorrentes
   ficam como placeholder até serem pesquisadas.

## Quando isto é útil

- Antes de `/redesign-screen` numa tela onde não está claro se um padrão é "óbvio na
  categoria" ou uma escolha arriscada — checar a matriz primeiro.
- Como insumo de `/redesign-audit` quando o pedido for amplo ("como estamos comparado
  ao mercado"), não só interno.
- **Não é necessário** para a maioria das tarefas de redesign pontuais — a fonte de
  verdade do dia a dia é `agroplan-design`, não este benchmark.
