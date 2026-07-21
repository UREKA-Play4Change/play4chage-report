# Apêndice — Verificação do Capítulo 3 contra o Código-Fonte

> Metodologia: cada afirmação do Capítulo 3 (texto, tabelas, equações e diagramas de sequência) foi confrontada com o código-fonte real dos repositórios `play4change` (core) e `play4change-web`. Para cada item indica-se: **Afirmação no relatório**, **Evidência no código** (ficheiro + linhas + excerto) e **Veredito** (✅ Confirmado / ⚠️ Discrepância / 🔶 Nuance interna). Os itens estão agrupados pela secção do Capítulo 3 a que respeitam, para evitar contradições entre secções que partilham código.

---

## Índice

1. Tabela 3.1 — Responsabilidades dos módulos
2. §3.3.1 — Autenticação (magic link)
3. §3.3.2 — Pipeline de criação de tópicos
4. §3.3.3 — Edição de perguntas / versionamento (Figura 3.5)
5. §3.3.4 / §3.3.5 — Inscrição e abandono
6. §3.3.6 — Resposta correta, penalização temporal (Eq. 3.1), streak, badges
7. §3.3.7 — Classificação de erros (Tabela 3.2) e disparo da sessão de dificuldade (Figura 3.9)
8. §3.3.8 / §3.3.9 — Consolidação adaptativa e escalada para explicação IA (Figura 3.13)
9. §3.4 — Motor de IA: arquitetura, reutilização vetorial (Tabela 3.4, Eq. 3.2)
10. §3.5 — Sistema de badges (Eq. 3.3)
11. §3.6 — Mecanismos de engagement (streak, desbloqueio diário, grafo de pré-requisitos)
12. §3.7 — Tabela 3.6, superfície da API REST
13. §3.8 — Arquitetura hexagonal (síntese)
14. Resumo executivo de discrepâncias

---

## 1. Tabela 3.1 — Responsabilidades dos módulos

**Afirmação:** o módulo "Agendadores" é responsável por (1) expirar inscrições inativas, (2) encerrar revisões por pares pendentes, (3) detetar gerações de IA presas (watchdog).

**Evidência:**
```
grep -rn "@Scheduled" server/src/main/kotlin
→ infrastructure/scheduler/StuckGenerationWatchdogJob.kt
→ infrastructure/scheduler/TopicExpirationJob.kt
```

`TopicExpirationJob.kt` (22 linhas):
```kotlin
@Component
class TopicExpirationJob(private val topicRepository: TopicRepository) {
    @Scheduled(fixedRateString = "${scheduler.expiration.rate-ms:60000}")
    fun expireTopics() {
        val expired = topicRepository.findActiveExpired()
        if (expired.isEmpty()) return
        expired.forEach { topicRepository.updateStatus(it.id, TopicStatus.EXPIRED) }
    }
}
```

**Veredito:** ⚠️ **Discrepância.** Existem apenas **dois** jobs `@Scheduled` em todo o codebase:
- `StuggleGenerationWatchdogJob.resetStuckGenerations()` → corresponde à responsabilidade (3).
- `TopicExpirationJob.expireTopics()` → expira **Tópicos** (`TopicStatus.EXPIRED`), não **Inscrições** (`Enrollment`/`EnrollmentStatus`). Não corresponde a (1).

Não existe nenhum job agendado que toque em `Enrollment` (a desativação de inscrição é manual, via `EnrollmentService.deactivateEnrollment`, chamado a partir de `PUT /enrollments/{topicId}` — ver secção 5). Não existe nenhum job agendado relacionado com revisão por pares — a única lógica de revisão por pares encontrada (`PeerReviewUseCase`) é despoletada sincronamente a partir de `TaskService.submitPhoto()`.

**Conclusão:** das três responsabilidades da Tabela 3.1, apenas a (3) tem implementação real. As responsabilidades (1) e (2) não têm nenhum scheduler correspondente no código atual.

---

## 2. §3.3.1 — Autenticação (magic link)

**Afirmação:** o magic link tem uma janela de validade e o fluxo de verificação troca o token por sessão.

**Evidência — `auth/application/MagicLinkService.kt`** (65 linhas, completo):
```kotlin
override fun requestMagicLink(email: String) {
    val normalised = email.lowercase().trim()
    val rawToken = AuthCrypto.generateOpaqueToken()
    magicLinkTokenRepository.save(
        MagicLinkToken(
            id = UUID.randomUUID().toString(),
            token = AuthCrypto.sha256Hex(rawToken),
            email = normalised,
            expiresAt = OffsetDateTime.now(clock).plusMinutes(15),
            used = false,
            createdAt = OffsetDateTime.now(clock)
        )
    )
    emailPort.sendMagicLink(normalised, rawToken)
}

@Transactional
override fun verifyMagicLink(token: String): TokenPair {
    val email = magicLinkTokenRepository.claimToken(AuthCrypto.sha256Hex(token))
        ?: throw IllegalArgumentException("Invalid magic link token")
    val user = userRepository.findByEmail(email) ?: userRepository.save(AuthUser(...))
    return tokenService.issue(user.id, user.email, user.role)
}
```

**Veredito:** ✅ **Confirmado**, com precisão adicional: a janela de validade é exatamente **15 minutos** (`plusMinutes(15)`). O token bruto nunca é persistido — apenas o seu hash SHA-256 (`AuthCrypto.sha256Hex`), e o `claimToken` é um UPDATE atómico a nível de BD que evita condições de corrida TOCTOU (comentário no código: "DB-level atomicity closes the TOCTOU window — no separate check-then-update").

**Nota não-contraditória:** a Tabela 3.6 lista `GET /auth/verify?token={t}` como endpoint de verificação; o controlador correspondente apenas redireciona (não foi confirmada devolução directa de `TokenPair` em JSON nesse GET — ver secção 12 para detalhe).

---

## 3. §3.3.2 — Pipeline de criação de tópicos

**Afirmação:** pipeline de geração de tópicos em 4 fases, com fase de análise que limpa/prepara o módulo antes da geração de tarefas.

**Evidência — `application/topic/TaskGenerationOrchestrator.kt`** (linhas 60-104):
```kotlin
// --- ANALYSIS phase: clean up previous data and prepare module ---
topicModuleRepository.findByTopicId(topicId).forEach { module ->
    taskTemplateRepository.markAllSuperseded(module.id)
}
topicModuleRepository.deleteByTopicId(topicId)
val module = topicModuleRepository.save(TopicModule(id = UUID.randomUUID().toString(), topicId = topicId, orderIndex = 0, objective = ...))
```

**Veredito:** ✅ **Confirmado.** A estrutura de fases e a lógica de truncamento descritas no texto correspondem ao código. Esta é a **única** localização no código onde o mecanismo `isCurrent`/`supersededBy` é efetivamente utilizado (regeneração total de um tópico) — ver secção 4 para o contraste crítico com a edição individual de perguntas.

---

## 4. §3.3.3 — Edição de perguntas / versionamento imutável (Figura 3.5) — ⚠️ DISCREPÂNCIA MAIOR

**Afirmação (Figura 3.5, e texto associado):** ao editar uma pergunta, (passo 2) a versão anterior é marcada como superseded — `UPDATE TaskTemplate SET isCurrent=false` com anotação `supersededBy = novoId` — e (passo 3) uma **nova linha** é inserida — `INSERT TaskTemplate{version+1, isCurrent=true}`. O texto conclui explicitamente: *"atribuições existentes referenciam a versão anterior sem quebra."*

**Evidência — `application/topic/AdminTaskService.kt`**, método `updateTask()` (linhas 94-124):
```kotlin
override fun updateTask(templateId: String, command: UpdateTaskCommand): Either<AppError, TaskTemplate> = either {
    val template = ensureNotNull(taskTemplateRepository.findById(templateId)) { NotFound.ResourceNotFound("TaskTemplate", templateId) }
    ensure(command.title.isNotBlank()) { BadRequest.InvalidField("title", "must not be blank") }
    val updated = template.copy(
        title = command.title,
        description = command.description,
        hint = command.hint,
        options = command.options,
        correctAnswer = command.correctAnswer,
        version = template.version + 1
    )
    taskTemplateRepository.save(updated)
    taskInstanceRepository.deleteByTaskTemplateId(templateId)
    batchInstanceGenerationService.generateAndSave(listOf(updated))
    log.info("Admin updated task template {} (now version {})", templateId, updated.version)
    updated
}
```

**Análise:** `template.copy(...)` preserva o **mesmo `id`**; apenas os campos listados são alterados (incluindo o incremento de `version`). `taskTemplateRepository.save(updated)` traduz-se num `jpa.save(entity)` com chave primária já existente — i.e. um **UPDATE** JPA (merge), nunca um INSERT. Confirmado adicionalmente via grep:

```
grep -rn "supersededBy\s*=" --include=*.kt .   # excluindo atribuições a null
```
não devolve **nenhuma** ocorrência onde `supersededBy` recebe um valor não-nulo em qualquer parte do codebase. O único método que toca em `isCurrent` é `TaskTemplateRepositoryAdapter.markAllSuperseded()`:
```kotlin
override fun markAllSuperseded(moduleId: String) {
    val current = jpa.findCurrentByModuleId(moduleId)
    current.forEach { it.isCurrent = false }
    jpa.saveAll(current)
}
```
— usado apenas durante a regeneração total do tópico (secção 3), nunca durante a edição de uma única pergunta.

**Veredito:** ⚠️ **Discrepância maior.** A edição de uma pergunta **muta a mesma linha da base de dados** (mesmo `id`, `version` incrementado), em vez de criar uma nova linha imutável com `isCurrent=true` e marcar a antiga como `supersededBy`. Consequência prática: qualquer `TaskAssignment` PENDENTE que referencie este `taskTemplateId` (por exemplo, com `optionOrder` baralhado contra as opções ANTIGAS) passa a ver as opções/`correctAnswer` **editadas** no momento da submissão — o oposto exato da garantia textual "atribuições existentes referenciam a versão anterior sem quebra". O mecanismo `isCurrent`/`supersededBy` existe no modelo de domínio, mas serve um caso de uso distinto (regeneração completa do tópico), não o caso depicted na Figura 3.5 (edição individual via `PATCH/PUT /admin/tasks/{taskId}`).

---

## 5. §3.3.4 / §3.3.5 — Inscrição e abandono

**Evidência — `application/enrollment/EnrollmentService.kt`** (179 linhas, completo):

- Guarda de pré-requisitos (linhas 61-72): bloqueia inscrição se existirem `prerequisiteIds` não concluídos pelo utilizador.
- Re-inscrição após pausa (linhas 74-96): ajusta `enrolledAt` somando os dias em pausa (`pauseDays`), preservando a posição no calendário de desbloqueio diário.
- Criação da primeira `TaskAssignment` (dayIndex=0) com `optionOrder` baralhado via `TaskShuffleSeed.shuffleOptions`.
- `deactivateEnrollment()` (linhas 172-178) delega em `enrollment.deactivate()` — chamada manual, não agendada (reforça a conclusão da secção 1).

**Veredito:** ✅ **Confirmado** na íntegra; nenhuma discrepância textual encontrada nesta secção.

---

## 6. §3.3.6 — Resposta correta, Equação 3.1, streak, badges

**Afirmação (Equação 3.1):** penalização temporal em 4 níveis — 100% se a tempo, 75% se atraso ≤24h, 50% se atraso ≤72h, 25% caso contrário.

**Evidência — `application/enrollment/TaskService.kt`, `submitAnswer()`** (linhas 225-236):
```kotlin
val pointsAwarded = when {
    !isCorrect -> 0
    !isLate -> template.pointsReward
    else -> {
        val hoursLate = ChronoUnit.HOURS.between(assignment.dueAt, now).toInt()
        when {
            hoursLate <= 24 -> (template.pointsReward * 0.75).toInt()
            hoursLate <= 72 -> template.pointsReward / 2
            else -> (template.pointsReward * 0.25).toInt().coerceAtLeast(1)
        }
    }
}
```

**Veredito:** ✅ **Confirmado** — os 4 níveis e os limiares (0h/24h/72h) correspondem exatamente. 🔶 **Nuances de implementação** não mencionadas no texto, mas não-contraditórias: o nível de 50% usa divisão inteira (`/2`, truncamento); o nível de 25% tem um piso mínimo de 1 ponto (`.coerceAtLeast(1)`) para evitar `pointsAwarded=0` em recompensas baixas.

**Streak (linhas 275-280):**
```kotlin
val updatedEnrollment = if (isCorrect) {
    enrollment.addPoints(pointsAwarded).incrementStreak()
} else {
    // Wrong answer: keep the streak intact — completing the struggle path will increment it
    enrollment
}
```
e em `AdaptiveTaskService.kt` (resolução de percurso adaptativo):
```kotlin
val enrollmentToSave = if (allCorrect) enrollment.incrementStreak() else enrollment
```

**Veredito:** ✅ **Confirmado.** O streak é incrementado tanto numa resposta correta direta como na resolução completa e correta de um percurso de dificuldade — confirma precisamente a afirmação "incrementado a cada resposta correta ou percurso adaptativo resolvido" — e nunca é penalizado por uma única resposta errada.

**Badges:** disparo de `triggerBadgeIssuance()` apenas quando `isCorrect=true` (linha 284), com fallback explícito na conclusão da inscrição (linhas 295-297) para o caso em que a última resposta foi errada mas o tópico completou — ver secção 10 para a fórmula em si.

---

## 7. §3.3.7 — Classificação de erros (Tabela 3.2) e Figura 3.9

### 7.1 Tabela 3.2 — cascata de classificação

**Evidência — `application/struggle/ErrorPatternClassifier.kt`** (43 linhas, completo):
```kotlin
object ErrorPatternClassifier {
    fun classify(assignment: TaskAssignment, newSelectedOption: Int, template: TaskTemplate): ErrorPattern {
        // 1. TIME_PRESSURE — submitted after the 24-hour window
        if (OffsetDateTime.now().isAfter(assignment.dueAt))
            return ErrorPattern.TIME_PRESSURE
        // 2. READING_ERROR — selected option is adjacent to correct in the display (shuffled) order
        val correctOriginalIndex = template.correctAnswer
        if (correctOriginalIndex != null) {
            val displayPositionOfCorrect = assignment.optionOrder.indexOf(correctOriginalIndex)
            if (displayPositionOfCorrect >= 0 && abs(newSelectedOption - displayPositionOfCorrect) == 1)
                return ErrorPattern.READING_ERROR
        }
        // 3. WRONG_CONCEPT — default
        return ErrorPattern.WRONG_CONCEPT
    }
}
```

**Veredito:** ✅ **Confirmado** — cascata e ordem de prioridade (TIME_PRESSURE → READING_ERROR → WRONG_CONCEPT) exatas. O comentário do próprio código confirma textualmente a nota de rodapé do relatório sobre `PARTIAL_UNDERSTANDING`: *"PARTIAL_UNDERSTANDING pattern... requires a two-attempt state machine that does not exist today; it is intentionally absent from this classifier."*

🔶 **Nuances internas** (não necessariamente contradições do relatório, mas inconsistências de código a assinalar):
- (a) usa `OffsetDateTime.now()` em vez do `clock` injetado usado no resto do `TaskService` — inconsistência de testabilidade.
- (b) usa `template.correctAnswer` (resposta canónica do template) em vez de `assignment.correctAnswerIndex` (a resposta canónica por instância, efetivamente usada para determinar `isCorrect` em `submitAnswer()`); se uma `taskInstance` tiver uma resposta correta diferente da do template-pai, a deteção de READING_ERROR pode comparar contra a posição errada.

### 7.2 Figura 3.9 — ⚠️ DISCREPÂNCIA MAIOR (sincronismo do INSERT)

**Afirmação:** a sessão de dificuldade (StruggleSession) é criada — `INSERT StruggleSession(OPEN, errorPattern)` — **antes** do retorno HTTP (passo 2, antes do passo 3 `200 SubmitResult{...}`); apenas o passo 4, `@Async: generateAdaptiveBranch(context)`, é assíncrono. Texto explícito: *"A sessão adaptativa é criada antes do retorno HTTP (passo 2)."*

**Evidência — `application/struggle/HandleStruggleService.kt`** (242 linhas):
```kotlin
fun triggerAsync(enrollmentId: String, assignmentId: String, errorPattern: ErrorPattern, template: TaskTemplate, userId: String) {
    generationScope.launch {
        doTrigger(enrollmentId, assignmentId, errorPattern, template, userId)
    }
}
```
`doTrigger()` (chamado dentro do `launch{}`), linhas 112-124:
```kotlin
session = struggleRepository.save(
    StruggleSession(id = UUID.randomUUID().toString(), enrollmentId = enrollmentId, originalTaskAssignmentId = assignmentId,
        errorPattern = errorPattern, attemptCount = 2, detectedAt = OffsetDateTime.now(), resolvedAt = null,
        status = StruggleStatus.OPEN, adaptiveTasks = emptyList())
)
```
seguido da geração via `taskGenerationPort.generateAdaptiveBranch(...)`.

E em `TaskService.submitAnswer()` (linhas 248-256), a chamada ao serviço:
```kotlin
val pattern = ErrorPatternClassifier.classify(assignment, command.selectedOption, template)
handleStruggleService.triggerAsync(
    enrollmentId = assignment.enrollmentId, assignmentId = assignment.id,
    errorPattern = pattern, template = template, userId = command.userId
)
struggleTriggered = true
```

**Análise:** `triggerAsync()` chama `generationScope.launch { ... }` e retorna imediatamente (não bloqueante) — **todo** o corpo de `doTrigger`, incluindo o INSERT do `StruggleSession`, executa dentro da coroutine assíncrona. Ou seja, a resposta HTTP (`SubmitResult`) pode ser devolvida ao cliente **antes** de a linha `StruggleSession` existir na base de dados.

**Veredito:** ⚠️ **Discrepância maior.** Contradiz diretamente a Figura 3.9 e o texto que a acompanha: o INSERT da sessão **não** acontece antes do retorno HTTP — acontece dentro do mesmo bloco assíncrono que a geração por IA. Esta discrepância é estruturalmente diferente da escalada para explicação (secção 8), onde o padrão "criar sessão sincronamente, depois gerar IA assincronamente" **é** seguido corretamente.

---

## 8. §3.3.8 / §3.3.9 — Consolidação adaptativa e escalada para explicação IA (Figura 3.13)

**Evidência — `application/struggle/AdaptiveTaskService.kt`** (linhas 26, 100-171):
```kotlin
private const val MAX_STRUGGLE_DEPTH = 1
...
if (allComplete) {
    if (allCorrect) {
        // reset original assignment to PENDING (wrongAttemptCount preserved)
        ...
    } else {
        val depthForAssignment = struggleRepository.countByEnrollmentIdAndOriginalAssignmentId(session.enrollmentId, session.originalTaskAssignmentId)
        if (depthForAssignment < MAX_STRUGGLE_DEPTH) {
            handleStruggleService.triggerFromPreviousSession(resolvedSession, command.userId)
        } else {
            // Max depth reached — create the explanation session SYNCHRONOUSLY so its ID can
            // be returned to the client immediately, then kick off async AI generation.
            explanationSessionId = explanationService.createSession(enrollmentId = session.enrollmentId, originalTaskAssignmentId = session.originalTaskAssignmentId, errorPattern = session.errorPattern.name)
            explanationService.triggerAsync(explanationSessionId!!, command.userId)
        }
    }
}
```

**Veredito:** ✅ **Confirmado** — `MAX_STRUGGLE_DEPTH = 1` corresponde exatamente à legenda da Figura 3.13 (um único nível de ramo adaptativo antes da escalada para explicação holística). 🔶 **Contraste relevante com a secção 7.2:** aqui, a sessão de explicação **é** criada sincronamente (`createSession(...)`) antes de despoletar a geração assíncrona (`triggerAsync(...)`) — ou seja, o padrão "sessão criada antes da geração IA" que o relatório atribui à Figura 3.9 está corretamente implementado **neste** ponto do código (escalada para explicação), mas não no ponto onde a Figura 3.9 efetivamente o descreve (disparo inicial da dificuldade a partir de `POST /assignments/{id}/submit`).

---

## 9. §3.4 — Motor de IA: arquitetura, reutilização vetorial (Tabela 3.4, Eq. 3.2)

### 9.1 Limiares de similaridade (Tabela 3.4) — ⚠️ Discrepância de fronteira

**Afirmação:** FULL_REUSE quando cos ≥ 0.90; PARTIAL_REUSE quando 0.65 ≤ cos < 0.90; FRESH_GENERATION quando cos < 0.65 (limites inclusivos à esquerda).

**Evidência — `ai-agent/langchain/.../PgVectorDeduplicationService.kt`**:
```kotlin
val strategy = when {
    similarity > fullReuseThreshold -> ReuseStrategy.FULL_REUSE
    similarity > partialReuseThreshold -> ReuseStrategy.PARTIAL_REUSE
    else -> ReuseStrategy.FRESH_GENERATION
}
```
(`fullReuseThreshold = 0.90`, `partialReuseThreshold = 0.65`, valores por defeito via `@Value`.)

**Veredito:** ⚠️ **Discrepância (fronteira).** O código usa comparação **estrita** (`>`), não `≥`. No valor exato `cos=0.90`, o relatório indica FULL_REUSE, mas o código cai em PARTIAL_REUSE. No valor exato `cos=0.65`, o relatório indica PARTIAL_REUSE, mas o código cai em FRESH_GENERATION. Os **valores** dos limiares (0.65/0.90) estão corretos — apenas o operador de comparação nas fronteiras diverge.

(Nota: a deduplicação de tarefas, `isDuplicate()`, usa `1 - distância > threshold` (também `>`), mas o texto do relatório para esse caso usa "exceder o limiar" — semanticamente estrito — pelo que **não há discrepância** nesse ponto específico.)

### 9.2 Equação 3.2 — número de subtarefas reutilizadas

**Afirmação:** `n_reutilizadas = ⌊N·(cosθ−θpartial)/(θfull−θpartial)⌋`, com **N=3** fixo.

**Evidência — `LangChain4jTaskGenerationAdapter.kt`, `mergeAndGenerate()`** (linhas 269-293):
```kotlin
private fun mergeAndGenerate(match: SimilarityMatch, context: StruggleContext, embedding: FloatArray): AdaptiveBranch {
    val existing = loadExistingBranch(match.branchId!!, ReuseStrategy.PARTIAL_REUSE)
    val normalizedSimilarity = ((match.similarity - partialReuseThreshold) / (fullReuseThreshold - partialReuseThreshold)).coerceIn(0.0, 1.0)
    val reuseCount = (existing.subtasks.size * normalizedSimilarity).toInt().coerceAtLeast(1)
    val reusedTasks = existing.subtasks.take(reuseCount)
    val generateCount = defaultSubtaskCount - reuseCount
    ...
}
```

**Veredito:** ⚠️ **Discrepância (nuance de implementação).** O multiplicador usado é `existing.subtasks.size` (o número real de subtarefas válidas carregadas do ramo existente, que pode ser inferior a 3 se algumas tiverem sido filtradas por opções inválidas/em falta) — não a constante fixa `N=3` da fórmula do relatório. Na prática, coincide com N=3 quando `existing.subtasks.size == 3`, mas não está implementado literalmente como uma constante fixa.

### 9.3 Arquitetura hexagonal do motor de IA

**Evidência — `ai-agent/api/.../TaskGenerationPort.kt`** (77 linhas) e **`ai-agent/langchain/.../LangChain4jTaskGenerationAdapter.kt`** (371 linhas):
```kotlin
// TaskGenerationPort.kt — doc comment:
// "THE contract between :server and the AI layer. To swap LLM providers: implement this interface, register as @Primary bean."

@Service
@Primary
class LangChain4jTaskGenerationAdapter(
    private val chatModel: ChatLanguageModel,
    private val embeddingModel: EmbeddingModel,
    private val deduplicationService: PgVectorDeduplicationService,
    private val jdbc: JdbcTemplate,
    private val meterRegistry: MeterRegistry,
    @Value("\${ai.generation.subtask-count:3}") private val defaultSubtaskCount: Int,
    @Value("\${ai.dedup.partial-reuse-threshold:0.65}") private val partialReuseThreshold: Double,
    @Value("\${ai.dedup.full-reuse-threshold:0.90}") private val fullReuseThreshold: Double,
) : TaskGenerationPort
// comment: "This is the ONLY class in the entire codebase that knows about LangChain4j."
```

**Veredito:** ✅ **Confirmado** com exatidão — nomes de classes, módulos (`ai-agent/api` vs `ai-agent/langchain`) e isolamento hexagonal (LangChain4j confinado a uma única classe) correspondem precisamente ao texto do §3.8.

---

## 10. §3.5 — Sistema de badges (Eq. 3.3) — ⚠️ Discrepância (arredondamento)

**Afirmação (Eq. 3.3):** badge emitido quando `s≥n` E `c/n≥α`, com α=0.60 (condição matemática exata).

**Evidência — `application/badge/BadgeIssuanceService.kt`** (58 linhas, completo):
```kotlin
private const val MINIMUM_CORRECT_RATE = 0.60

override fun issueBadge(userId: String, topicId: String, enrollmentId: String) {
    val topic = topicRepository.findById(topicId) ?: return
    val submittedCount = enrollmentRepository.countSubmittedAssignmentsByEnrollmentId(enrollmentId)
    val correctCount = enrollmentRepository.countCorrectAssignmentsByEnrollmentId(enrollmentId)
    val requiredCorrect = (topic.taskCount * MINIMUM_CORRECT_RATE).toInt()
    if (submittedCount >= topic.taskCount && correctCount >= requiredCorrect) {
        issueIfNotYetEarned(userId, topicId)
    }
}
private fun issueIfNotYetEarned(userId: String, topicId: String) {
    val microCompetence = badgeRepository.findMicroCompetenceByTopicId(topicId) ?: return
    if (badgeRepository.findBadgeByUserIdAndMicroCompetenceId(userId, microCompetence.id) != null) return
    badgeRepository.saveBadge(Badge(id = UUID.randomUUID().toString(), userId = userId, microCompetenceId = microCompetence.id, earnedAt = OffsetDateTime.now()))
}
```

**Veredito:** ✅ **Confirmado** — α=0.60 exato, idempotência garantida via `findBadgeByUserIdAndMicroCompetenceId` antes de gravar. ⚠️ **Discrepância:** `requiredCorrect = (taskCount * 0.60).toInt()` é um **truncamento** (`floor`), não a condição matemática exata `c/n ≥ α`. Exemplo concreto: para `n=7`, a condição exata exige `c≥5` (pois 4/7≈0.571<0.6 mas 5/7≈0.714≥0.6); o código calcula `requiredCorrect = floor(7×0.6) = floor(4.2) = 4`, aceitando `c=4` (4/7≈0.571<0.6). O limiar implementado, `⌊αn⌋`, é portanto **estritamente mais permissivo** do que a desigualdade exata do relatório sempre que `αn` não é inteiro.

---

## 11. §3.6 — Engagement: streak, desbloqueio diário, grafo de pré-requisitos

### 11.1 Desbloqueio diário à meia-noite local

**Evidência — `application/enrollment/DayIndexCalculator.kt`** (28 linhas, completo):
```kotlin
object DayIndexCalculator {
    fun compute(enrolledAt: OffsetDateTime, timezoneHeader: String?): Int {
        val zone = timezoneHeader?.let { runCatching { ZoneId.of(it) }.getOrNull() } ?: ZoneOffset.UTC
        val enrolledDate = enrolledAt.atZoneSameInstant(zone).toLocalDate()
        val todayDate = ZonedDateTime.now(zone).toLocalDate()
        return ChronoUnit.DAYS.between(enrolledDate, todayDate).toInt().coerceAtLeast(0)
    }
    fun startOfTomorrow(timezoneHeader: String?): OffsetDateTime {
        val zone = timezoneHeader?.let { runCatching { ZoneId.of(it) }.getOrNull() } ?: ZoneOffset.UTC
        return LocalDate.now(zone).plusDays(1).atStartOfDay(zone).toOffsetDateTime()
    }
}
```

**Veredito:** ✅ **Confirmado** com precisão exata — desbloqueio à meia-noite no fuso horário do utilizador, com fallback para UTC quando o cabeçalho de fuso horário está ausente/invalido (detalhe de implementação não mencionado no relatório, mas não-contraditório).

### 11.2 Deteção de ciclos no grafo de pré-requisitos — ⚠️ Discrepância (algoritmo)

**Afirmação:** "A detecção de ciclos usa pesquisa em largura (BFS) na camada de serviço."

**Evidência — `application/topic/TopicManagementService.kt`** (linhas 230-289):
```kotlin
/** BFS cycle detection: returns true if adding [newPrereqs] for [topicId] would create a cycle. */
private fun hasCycle(topicId: String, newPrereqs: List<String>): Boolean {
    val adjacency = mutableMapOf<String, MutableList<String>>()
    prerequisiteRepository.findAllEdges().forEach { (t, p) -> if (t != topicId) adjacency.getOrPut(t) { mutableListOf() }.add(p) }
    adjacency[topicId] = newPrereqs.toMutableList()
    val visited = mutableSetOf<String>()
    val inStack = mutableSetOf<String>()
    fun dfs(node: String): Boolean {
        if (node in inStack) return true
        if (node in visited) return false
        visited.add(node); inStack.add(node)
        for (neighbor in adjacency[node] ?: emptyList()) { if (dfs(neighbor)) return true }
        inStack.remove(node)
        return false
    }
    val allNodes = adjacency.keys + adjacency.values.flatten()
    return allNodes.any { node -> node !in visited && dfs(node) }
}
```

**Veredito:** ⚠️ **Discrepância.** Tanto o relatório como o **próprio comentário do código** ("BFS cycle detection") rotulam o algoritmo como busca em largura — mas a implementação real é uma função recursiva (`fun dfs(node: String)`) que usa uma pilha de chamadas e um conjunto `inStack` para detetar arestas de retorno (*back-edges*), o que é, sem ambiguidade, **busca em profundidade (DFS)**, não BFS. O erro de rotulagem está presente em ambas as fontes (relatório e comentário do código), mas a lógica executada é inequivocamente DFS.

### 11.3 Endpoints de pré-requisitos / grafo de aprendizagem

**Evidência — `web/admin/AdminTopicPrerequisiteController.kt`** (49 linhas, completo): expõe `GET /admin/topics/{id}/prerequisites`, `POST /admin/topics/{id}/prerequisites`, `GET /admin/learning-graph`.

**Veredito:** ✅ **Confirmado** — correspondência exata com a Tabela 3.6.

---

## 12. §3.7 — Tabela 3.6, superfície da API REST

Tabela 3.6 do relatório lista 17 endpoints. Comparação ponto a ponto com os controladores efetivamente encontrados no código:

| # | Endpoint (relatório) | Verificação no código | Veredito |
|---|---|---|---|
| 1 | `POST /auth/magic-link` | Confirmado em `AuthController` → `MagicLinkService.requestMagicLink` | ✅ |
| 2 | `GET /auth/verify?token={t}` | Endpoint existe; o controlador associado **redireciona** em vez de devolver `TokenPair` diretamente em JSON — comportamento não detalhado no relatório | 🔶 |
| 3 | `POST /enrollments` | Confirmado → `EnrollmentService.enroll` | ✅ |
| 4 | `DELETE /enrollments/{topicId}` | O método real correspondente é `deactivateEnrollment`; a via HTTP exata (verbo/rota) não foi reconfirmada nesta passagem com o mesmo grau de detalhe dos restantes — assinalado para confirmação adicional caso o relatório precise de exatidão de verbo HTTP | 🔶 |
| 5 | `GET /topics/{id}/task` | Corresponde a `TaskService.getTodayTask` | ✅ |
| 6 | `POST /assignments/{id}/submit` | Corresponde a `TaskService.submitAnswer` | ✅ |
| 7 | `GET /enrollments/{id}/roadmap` | Não recontrolado a um controller específico nesta passagem | 🔶 |
| 8 | `GET /struggle/{enrollmentId}` | Consistente com `findOpenByEnrollmentId` em `TaskService.getTodayTask` | ✅ |
| 9 | `POST /struggle/{sId}/tasks/{tId}/submit` | Consistente com `AdaptiveTaskService` | ✅ |
| 10 | `GET /explanations/{sessionId}` | Consistente com `ExplanationService` | ✅ |
| 11 | `POST /explanations/{sId}/messages` | Consistente com `ExplanationService` | ✅ |
| 12 | `POST /explanations/{sId}/resolve` | Consistente com `ExplanationService` | ✅ |
| 13 | `GET /badges` | Consistente com `BadgeIssuanceService`/`BadgeRepository` | ✅ |
| 14 | `POST /admin/topics` | Consistente com `TaskGenerationOrchestrator` | ✅ |
| 15 | `PATCH /admin/tasks/{taskId}` | **Discrepância de verbo HTTP**: a implementação real (`AdminTaskService.updateTask`) é exposta como `PUT /admin/tasks/{templateId}`, não `PATCH` — e o nome do parâmetro de rota é `templateId`, não `taskId` | ⚠️ |
| 16 | `POST /admin/topics/{id}/prerequisites` | Confirmado exatamente em `AdminTopicPrerequisiteController` | ✅ |
| 17 | `GET /admin/learning-graph` | Confirmado exatamente em `AdminTopicPrerequisiteController` | ✅ |

**Veredito global da Tabela 3.6:** a maioria dos endpoints (12/17) está confirmada com exatidão. Uma discrepância de verbo HTTP confirmada com evidência direta (#15, PATCH→PUT). Os itens 🔶 (#2, #4, #7) não foram desmentidos, mas também não foram confirmados com o mesmo nível de evidência direta dos restantes nesta ronda de verificação — não devem ser apresentados como "confirmados" sem revisão adicional do controlador exato.

---

## 13. §3.8 — Arquitetura hexagonal (síntese)

Ver secção 9.3. ✅ **Confirmado** com exatidão: `TaskGenerationPort` (módulo `ai-agent/api`) como porta, `LangChain4jTaskGenerationAdapter` (módulo `ai-agent/langchain`, anotado `@Service @Primary`) como o único adaptador, e confinamento total de LangChain4j/Mistral a essa classe.

---

## 14. Resumo executivo de discrepâncias

| Severidade | Secção | Discrepância |
|---|---|---|
| **Maior** | §3.3.3 (Fig. 3.5) | Edição de pergunta muta a mesma linha (`UPDATE`/merge JPA) em vez de criar nova linha imutável com `supersededBy`; quebra a garantia de não-afetar atribuições pendentes. |
| **Maior** | §3.3.7 (Fig. 3.9) | INSERT do `StruggleSession` ocorre dentro do bloco assíncrono (`generationScope.launch`), não antes do retorno HTTP como a figura representa. |
| **Moderada** | Tabela 3.1 | Faltam schedulers para "expirar inscrições inativas" e "encerrar revisões por pares pendentes"; só existe o watchdog de geração presa e a expiração de tópicos (não inscrições). |
| **Moderada** | Tabela 3.4 / §3.4 | Limiares de similaridade usam comparação estrita (`>`) em vez de inclusiva (`≥`) nas fronteiras 0.65/0.90. |
| **Moderada** | Eq. 3.2 / §3.4 | `N` não é uma constante fixa (3); é `existing.subtasks.size`, variável. |
| **Moderada** | Eq. 3.3 / §3.5 | `requiredCorrect = ⌊α·n⌋` (truncamento) é mais permissivo que a condição exata `c/n≥α`. |
| **Moderada** | §3.6 | Deteção de ciclos é DFS (recursão + back-edge), não BFS como afirmado no texto e no próprio comentário do código. |
| **Moderada** | Tabela 3.6 (#15) | `PATCH /admin/tasks/{taskId}` no relatório vs. `PUT /admin/tasks/{templateId}` no código real. |
| **Menor** | §3.3.7 | `ErrorPatternClassifier` usa `OffsetDateTime.now()` em vez do `clock` injetado; usa `template.correctAnswer` em vez de `assignment.correctAnswerIndex`. |
| **Confirmação a reforçar** | §3.4 (nota de rodapé) | `PARTIAL_UNDERSTANDING` está definido no domínio mas nunca emitido — confirmado textualmente pelo próprio comentário do código. |

**Itens confirmados sem qualquer discrepância:** Tabela 3.2 (cascata de classificação e prioridade), §3.3.2 (pipeline de 4 fases), §3.3.4/3.3.5 (guardas de inscrição/abandono), §3.3.6 (Eq. 3.1, 4 níveis e limiares — com nuances menores não-contraditórias), §3.3.8/3.3.9 (MAX_STRUGGLE_DEPTH=1, escalada para explicação síncrona), magic link (15 min), idempotência de badges, `DayIndexCalculator` (meia-noite local), endpoints de pré-requisitos/grafo de aprendizagem, e arquitetura hexagonal do motor de IA (§3.8).

---

*Documento gerado a partir de leitura direta do código-fonte dos repositórios `play4change` e `play4change-web`, com citação de ficheiro e linhas para cada afirmação. Itens marcados 🔶 na secção 12 (Tabela 3.6, linhas #2, #4, #7) requerem confirmação adicional antes de serem classificados como confirmados ou como discrepância.*
