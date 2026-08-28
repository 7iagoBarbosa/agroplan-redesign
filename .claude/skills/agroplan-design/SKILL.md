---
name: agroplan-design
description: Design system real do AgroPlan (Agro-Plataform) — tokens de cor/tipografia, paletas de status e de tipo de produto, e inventário de componentes compartilhados. Use sempre que for propor, avaliar ou construir UI para este produto especificamente, para não inventar tokens que não existem ou contradizer os que já existem sem avisar.
---

# AgroPlan Design

Sistema de design de um produto de **planejamento de manejo agrícola** (defensivos,
por cliente → fazenda → talhão → safra), multi-tenant por consultoria, com telas de
calendário/kanban de aplicações, recomendação técnica e relatórios. Público: agrônomos
e consultorias agrícolas, uso majoritariamente em escritório hoje (ver
[ux-principles.md](ux-principles.md) sobre a lacuna de uso em campo).

## Como usar esta skill

1. **[design-system.md](design-system.md)** — tokens e componentes. Cor, tipografia,
   espaçamento, raio, borda, sombra, ícone, dimensão; contrato dos 25 componentes; e a
   **Parte D**, que documenta o que existe no código hoje mais o mapa de migração.
   ⚠️ Contém dois sistemas: o aprovado (azul + dourado, **não implementado**) e o atual
   (verde, em produção). Ler o aviso no topo antes de usar qualquer valor.
2. **[ux-principles.md](ux-principles.md)** — a doutrina: quem é o usuário e que pergunta
   ele faz, por que status é o eixo da informação, contexto ≠ filtro, hierarquia por
   estrutura e não por cor, piso de acessibilidade, microcopy. Resolve ambiguidade quando
   dois caminhos parecem válidos.
3. **[ui-patterns.md](ui-patterns.md)** — catálogo dos **23 padrões oficiais** (App Shell,
   sidebar, seleção de cliente/fazenda/safra, CRUD, tabelas, calendário, mapas, estados…),
   cada um com objetivo, estrutura, comportamento, hierarquia, componentes, e quando usar
   ou evitar.

## Regra de ouro

Isto é sistema de **produção**, com dado real em `Agro-Plataform/frontend/src/`. Sempre
que uma proposta de redesign for além do que está documentado aqui, dizer explicitamente
"isto é um token/padrão novo, não existe hoje" — nunca apresentar como se já fizesse
parte do sistema. Propor evolução do sistema é legítimo (é o papel de
`/redesign-system`), mas é uma decisão consciente, não um acidente de não ter checado.

Para critério estético (como combinar tipografia, quando arriscar, como escrever
microcopy em geral), usar em conjunto [`frontend-design`](../frontend-design/SKILL.md).
Esta skill aqui é a fonte da verdade de *o que já existe*; aquela é o critério de *como
usar bem*.

## Fora de escopo

Esta skill não decide se uma mudança deve ser aplicada ao código — isso é
`/redesign-screen` ou `/redesign-system`, e passa pelas regras de branch/PR do
`CLAUDE.md` da raiz sempre que toca `Agro-Plataform/`.
