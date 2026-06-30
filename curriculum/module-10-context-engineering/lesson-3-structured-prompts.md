---
type: lesson
module: 10
lesson: 3
title: "Structured Prompts"
duration: "30 phút"
prerequisites: ["module-10/lesson-2"]
narrative_phase: "context engineering"
migration_phase: "Phase 5: Reusable prompt templates kết hợp DDD knowledge"
business_invariant: "Structured Prompt = Template + Domain Context + Output Format; Ad-hoc prompt = 3 rounds to correct; Prompt Library = version-controlled, reusable templates; Chain-of-Thought with DDD = systematic design flow"
---

# Lesson 10.3: Structured Prompts — "Prompt hay = Domain knowledge + Structure"

## 📍 Context — Bạn đang ở đây

> Lesson 10.1-10.2: Context Window (chọn lọc knowledge) + RAG (auto-retrieve). AI có đúng context rồi — nhưng developer vẫn viết prompt ad-hoc mỗi lần: "Tạo API cho X" → AI thiếu structure → output inconsistent. **Structured Prompts** = reusable templates bao gồm Task + Domain Context + Business Rules + Output Format → 1 shot, đúng luôn.

## 🔥 Tension — "3 rounds mới đúng vì prompt quá đơn giản"

Sprint 17. Team review AI usage:

> **Dev A:** *"Mỗi lần tôi cần generate code, tôi type: 'Tạo API cho allocation.' AI output generic CRUD. Round 2: thêm business rules. Round 3: sửa terminology. Round 4: thêm events. 4 rounds = 20 phút."*

> **Dev B:** *"Tôi cũng vậy — nhưng mỗi lần nhắc rules khác nhau. Lần trước tôi quên ADR-001, AI generate Man-Day. Lần này quên events. Không consistent."*

> **PM Loan:** *"Team dùng AI nhưng productivity không tăng vì phải iterate nhiều rounds. Có cách nào 1 shot đúng luôn?"*

**Tuấn:**
> *"Ad-hoc prompt = phụ thuộc vào developer nhớ gì. Structured Prompt = template chứa sẵn Task + Domain Context + Business Rules + Events + Output Format. Developer fill template → AI nhận đủ context → 1 shot đúng. Và template tái sử dụng cho cả team."*

> 💭 **Câu hỏi:** 4 rounds × 10 tasks/day = 40 rounds wasted. Structured Prompt = 1 round × 10 tasks = 10 rounds. **Savings: 75% AI interaction time.** Template = investment up-front, savings forever.

## 🎓 Explanation — Structured Prompts

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Structured Prompt = Template (reusable structure) + Domain Context (from M10.1/10.2) + Output Format (specific deliverables). Ad-hoc prompt → 3-4 rounds to correct. Structured Prompt → 1 shot, consistent. Prompt Library = version-controlled templates in repo, reviewed like code."*

### Ad-hoc vs Structured

```
Ad-hoc (3+ rounds):
  Round 1: "Tạo API cho allocation"         → Generic CRUD
  Round 2: "Dùng %, tổng ≤ 100%"            → Sửa nhưng thiếu events
  Round 3: "Publish ResourceAllocated event" → OK nhưng sai terminology
  Round 4: "Dùng 'Allocation' không 'Assignment'" → Finally correct
  → 20 phút, inconsistent between developers

Structured (1 shot):
  [Fill template with Domain Context]        → Correct output
  → 5 phút, consistent across team
```

### Structured Prompt Template

```markdown
# Prompt: Generate [ARTIFACT_TYPE]

## Task
[Clear description of what to generate]
in [BOUNDED_CONTEXT].

## Domain Context
- Bounded Context: [NAME] ([Core/Supporting/Generic])
- Aggregate Root: [ENTITY]
- Key Entities: [LIST]
- Key Value Objects: [LIST]

## Business Rules
1. [RULE_1] (source: [glossary/ADR])
2. [RULE_2]

## Events
- On success: [EVENT_NAME] { fields }
- On failure: [EVENT_NAME] { fields }

## Constraints
- Follow ADR: [ADR_REFERENCE]
- Terminology: [TERM_1 not WRONG_1, TERM_2 not WRONG_2]

## Output Format
1. [DELIVERABLE_1]
2. [DELIVERABLE_2]
3. [DELIVERABLE_3]
4. [DELIVERABLE_4]
```

### ITO — Generate Allocation API (filled template)

```markdown
# Prompt: Generate REST API Endpoint

## Task
Create REST API endpoint for Resource Allocation
in Resource Management Context.

## Domain Context
- Bounded Context: Resource Management (Core Domain)
- Aggregate Root: Resource
- Key Entities: Allocation (within Resource aggregate)
- Key VOs: AllocationPercentage, Skill, DateRange

## Business Rules
1. totalAllocation ≤ 100% per Resource (source: ADR-001)
2. AllocationPercentage: 10-100%, increments of 10%
3. DateRange must be within Project timeline
4. Resource must have ≥80% skill match for Project

## Events
- Success: ResourceAllocated { resourceId, projectId, percentage, dateRange }
- Failure: AllocationRejected { resourceId, reason, requestedPercentage }

## Constraints
- ADR-001: Use percentage (NOT Man-Day)
- Terminology: "Resource" not "Staff", "Allocation" not "Assignment"

## Output Format
1. OpenAPI spec for POST /resources/{resourceId}/allocations
2. AllocateResourceUseCase (Application Service)
3. Resource.allocate() method (Domain logic in Aggregate)
4. Unit tests for invariant validation
```

### Logistics — Review Trip Aggregate (filled template)

```markdown
# Prompt: Review Domain Model

## Task
Review the Trip aggregate design against DDD tactical patterns
in Transportation Context.

## Domain Context
- Bounded Context: Transportation (Core Domain)
- Aggregate Root: Trip
- Key Entities: Stop (within Trip)
- Key VOs: TimeWindow, Location, Distance

## Review Criteria
1. Is Aggregate Root correctly identified?
2. Are invariants properly enforced?
3. References by ID (not object)?
4. Is aggregate small enough? (M7.2 sizing rules)
5. Are Value Objects used where appropriate? (M7.1 criteria)

## Domain Knowledge
- Glossary: Trip, Stop, Route, Vehicle, Driver
- Business Rules: capacity constraint, time window compliance
- Events: TripStarted, StopCompleted, TripCompleted

## Output Format
- Issues found (with severity: Critical/Medium/Low)
- Suggested improvements with code examples
- Trade-off analysis for each suggestion
```

### Chain-of-Thought with DDD

```markdown
# Prompt: Design Feature — DDD Thinking Chain

Before implementing, reason through these steps:

1. **Domain Discovery (M1):** What domain does this feature belong to?
2. **Language Check (M2):** Are we using Ubiquitous Language correctly?
3. **Strategic Check (M3):** Is this Core, Supporting, or Generic?
4. **Context Check (M5):** Which Bounded Context? Integration needed?
5. **Tactical Design (M7):** What Entity/VO/Aggregate patterns apply?
6. **Events (M4/M8):** What events should this produce/consume?
7. **Knowledge Update (M6/M9):** What glossary/ADR/event-storm to update?

THEN generate the implementation.

→ Forces AI to follow DDD design flow
→ Catches issues at design level, not code level
```

### Prompt Library — Tái sử dụng

```
prompts/
├── generate-api-spec.md          ← REST API generation template
├── review-domain-model.md        ← Aggregate/Entity review template
├── create-test-cases.md          ← Test generation from events
├── analyze-business-rules.md     ← Rule conflict detection
├── write-glossary-entry.md       ← Glossary creation template
├── design-aggregate.md           ← Aggregate design template
├── design-saga.md                ← Saga/Process Manager template
└── migration-check.md            ← Schema migration review template

→ Version-controlled: prompt thay đổi khi domain thay đổi
→ Reviewed like code: PR review cho prompt changes
→ Team-shared: consistent output across all developers
```

---

## 🏋️ Exercise — Tạo Prompt Library

### Phần A: Viết 1 Structured Prompt (15 phút)

Chọn 1 task:
- Option 1: "Generate Event Handler cho ResourceBenched"
- Option 2: "Create Integration Test cho Staffing Saga"
- Option 3: Tự chọn

```markdown
# Prompt: _______________

## Task

## Domain Context

## Business Rules

## Events

## Output Format
```

### Phần B: Logistics Prompt (10 phút)

Viết structured prompt cho: "Generate Route Optimization Service"

### Phần C: Compare effectiveness (5 phút)

Nếu có AI assistant, test:
- **(A)** "Tạo API cho allocation" (ad-hoc)
- **(B)** Structured prompt từ Phần A

Ghi nhận: rounds cần thiết, accuracy, terminology correctness.

---

## 🪞 Reflect

1. **Prompt Library version-controlled?** → **CÓ!** Prompts change khi domain change. ADR mới → update constraints trong prompt. Glossary update → update terminology section. **Prompts = living documents, review like code.**

2. **Structured Prompts có risk?** → **Over-constraining** — quá nhiều rules → AI "sợ sai" → output quá conservative/safe. **Balance:** include critical rules, leave room for AI creativity trong implementation details.

3. **RAG (auto) vs Structured Prompt (manual template)?** → **RAG** cho exploratory tasks ("explore options for X"). **Structured Prompt** cho repetitive tasks ("generate API for context Y"). **Best:** combine — RAG auto-fills Domain Context section → developer adds Task + Output Format.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Structured Prompt structure (5 sections) + Prompt Library concept
- [ ] **Apply:** Viết 1 Structured Prompt cho ITO + 1 cho Logistics
- [ ] **Analyze:** So sánh ad-hoc vs structured prompt → estimate productivity gain

---

> 🔗 **Tiếp theo:** Module 10 hoàn tất Context Engineering — Context Window, RAG, Structured Prompts. Module 11 — *Knowledge-Driven Development* — sẽ đưa everything together: AI Agent Instructions từ DDD, Agent Orchestration patterns, và AI-Native Workflow = development flow mới nơi DDD knowledge là nền tảng.
