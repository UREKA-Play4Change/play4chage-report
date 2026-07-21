# Draft: Capítulo 5 — Avaliação e Resultados (Estrutura Refatorada)

**Gerado por ingestão direta do CSV `Play4Change - User Experience Evaluation.csv` (n=12)**  
**Data de processamento:** 2026-06-14

---

## Métricas reais extraídas do CSV

### SUS (System Usability Scale) — pontuações brutas

| P# | Role | SUS Score | Grupo (comportamental) |
|----|------|-----------|------------------------|
| P01 | Current Student | **80.0** | A (≥68) |
| P02 | Current Student | **97.5** | A |
| P03 | External Tester | **90.0** | A |
| P04 | External Tester | **45.0** | B (<68) |
| P05 | Alumnus/Alumna | **35.0** | B |
| P06 | External Tester | **50.0** | B |
| P07 | External Tester | **77.5** | A |
| P08 | External Tester | **95.0** | A |
| P09 | Current Student | **60.0** | B |
| P10 | External Tester | **97.5** | A |
| P11 | External Tester | **92.5** | A |
| P12 | Current Student | **92.5** | A |

**Média global:** 76.0 / 100 → Classificação: **Good (B)** pela escala de Bangor et al.  
**Grupo A** (SUS ≥ 68, n=8): média 89.4 | Sorted: 77.5, 80, 90, 92.5, 92.5, 95, 97.5, 97.5  
**Grupo B** (SUS < 68, n=4): média 47.5 | Sorted: 35, 45, 50, 60  

> ⚠️ **Nota:** O agrupamento (A/B) é comportamental (SUS), não por papel/afiliação. P05 (Alumni) e P09 (Student) ficam no Grupo B; P03, P07, P08, P10, P11 (External) ficam no Grupo A. Isto refuta a hipótese de que ISEL-affiliation = high digital literacy.

### Usabilidade geral (1–5 Likert)
- Média: **4.17** | Distribuição: 1×1, 1×3, 4×4, 6×5

### Engajamento
| Dimensão | Média | Distribuição |
|----------|-------|--------------|
| Motivação cívica/cidades sustentáveis | 3.83 | 1×SA, 8×A, 3×N |
| Relevância real-world | 4.00 | 4×SA, 5×A, 2×N, 1×D |
| Apelo visual e estética | **4.33** | 6×SA, 4×A, 2×N |
| Encorajamento ao regresso diário | 3.92 | 2×SA, 7×A, 3×N |
| Adaptatividade IA | 4.08 | 4×SA, 5×A, 3×N |

### SUS por questão (médias)
| Q# | Formulação (resumida) | Tipo | Média |
|----|----------------------|------|-------|
| Q1 | Usaria frequentemente | + | 3.67 |
| Q2 | Desnecessariamente complexo | − | 2.17 |
| Q3 | Fácil de usar | + | **4.25** |
| Q4 | Precisaria de suporte técnico | − | **1.75** |
| Q5 | Funções bem integradas | + | 4.00 |
| Q6 | Demasiada inconsistência | − | **1.67** |
| Q7 | Aprendizagem rápida | + | 3.67 |
| Q8 | Pesado de usar | − | **1.83** |
| Q9 | Confiante a usar | + | **4.25** |
| Q10 | Precisou aprender muito antes | − | 2.00 |

### Bugs reportados (Frente 1 - qualitativo)
- **Padrão dominante (P04, P06):** "When I make a mistake another question suddenly appears, making the experience confusing" → feedback de erro ausente / READING_ERROR não explicado ao utilizador na interface
- **P03:** Questões repetidas no percurso alternativo; streak não atualizou; IA "foi confusa"
- **P11:** "In the consolidation path, when I got the question right it marked me as correct, but then half a second later it showed me as wrong" → race condition no estado de resposta
- **P02:** Search bar oculta ao inserir email
- **P05:** Títulos em inglês e português misturados

### Melhor funcionalidade (citações qualitativas)
- 7 de 11 respostas mencionam espontaneamente a **IA de feedback/explicação** como melhor funcionalidade
- P02: *"The fact that when we get an answer wrong, AI tries to help us understand why we got it wrong"*
- P08: *"O facto de apresentar explicações rápidas quando não se acerta em alguma pergunta"*
- P12: *"AI adaptability to your answers"*

---

## Estrutura do Capítulo 5 — Nova Arquitetura

```
\chapter{Avaliação e Resultados}

Intro: triangulação de 3 fontes: (1) questionário CSV n=12, 
       (2) dados comportamentais BD, (3) integridade do pipeline IA

\section{Metodologia de avaliação}                [manter, atualizar]

\section{FRENTE 1 — Uso e Comportamento}
  \subsection{Dados comportamentais: visão geral} [manter tabela stats-gerais]
  \subsection{Taxas de conclusão por tópico}      [manter fig conclusao-topicos]
  \subsection{Padrões de erro e ciclo adaptativo} [manter + expandir]
  \subsection{Resultados do questionário SUS}     [ATUALIZAR valores CSV reais]
    → Fig sus-distribuicao: valores exatos (77.5, 92.5, 97.5 — não arredondados)
    → SUS por questão: nova tabela heurística (Q3/Q9 fortes, Q1/Q7 moderados)
  \subsection{Envolvimento e adaptatividade}      [manter tabela engagement]
  \subsection{Feedback qualitativo}               [enriquecer com citações CSV]
  \subsection{Dois perfis de utilizador}          [CORRIGIR: grupos por SUS, não por role]
  \subsection{Ciclo adaptativo: exemplo real}     [manter]
  \subsection{Posicionamento U!REKA}              [manter]
  \subsection{Discussão + Limitações}             [manter, atualizar n]

\section{FRENTE 2 — Integridade e Resiliência da IA [NOVO]}
  \subsection{Visão geral do pipeline RAG}
    → Diagrama: FETCH → STRUCTURE → GENERATION → INDEXING
    → 3 tipos de chamada LLM + token budget (Tabela inferencia_ia)
  \subsection{Integridade estrutural: validação JSON e retentação}
    → Mecanismo: 1ª chamada → valida schema → se inválido: 2ª com schema reminder
    → Resultados observados: 
       - N tópicos gerados com sucesso: 4 (activos no sistema)
       - N tópicos em FAILED: 0 observados no período
       - Taxa de sucesso geração inferida: ≥ 95% (PLACEHOLDER — substituir com logs)
    → Tabela: JSON válido 1ª tentativa / válido 2ª tentativa / FAILED
  \subsection{Resiliência adaptativa: reutilização vectorial}
    → 55 sessões adaptativas geradas: quantas por FULL_REUSE / PARTIAL_REUSE / NEW_GEN
    → Threshold coseno: 0.90 (FULL) e 0.65 (PARTIAL)
    → Eficácia da deduplicação: 0 casos de conteúdo duplicado reportado pelos utilizadores
       (cross-referência: P03 reportou repetição → bug de filtro "já visto" para a mesma assignment)
  \subsection{Latência de inferência}
    → P50/P95/P99 por tipo (PLACEHOLDER — valores da inferencia_ia.tex são representativos)
    → Impacto no UX: sessões explicação ≤ 3 min (confirmado por dados BD)
  \subsection{Discussão: limites da avaliação técnica sem telemetria}
    → O que seria necessário: Prometheus/Grafana export, Spring Boot logs JSON
    → O que é inferível dos dados comportamentais disponíveis
```

---

## Dados placeholder para Frente 2 (a substituir com logs reais)

| Métrica | Valor placeholder | Fonte para substituição |
|---------|------------------|------------------------|
| Tópicos gerados com sucesso (sem FAILED) | 4/4 = 100% | DB: `topics` WHERE status='ACTIVE' |
| Taxa JSON válido 1ª tentativa | ~85% (estimativa) | Spring Boot logs: `ai-agent` |
| Taxa JSON válido 2ª tentativa | ~12% | Spring Boot logs: `schema_reminder` |
| Taxa FAILED após 2 tentativas | ~3% | Spring Boot logs: `FAILED` |
| Sessões adaptativas FULL_REUSE | ? | DB: `struggle_paths` WHERE reuse_strategy='FULL_REUSE' |
| Sessões adaptativas PARTIAL_REUSE | ? | DB: `struggle_paths` WHERE reuse_strategy='PARTIAL_REUSE' |
| Tasks marcadas DUPLICATE | ? | DB: `tasks` WHERE status='DUPLICATE' |
| Latência P50 geração tópico | 4.8s (representativo) | Prometheus: `ai_generation_duration_seconds` |
| Latência P50 adaptativo | 2.1s (representativo) | Prometheus: `ai_generation_duration_seconds` |
| Latência P50 explicação | 1.4s (representativo) | Prometheus: `ai_generation_duration_seconds` |

---

## Nota final: o que o CSV confirma vs. o que o capítulo anterior assumia

| Afirmação no capítulo anterior | Status após ingestão CSV |
|-------------------------------|--------------------------|
| SUS médio = 76.0 | ✅ Confirmado |
| 12 participantes | ✅ Confirmado |
| Valores individuais SUS (78, 92, 98) | ⚠️ Corrigidos (77.5, 92.5, 97.5) |
| Grupo A = ISEL/tech-savvy, Grupo B = externa/low-tech | ❌ Não sustentado pelo role: P05 Alumni→Grupo B, P03/P07/P08 External→Grupo A |
| Nenhum participante discordou de adaptatividade IA | ✅ Confirmado (3×Neutro, 0 discordâncias) |
| Usabilidade geral média 4.17 | ✅ Confirmado |
| Engajamento visual 4.33 = mais alto | ✅ Confirmado |
