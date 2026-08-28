# Análise prévia — frontend `hidekimva/Agro-Plataform`

> Commit analisado: `f071e5c` · 23/08/2026 · escopo: `frontend/` (19.461 linhas, 86 componentes `.tsx`)
> Documento de trabalho interno — **nenhuma alteração foi feita no repositório**.

## Sumário executivo

O frontend está **funcional e coeso no essencial**: `tsc --noEmit` passa limpo, `vite build`
compila em 5,7s, `npm audit` reporta **0 vulnerabilidades**, as dependências estão a
uma ou duas versões menores do topo, e o sistema de design (tokens de marca em
`@theme`, paleta de status documentada em `lib/statusCores.ts`) é claramente
pensado, não improvisado.

O problema não é qualidade pontual — é **consistência**. Existe uma boa camada de
componentes e utilitários compartilhados que **não foi adotada de forma uniforme**.
O resultado é duplicação larga: 253 chamadas HTTP soltas dentro de componentes,
49 blocos idênticos de tratamento de erro, 25 repetições da mesma string de
classes de input (embora `.input` exista no CSS), 14 modais escritos à mão apesar
de `<Modal>` existir.

Nada disso quebra o produto hoje. Tudo isso encarece cada mudança futura — que é
exatamente o que se está prestes a fazer.

**Estado da build (verificado, não estimado):**

| Verificação | Resultado |
|---|---|
| `tsc --noEmit` | ✅ exit 0, zero erros |
| `vite build` | ✅ 5,67s · 2.016 módulos |
| Bundle JS | ⚠️ **1.011 KB** em chunk único (248 KB gzip) |
| CSS | 52,9 KB (10,3 KB gzip) |
| `npm audit` | ✅ 0 vulnerabilidades (143 deps) |
| Lint / format | ❌ não existe |
| Testes de frontend | ❌ não existem (backend tem 22 arquivos) |

---

## Prioridade alta

### 1. Nenhuma camada de acesso à API — 253 chamadas espalhadas

`lib/api.ts` expõe só a instância axios crua. Cada componente monta suas próprias
URLs, `queryKey`s e transformações.

- **253** chamadas `api.get/post/patch/put/delete` diretas em componentes
- **258** `queryKey` escritos como literais, sem fonte única
- Concentração: `PlanejamentoListPage.tsx` (12), `ApFormModal.tsx` (12),
  `PlanejamentoEditPage.tsx` (9), `CalendarioListPage.tsx` (9)

**Consequência prática:** invalidação de cache é frágil. Se uma tela grava em
`/tipo-atividades` mas outra cacheou com `queryKey: ["tipo-atividades", showInactive]`,
acertar a invalidação depende de lembrar do formato exato em outro arquivo. Já há
sinal disso: chaves com e sem parâmetro convivendo, e `invalidateQueries` usando
prefixo às vezes e chave completa noutras.

**Direção:** um módulo por domínio em `lib/queries/` (ou `features/<x>/api.ts`)
exportando as funções de fetch e as factories de `queryKey`. Não precisa ser
migração de tudo de uma vez — dá pra fazer por feature, conforme se mexe nelas.

### 2. Tratamento de erro por copiar-e-colar, com `any`

**49** ocorrências literais de `err?.response?.data?.detail ?? "Erro ao ..."` e
**38** usos de `any` — praticamente todos no mesmo padrão:

```ts
onError: (err: any) => toast(err?.response?.data?.detail ?? "Erro ao salvar", "error"),
```

O `strict: true` do `tsconfig.json` está ativo, mas esses `any` furam a checagem
exatamente no ponto onde o formato do erro do backend importa.

**Direção:** um helper `mensagemErro(err: unknown, fallback: string): string` em
`lib/` + um tipo `ApiError`. Elimina os 38 `any` e centraliza o formato do
`detail` do FastAPI num lugar só (que é onde ele deve estar, já que é contrato de
backend).

### 3. Sem code-splitting — bundle único de 1 MB

`App.tsx` importa **as 33 rotas de forma estática**. Quem abre a tela de login
baixa junto o Google Maps, o kanban do calendário e todas as telas de relatório.

O próprio Vite avisa na build: *"Some chunks are larger than 500 kB"*.

**Direção:** `React.lazy()` + `<Suspense>` nas rotas. As candidatas óbvias de
maior ganho são `FazendaMapaPage` / `FazendaContornoPage` (carregam
`@react-google-maps/api`) e todo o grupo `calendario/`. É uma mudança de baixo
risco e efeito imediato no tempo de primeira carga — relevante para uso em campo,
com conexão ruim.

### 4. Falhas de carregamento são invisíveis para o usuário

**40 arquivos** usam `useQuery` sem tratar `isError`. O padrão dominante é:

```tsx
{isLoading ? <p>Carregando...</p> : <table>...</table>}
```

Se a requisição falhar (rede caiu, backend 500), `isLoading` vira `false`, `data`
cai no default `[]`, e a tela renderiza **uma tabela vazia** — indistinguível de
"não há registros". O usuário conclui que perdeu dados.

Não há `ErrorBoundary` em lugar nenhum: uma exceção de render derruba a aplicação
para tela branca.

**Direção:** (a) um `<ErrorBoundary>` em volta do `<Outlet />` no `Layout`;
(b) um componente `<EstadoConsulta>` (ou similar) que receba
`{isLoading, isError, vazio}` e renderize os três estados de forma padronizada.
Esse é provavelmente o item de maior retorno percebido pelo usuário final.

---

## Prioridade média

### 5. Componentes compartilhados subutilizados

Existe a infraestrutura; falta adoção.

| Componente | Existe | Importado em | Observação |
|---|---|---|---|
| `Modal.tsx` | ✅ | 17 arquivos | **14 modais escritos à mão** ignoram ele |
| `FormField.tsx` | ✅ | **2 arquivos** | Docstring diz "substitui o `Field` redefinido em cada modal" — a substituição não aconteceu |
| `PageHeader.tsx` | ✅ | 7 arquivos | ~26 páginas montam cabeçalho à mão |
| `SearchableSelect.tsx` | ✅ | 12 arquivos | Boa adoção |
| `.input` (CSS) | ✅ | — | **25 repetições** da string `w-full border border-gray-300 rounded-lg px-3 py-2 text-sm focus:ring-brand-600` |

Modais hand-rolled (usam `fixed inset-0` sem importar `<Modal>`):
`ClientesAcessoModal`, `UsuarioFormModal`, `UsuariosPage`, `AlmoxarifadoFormModal`,
`BicosPage`, `EquipamentosPage`, `PerfisIntervaloPage`, `TipoAtividadesPage`,
`TiposContratoClientePage`, `EventoDetalheDrawer`, `AdicionarProdutoModal`,
`HistoricoTab`, `PlanejamentoEstoqueModal`, `PlanejamentoListPage`.

Cada um desses perde o que `<Modal>` já resolve: fechar no `Esc`, travar o scroll
do body, fechar clicando fora, tamanhos padronizados.

**Direção:** migração incremental — 14 modais é grande demais para um PR só, mas
cada tela que for tocada pode ser convertida de passagem. Trocar as 25 strings de
input por `className="input"` é mecânico e cabe num PR isolado.

### 6. `confirm()` e `alert()` nativos — 30 ocorrências em 24 arquivos

Ações destrutivas (inativar cliente, remover AP, excluir item de manejo) usam o
diálogo nativo do navegador. Isso quebra a identidade visual, não é estilizável,
e em alguns navegadores/modos pode ser suprimido — nesse caso a ação simplesmente
não acontece, sem feedback.

Já existe `toast()` para feedback positivo; falta o par para confirmação.

**Direção:** `useConfirm()` + `<ConfirmDialog>` sobre o `<Modal>` existente.
30 call sites, substituição mecânica.

### 7. Acessibilidade

- **141** botões usam `title=` como única rotulagem; apenas **9** `aria-label` no
  projeto inteiro. Botões só-com-ícone (editar, inativar, remover) ficam sem nome
  acessível — `title` não é lido de forma confiável por leitor de tela.
- `Modal.tsx` não tem `role="dialog"`, `aria-modal`, focus trap nem devolução do
  foco ao fechar. O foco do teclado permanece na página de trás.
- Em `TipoAtividadesPage.tsx:151-152` há dois SVGs inline (inativar/reativar) sem
  `aria-hidden` nem título — e o projeto já usa `lucide-react`, que tem esses
  ícones (`Ban`, `CheckCircle`).

**Direção:** `aria-label` nos botões de ícone (pode acompanhar cada PR de tela) +
uma correção pontual no `Modal.tsx` que beneficia os 17 usos de uma vez.

### 8. Praticamente sem responsividade — aplicação é desktop-only

- Apenas **11 de 86** arquivos `.tsx` usam algum breakpoint Tailwind (29
  ocorrências no total)
- **26 tabelas**, das quais só **8** têm `overflow-x-auto` — as outras 18 estouram
  a largura em tela estreita
- `Layout.tsx` usa sidebar fixa (`w-56` / `w-16` colapsada) sem menu mobile: em
  telas pequenas ela come metade do viewport

É uma decisão legítima se o produto é de escritório. **Mas vale confirmar com o
dono**, porque "planejamento de manejo agrícola" sugere uso em campo, e nesse
caso é uma lacuna estrutural, não um detalhe.

---

## Prioridade baixa / observações

### 9. Autenticação no cliente é otimista

`PrivateRoute` decide só por uma flag em `localStorage` (`apv2_authed`), e o
`api.ts` reage ao 401 com `window.location.href = "/login"`.

Isso **não é falha de segurança** — o backend valida o cookie httpOnly, e o
comentário em `lib/auth.ts` deixa a intenção explícita e correta. Mas gera dois
efeitos de UX:

- Sessão expirada: a tela monta inteira, dispara as queries, e só então o usuário
  é expulso — pisca conteúdo antes de sumir
- `window.location.href` força reload completo, descartando o cache do React Query
  e o estado do router (uma navegação `navigate("/login")` seria suficiente)

### 10. Componentes grandes demais

| Arquivo | Linhas | `useState` |
|---|---|---|
| `ItensManejoModal.tsx` | 807 | 11 (em 2 componentes) |
| `PlanejamentoListPage.tsx` | 620 | 13 |
| `ApFormModal.tsx` | 577 | 14 |
| `PlanejamentoEditPage.tsx` | 571 | 8 |
| `ItemManejoFormModal.tsx` | 414 | **16** |

`ItemManejoFormModal` com 16 `useState` num só componente é candidato claro a
`useReducer` ou a `react-hook-form` — que **já é dependência do projeto**, usado
em 10 arquivos. A convivência dos dois padrões (RHF+zod em algumas telas, estado
manual em outras) é a inconsistência de fundo aqui.

Também vale notar: 45 `useEffect` contra apenas 18 `useMemo`/`useCallback` — vários
`useEffect` fazem sincronização de estado derivado, que costuma ser `useMemo`.

### 11. `types/index.ts` monolítico — 927 linhas, 88 tipos

É o maior arquivo do frontend. Divisão por domínio (`types/planejamento.ts`,
`types/calendario.ts`, …) reduziria conflito de merge e facilitaria achar as coisas.
Baixa urgência, mas barato de fazer.

### 12. Ferramental ausente

`package.json` tem apenas `dev`, `build`, `preview`. Não há ESLint, Prettier nem
runner de teste. O backend tem 22 arquivos de teste; o frontend, zero.

Considerando que este é um repositório com múltiplos colaboradores recebendo PRs,
um ESLint + Prettier configurados fazem mais pela consistência do código do que
qualquer refatoração pontual — e tornam os próximos PRs mais fáceis de revisar.

### 13. Documentação de convenções vazia

`.docs/conventions-react-vite.md` está com todos os campos em "(não informado)".
Como o repositório usa um fluxo formal de spec (`project-state.yaml` + `.spec/`),
preencher essas convenções seria uma contribuição de baixo atrito e alto valor —
e daria respaldo documental a qualquer padronização proposta depois.

### 14. Dependências

Zero vulnerabilidades. Tudo a uma ou duas versões menores do topo
(`lucide-react` 1.14→1.33 é a maior distância). `typescript` 6.0.3 com 7.0.2
disponível — atualização de major, avaliar à parte.

---

## Sugestão de agrupamento em PRs

Se o objetivo é que o dono aceite, PRs pequenos e temáticos funcionam melhor que
um grande. Ordenados por (valor ÷ atrito):

| # | Escopo | Toca | Risco |
|---|---|---|---|
| 1 | `React.lazy` nas rotas + `manualChunks` | `App.tsx`, `vite.config.ts` | Baixo |
| 2 | `ErrorBoundary` + componente de estado de consulta | ~3 novos arquivos | Baixo |
| 3 | Helper `mensagemErro()` — remove 38 `any` | ~25 arquivos, mecânico | Baixo |
| 4 | `className="input"` — remove 25 strings repetidas | ~20 arquivos, mecânico | Muito baixo |
| 5 | `<ConfirmDialog>` no lugar de `confirm()` | 24 arquivos | Médio (é UX visível) |
| 6 | `aria-label` + `role="dialog"`/focus trap no `Modal` | `Modal.tsx` + telas | Baixo |
| 7 | ESLint + Prettier + `npm run lint` | config + `package.json` | Baixo, mas gera ruído no diff inicial |
| 8 | Camada `lib/queries/` por domínio | grande — fatiar por feature | Médio/alto |
| 9 | Preencher `.docs/conventions-react-vite.md` | 1 arquivo | Nenhum |

Os itens 1–4, 6 e 9 são candidatos naturais a "PR de boas-vindas": objetivos,
verificáveis pela build, sem mudança de comportamento visível.

---

## O que ainda não foi verificado

Esta análise é estática (leitura, build, typecheck, audit). Não foi feito:

- Execução da aplicação com o backend em pé — não há banco local configurado
- Auditoria de acessibilidade automatizada (axe/Lighthouse)
- Verificação de contrato frontend↔backend (os schemas Pydantic vs. `types/index.ts`)
- Revisão do backend (fora do escopo pedido)

Se algum desses importar antes de decidir os ajustes, dá para cobrir.
