# AgroPlan — Redesign

Documentação, decisões e ferramentas do redesign do **AgroPlan** (frontend do
[Agro-Plataform](https://github.com/hidekimva/Agro-Plataform)).

Este repositório contém **apenas documentação e configuração de agente** — nenhum código
da aplicação. O produto vive em repositório separado, de terceiro, e não é modificado por
nada daqui.

## Comece por aqui

1. **[HANDOFF.md](HANDOFF.md)** — estado do trabalho: o que está feito, o que está em
   andamento, armadilhas conhecidas e como continuar em outra máquina.
2. **[CLAUDE.md](CLAUDE.md)** — regras do projeto, lidas automaticamente pelo Claude Code.
3. **[docs/redesign/00-redesign-brief.md](docs/redesign/00-redesign-brief.md)** — o brief
   do redesign: objetivo, público, escopo e critérios de sucesso.

## Conteúdo

| | |
|---|---|
| [`docs/redesign/`](docs/redesign/) | Série 00–08: brief, auditorias, benchmark, direção visual, design system, inventário de componentes e revisão de fechamento |
| [`.claude/skills/`](.claude/skills/) | `agroplan-design` (tokens, 24 padrões, doutrina de UX), `agroplan-ui-review`, `agroplan-benchmark`, `frontend-design` |
| [`.claude/commands/`](.claude/commands/) | `/redesign-audit`, `/redesign-system`, `/redesign-screen` |
| [`docs/redesign/prototypes/`](docs/redesign/prototypes/) | Protótipos HTML autocontidos, para abrir no navegador |

## Estado

**Fase 1 encerrada** (28/08/2026) — inteiramente documental, nenhuma linha de código da
aplicação alterada. As 15 etapas da Fase 2 estão desbloqueadas; o plano de execução
(`07-implementation-plan.md`) é o próximo documento.

## Nota

As capturas das plataformas de referência não são versionadas: contêm dados reais de
clientes. O [`02-market-benchmark.md`](docs/redesign/02-market-benchmark.md) já registra em
texto tudo o que foi observado.
