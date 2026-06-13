# Análise Crítica e Reescrita — Play4Change

> **Veredicto geral:** O corpo do relatório (Capítulos 1–6) está tecnicamente sólido e demonstra maturidade de engenharia. Os problemas são reais mas não são de conteúdo — são de forma, de consistência ortográfica e de alguns erros estruturais graves que comprometem a seriedade do documento perante um júri. Dito isto, há secções com redacção genuinamente fraca que precisam de revisão profunda.

---

## ERROS CRÍTICOS — não submeter sem corrigir

### 1. Apêndice A — Artefacto de template não substituído

O Apêndice A intitula-se **"Applied Survival Analysis by Hosmer and Lemeshow"** e contém código R para análise de sobrevivência. Não tem qualquer relação com este trabalho. É um exemplo de demonstração deixado pelo template LaTeX. **Deve ser removido integralmente.** A sua presença num documento submetido a avaliação académica é inaceitável.

### 2. Referência [15] — Placeholder de template

A referência [15] é:
> J. Bruin. "Newtest: command to compute new test @ONLINE". URL: http://www.ats.ucla.edu/stat/stata/ado/analysis/

Esta referência não é citada no texto, não tem relevância para o tema, e é claramente um artefacto do template base. **Deve ser removida.**

### 3. Referência cruzada quebrada em 3.11

Na Secção 3.11 aparece:
> "...classificação determinística de erros (Secção **??**)"

O label LaTeX correspondente não está definido. **Deve ser corrigido** para apontar para a Secção 3.8.1.

### 4. Nota editorial exposta na legenda da Figura 3.9

A legenda da Figura 3.9 contém:
> "**Nota:** os valores apresentados são representativos do período de avaliação; **substituir pelos dados exactos exportados do Grafana/Prometheus antes de submeter o relatório final.**"

Esta nota interna foi deixada no documento final. É um erro grave de revisão — expõe ao júri que os dados apresentados são provisórios. **Deve ser removida antes de qualquer submissão.**

---

## PROBLEMA MAIOR — Inconsistência ortográfica sistemática

O documento mistura ortografia pré-AO1990 com ortografia pós-AO1990 de forma aleatória. Num relatório académico formal, esta inconsistência é considerada falta de cuidado editorial. A lista abaixo indica as ocorrências a corrigir para a norma pós-AO1990 (dominante no documento):

| Forma usada (pré-AO1990) | Forma correcta (pós-AO1990) | Exemplos no texto |
|---|---|---|
| arquitectura / arquitectural | **arquitetura / arquitetural** | Títulos 3.1, 4.1, 4.3 |
| projecto | **projeto** | p. vii (Declaração IA) |
| directamente / directo | **diretamente / direto** | pp. 2, 8, 14, 24... |
| detecção | **deteção** | Título 3.3.3, 3.7 |
| selecção | **seleção** | Título 3.6.3 |
| transacção | **transação** | p. 18 |
| injecção | **injeção** | p. 14 |
| correcto / correcta | **correto / correta** | Múltiplas ocorrências |
| actua / actuar / actualizações | **atua / atuar / atualizações** | pp. 15, 33, 36, 38 |
| acção | **ação** | p. 33 |
| projecção | **projeção** | Título 3.6.4 |
| objecto | **objeto** | p. 15 |
| extracção | **extração** | Diagrama p. 23 |
| efectivo / efectivamente | **efetivo / efetivamente** | p. 35 |
| colectivo | **coletivo** | p. 51 |
| reacção | **reação** | Tabela 5.4 |
| exactos | **exatos** | Legenda Fig. 3.9 |
| directas | **diretas** | p. 24 |
| interacção | **interação** | Título 3.8.3 |

---

## PROBLEMAS DE REDACÇÃO — Secção por secção

### Resumo — Reescrita necessária

**Diagnóstico:** O Resumo é estruturalmente pobre. Está escrito como texto de apresentação de produto, não como resumo científico. Um resumo académico deve cobrir: (1) contexto e motivação, (2) problema, (3) abordagem/solução, (4) metodologia de avaliação, (5) resultados principais, (6) conclusão. O actual omite os resultados de forma explícita e usa linguagem informal.

**Problemas específicos:**
- "Como possível contributo a essa missão surge o Play4Change" — construção informal e vaga
- "convidando-o a aprender algo novo todos os dias" — registo promocional, não científico
- "Juntam-se ainda mecanismos que mantêm o utilizador motivado a regressar" — frase solta, sem sujeito gramatical claro
- Os resultados da avaliação (SUS 76/100, 99% acerto, 98% resolução adaptativa) não são mencionados
- A metodologia de avaliação não é mencionada

**REESCRITA:**

> A U!REKA é uma aliança europeia de instituições de ensino superior com a missão de preparar cidadãos para a transição para cidades mais sustentáveis. No âmbito desta missão, o presente trabalho propõe o Play4Change: uma plataforma de aprendizagem digital, informal e gamificada, orientada para a educação cívica e para as competências que essa transição exige aos cidadãos. A plataforma organiza-se em torno de três pilares complementares — reconhecimento, envolvimento e personalização — e utiliza inteligência artificial para adaptar os percursos de aprendizagem ao desempenho individual de cada utilizador, identificar padrões de erro e gerar percursos de recuperação dirigidos.
>
> O protótipo foi desenvolvido com uma arquitectura distribuída (servidor Spring Boot em Kotlin, aplicação móvel Kotlin Multiplatform, painel React) e avaliado junto de 15 utilizadores reais, combinando dados comportamentais extraídos da base de dados e um questionário estruturado com 12 participantes. Os resultados indicam que a personalização adaptativa funciona eficazmente: 99% das tarefas foram concluídas com resposta correcta, 98% das sessões adaptativas foram resolvidas sem abandono, e o ciclo de explicação por IA conduziu utilizadores de confusões conceptuais a resoluções em menos de três minutos. A pontuação de usabilidade SUS obtida foi de 76/100, classificada como "boa". A avaliação revelou ainda um paradoxo de acessibilidade relevante para versões futuras: a plataforma é mais eficaz para utilizadores com literacia digital prévia, colocando um desafio central para o escalonamento.

**Palavras-chave:** aprendizagem adaptativa, educação cívica, gamificação, inteligência artificial, cidades sustentáveis

---

### Abstract — Reescrita necessária

**Diagnóstico:** Os mesmos problemas do Resumo, agora em inglês. Adicionalmente: "to see that progress recognised through badges" tem erro gramatical (omite artigo e o verbo não está correcto — deveria ser "to see their progress recognised" ou "to have their progress acknowledged").

**REESCRITA:**

> U!REKA is a European alliance of higher-education institutions whose mission is to prepare citizens for the transition towards more sustainable cities. In this context, this work proposes Play4Change: an informal, gamified digital learning platform aimed at civic education and the competencies that urban sustainability demands from citizens. The platform is structured around three complementary pillars — recognition, engagement and personalisation — and uses artificial intelligence to adapt learning pathways to each user's performance, classify error patterns, and generate targeted remediation sequences.
>
> The prototype was built on a distributed architecture (Kotlin/Spring Boot server, Kotlin Multiplatform mobile application, React administration panel) and evaluated with 15 real users through a combination of behavioural data extracted from the production database and a structured questionnaire administered to 12 participants. Results show that adaptive personalisation performs effectively: 99% of tasks were completed with a correct final response, 98% of adaptive sessions were resolved without dropout, and the AI-assisted explanation cycle guided users from conceptual confusion to resolution in under three minutes on average. The System Usability Scale score was 76/100, rated "good". The evaluation also identified an accessibility paradox relevant to future iterations: the platform is most effective for users with pre-existing digital literacy, posing a challenge for broader deployment.

**Keywords:** adaptive learning, civic education, gamification, artificial intelligence, sustainable cities

---

### Secção 1.1 — Problemas de registo

**Problema:** "O Play4Change nasce da vontade de dar um contributo concreto a este problema" — "nasce da vontade" é uma expressão de propósito informal que não pertence ao registo académico técnico. Um projeto não tem vontade; o autor tem.

**Original:**
> O Play4Change nasce da vontade de dar um contributo concreto a este problema, propondo-se como uma possível solução — ainda numa fase de protótipo — a apresentar à própria U!REKA

**Reescrita:**
> Este trabalho propõe o Play4Change como contributo concreto a este problema — uma solução em fase de protótipo, desenvolvida no âmbito da U!REKA, com o objectivo de ajudar os cidadãos a adquirir, de forma simples e acessível, as competências cívicas e digitais necessárias para participar nesta transição.

---

### Secção 2.3 — Título informal

**Original:** "Trabalho relacionado: o que já existe e o que se pode aprender com isso"

**"Com isso"** é coloquial e desadequado num título académico.

**Reescrita do título:** "Trabalho relacionado: análise comparativa de plataformas existentes e lições aplicadas"

---

### Secção 2.3 — "Análise informal"

**Original:** "foi feita uma análise informal de um conjunto de plataformas"

Chamar "informal" à própria metodologia de análise é auto-depreciativo sem ganho argumentativo. O resultado é que o júri pode questionar o valor da análise.

**Reescrita:** "foi conduzida uma análise comparativa e qualitativa de um conjunto de plataformas de educação digital amplamente conhecidas"

---

### Secção 4.4 — Secção demasiado vazia

**Diagnóstico:** A Secção 4.4 ("Utilização de inteligência artificial no desenvolvimento") contém uma única frase a remeter para o capítulo pré-textual. Para uma secção numerada num capítulo técnico, isto é inadequado — aparece no índice e ocupa espaço mas não acrescenta nada.

**Duas opções:**
1. **Remover a secção** e incluir a remissão no parágrafo introdutório do capítulo.
2. **Expandir minimamente** com uma frase de contextualização técnica antes da remissão.

**Opção 2 (reescrita mínima):**
> O processo de desenvolvimento do Play4Change recorreu a ferramentas de inteligência artificial generativa em três papéis distintos: exploração conceptual e análise de trade-offs arquitecturais, suporte à decisão técnica, e aceleração da escrita de código estrutural. A declaração completa sobre as ferramentas utilizadas, o âmbito da sua aplicação e as considerações éticas associadas encontra-se no capítulo pré-textual *Utilização de Inteligência Artificial* (página vii), em conformidade com as normas de integridade académica aplicáveis.

---

### Secção 3.11 — Referência cruzada e frase com problema

**Original:**
> "a combinação de classificação determinística de erros (Secção ??)"

**Correcção:** Substituir `(??)` pela referência correcta à Secção 3.8.1.

---

### Agradecimentos — Registo aceitável mas inconsistente

Os Agradecimentos têm bom nível de escrita mas contêm alguns anglicismos desnecessários ("não convencional" — aceitável) e a frase "A dedicação exigida por um trabalho desta envergadura impõe custos que recaem, inevitavelmente, sobre aqueles que estão mais próximos" — tecnicamente correcta mas a metáfora de "custos" para descrever o impacto pessoal soa a registo económico pouco adequado ao contexto emocional.

**Sugestão de reescrita desta frase:**
> A dedicação que um trabalho desta natureza exige tem consequências reais sobre quem está próximo — a paciência, o tempo e o apoio incondicional daqueles que não pediram para fazer parte disto, mas fizeram.

---

### Declaração de IA — Muito bem escrita, uma correcção ortográfica

Esta secção está muito bem redigida — clara, precisa e com bom equilíbrio entre transparência e rigor. Apenas uma nota: "projecto" (pré-AO) → **"projeto"** na primeira frase.

---

## RESUMO DOS PROBLEMAS POR PRIORIDADE

| # | Problema | Gravidade | Ficheiro |
|---|---|---|---|
| 1 | Apêndice A é um artefacto de template | 🔴 CRÍTICO | template.tex / apendice |
| 2 | Referência [15] é um placeholder | 🔴 CRÍTICO | referencias.bib |
| 3 | Nota editorial exposta na Fig. 3.9 | 🔴 CRÍTICO | chapter3.tex |
| 4 | Referência cruzada `??` em 3.11 | 🔴 CRÍTICO | chapter3.tex |
| 5 | Inconsistência ortográfica sistemática | 🟠 GRAVE | Todos os capítulos |
| 6 | Resumo — estrutura e registo | 🟠 GRAVE | resumo.tex |
| 7 | Abstract — estrutura, registo, erro gramatical | 🟠 GRAVE | abstract.tex |
| 8 | Secção 4.4 vazia | 🟡 MÉDIO | ferramentas.tex |
| 9 | Título 2.3 informal | 🟡 MÉDIO | chapter2.tex |
| 10 | "análise informal" em 2.3 | 🟡 MÉDIO | chapter2.tex |
| 11 | "nasce da vontade" em 1.1 | 🟡 MÉDIO | chapter1.tex |
| 12 | Frase de "custos" nos Agradecimentos | 🟢 MENOR | agradecimentos.tex |

---

## NOTA FINAL SOBRE O QUE ESTÁ BEM

Para ser justo: os Capítulos 3, 4, 5 e 6 demonstram competência técnica e discursiva acima do esperado para um trabalho de licenciatura. A precisão com que são descritos os mecanismos adaptativos, a justificação das escolhas arquitecturais, a análise de custo de inferência, e a discussão honesta das limitações são pontos fortes genuínos. O Capítulo 6 em particular é exemplar — a separação entre limitações da avaliação, do protótipo e de alcance é exactamente o rigor que um júri quer ver.

Os problemas identificados são corrigíveis em poucas horas. Nenhum deles compromete o trabalho em si — comprometem a apresentação do trabalho. A diferença entre uma nota boa e uma nota excelente, neste documento, está nestes detalhes.
