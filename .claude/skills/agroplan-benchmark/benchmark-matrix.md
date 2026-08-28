# Matriz de comparação

> Coluna **AgroPlan** preenchida com base no código real (28/08/2026, ver
> `agroplan-design`). Colunas de concorrentes são placeholder — preencher só depois de
> pesquisa real (ver [competitors.md](competitors.md)). Não inventar valor de
> concorrente "por plausibilidade" — deixar `?` até verificar.

| Dimensão | AgroPlan (hoje) | Aegro | Strider | Solinftec |
|---|---|---|---|---|
| Navegação principal | Sidebar fixa colapsável, agrupada por seção, desktop-only | ? | ? | ? |
| Visualização de status/pendência | Paleta de 7 estados com 6 representações por contexto (tabela/kanban/legenda) — ver `design-system.md` | ? | ? | ? |
| Uso em campo / mobile | Sem menu mobile, poucos breakpoints — lacuna aberta (`ux-principles.md` item 5) | ? | ? | ? |
| Fluxo recomendação → aplicação | Módulo dedicado (`/calendario/aps`), status derivado do evento | ? | ? | ? |
| Multi-tenant (consultoria → clientes) | Sim, por `consultoria_id`, não visível na UI (implícito) | ? | ? | ? |
| Catálogo de produto | Sync automático com Agrofit/Bioinsumos (Embrapa) no startup | ? | ? | ? |
| Relatórios | Geral por fazenda + manejo por talhão, com utilitário de impressão (`.no-print`) | ? | ? | ? |
| Confirmação de ação destrutiva | `ConfirmDialog` disponível; parte do código ainda usa `confirm()` nativo (gap conhecido) | ? | ? | ? |
| Acessibilidade (nome em botão de ícone) | Parcial — maioria usa `title=`, poucos `aria-label` (gap conhecido) | ? | ? | ? |

## Como usar esta matriz numa auditoria

- Se uma linha do AgroPlan aponta um gap conhecido (mobile, acessibilidade), não é
  benchmark que decide se vale corrigir — é `ux-principles.md`/`agroplan-ui-review` que
  já sinalizam isso independentemente de concorrência.
- Onde vale mesmo o benchmark: para desempatar uma escolha de design onde o time está
  em dúvida se um padrão é "esperado pelo usuário vindo de outra ferramenta" ou é
  realmente uma escolha nova a assumir com risco.
