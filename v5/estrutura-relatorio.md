# [TÍTULO DO PROJETO]
### Relatório de Projeto — Engenharia Informática
**Autor:** [Nome]
**Instituição:** [Nome da instituição]
**Curso / Unidade Curricular:** [Nome]
**Orientador:** [Nome]
**Data:** [Mês, Ano]

---

## Resumo

- [Contextualização breve do problema e motivação]
- [Objetivo principal do projeto]
- [Abordagem e tecnologias usadas]
- [Principais resultados obtidos]
- [Conclusão em frase]

**Palavras-chave:** [palavra1], [palavra2], [palavra3], [palavra4], [palavra5]

---

## Abstract

- [Versão em inglês do resumo acima]

---

## Índice

*(gerado automaticamente ou preenchido no final)*

---

## Lista de Figuras

*(gerado no final)*

---

## Lista de Acrónimos e Siglas

| Sigla | Significado |
|-------|-------------|
| IA    | Inteligência Artificial |
| RAG   | Retrieval-Augmented Generation |
| API   | Application Programming Interface |
| JWT   | JSON Web Token |
| OTP   | One-Time Password |
| XSS   | Cross-Site Scripting |
| [...]  | [...] |

---

## 1. Introdução

### 1.1 Contextualização
- [Problema ou necessidade que o projeto endereça]
- [Contexto educacional / tecnológico em que se insere]
- [Relevância do tema]

### 1.2 Motivação
- [Porquê este projeto? O que o torna pertinente?]
- [Lacunas existentes nas soluções atuais]

### 1.3 Objetivos
- [Objetivo geral]
- [Objetivo específico 1]
- [Objetivo específico 2]
- [Objetivo específico N]

### 1.4 Âmbito e Limitações
- [O que está dentro do âmbito do projeto]
- [O que foi explicitamente excluído e porquê]

### 1.5 Estrutura do Relatório
- [Breve descrição do que se encontra em cada capítulo]

---

## 2. Estado da Arte / Revisão Bibliográfica

### 2.1 Plataformas de Aprendizagem Existentes
- [Referência e análise de solução A]
- [Referência e análise de solução B]
- [Comparação e lacunas identificadas]

### 2.2 Modelos de Linguagem Aplicados à Educação
- [Panorama do uso de LLMs em contextos educativos]
- [Trabalhos académicos relevantes]

### 2.3 Retrieval-Augmented Generation (RAG)
- [Definição e fundamentos teóricos]
- [Estado atual da investigação]
- [Casos de uso em sistemas de Q&A e aprendizagem]

### 2.4 Autenticação Passwordless
- [Motivação e adoção na indústria]
- [Soluções existentes (magic link, OTP, passkeys)]
- [Referências a normas / RFC relevantes]

### 2.5 Síntese Comparativa
- [Tabela ou análise comparativa das soluções revistas]
- [Posicionamento do projeto face ao estado da arte]

---

## 3. Análise e Requisitos

### 3.1 Identificação dos Utilizadores
- [Perfil do utilizador tipo A — ex: aluno]
- [Perfil do utilizador tipo B — ex: professor / criador de conteúdo]
- [Necessidades e expectativas de cada perfil]

### 3.2 Requisitos Funcionais
- [RF01 — Autenticação sem password]
- [RF02 — Upload e extração de conteúdo (PDF, URL)]
- [RF03 — Geração automática de tópicos]
- [RF04 — Geração e atribuição de desafios]
- [RF05 — Consolidation path / percurso de consolidação]
- [RF06 — Sistema RAG para reinjeção de contexto]
- [RF07 — Edição e invalidação de conteúdo]
- [RFN — ...]

### 3.3 Requisitos Não Funcionais
- [RNF01 — Segurança (autenticação, XSS, rate limiting)]
- [RNF02 — Desempenho (latência aceitável nas chamadas à IA)]
- [RNF03 — Escalabilidade]
- [RNF04 — Usabilidade]
- [RNF05 — Manutenibilidade]

### 3.4 Casos de Uso Principais
- [Diagrama de casos de uso ou descrição textual]
- [Caso de uso 1: Submeter conteúdo e gerar tópicos]
- [Caso de uso 2: Responder a desafio]
- [Caso de uso 3: Percurso de consolidação]
- [...]

---

## 4. Desenvolvimento

### 4.1 Arquitetura Geral do Sistema
- [Visão macro — diagrama de arquitetura]
- [Descrição das camadas: frontend, backend, IA, base de dados, armazenamento de embeddings]
- [Tecnologias escolhidas e justificação de cada escolha]
- [Fluxo geral de dados de ponta a ponta]

---

### 4.2 Backend — Spring Server & Pipeline

#### 4.2.1 Organização do Projeto
- [Estrutura de packages / módulos]
- [Padrão de arquitetura adotado (ex: Clean Architecture, Layered)]

#### 4.2.2 Pipeline de Processamento de Pedidos
- [Fluxo de um pedido HTTP: Controller → Service → Repository]
- [Interceptors, filters e middleware relevantes]
- [Gestão de erros e respostas padronizadas]

#### 4.2.3 Integração com os Restantes Componentes
- [Como o backend orquestra a chamada à IA]
- [Como comunica com a base de dados de embeddings]
- [Comunicação assíncrona (se aplicável)]

---

### 4.3 Autenticação Passwordless

- [Mecanismo escolhido: magic link / OTP / passkey — e justificação]
- [Fluxo de autenticação passo a passo (diagrama de sequência)]
- [Geração, envio e validação do token]
- [Tempo de vida e revogação do token]
- [Vantagens de segurança face à autenticação tradicional com password]
- [Gestão de sessão após autenticação (JWT, cookie seguro, etc.)]

---

### 4.4 Ingestão e Extração de Conteúdo

#### 4.4.1 Extração de Texto de PDF
- [Biblioteca / serviço usado]
- [Tratamento de PDFs com imagens ou texto não selecionável]
- [Limpeza e normalização do texto extraído]

#### 4.4.2 Extração de Texto de URL
- [Abordagem de scraping / parsing]
- [Tratamento de páginas com JavaScript (se aplicável)]
- [Remoção de ruído (navegação, rodapés, publicidade)]

#### 4.4.3 Limites e Validações
- [Tamanho máximo de ficheiro / página]
- [Formatos suportados]
- [Rate limiting na submissão de conteúdo]
- [Mensagens de erro e feedback ao utilizador]

---

### 4.5 Modelo de IA & Prompting

#### 4.5.1 Modelo Escolhido e Justificação
- [Modelo utilizado (ex: GPT-4o, Claude, Gemini, modelo open-source)]
- [Critérios de seleção: desempenho, custo, latência, limites de contexto]
- [Forma de acesso: API, self-hosted, etc.]

#### 4.5.2 Estratégia de Prompting
- [Estrutura geral dos prompts (system, user, context)]
- [Prompt para geração de tópicos a partir de conteúdo]
- [Prompt para geração de desafios/questões]
- [Diferença de prompts entre utilizador tipo A e tipo B]
- [Exemplos de prompts (few-shot, se aplicável)]
- [Gestão de tokens: limites, truncagem, chunking]

#### 4.5.3 Proteção e Limites da API de IA
- [Rate limiting por utilizador nas chamadas ao modelo]
- [Limite de tokens por pedido e por sessão]
- [Timeout e fallback em caso de falha]
- [Guardrails contra prompt injection]
- [Monitorização de custos]

---

### 4.6 RAG — Retrieval-Augmented Generation

#### 4.6.1 Armazenamento de Conhecimento
- [Chunking do texto: estratégia e tamanho dos chunks]
- [Geração de embeddings: modelo usado]
- [Base de dados vetorial: tecnologia escolhida e justificação]
- [Estrutura dos objetos armazenados (chunk, metadata, embedding)]

#### 4.6.2 Recuperação e Reinjeção de Contexto
- [Fluxo de uma query: embedding → similaridade → recuperação]
- [Número de chunks recuperados e critério de seleção]
- [Como os chunks são injetados no prompt]
- [Exemplo de prompt com contexto RAG injetado]

#### 4.6.3 Edição de Conteúdo e Invalidação
- [O que acontece quando o utilizador edita um documento/conteúdo]
- [Estratégia de invalidação: re-indexação total vs. incremental]
- [Como se garante que o conhecimento desatualizado não é usado]
- [Impacto nas questões já geradas — invalidação ou manutenção]

---

### 4.7 Geração e Atribuição de Desafios

#### 4.7.1 Geração de Desafios
- [Como os desafios são gerados a partir dos tópicos]
- [Tipos de desafios suportados (MCQ, aberta, verdadeiro/falso, etc.)]
- [Validação da qualidade das questões geradas]

#### 4.7.2 Atribuição aos Utilizadores
- [Lógica de atribuição: aleatória, baseada em tópico, baseada em desempenho]
- [Como se evita repetição de desafios já respondidos]
- [Diferença de atribuição entre o utilizador tipo A e tipo B]

#### 4.7.3 Consolidation Path
- [Definição e objetivo do percurso de consolidação]
- [Algoritmo ou lógica de progressão (ex: spaced repetition, scoring)]
- [Critérios de conclusão / domínio de um tópico]
- [Representação do progresso ao utilizador]

---

### 4.8 Segurança da Aplicação

#### 4.8.1 Proteção Contra XSS
- [HTML encoding de todos os inputs e outputs]
- [Content Security Policy (CSP)]
- [Sanitização no frontend e no backend]

#### 4.8.2 Rate Limiting por IP
- [Ferramenta / biblioteca usada]
- [Limites definidos por endpoint (auth, upload, geração, etc.)]
- [Comportamento em caso de excesso (429, bloqueio temporário)]

#### 4.8.3 Validação e Sanitização de Inputs
- [Validação de tipos, tamanhos e formatos]
- [Rejeição de inputs maliciosos]
- [Tratamento de uploads (mime type, tamanho, antivírus se aplicável)]

#### 4.8.4 Outros Controlos de Segurança
- [Proteção CSRF]
- [Headers de segurança HTTP (HSTS, X-Frame-Options, etc.)]
- [Gestão segura de segredos e variáveis de ambiente]
- [Logs de segurança e auditoria]

#### 4.8.5 Análise de Riscos
- [Identificação dos principais riscos (tabela: risco, probabilidade, impacto, mitigação)]
- [Ameaças específicas ao componente de IA (prompt injection, data leakage)]
- [Decisões de trade-off tomadas]

---

## 5. Testes e Validação

### 5.1 Estratégia de Testes
- [Tipos de testes realizados: unitários, integração, end-to-end]
- [Ferramentas usadas]

### 5.2 Testes Unitários
- [Componentes testados]
- [Cobertura de código obtida]

### 5.3 Testes de Integração
- [Fluxos testados de ponta a ponta]
- [Resultados]

### 5.4 Testes de Segurança
- [Testes realizados: XSS, injeção, rate limiting]
- [Ferramentas (ex: OWASP ZAP, testes manuais)]
- [Resultados e correções aplicadas]

### 5.5 Testes de Usabilidade *(se aplicável)*
- [Metodologia: utilizadores, tarefas, métricas]
- [Principais resultados e ajustes feitos]

---

## 6. Resultados

- [Demonstração do sistema funcional]
- [Capturas de ecrã / outputs das funcionalidades principais]
- [Métricas obtidas: tempo de resposta, qualidade das questões geradas, etc.]
- [Dados quantitativos (tabelas, gráficos)]

---

## 7. Discussão

- [Análise crítica dos resultados face aos objetivos]
- [O que correu bem e porquê]
- [Limitações encontradas e como foram (ou não) resolvidas]
- [Comparação com soluções do estado da arte]
- [Trabalho futuro: o que melhoraria ou expandiria]

---

## 8. Conclusão

- [Retoma dos objetivos definidos na introdução]
- [Confirmação do que foi alcançado]
- [Principais contribuições do projeto]
- [Reflexão sobre o processo de desenvolvimento]
- [Perspetivas de evolução / trabalho futuro]

---

## Referências Bibliográficas

*(Formatadas segundo a norma exigida — ex: APA 7ª edição ou ABNT)*

- [Referência 1]
- [Referência 2]
- [...]

---

## Apêndices

### Apêndice A — Exemplos de Prompts Completos
- [Prompt de geração de tópicos — versão completa]
- [Prompt de geração de desafios — versão completa]

### Apêndice B — Diagramas Complementares
- [Diagrama de sequência — fluxo de autenticação]
- [Diagrama de sequência — fluxo RAG]
- [Diagrama entidade-relação]

### Apêndice C — Configurações e Variáveis de Ambiente
- [Lista de variáveis de ambiente e o que configuram (sem valores reais)]

---

*Documento gerado como estrutura base — substituir os itens entre [ ] pelo conteúdo real.*
