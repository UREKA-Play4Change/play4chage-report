# Notas de Revisão — Relatório Final Play4Change

Notas acumuladas da revisão secção a secção (engenheiro rigoroso + avaliador de escrita académica). Marcar `[ ]` → `[x]` à medida que forem resolvidas.

---

## Preâmbulo (Utilização de IA, Declaração de Integridade, Agradecimentos)

### Engenharia / rigor
- [x] **RESOLVIDO (confirmado no PDF compilado, pág. 7).** A Tabela 1 já foi reescrita: "Apoio à investigação preliminar de padrões" (Gemini) e "Auxílio na organização de notas de design" (Claude Code Opus) — já não contradiz a declaração de integridade. Nenhuma ação adicional necessária.
- [ ] Falta uma frase sobre **verificação/validação** do output de IA (como validaste o código do Sonnet e as estruturações do Opus).

### Escrita
- [ ] Lista final de nomes nos agradecimentos: sem separadores claros, difícil de ler — agrupar ou pontuar.
- [ ] O nome do próprio autor ("Radesh Ilesh Gamanbhai Govind") aparece dentro da lista de agradecidos — separar como assinatura ou remover.
- [ ] Confirmar consistência ortográfica (pré-AO90: "directo", "arquitectural") em todo o documento — confirmar se é norma exigida pelo ISEL.
- [ ] Confirmar com orientador se a ordem (IA → integridade → agradecimentos) segue template institucional.

---

## Capítulo 1 — Introdução

### Engenharia / rigor
- [ ] **"Significativamente"** na hipótese central (1.3) é uma promessa estatística forte — confirmar se a avaliação do Capítulo 5 (preliminar, presumivelmente pequena amostra/qualitativa) sustenta isto. Considerar suavizar para "pode melhorar a retenção e o envolvimento".
- [ ] **Possível erro de citação:** a referência [9] é usada tanto para a taxa de abandono ("raramente ultrapassa os 10%") como para "modelo de linguagem de grande dimensão" — duas afirmações não relacionadas. Verificar numeração da bibliografia.
- [ ] Lista de objetivos mistura **objetivos de conceção** (3 primeiros, ligados aos pilares) com **objetivos de processo** (construir/validar protótipo; recolher avaliação) sem distinção visual — separar em dois grupos para facilitar o mapeamento Intro → Cap.3 → Cap.5.
- [ ] Não há **hipóteses/perguntas de investigação formalizadas** (H1/H2/H3 ou RQ1/RQ2) — só prosa. Formalizar ajuda a rastreabilidade entre introdução, design e avaliação.
- [ ] "Uma parte significativa da população europeia não possui as competências básicas" — quantificar com dado concreto (Eurostat/DigComp) se disponível.
- [ ] Clarificar a relação institucional com a U!REKA (projeto comissionado vs. alinhado por iniciativa própria) — pode ser questionado na defesa.

### Escrita
- [ ] Secção 1.2 é desproporcionadamente curta vs. 1.1/1.3 — funciona como transição, não como subsecção própria. Considerar fundir com abertura de 1.3 ou desenvolver.
- [ ] Redundância entre o fecho de 1.1 (pergunta motivadora) e a abertura de 1.3 (hipótese central) — ligar explicitamente ("Para responder a esta questão, parte-se da hipótese de que...").
- [ ] Lista de objetivos com **estrutura paralela inconsistente**: os 3 primeiros têm rótulo + travessão + infinitivo, os 2 últimos só têm o verbo — uniformizar.
- [ ] Título "1.3 Objetivos e Arquitetura da Solução" promete arquitetura que só aparece no Cap. 3 — considerar renomear (ex.: "Hipótese, Pilares e Objetivos").

---

## Capítulo 2 — Enquadramento e Estado da Arte

### Engenharia / rigor
- [ ] Erro de concordância: "Os mecanismos de Points e Badges **gera** envolvimento" → "**geram**". Também traduzir/itálico em "Points e Badges" (termos em inglês não tratados).
- [ ] **Claims superlativos sem hedge:** "o maior delta de desempenho documentado em pedagogia" (2 sigma) e "alternativa viável e escalável à tutoria humana" (step-level feedback) são afirmações fortes/absolutas — suavizar ("um dos efeitos mais expressivos documentados...", "pode constituir uma alternativa...").
- [ ] **Precisão técnica em "RAG como delimitador do espaço latente":** dizer que "o espaço latente disponível é delimitado" é impreciso — o RAG condiciona a distribuição de probabilidade da geração via o contexto fornecido, não restringe literalmente o espaço latente (pesos do modelo). Reformular para algo tecnicamente mais exato (ex.: "condiciona a distribuição de geração, reduzindo — não eliminando — a probabilidade de alucinações").
- [ ] Verificar se a referência **[17]** é a mesma fonte para três afirmações distintas (definição geral de aprendizagem adaptativa, classificação de erros em padrões, e step-level feedback com efeitos 0,4–1,0 SD) — confirmar que não há atribuição incorreta.
- [ ] Verificar novamente **[9]** (já notado no Cap. 1): agora usado consistentemente para LLMs, o que sugere que pode ser um survey que cobre também taxas de abandono — mas vale confirmar.
- [ ] Secção 2.2 (Educação cívica) é fina em citações próprias — apoia-se só em DigComp/U!REKA (que justificam *o quê* conta como competência, não *porquê* a abordagem informal/gamificada funciona para literacia cívica). Considerar acrescentar literatura específica de educação cívica/informal learning.
- [ ] **Figura 2.1:** o "tracejado" é usado para duas coisas diferentes (marcador do edX, possivelmente por sobreposição visual, vs. área-meta do Play4Change) — risco de ambiguidade visual; usar codificação distinta (cor/padrão) para cada. Confirmar também se o tipo de gráfico (radar/triangular) não sugere indevidamente uma relação de soma fixa entre os três eixos (não são proporções, são níveis ordinais independentes).

### Escrita
- [ ] Heading "Inteligência artificial na personalização da aprendizagem" é uma frase longa, inconsistente com os headers curtos das subsecções vizinhas ("Gamificação", "Aprendizagem adaptativa") — considerar encurtar.
- [ ] **Pontos fortes a manter:** excelente uso de "Secção 1.3" para ligar conceitos aos pilares definidos no Cap.1 — resolve exatamente a preocupação de rastreabilidade já levantada; honestidade explícita sobre a limitação de não computar métricas RAGAS no protótipo atual; fecho da secção 2.4 bem calibrado ("prova de viabilidade... não ainda uma plataforma completa e madura"), consistente com o tom do Cap.1.
- [ ] Tabela 2.1 e critérios de seleção em 2.4 (streaks/distintivos do Duolingo, mastery do Khan, quiz do Kahoot!, exclusão consciente de certificados e leaderboards competitivos) — muito bem argumentado e justificado; manter esta lógica explícita de "porquê adotar X e não Y" noutros capítulos de design.

## Capítulo 3 — Design e Implementação do Sistema

### Engenharia / rigor
- [ ] **Inconsistência EN/PT nos diagramas.** A Figura 3.1 usa rótulos em inglês ("Presentation", "Edge Network", "Private Network", "Databases", "Observability", "Application", "External Services", "U/D", "R/W", "Pull Metrics", "Scrape Metrics"...) enquanto o corpo do texto usa nomes em português ("camada de apresentação", "rede de fronteira", etc.). Decidir e justificar (ex.: nota de rodapé "mantêm-se os termos em inglês por reflectirem a nomenclatura do código-fonte") ou traduzir os diagramas.
- [ ] **Abreviatura ambígua "U/D" na Figura 3.1** — não é convenção standard (normalmente CRUD ou R/W). Esclarecer na legenda o que significa (Update/Delete? Upload/Download?).
- [ ] **Inconsistência numérica:** "8.000 caracteres" no Cap. 3 (ponto como separador de milhares) vs "8 000 caracteres" no Cap. 2 (espaço) — mesmo valor, formatação diferente. Escolher uma convenção e aplicar globalmente.
- [ ] Typo real (não é artefacto de extração de PDF): **"terminacção"** → deveria ser "terminação" (mesmo em grafia pré-AO90 não tem este "cc").
- [ ] **CONFIRMADO no PDF compilado — bug real, não artefacto.** "dist intivos" / "distint ivo" / "dedup licação" aparecem com um espaço a meio da palavra mesmo na imagem renderizada da página (ex.: o próprio título da secção 3.5: "o sistema de dist intivos", pág. 30). Não é hifenização de fim de linha — é um espaço inserido dentro da palavra. Ocorre de forma inconsistente: por vezes a palavra está correta ("distintivos" em várias outras linhas), por vezes está partida. Padrão típico de um find-and-replace mal feito (provavelmente ao substituir "badge"/termo inglês por "distintivo" em massa). **Fazer uma pesquisa global por "dist intivo", "distint ivo" e "dedup licação" no documento e corrigir todas as ocorrências.** ("respon- sabilidades" à parte — esse é hifenização normal de fim de linha, não é um problema.)
- [ ] O campo interno `subjectDomain` é mencionado sem explicação do que representa — definir inline na primeira menção (ex.: "o campo subjectDomain do prompt, que carrega o contexto temático extraído").
- [ ] Considerar justificar explicitamente por que o hash do magic link é SHA-256 simples sem salt — é uma escolha correta dado que o token já é aleatório de alta entropia (não é uma password), mas uma frase a justificar isto preempts a pergunta do júri.
- [ ] A frase "o servidor rejeita sem informação adicional" (Fig. 3.2) é uma boa decisão de segurança (anti-enumeração) mas não é justificada explicitamente no texto — acrescentar uma frase a explicar o porquê.

### Escrita
- [ ] **Pontos fortes a destacar:** a Tabela 3.1 e a separação "o quê vs. como" é uma prática de documentação de arquitetura exemplar; o padrão de isolamento do Agente de IA (servidor nunca sabe que modelo/API está a ser usado) é uma decisão de design defensável e bem articulada; o watchdog de gerações presas tem lógica de transição de estado bem explicada; o fluxo de autenticação com UPDATE atómico anti-TOCTOU é o trecho de maior rigor técnico do capítulo; a arquitetura *pull* de métricas está bem justificada (falha da Métricas DB não afeta o servidor); e o pipeline de ingestão (3.3.2.1) tem uma discussão honesta e completa das limitações (PDFs digitalizados, JS rendering, paywalls, conteúdo desatualizado).
- [ ] **Excelente continuidade Cap.2 → Cap.3:** o limite de 8000 caracteres e a menção a "chunking inteligente" como trabalho futuro recupera diretamente a discussão sobre *lost in the middle* e chunking do Capítulo 2 — manter este padrão de fechar o ciclo teoria→prática nos próximos capítulos.
- [ ] Capítulo denso — considerar uma frase de orientação no início de 3.2 a antecipar a estrutura (módulo a módulo) para o leitor, já que tem 5 subsecções longas.
- [ ] Verificar paleta de cores da Figura 3.3 (setas vermelhas para falha, roxas para SSE) quanto a acessibilidade/daltonismo, já que é um diagrama central do capítulo.

## Capítulo 4 — Ferramentas, Tecnologias e Metodologia

### Engenharia / rigor
- [ ] **Afirmação provavelmente incorrecta:** "Corre num agente macOS (exigência do toolchain Android)" — builds Android/Gradle não exigem macOS; o requisito de runner macOS é tipicamente do toolchain **iOS/Xcode** (relevante porque o módulo é KMP e compila também para iOS). Corrigir a justificação.
- [ ] **Dependência em alpha em produção sem justificação/risco assumido:** Decompose **3.3.0-alpha01** é usado para navegação/estado na app móvel, descrita como parte da "infraestrutura de produção". Acrescentar uma frase a justificar a escolha (ex.: única solução madura para navegação KMP partilhada) ou a assumir o risco como limitação conhecida.
- [ ] **Falta justificação da escolha do modelo `mistral-small-latest`.** Não há razão explícita (custo, latência, residência de dados na UE/GDPR, alinhamento com o contexto europeu da U!REKA). Dado o enquadramento do projeto na U!REKA (aliança europeia), uma justificação ligada a soberania de dados/UE reforçaria a narrativa — vale a pena confirmar se essa foi de facto a razão e tornar isso explícito.
- [x] **RESOLVIDO — falso alarme.** Confirmado visualmente na pág. 35 do PDF compilado: a fórmula do `versionCode` aparece correctamente como "MAJOR × 10000 + MINOR × 100 + PATCH". O `ˆ`/`` ` `` eram só artefactos da ferramenta `pdftotext` usada para extrair o texto, não um erro real do documento.
- [ ] **Clarificar quem faz "revisão de código"** antes do merge — se o projeto é maioritariamente individual, esclarecer se é auto-revisão, revisão com orientador, ou suportada por ferramentas de IA (liga-se directamente à declaração de integridade do preâmbulo — manter consistência sobre o papel exacto da IA no processo).

### Escrita
- [ ] **Pontos fortes:** arquitectura tecnológica bem documentada com versões precisas (reprodutibilidade); justificação técnica sólida do pgvector co-localizado (evita sistema vectorial separado, mantém ACID); pipeline CI/CD com três workflows bem segmentados e tabela de critérios de falha (Tabela 4.2) clara; boa contextualização de risco no workflow OWASP ("plataforma que usa um LLM externo e processa dados de utilizadores"); uso de version catalog (`libs.versions.toml`) e Flyway para migrações são boas práticas modernas bem aplicadas.
- [ ] A Figura 4.1 (topologia) não tem o problema EN/PT do Cap. 3 — usa nomes próprios de tecnologia (PostgreSQL, Redis, Grafana...), que é apropriado e não precisa de tradução.

## Capítulo 5 — Avaliação e Resultados

### Engenharia / rigor — itens críticos (corrigir antes de defesa)
- [ ] **CONFIRMADO no PDF compilado (pág. 39 e pág. 42) — contradição real, não erro de leitura.** Pág. 39 (§5.1): "acima de 80,3 são consideradas 'boas' por Bangor et al.". Pág. 42 (§5.3.4): "76,0/100 ... na banda 'Good' da escala de Bangor et al." — 76,0 < 80,3, contradição directa. Para piorar, a Figura 5.1 (pág. 42) usa ainda um **terceiro** valor para o mesmo corte: a legenda do gráfico marca "SUS > 80 (bom/excelente)" como categoria, arredondado, diferente dos outros dois. Três números diferentes (80,3 / 80 / banda onde 76,0 já conta como "Good") para o mesmo limiar. **Verificar a fonte exacta em Bangor et al. e uniformizar os três.**
- [ ] **Interpretação dos dados de reutilização de ramos parece contradizer os próprios números.** "33 ramos distintos servidos a 55 sessões (máximo 22 reutilizações, 40%) confirmam que a diversidade... é suficiente para evitar repetição" — mas um único ramo a cobrir 40% de todas as sessões é sinal de **concentração**, não de diversidade. Rever a interpretação: talvez o argumento correcto seja "a deduplicação vectorial funciona correctamente para padrões de erro comuns, mantendo diversidade nos contextos menos frequentes" — mas como está escrito, a conclusão não decorre dos números apresentados.
- [ ] **Números que não reconciliam:** 25 "tarefas com pelo menos um erro inicial" (24% de 105) vs. 55 "sessões adaptativas (struggle) geradas" — como é que há mais sessões adaptativas do que tarefas com erro inicial? Falta uma frase a explicar a relação entre estas duas contagens (ex.: uma tarefa pode gerar múltiplas sessões, ou as sessões incluem casos de ciclos anteriores).
- [ ] **Métricas potencialmente circulares/trivialmente 100%:** "taxa de aprovação de 100%" dos distintivos (se um distintivo só é emitido quando o critério de mestria é cumprido, "100% aprovação" é verdadeiro por definição e não constitui evidência) e "100% RESOLVED" das 39 sessões de explicação por IA (esclarecer o que "RESOLVED" mede exactamente — confirmação de compreensão do utilizador, ou apenas o encerramento técnico da sessão sem erro). Clarificar a definição ou suavizar a interpretação.
- [ ] **Validade do ErrorPatternClassifier não verificada.** A heurística de READING_ERROR (opção escolhida adjacente à correcta na ordem shuffled) é estrutural/posicional, não semântica — não foi validada contra um ground truth humano. O próprio exemplo da Secção 5.3.5 (escolhido para ilustrar um READING_ERROR) mostra na verdade uma confusão conceptual genuína sobre o que conta como "dispositivo digital" (torradeira vs. smartphone) — mais próximo de WRONG_CONCEPT do que de erro de leitura apressada. Vale a pena reconhecer esta limitação de validade do classificador.
- [ ] **Esclarecer a correspondência entre os 12 respondentes do questionário e os utilizadores da BD pós-reset.** A nota de rodapé de 5.1 admite que alguns respondentes do "ciclo anterior" não voltaram a interagir após a reinicialização — mas a Secção 5.3.4 cruza directamente P01–P12 com comportamento da BD (streaks, tópicos concluídos). Confirmar e explicitar que os 12 respondentes correspondem exactamente ao subconjunto de utilizadores cujo comportamento está a ser analisado.
- [ ] N=6 tópicos com 0 falhas é evidência fraca para "confirma que o pipeline operou dentro dos parâmetros de fiabilidade definidos" — suavizar para "é consistente com" dado o tamanho da amostra.
- [x] **RESOLVIDO — falso alarme.** Confirmado visualmente nas págs. 30, 39, 40 e 42 do PDF compilado: as fórmulas (`s ≥ n`, `c/n ≥ α, com α = 0,60`, `n = 12`, etc.) aparecem todas correctas. Os símbolos `n " 12`, `Ñ`, `ě`, `«` eram só artefactos da extracção `pdftotext`, não erros do documento. Não é necessária nenhuma correcção.

### Escrita
- [ ] **Pontos fortes a manter:** secção de limitações (5.3.6) é exemplarmente honesta e específica (amostra, conveniência, ausência de grupo de controlo, ausência de validação pedagógica); a distinção entre "falha de comunicação UX, não do sistema" é uma análise madura; o exemplo de diálogo real (5.3.5) é uma escolha de evidência qualitativa muito forte — mantém-se como destaque do capítulo; framing cuidadoso ao defender a taxa de acerto de 99% sem cair em "é fácil demais" é bem argumentado (apesar do problema de reconciliação de números acima).
- [ ] O diálogo do exemplo real (5.3.5) e a citação de P04 estão em inglês — esclarecer com uma frase por que a interface/conteúdo do produto está em inglês (público internacional/U!REKA) apesar do relatório estar em português.
- [ ] Tabela 5.3: o tópico "Capstone" tem n=1 inscrito — deixar explícito que esta linha é anedótica e não comparável estatisticamente às restantes.
- [ ] Adicionar à secção de limitações (5.3.6): a contradição/ambiguidade do limiar SUS deve ser corrigida na fonte, não tratada como limitação; mas a falta de validação do ErrorPatternClassifier e a possível não-correspondência entre amostra do questionário e amostra comportamental são limitações genuínas que vale a pena adicionar explicitamente à lista já existente.

## Capítulo 6 — Conclusão

### Engenharia / rigor
- [ ] **Sobreclaim causal:** "sequências de até 13 dias... são um resultado que nenhum incentivo externo obrigatório pode explicar" — excluir uma explicação (incentivo externo) não confirma a explicação preferida (motivação intrínseca desenhada); ignora alternativas plausíveis (efeito de novidade, desejabilidade social para com o autor, n pequeno/conveniência). Suavizar para "sugerem", não "demonstram"/"confirmam".
- [ ] Repete-se aqui o problema já notado no Cap. 5: "100% das conclusões de tópicos a resultar na emissão do respectivo emblema" é uma afirmação circular (o emblema só é emitido quando o critério é cumprido) — corrigir na fonte (Cap. 5) para não se propagar à conclusão.
- [ ] **"Cobertura de testes incompleta" aparece pela primeira vez como limitação**, sem nunca ter sido quantificada nos capítulos de implementação/CI (Cap. 3/4). Reforça-se se o Cap. 4 reportar uma métrica real de cobertura.
- [ ] **Inconsistência de formatação decimal:** `α “ 0.60` (ponto) no Cap. 6 vs. `α “ 0,60` (vírgula) no Cap. 5 — mesmo valor, dois formatos. Mais um caso a juntar à lista de inconsistências numéricas do documento (ver também 8.000 vs 8 000 no Cap. 3/4).
- [ ] **Quebra silenciosa do enquadramento dos "três pilares".** A Tabela 6.1 introduz "Acessibilidade" e "Qualidade" como novas categorias de trabalho futuro, além dos três pilares originais (Reconhecimento, Envolvimento, Personalização) repetidos ao longo de toda a tese. Não há nenhuma frase a assinalar esta expansão — parece uma inconsistência estrutural. Acrescentar uma frase explícita (ex.: "para além dos três pilares originais, a avaliação revelou duas dimensões adicionais críticas: acessibilidade e qualidade de conteúdo").

### Escrita
- [ ] **Ponto forte a destacar:** o "paradoxo de acessibilidade estrutural" (a plataforma beneficia mais quem já tem literacia digital de base) é uma reflexão crítica madura e auto-consciente, que liga de volta à motivação original do Cap. 1 (défice de competências digitais). É um dos melhores momentos analíticos da tese — manter e eventualmente destacar mais.
- [ ] Tabela 6.1 (pilar → evidência → direcção futura) é uma boa prática de rastreabilidade — consistente com o padrão "porquê X e não Y" já elogiado no Cap. 2/3.
- [ ] Parágrafo de fecho bem equilibrado, tom apropriadamente modesto ("base técnica, pedagógica e conceptual sólida sobre a qual construir") — consistente com a honestidade de escopo mantida desde o Cap. 1.

---

## Resumo executivo — top 10 prioridades antes da submissão

1. ~~**Preâmbulo:** contradição Tabela 1 / declaração de integridade.~~ **RESOLVIDO** — confirmado no PDF (pág. 7), já corrigido.
2. **Cap. 5:** corrigir a contradição do limiar SUS “Good” — **CONFIRMADO no PDF**: 80,3 (pág. 39) vs. 76,0 classificado como “Good” (pág. 42), e a Figura 5.1 usa ainda um terceiro valor (80, arredondado) na legenda. Verificar a fonte de Bangor et al. e uniformizar os três.
3. **Cap. 4:** corrigir a justificação do agente macOS (“exigência do toolchain Android” → provavelmente iOS/Xcode). **CONFIRMADO no PDF** (pág. 51), continua a precisar de correcção.
4. **Cap. 4:** justificar ou assumir o risco de usar Decompose em fase alpha (3.3.0-alpha01) em “infraestrutura de produção”. **CONFIRMADO no PDF** (Tabela 4.1, pág. 34).
5. **Cap. 3:** decidir e justificar a inconsistência EN/PT nos rótulos da Figura 3.1 (arquitectura). **CONFIRMADO no PDF** — a figura tem mesmo rótulos em inglês (“Presentation”, “Edge Network”, “Reverse Proxy”, etc.) misturados com texto em português.
6. **Cap. 5:** corrigir a interpretação dos dados de reutilização de ramos (40% de concentração num só ramo não é “diversidade suficiente para evitar repetição”). **CONFIRMADO no PDF** (pág. 40), texto idêntico ao que foi revisto.
7. **Cap. 5/6:** rever métricas circulares (“100% taxa de aprovação” de distintivos; “100% RESOLVED” de sessões de IA) — clarificar o que medem de facto. **CONFIRMADO no PDF** (pág. 40).
8. **Cap. 6:** assinalar explicitamente a expansão de “três pilares” para cinco categorias (+ Acessibilidade, Qualidade) na tabela de trabalho futuro.
9. ~~**Transversal:** símbolos matemáticos corrompidos.~~ **FALSO ALARME, resolvido** — confirmado visualmente que as fórmulas estão correctas no PDF compilado (`×`, `+`, `≥`, `=` todos aparecem bem); o problema era só da ferramenta de extracção de texto usada para verificação, não do documento.
10. **Transversal:** uniformizar formatação numérica (separador decimal vírgula vs. ponto; separador de milhares espaço vs. ponto) em todo o documento.

## NOVO — encontrado na verificação contra o PDF compilado

11. **Cap. 3 (e provavelmente outros capítulos): bug real de “espaço a meio da palavra”.** “dist intivos”, “distint ivo”, “dedup licação” aparecem assim mesmo na página renderizada (não é artefacto de extracção) — visível inclusive no título da secção 3.5 (“o sistema de dist intivos”, pág. 30). É inconsistente: a mesma palavra aparece correta noutras linhas. Fazer uma pesquisa global no documento por “dist intivo”, “distint ivo” e “dedup licação” e corrigir todas as ocorrências — é um erro tipográfico visível e recorrente, teria impacto na primeira impressão de um avaliador.

## Verificação contra o PDF compilado (68 págs., compilado 2026-06-16)

Conclusões da comparação das notas anteriores com o `template.pdf` enviado:

**Já corrigido pelo autor:**
- Tabela 1 do Preâmbulo (item 1).

**Confirmado como problema real (manter na lista de correcções):**
- Contradição do limiar SUS “Good” (item 2, agora com um terceiro valor encontrado).
- Justificação do agente macOS (item 3).
- Decompose em alpha (item 4).
- Inconsistência EN/PT na Figura 3.1 (item 5).
- Interpretação da reutilização de ramos (item 6).
- Métricas circulares 100% (item 7).
- Typo “terminacção” (pág. 13).
- Inconsistência “8.000” vs “8 000” (confirmada em ambos os capítulos no texto extraído).

**Novo problema encontrado (não estava nas notas anteriores):**
- Bug de espaço a meio da palavra em “dist intivos”/”dedup licação” (item 11 acima) — este é mais sério do que pensava porque está confirmado visualmente na página, não só no texto extraído, e aparece no título de uma secção.

**Falso alarme — não é preciso corrigir nada:**
- Símbolos matemáticos “corrompidos” (`ˆ`, `` ` ``, `”`, `ě`, `Ñ`, `«`) — eram só artefacto da ferramenta `pdftotext` usada para a verificação; as fórmulas reais no PDF estão todas correctas.

## Próximas secções
(a preencher)
