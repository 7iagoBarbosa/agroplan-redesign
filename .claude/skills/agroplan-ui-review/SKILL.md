---
name: agroplan-ui-review
description: Revisa uma tela, componente ou diff do frontend do AgroPlan contra o design system real do produto (agroplan-design) — aponta onde o código diverge dos tokens, componentes ou padrões já existentes. Use ao revisar um PR de UI, antes de considerar uma tela "pronta" num redesign, ou como parte de /redesign-audit.
---

# AgroPlan UI Review

Checklist de conformidade com [`agroplan-design`](../agroplan-design/SKILL.md). Isto é
**revisão de aderência ao sistema existente**, não revisão de gosto estético (para
crítica de qualidade visual, usar [`frontend-design`](../frontend-design/SKILL.md)) e
não é busca de bug funcional (isso é o subagente `code-reviewer`/`/code-review`).

## Postura

Somente leitura por padrão: aponta divergências, não corrige sozinho. Se o usuário
pedir para aplicar as correções, elas ainda passam pelas regras do `CLAUDE.md` da raiz
(mudança mínima, branch própria, PR) — ver [`../../CLAUDE.md`](../../CLAUDE.md) deste
toolkit. Prioridade da revisão segue a mesma ordem do `CLAUDE.md` raiz: nada que quebre
o que já funciona, depois compatibilidade, depois o resto.

## Checklist

Para cada arquivo `.tsx` sob revisão, checar contra `design-system.md` e
`ui-patterns.md`:

**Cor e tipografia**
- [ ] Cores usadas são tokens de `design-system.md` (`brand-*`, `accent`, `gold`,
      `canvas`, ou uma das paletas de status/tipo) — não um hex/Tailwind-color solto
      sem relação com o sistema.
- [ ] `font-display`/`Fraunces` só em título de página ou elemento equivalente — não em
      corpo de texto ou label.
- [ ] Coluna numérica (dose, área, volume, quantidade) usa `.tnum`.
- [ ] Se o componente mostra `StatusEvento`/`StatusAp`, usa a família de classe certa
      para o contexto (tabela vs. kanban vs. legenda) — não uma cor inventada.

**Componentes compartilhados**
- [ ] Modal usa `<Modal>` (`components/Modal.tsx`), não um `fixed inset-0` escrito à
      mão.
- [ ] Cabeçalho de página usa `<PageHeader>`, não um `<h1>` solto.
- [ ] Campo de formulário usa `<FormField>` para label+erro, não uma estrutura própria.
- [ ] Input/select/textarea usa `className="input"`, não a string de classes Tailwind
      equivalente reescrita.
- [ ] Ação destrutiva usa `<ConfirmDialog>`, não `window.confirm()`/`window.alert()`.
- [ ] Seleção com busca usa `SearchableSelect`/`CultivarSelect`/`ProdutoSearchSelect`
      conforme o domínio, não um `<select>` nativo com filtro reimplementado.

**Estado e feedback**
- [ ] `useQuery` trata `isError` visivelmente (não só `isLoading`) — uma falha de rede
      não pode renderizar como lista vazia (ver `ux-principles.md` item 3).
- [ ] Estado vazio (sem erro, mas sem dados) tem mensagem própria, distinta de erro e
      de carregando.
- [ ] Toast de sucesso usa o mesmo verbo do botão que disparou a ação (`ux-principles.md`
      item 7).
- [ ] Mensagem de erro usa `detail` do backend quando disponível, não só um texto
      genérico fixo.

**Acessibilidade**
- [ ] Botão só-com-ícone tem `aria-label` (não só `title`).
- [ ] Modal aberto tem `role="dialog"`/`aria-modal`/`aria-labelledby` e devolve foco ao
      fechar — já é o padrão do `<Modal>` compartilhado; só relevante se o diff evitou
      esse componente.

**Responsividade**
- [ ] Tabela larga tem `overflow-x-auto` no container.
- [ ] Se a tela é nova, considerar ao menos um breakpoint — mas não é bloqueante sozinho
      dado que "desktop-only" pode ser decisão intencional ainda não confirmada
      (`ux-principles.md` item 5); sinalizar, não recusar por conta disso.

## Formato de saída

Agrupar achados por prioridade, no mesmo espírito de `analise-frontend.md`: o que
quebra/confunde o usuário primeiro, o que é dívida silenciosa depois. Para cada item:
arquivo, o que diverge, e o token/componente que deveria ter sido usado no lugar — não
basta dizer "está errado", apontar o padrão correto de `agroplan-design`.
