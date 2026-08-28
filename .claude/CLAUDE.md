# Toolkit de redesign — AgroPlan

Este `.claude/` contém as skills e comandos usados para auditar e redesenhar a UI do
`Agro-Plataform` (frontend em `../Agro-Plataform/frontend/`). Não substitui o
`CLAUDE.md` da raiz — as prioridades e o fluxo de trabalho de lá (estabilidade,
compatibilidade, mudanças mínimas, branch própria + PR para qualquer alteração em
código do app) continuam valendo integralmente sempre que uma skill ou comando daqui
propõe ou aplica uma mudança em `Agro-Plataform/`.

**Distinção importante de escopo:** este diretório e `../docs/redesign/` vivem na raiz
do `AgroPlan`, que **não é um repositório git**. Editar os arquivos `.md` daqui (tokens,
notas de benchmark, relatórios de auditoria) não exige branch nem PR. Só passa a exigir
quando o resultado é aplicado dentro de `Agro-Plataform/` — aí é código de um repositório
de terceiro, e valem todas as regras da raiz: branch a partir de `develop`, PR para
`main`, nunca push direto, nunca commit/PR sem pedido explícito do dono.

## O que tem aqui

- **`skills/frontend-design/`** — cópia local da skill genérica de design visual
  (mesma do plugin `frontend-design`, Apache-2.0). Critério estético geral, não
  amarrado a este produto.
- **`skills/agroplan-design/`** — o design system real do AgroPlan: tokens de cor/tipografia
  extraídos de `index.css`, paletas de status e tipo de produto, inventário de componentes
  compartilhados. Fonte de verdade sempre que uma proposta visual precisa "parecer AgroPlan".
- **`skills/agroplan-benchmark/`** — como o AgroPlan se compara a outras plataformas de
  planejamento/manejo agrícola. Conteúdo inicial é conhecimento geral não verificado —
  tratar como ponto de partida a validar, não como pesquisa de mercado concluída.
- **`skills/agroplan-ui-review/`** — checklist de revisão: usa `agroplan-design` como régua
  para apontar onde uma tela desvia do sistema (cor fora da paleta, modal reinventado,
  botão sem `aria-label`, tabela sem scroll horizontal, etc.).
- **`commands/`** — `/redesign-audit`, `/redesign-system`, `/redesign-screen`: orquestram
  as skills acima. Detalhes de cada um no próprio arquivo do comando.

## Regra geral para as skills e comandos daqui

- Auditoria e diagnóstico (`/redesign-audit`, `agroplan-ui-review`) são **somente leitura**
  por padrão — produzem relatório em `../docs/redesign/`, não editam `Agro-Plataform/`.
- Qualquer proposta que implique mudar código do frontend precisa: (1) ser apresentada
  como plano antes de aplicar, (2) respeitar os tokens existentes em `agroplan-design`
  a menos que a mudança de token seja o próprio pedido, (3) seguir o fluxo de branch/PR
  da raiz antes de qualquer commit.
- `analise-frontend.md` (raiz do `AgroPlan`) é a auditoria de baseline (23/08/2026, commit
  `f071e5c`). É só leitura estática — números de adoção lá podem estar defasados, já que
  a branch atual (`feature/recomendacao-criar-e-finalizar`) tem mudanças em andamento,
  incluindo em `Modal.tsx`. Antes de citar um número de lá como atual, reconferir no código.
