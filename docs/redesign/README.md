# docs/redesign/

Documentação do redesign do AgroPlan. Fica na raiz do `AgroPlan`, **fora** do repositório
git do `Agro-Plataform` — são documentos de trabalho, não artefatos do produto, e não
viram commit/PR sem pedido explícito (ver [`CLAUDE.md`](../../CLAUDE.md) da raiz).

## Comece por aqui

**[`00-redesign-brief.md`](00-redesign-brief.md)** — consolida a série inteira. Objetivo,
público, problemas prioritários, direção aprovada, escopo da Fase 2 e critérios de sucesso.

## A série

| # | Documento | Responde |
|---|---|---|
| [00](00-redesign-brief.md) | Brief | Por que, para quem, o que muda e o que não muda |
| [01](01-product-audit.md) | Auditoria de produto | Arquitetura, rotas, fluxos, prioridade das telas |
| [02](02-market-benchmark.md) | Benchmark | Cropwise e Farmbox observados; o que adotar, adaptar, evitar |
| [03](03-current-ui-audit.md) | Auditoria de UI/UX | 21 problemas medidos, com gravidade e recomendação |
| [04](04-visual-directions.md) | Especificação visual | Identidade aprovada: azul-marinho + dourado |
| [05](05-design-system.md) | Design system | Tokens, 25 componentes, padrões de domínio |
| [06](06-component-inventory.md) | Inventário da tela piloto | Classificação, duplicações, refatorações |

## Diretórios

- **[`assets/`](assets/)** — capturas de tela das plataformas de referência (Cropwise,
  Farmbox), base observacional do `02-`.
- **[`prototypes/`](prototypes/)** — protótipos HTML isolados para validação visual.
  `telas-redesign.html` é o atual (5 telas); `planejamento.html` foi superado.

## Skills

A referência de consulta durante a implementação vive em
[`.claude/skills/agroplan-design/`](../../.claude/skills/agroplan-design/):
`design-system.md` (tokens e componentes), `ux-principles.md` (doutrina),
`ui-patterns.md` (23 padrões).

⚠️ O design system descreve o produto **aprovado**, não o que está no código. Os tokens
`navy-*` e `gold-*` ainda não existem.

## Convenção de nomes

Série numerada, na ordem em que o trabalho acontece: `NN-<assunto>.md`. Documentos
pontuais fora da sequência usam prefixo próprio (`screen-<rota>.md`).

Cada arquivo abre com data e branch analisados.
[`analise-frontend.md`](../../analise-frontend.md) (raiz) continua sendo a análise de
baseline de **qualidade de código**; o `03-` é a de **produto/UI**. Não duplicar:
referenciar.
