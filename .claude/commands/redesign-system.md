---
description: Atualiza os arquivos de agroplan-design (tokens/padrões documentados) a partir do estado real do código, ou propõe uma evolução consciente do design system. Nunca edita Agro-Plataform/ diretamente.
---

# /redesign-system

Dois modos, conforme `$ARGUMENTS`:

## Modo 1 — sincronizar (sem argumento, ou "sincronizar"/"atualizar")

Os arquivos de `agroplan-design` (`design-system.md`, `ux-principles.md`,
`ui-patterns.md`) podem ficar defasados conforme o código muda. Este modo relê as
fontes reais e reconcilia:

1. Reler `Agro-Plataform/frontend/src/index.css`, `lib/statusCores.ts`,
   `lib/tipoCores.ts`, e os componentes em `components/` citados em `ui-patterns.md`.
2. Comparar com o que está documentado em `.claude/skills/agroplan-design/`. Para cada
   divergência (token novo, token removido, componente que mudou de comportamento —
   como já aconteceu com `Modal.tsx` ganhando `role="dialog"`), atualizar o `.md`
   correspondente.
3. Não reescrever o arquivo inteiro por qualquer mudança pequena — editar só a seção
   afetada, preservando o resto (mesmo princípio de "mudança mínima" do `CLAUDE.md`
   raiz, aplicado aqui à documentação).
4. Atualizar a data/branch citada no topo de `design-system.md` e `ui-patterns.md`.

## Modo 2 — propor evolução (`$ARGUMENTS` descreve uma mudança de token/padrão)

Ex.: `/redesign-system adicionar uma paleta de status para "cancelada"` ou
`/redesign-system unificar a paleta de tipo de produto com a de status`.

1. Carregar `agroplan-design` + `frontend-design` (critério estético) +, se a mudança
   for motivada por comparação de mercado, `agroplan-benchmark`.
2. Produzir uma proposta: o que muda, por que, e o que **não** muda (tokens existentes
   que continuam valendo). Se afetar cor de status, considerar explicitamente o
   princípio de `ux-principles.md` item 1 (status é o eixo central da informação —
   qualquer mudança aí precisa preservar ou melhorar a escaneabilidade, nunca só
   "ficar mais bonito").
3. **Não aplicar a mudança em `Agro-Plataform/frontend/src/` nesta etapa.** Apresentar
   a proposta (pode incluir os diffs pretendidos como preview) e esperar confirmação —
   é exatamente o tipo de mudança que toca arquitetura visual compartilhada, que o
   `CLAUDE.md` raiz pede para explicar antes de aplicar.
4. Só depois de confirmado: aplicar no código real seguindo o fluxo normal (branch a
   partir de `develop`, PR para `main`), e então rodar o Modo 1 deste comando para
   sincronizar a documentação com o novo estado.
