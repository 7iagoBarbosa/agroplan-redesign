# Princípios de UX do AgroPlan

Doutrina. Responde **por que** os padrões são como são e resolve ambiguidade quando dois
caminhos parecem válidos. O catálogo dos padrões está em
[`ui-patterns.md`](ui-patterns.md); os tokens e contratos em
[`design-system.md`](design-system.md).

> **Estado:** estes princípios descrevem o produto aprovado. Parte já é verdade no código
> (paleta de status, `.tnum`, sidebar colapsável); parte é alvo. Ao alterar código
> existente, confirmar em [`design-system.md`](design-system.md) Parte D.

---

## 1. O usuário e a pergunta que ele faz

Agrônomo de **consultoria**, atendendo dezenas de clientes. Não é o produtor rural — não
fecha o caixa da fazenda, não opera máquina, não faz monitoramento georreferenciado.

A pergunta que ele faz ao abrir o produto não é *"como está esta fazenda?"*. É:

> **"Quais dos meus clientes precisam de mim esta semana?"**

Toda decisão de UX se subordina a essa pergunta. Uma tela que não ajuda a respondê-la —
direta ou indiretamente — está no lugar errado da hierarquia.

## 2. Status é o eixo da informação, não decoração

O produto gira em torno de *o que precisa de ação agora*. A paleta de 7 estados com 6
representações contextuais existe porque o consultor precisa **escanear** dezenas de
talhões e achar o atrasado sem ler texto.

- Qualquer mudança que dilua esse sinal (trocar cor por ícone pequeno, "simplificar" a
  paleta) é **regressão funcional**, não simplificação visual.
- `STATUS_RESUMO_ORDEM` já codifica a prioridade (atrasado › pendente › previsto). Reusar,
  nunca reinventar.
- **O trilho de status significa exatamente "isto precisa de você".** Por isso `prevista` e
  `desativada` não têm trilho — a ausência é o sinal.

É o maior ativo de design do produto: nenhuma plataforma pesquisada no benchmark tem essa
granularidade.

## 3. Confiabilidade do dado vence densidade visual

Falha de rede que renderiza lista vazia faz o agrônomo concluir *"não há pendências"*
quando o dado não carregou. Isso é indução a erro em decisão agronômica.

**Regra:** carregando, vazio e erro são três estados **visualmente distintos**, sempre.
`isError` tem precedência sobre `isEmpty`. Nenhuma tela é considerada pronta sem os três.

## 4. Densidade é aliada, não inimiga

O público compara linhas. Densidade ajuda; "arejar para parecer moderno" atrapalha.

Mas densidade é **escolha do usuário**, não imposição: `Compacto | Completo` nas telas de
lista. E **Compacto é desativado em `pointer: coarse`** — 36px fica abaixo do mínimo de
toque. Máxima densidade no escritório, ergonomia em campo, sem conflito.

## 5. Contexto ≠ filtro

A distinção que elimina a maior parte das dúvidas de "onde ponho este seletor?":

| | Contexto | Filtro |
|---|---|---|
| Responde | *Que recorte do mundo estou vendo?* | *Quais linhas desta lista?* |
| Exemplos | Cliente, fazenda, safra | Status, cultura, tipo, data |
| Onde vive | `ContextBar` (topo, persistente) | `FilterBar` (junto da tabela) |
| Persiste | Entre telas | Por tela |
| Aceita "Todos" | **Sim, é valor legítimo** | Sim, como ausência de filtro |

Se recorta *o mundo*, é contexto. Se recorta *a lista*, é filtro. Nunca se misturam.

## 6. Uso em campo é requisito, não cortesia

Decisão tomada: o produto **deve ser responsivo**. Consequências que não são negociáveis:

- Contraste mínimo real — sob sol direto, texto a 2,5:1 é invisível. Piso `slate-500`.
- Alvo de toque **48px** em `pointer: coarse`; ação destrutiva sempre 48px.
- Navegação primária vai para o **terço inferior** em tela estreita — uso com uma mão.
- Tabela larga não se resolve com rolagem horizontal. Coluna seletiva ou card empilhado.

**Fora de escopo:** offline-first. As referências do mercado entregam isso via app nativo;
para uma SPA com cookie httpOnly + CSRF, exigiria fila de escrita e resolução de conflito.
É projeto próprio, não item de redesign. Confundir os dois faz o redesign não terminar.

## 7. Multi-tenant é invisível por design

Cada conta é uma consultoria. O isolamento é garantido no backend — **nenhuma tela recria
esse filtro na UI**. Cliente é contexto de trabalho, não um seletor de segurança.

## 8. Regra de negócio não é detalhe de implementação

Cálculo de dose/área/volume e as regras de status **são o produto**. Trabalho de estilo,
redesign ou refatoração nunca altera esse comportamento como efeito colateral.

- A lógica vive no backend (`services/`). O frontend **não recalcula** para evitar uma
  requisição — busca o valor derivado.
- Campo calculado é **somente-leitura**, com `hint` explicando a fórmula. Nunca um input
  que finge ser calculado.
- Se um comportamento parecer errado, **reportar, não corrigir de passagem**. É onde um
  ajuste acidental causa o maior dano silencioso.

## 9. Hierarquia vem de estrutura, não de cor

O produto atual delegou hierarquia à cor — e por isso os cinzas foram sendo puxados até o
ilegível (`gray-400` a 2,54:1, a cor de texto mais usada).

A hierarquia correta, em ordem de força:

1. **Posição** — o que está no topo domina.
2. **Tamanho** — escala de 6 degraus, piso 12px.
3. **Peso** — `regular` é o padrão; `medium` é ênfase real; `semibold` é título.
4. **Cor** — por último, e **nunca abaixo do piso de legibilidade**.

Se algo só se distingue por ser mais claro, a hierarquia está errada.

## 10. Cor tem significado, não humor

- **Azul** = marca, estrutura, ação.
- **Dourado** = acento de alta consequência. Um por tela, no máximo.
- **Paleta de status** = estado. Nunca decoração.
- Fora disso, a interface é neutra.

É assim que o produto atende "evitar interfaces excessivamente coloridas" sendo, ao mesmo
tempo, fortemente orientado a cor: **densa em significado, escassa em área.**

## 11. Microcopy é material de design

- **Mesmo verbo do início ao fim.** Botão "Emitir recomendação" → toast "Recomendação
  emitida". Nunca "Salvo com sucesso" para uma ação chamada "Emitir".
- **Erro diz o que aconteceu.** Usar o `detail` do backend quando existir; texto genérico
  fixo é desperdício de informação que o servidor já mandou.
- **Estado vazio ensina a regra**, referindo-se ao contexto ativo: *"Nenhuma recomendação
  para os filtros desta safra"*, não *"Nenhum resultado"*.
- **Confirmação diz a consequência**, não só pergunta: *"Ele deixa de receber novas
  recomendações e continua nos relatórios das safras anteriores."*
- **Marca o opcional**, não o obrigatório — a maioria dos campos é obrigatória.
- Nomear pelo que o usuário controla, nunca pela implementação.

## 12. Piso de acessibilidade

Não é meta, é piso. Nada entra abaixo disto:

| Item | Mínimo |
|---|---|
| Texto | 4,5:1 · piso `slate-500` |
| Borda de campo, gráfico informativo | 3:1 |
| Tamanho de fonte | 12px |
| Alvo de toque | 36px fino · 48px grosseiro |
| Botão só-ícone | `aria-label` obrigatório (`IconButton` exige no tipo) |
| Heading | Um `<h1>` por rota |
| Foco | Visível em todo elemento focável |
| Ícone | Nunca portador único de significado |
| Movimento | Respeita `prefers-reduced-motion` |

## 13. Números alinham

Dose, área, volume, contagem: `IBM Plex Mono` + `tabular-nums`, alinhados à direita. O
usuário compara valores entre linhas — texto proporcional impede o escaneamento.

## 14. Movimento é funcional

Transição só em cor (100ms), altura (160ms), expansão (180ms) e sobreposição (200ms).
**Nada anima na carga inicial.** Nenhum efeito decorativo, nenhuma entrada escalonada.

Em um produto de trabalho diário, animação de entrada é imposto cobrado toda vez que a
tela abre.

## 15. O que não fazer — clichês a evitar

Identificados como genéricos na auditoria do produto atual:

- Fileira de 4 KPIs de contagem total **sem ação associada**.
- Cartão de "Atalhos" duplicando a sidebar permanentemente visível.
- Saudação "Olá, {nome}" + data ocupando a posição mais valiosa da tela.
- Card branco com sombra como recurso universal, sem hierarquia entre primário e apoio.
- Copy de vazio genérica.
- Ilustração em estado vazio — custo de manutenção que não se paga em produto de trabalho.
- Gráfico sem pergunta associada.
