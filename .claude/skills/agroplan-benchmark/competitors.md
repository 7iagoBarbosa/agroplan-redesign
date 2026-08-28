# Concorrentes / referências candidatos

> **Não verificado.** Lista de conhecimento geral, sem pesquisa ao vivo — ver aviso em
> [SKILL.md](SKILL.md). Confirmar existência, nome atual e produto antes de usar.

## Contexto do AgroPlan para calibrar a comparação

Nicho específico: ferramenta de **planejamento e recomendação técnica de manejo de
defensivos**, usada por uma **consultoria agronômica** que atende múltiplos clientes
(fazendas/talhões), com fluxo recomendação → aplicação → relatório de demanda de
compra, e catálogo de produtos sincronizado com fontes públicas (Agrofit/Bioinsumos,
Embrapa). Isso é mais estreito que "agricultura de precisão" em geral — plataformas de
sensor/imagem/maquinário não são o comparável mais direto, mesmo que apareçam nas
mesmas buscas de mercado.

## Candidatos mais próximos (planejamento/manejo, mercado brasileiro)

- **Aegro** — gestão agrícola para produtor/fazenda no Brasil; módulo de manejo e
  aplicações. Comparável mais direto em geografia e idioma.
- **Strider** — recomendação/monitoramento agronômico no Brasil, historicamente ligado
  a consultoria técnica de campo. Possivelmente o concorrente mais próximo do nicho
  específico (consultoria → recomendação → aplicação).
- **Solinftec** — plataforma de operações agrícolas brasileira, escopo mais amplo
  (inclui monitoramento por sensor/robótica) — referência para "onde o mercado está
  indo", não necessariamente comparável direto de UX no módulo de manejo.
- **AgroTools** — inteligência de dados agrícolas no Brasil; possivelmente mais
  analytics/dado que planejamento operacional.

## Candidatos mais distantes (mercado global, escopo mais amplo)

- **Climate FieldView** (Bayer) — agricultura de precisão, forte em dado de campo via
  sensor/imagem; UX de referência para visualização de talhão/mapa, menos para o fluxo
  de recomendação técnica por consultoria.
- **Granular** (Corteva) — gestão financeira/operacional de fazenda, mercado americano.
- **xarvio Field Manager** (BASF) — recomendação agronômica com forte componente de
  modelagem/previsão, mercado global.
- **CropX** — sensoriamento de solo, escopo mais estreito (irrigação/solo).

## Como preencher isto de verdade

1. Confirmar com o usuário (dono do produto) quem ele já considera concorrente direto
   — provavelmente sabe melhor que qualquer busca genérica.
2. Para cada um confirmado, usar `WebSearch`/`WebFetch` para capturar: proposta de
   valor atual, se tem app mobile, como visualiza status/pendências, se atende
   consultoria multi-cliente ou só produtor único.
3. Atualizar [benchmark-matrix.md](benchmark-matrix.md) com o que for encontrado,
   marcando cada célula como verificada (com data) ou como suposição.
