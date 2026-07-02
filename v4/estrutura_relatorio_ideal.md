# Estrutura ideal de um relatório académico (pt-PT, modelo ISEL/IPL)

Estrutura com base no template `reportisel` (LaTeX), nos exemplos de relatórios fornecidos e nas orientações de Projeto e Seminário.

## 1. Elementos pré-textuais (frontmatter)

1. **Capa** — título curto e específico (não genérico), nome(s) do(s) autor(es), curso/UC, orientador(es), instituição (ISEL/IPL), data. Preenchida a partir de `personaldataofreport.tex`.
2. **Agradecimentos** (opcional) — secção breve de reconhecimento ao(s) orientador(es), instituição, colegas, família, etc.
3. **Resumo (PT)** e **Abstract (EN)** — versão precisa, sintética e seletiva do trabalho (máx. 150 palavras cada). Sem citações, tabelas, gráficos ou siglas não explicadas; sem expressões como "o presente trabalho trata...". Deve cobrir: contexto, objetivo, métodos, resultados e conclusões.
4. **Índice (Tabela de Conteúdos)** — capítulos, secções e subsecções com numeração de página.
5. **Lista de Figuras**, **Lista de Tabelas**, **Lista de Listagens de Código** (incluídas condicionalmente, conforme existam).
6. **Lista de Abreviaturas/Siglas** e **Glossário** (se aplicável).

## 2. Capítulo 1 — Introdução

- **Contextualização** do tema/problema e enquadramento institucional (UC, projeto, etc.).
- **Objetivo(s)** do trabalho, expressos com clareza.
- **Motivação** / relevância do problema.
- **Organização do documento** — síntese do conteúdo de cada capítulo seguinte.

## 3. Capítulo 2 — Enquadramento / Estado da arte

- **Trabalho relacionado** — revisão da literatura e soluções existentes, com referências bibliográficas.
- **Sistemas/abordagens semelhantes** — comparação crítica com a solução a desenvolver.
- Serve de base teórica e de justificação das opções tomadas adiante.

## 4. Capítulo — Ferramentas, Tecnologias e Utilização de IA

- **Tecnologias e ferramentas utilizadas** — linguagens, frameworks, plataformas, bibliotecas, ambientes de desenvolvimento, justificando as escolhas face às alternativas.
- **Metodologias/processos de trabalho** — ex.: gestão de projeto, controlo de versões, integração contínua.
- **Utilização de Inteligência Artificial** — declaração explícita de que ferramentas de IA (ex.: assistentes de geração de código, de texto ou de imagem) foram usadas, em que tarefas, com que limites e como o trabalho do(s) autor(es) foi revisto/validado. Esta secção segue a prática já adotada nos relatórios-modelo (`Utilização de Inteligência Artificial`) e responde a requisitos de transparência académica.

## 5. Capítulo(s) — Desenvolvimento / Solução

- **Descrição da solução/arquitetura** — definições, modelos, diagramas, decisões de design, sustentadas por referências.
- **Detalhe técnico** — algoritmos, estruturas de dados, tecnologias, excertos de código (via listagens, `\lstfromfile`).
- Pode estar dividido em vários capítulos (ex.: "Análise", "Conceção", "Implementação"), cada um com secções e subsecções coerentes, ligadas logicamente entre si.
- Figuras, tabelas e listagens devem ser sempre **referenciadas no texto** (`\ref{}`).

## 6. Capítulo — Avaliação / Testes / Resultados

- **Metodologia de avaliação/testes** adotada.
- **Resultados** apresentados de forma objetiva — dados quantitativos com significância estatística quando aplicável.
- **Discussão** dos resultados face aos objetivos definidos no capítulo 1.

## 7. Capítulo final — Conclusão

- **Síntese** dos aspetos mais relevantes do trabalho.
- **Verificação dos objetivos** (cumpridos/não cumpridos e porquê).
- **Limitações** identificadas.
- **Trabalho futuro** — estudos/desenvolvimentos adicionais recomendados.

## 8. Elementos pós-textuais (backmatter)

1. **Bibliografia** — todas as referências citadas no texto, ordenadas alfabeticamente pelo apelido do(s) autor(es) e depois cronologicamente; gerida em `bibliography.bib` (BibTeX). Incluir páginas consultadas (livros), data de consulta (websites) e indicação de "não publicado" quando for o caso (dissertações/relatórios).
2. **Apêndices/Anexos** — material complementar não essencial ao corpo do texto (especificações extensas, código completo, questionários, dados brutos, etc.).

## Regras transversais de escrita e formatação

- **Linguagem**: clara, objetiva, em discurso direto, frases curtas; cada parágrafo começa pela ideia principal e termina a fazer a ponte para o seguinte.
- **Título**: curto e específico, nunca genérico.
- **Citações**: apenas fontes indexadas/revistas por pares sempre que possível; formato BibTeX (ex.: via Google Académico).
- **Figuras/Tabelas/Listagens**: legenda no topo (tabelas) ou conforme convenção; sempre referenciadas no corpo do texto e listadas nos índices respetivos.
- **Extensão**: limite de páginas definido pelo docente/UC.
- **Idiomas**: documento principal em português, com resumo/abstract no idioma alternativo (inglês), respeitando regras de hifenização de cada um.

---
*Estrutura derivada da análise do template `reportisel` (v3.1), dos capítulos de exemplo (`Introdução`, `Manual do utilizador`, `Tutorial de LaTeX`) e dos relatórios-exemplo (`ExemploRelatorioLatex2026`, `ExemplosRelatorios20242025`).*
