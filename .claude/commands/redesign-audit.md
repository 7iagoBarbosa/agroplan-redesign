---
description: Audita o frontend do AgroPlan (tudo ou uma área) contra o design system real, produz relatório em docs/redesign/. Somente leitura — não edita Agro-Plataform/.
---

# /redesign-audit

Argumento opcional: `$ARGUMENTS` — uma área/rota/diretório para focar (ex.:
`calendario`, `PlanejamentoListPage`). Sem argumento, audita o frontend inteiro.

## Passos

1. Carregar as skills [`agroplan-design`](../skills/agroplan-design/SKILL.md) e
   [`agroplan-ui-review`](../skills/agroplan-ui-review/SKILL.md). Se o pedido envolver
   comparação com concorrentes, carregar também
   [`agroplan-benchmark`](../skills/agroplan-benchmark/SKILL.md).
2. Ler `analise-frontend.md` (raiz do `AgroPlan`) como baseline — não repetir o trabalho
   que já está lá, mas **revalidar** os números antes de reafirmá-los (a branch atual já
   mudou pelo menos `Modal.tsx`; ver nota em `ui-patterns.md`). Usar `Grep`/`Glob` para
   recontar, não confiar cegamente na data de 23/08.
3. Rodar o checklist de `agroplan-ui-review` no escopo definido (todo o
   `frontend/src/` ou só `$ARGUMENTS`). Priorizar por impacto real no usuário
   (agrônomo decidindo o que aplicar), não por volume de ocorrências.
4. Escrever o relatório em `docs/redesign/audit-<AAAA-MM-DD>.md` (ver convenção em
   [`docs/redesign/README.md`](../../docs/redesign/README.md)): abrir com data e
   branch/commit analisados, agrupar achados por prioridade (alta/média/baixa, mesmo
   formato de `analise-frontend.md`), e — quando relevante — fechar com uma seção
   "perguntas em aberto para o dono" (ex.: uso em campo/mobile, se vale unificar as
   paletas de status e tipo de produto).

## O que este comando NÃO faz

- Não edita nada em `Agro-Plataform/`. É diagnóstico, não correção.
- Não decide sozinho o que priorizar num PR futuro — isso é decisão do dono do repo.
- Se o usuário pedir para já corrigir algo encontrado, isso vira um pedido novo, com
  branch própria a partir de `develop` e PR — nunca commit direto a partir de uma
  auditoria.
