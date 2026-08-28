---
description: Propõe (e, só com confirmação, aplica) o redesign de uma tela específica do AgroPlan, respeitando o design system existente e o fluxo de branch/PR da raiz.
---

# /redesign-screen

Argumento obrigatório: `$ARGUMENTS` — a tela/rota/componente alvo (ex.:
`PlanejamentoListPage`, `/calendario/aps`, `ItemManejoFormModal`).

## Passos

1. Carregar [`agroplan-design`](../skills/agroplan-design/SKILL.md) (tokens/padrões
   existentes), [`frontend-design`](../skills/frontend-design/SKILL.md) (critério
   estético) e [`agroplan-ui-review`](../skills/agroplan-ui-review/SKILL.md) (checklist
   de conformidade). Carregar `agroplan-benchmark` só se a tela em questão for
   candidata a comparação com concorrente (ex.: fluxo de navegação principal).
2. Ler o componente/tela alvo por completo, e os componentes compartilhados que ele já
   usa ou deveria usar (ver `ui-patterns.md`). Rodar mentalmente o checklist de
   `agroplan-ui-review` nele antes de propor qualquer coisa nova.
3. Montar a proposta:
   - O que muda e por quê, amarrado a um princípio concreto de `ux-principles.md` ou a
     um gap real encontrado no passo 2 — não mudar por mudar.
   - Todo token de cor/tipografia usado na proposta deve vir de `design-system.md`. Se
     a proposta exigir um token novo, dizer isso explicitamente e sugerir rodar
     `/redesign-system` primeiro (não inventar um token só para esta tela).
   - Preferir reduzir divergência (ex.: migrar para `<Modal>`/`<PageHeader>`/`.input`)
     antes de adicionar camada nova.
4. Apresentar a proposta ao usuário antes de tocar em código — igual a qualquer outra
   mudança de UI, conforme `CLAUDE.md` raiz ("explicar o impacto da mudança antes de
   aplicá-la, quando ela tocar algo compartilhado"). Isso vale em dobro aqui: um
   redesign de tela quase sempre toca componente compartilhado.
5. Só com confirmação: aplicar como mudança mínima (menor diff que atinge o resultado
   proposto), numa branch própria a partir de `develop` (`feat/redesign-<tela>` ou
   similar), nunca direto em `main`/`master`. PR ao final, não commit solto.
6. Opcional: salvar a proposta em `docs/redesign/screen-<nome>.md` antes de aplicar —
   útil quando a mudança é grande o suficiente para valer registro (ver convenção em
   [`docs/redesign/README.md`](../../docs/redesign/README.md)). Para um ajuste pequeno
   e óbvio, não é necessário criar o arquivo.

## Limites

- Não redesenhar telas fora do escopo pedido "de passagem" — se notar um problema em
  outra tela durante o trabalho, apontar ao usuário em vez de consertar sem avisar
  (mesma regra do `CLAUDE.md` raiz sobre não "corrigir" código não relacionado).
- Não introduzir componente/biblioteca nova sem perguntar antes — isso conta como
  dependência nova.
