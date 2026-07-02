# Avaliação Crítica — Relatório Play4Change (v5)

**Estado actual:** 105 páginas físicas, 78 até ao fim da Conclusão (o teu alvo: 53–57).
**Veredito:** o conteúdo técnico é sólido — provavelmente o melhor do lote deste ano. Mas o documento não está pronto para entrega, por duas razões que nada têm a ver com o número de páginas, e está inchado por uma razão muito concreta: excesso de diagramas e uma secção inteira duplicada. Corrige os dois bloqueadores primeiro. O corte de páginas resolve-se depois, quase sozinho.

---

## 0. Bloqueadores — nada disto se discute, corrige-se

### 0.1 A Conclusão não existe
`capconclusao.tex` tem seis linhas, todas comentários (`% [Retoma dos objectivos...]`). Não escreveste a conclusão. É o capítulo mais lido de um relatório académico a seguir ao resumo, e neste momento é um título de capítulo sozinho numa página. Não interessa quantas páginas cortas nos outros capítulos se este continua vazio — o relatório reprova por incompletude antes de qualquer avaliador chegar à discussão sobre cortes.

Escreve-a. 2–3 páginas, prosa fechada, sem bullets soltos: retoma dos 5 objectivos (tens a tabela pronta no Cap. 7 §7.1, não reescrevas — sintetiza numa frase por objectivo), contribuições técnicas reais (reutilização semântica de ramos, arquitectura hexagonal testável, defesa em profundidade), limitação mais honesta que já identificaste (a avaliação foi com 12 pessoas, não há dados de produção), e uma frase de fecho. Não repitas a Discussão — a Discussão já fez o trabalho analítico, a Conclusão sintetiza-o.

### 0.2 O Resumo é texto de preenchimento
Linha 110 de `relatorio.tex`: *"Texto do resumo. Breve descrição do projeto..."* — isto é a instrução do template, não o resumo. Ninguém apagou o placeholder. Além disso:
- Não existe **Abstract** em inglês em lado nenhum do documento (o teu próprio `estrutura-relatorio.md` previa um e nunca chegou ao `.tex`).
- **Palavras-chave** está comentado (linha 113), portanto inexistente.

Um relatório de engenharia sem resumo e sem abstract não é um relatório incompleto — é um relatório sem página de rosto funcional. Escreve os dois (resumo PT + abstract EN, ~150–200 palavras cada, 5 palavras-chave). Não conta para o teu orçamento de 53–57 páginas porque fica em numeração romana, mas conta muito para a nota.

### 0.3 Falta lista de acrónimos
Tens RAG, JWT, XSS, SSRF, OWASP, DAG, SUS, ITS, DDD, MVI, RBAC, CSP, CORS, TOCTOU, SPA... a circular por 78 páginas sem uma lista de siglas (previstas no teu próprio esboço, nunca implementadas — `\listoffigures`/`\listoftables` também estão comentadas). Uma página de front-matter, ganho de legibilidade desproporcional ao custo. Não é bloqueador, mas é gratuito não fazer.

---

## 1. O verdadeiro problema do tamanho: o Capítulo 4 tem 21 diagramas

38 das 78 páginas (quase metade do relatório) estão no Capítulo 4 (Desenvolvimento). A causa não é o texto — o texto é denso e bem escrito. A causa é que **cada subsecção gerou o seu próprio diagrama de sequência a `\textwidth`**, num total de 21 figuras só neste capítulo (contra 0–3 nos restantes). Um diagrama de sequência bem colocado vale uma página de prosa; 21 diagramas, muitos deles a ilustrar processos já enumerados em texto ou tabela na frase anterior, deixam de acrescentar informação e passam a ser puro preenchimento visual.

Regra de juiz: **um diagrama só ganha o direito à página se mostrar concorrência, ordenação temporal entre actores, ou um fluxo condicional que a prosa não consegue transmitir tão bem.** Uma lista de "quatro casos por ordem de precedência" ou "três estados possíveis" não precisa de diagrama — já está na tabela ou na lista ao lado.

**Mantém** (ganham o espaço):
- `seq-magic-link` (autenticação, multi-actor, condições de falha)
- `seq-topic-generation` (pipeline assíncrono, é o fluxo mais complexo do sistema)
- `seq-ai-anti-injection` (construção da lista de mensagens tipadas — é o argumento de segurança mais original do projecto)
- `seq-rag-branch`/reutilização adaptativa (é o contributo técnico mais original, merece a página)
- Arquitectura geral (`Play4change-System_Design`)

**Corta ou reduz a metade de página (duas figuras por página, ou remove e deixa só a tabela/lista já existente):**
- `seq-token-rotation` — o texto já descreve o mecanismo passo a passo; o diagrama repete
- `seq-ingest-pdf` e `seq-ingest-url` — mantém uma, a outra é 90% igual (já tens a Tabela de comparação PDF vs URL a fazer esse trabalho)
- `seq-ingest-validation` — as 4 camadas já estão em subsubsecções numeradas com tabela de erros; o diagrama não acrescenta
- `seq-ai-task-gen`, `seq-ai-defense-layers` — "visão consolidada" de camadas já descritas uma a uma nas secções anteriores; redundante por definição
- `seq-rag-ingest`, `seq-rag-regen` — o `\[INGESTION \to ANALYSIS \to ...\]` já aparece em texto três vezes ao longo do capítulo (4.2, 4.6, 4.7) — escolhe **um** sítio para o mostrar e referencia-o nas outras duas, com ou sem diagrama
- `seq-task-assign`, `seq-task-submit`, `seq-struggle-resolve` — cada um ilustra uma lista de "N casos" já enumerada em prosa na frase imediatamente anterior
- `seq-token-theft` — a Secção de Análise de Riscos já descreve R04 em texto corrido; o diagrama é redundante com o próprio parágrafo que o introduz

Isto sozinho, bem executado, poupa **10–14 páginas** sem tirar uma única frase de conteúdo técnico.

**Redundância adicional a limpar:** o diagrama de estados `INGESTION → ANALYSIS → GENERATION → INDEXING → ACTIVE` é escrito por extenso, em texto, três vezes (§4.2.2, §4.6.1, §4.7.1). Escolhe uma âncora, remove as outras duas repetições e usa `\ref`.

---

## 2. O Capítulo 6 (Resultados) duplica o Capítulo 4

Isto é mais grave do que parece à primeira vista: as subsecções 6.1.1–6.1.6 ("Demonstração do Sistema") são, frase a frase, quase a mesma prosa das secções 4.3, 4.2, 4.7 e 4.8. Compara tu mesmo o parágrafo de "Autenticação por Ligação Mágica" em §4.3 com §6.1.1 — é o mesmo texto, reescrito com sinónimos mínimos. Isto acontece quando capítulos são escritos em sessões separadas e nunca reconciliados no fim; é o sintoma clássico de relatório não editado como um todo.

Pior: um capítulo chamado "**Demonstração** do Sistema" não tem uma única figura, screenshot ou captura de ecrã. Tens 21 diagramas de sequência no capítulo técnico e zero imagens da aplicação a funcionar no capítulo cujo propósito é precisamente mostrá-la a funcionar. Isto é ao contrário do que devia ser.

**Ação concreta:**
- Reescreve 6.1 como o que devia ser: 6 subsecções curtas (3–5 linhas cada), cada uma com **uma captura de ecrã real** da app/painel, e uma frase de ligação ao capítulo 4 ("o mecanismo está descrito em detalhe na Secção X"). Não re-expliques o mecanismo — mostra o resultado.
- 6.2 (Métricas) repete a configuração do thread pool e do rate limiter já dada em 4.2/4.8. Mantém só o que é genuinamente novo aqui: a tabela de métricas instrumentadas e as regras de alerta. Corta a re-explicação da configuração.

Isto poupa **2–3 páginas** e, mais importante, resolve um problema de credibilidade: um avaliador que note a duplicação vai perguntar-se o que mais no relatório foi escrito sem revisão cruzada.

---

## 3. Cortes menores, capítulo a capítulo

| Cap. | Título | Actual | Alvo | Acção |
|---|---|---|---|---|
| 1 | Introdução | 4 | 3,5 | Fundir §1.4 (Âmbito) e §1.5 (Organização) — a organização do documento pode ser 3 frases, não uma secção |
| 2 | Estado da Arte | 6 | 5 | Fundir §2.3 (Formal/Informal) com §2.5 (Mobile Learning) — ambas são curtas e complementares; o resto fica, é o capítulo mais bem calibrado do relatório (boa nota para o espírito crítico: citares Krashen a contestar o próprio estudo do Duolingo é o tipo de rigor que falta noutros relatórios) |
| 3 | Requisitos | 6 | 5,5 | Está bem. Não mexas. |
| 4 | Desenvolvimento | 38 | ~19-20 | Ver secção 1 acima — o corte principal |
| 5 | Testes | 8 | 7 | §5.4.3 diz literalmente "já foi coberto em §X e §Y" e depois repete a informação na mesma frase — corta a repetição, deixa só a remissão |
| 6 | Resultados | 6 | 3,5 | Ver secção 2 acima |
| 7 | Discussão | 6 | 5 | "Trabalho Futuro" tem 8 blocos em prosa longa — converte 5 dos 8 numa tabela (ideia \| esforço \| impacto), mantém em prosa só os 2-3 estrategicamente mais relevantes (o fecho sobre as duas direcções estratégicas é o melhor parágrafo do capítulo, esse fica) |
| 8 | Conclusão | 0 | 2,5 | Escrever — ver 0.1 |
| **Total** | | **78** | **~54** | Dentro do alvo 53–57 |

---

## 4. O que está bem e não deves tocar

Um relatório destes não se resolve só a cortar — vale a pena saberes onde não mexer, para não desperdiçares tempo a "melhorar" o que já está no ponto:

- **Auto-crítica genuína.** Identificas duas regras de rate limiting mortas por desalinhamento de rota nos endpoints mais usados da aplicação (§6.2.3), documentas os dois piores resultados SUS em vez de os esconder, e assumes limitações reais (profundidade de struggle fixa, dependência de fornecedor único). Isto é raro e vale pontos — não dilui isto ao cortar.
- **Estado da Arte com contra-argumentação.** Citar Krashen a contestar Vesselinov/Duolingo e Simpson a criticar a metodologia do effect size de Hattie, em vez de citar acriticamente, é exactamente o nível de leitura crítica que se espera e raramente se vê.
- **Arquitectura hexagonal + `Either<Error,T>`** como argumento de testabilidade — bem justificado, com número concreto (49 ficheiros de teste) a sustentar a afirmação. Mantém.
- Capítulos 2, 3, 5 e 7 estão, em extensão e densidade, praticamente no ponto. O trabalho está quase todo concentrado nos Capítulos 4, 6 e 8.

---

## 5. Ordem de execução recomendada

1. Escrever Conclusão (bloqueador de submissão)
2. Escrever Resumo + Abstract + palavras-chave (bloqueador de submissão)
3. Cortar/reduzir diagramas do Capítulo 4 (maior alavanca de páginas)
4. Reescrever Capítulo 6 sem duplicar o Capítulo 4, com screenshots reais
5. Cortes menores nos restantes capítulos (tabela acima)
6. Adicionar lista de acrónimos (opcional, mas barato e valioso)
7. Recompilar, medir páginas, ajustar

Não inverter esta ordem: cortar páginas antes de a Conclusão existir é reorganizar mobília numa casa sem tecto.
