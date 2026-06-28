---
type: lesson
module: 10
lesson: 3
title: "Structured Prompts"
duration: "30 phút"
prerequisites: ["module-10/lesson-2"]
---

# Lesson 10.3: Structured Prompts

## 🎓 Concept — "Prompt hay = Domain knowledge + Structure"

### Vấn đề: Ad-hoc prompting

```
Developer prompt: "Tạo API cho resource allocation"
AI output: Generic CRUD endpoint

Developer prompt (lần 2): "Không, allocation dùng %, tổng ≤ 100%"
AI output: Sửa nhưng thiếu events

Developer prompt (lần 3): "Cần publish ResourceAllocated event"
AI output: OK nhưng sai terminology...

→ 3 rounds để AI hiểu context
→ Mỗi lần phải nhắc lại business rules
```

### Structured Prompt = Template + Domain Context

```markdown
# Prompt: Generate API Endpoint

## Task
Create REST API endpoint for [OPERATION] in [BOUNDED_CONTEXT].

## Domain Context
- Bounded Context: [NAME]
- Aggregate Root: [ENTITY]
- Key Entities: [LIST]
- Business Rules:
  1. [RULE_1]
  2. [RULE_2]

## Events to Publish
- On success: [EVENT_NAME]
- On failure: [EVENT_NAME]

## Constraints
- Follow ADR: [ADR_REFERENCE]
- Use terminology from: [GLOSSARY_REFERENCES]

## Output Format
1. API specification (method, path, request/response)
2. Service layer code
3. Domain model validation
4. Unit test skeleton
```

### Prompt Library — Tái sử dụng

```
prompts/
├── generate-api-spec.md          ← Prompt template cho API generation
├── review-domain-model.md        ← Prompt cho model review
├── create-test-cases.md          ← Prompt cho test generation từ events
├── analyze-business-rules.md     ← Prompt cho rule conflict detection
├── write-glossary-entry.md       ← Prompt cho glossary creation
└── design-aggregate.md           ← Prompt cho aggregate design
```

### Ví dụ — Generate API Prompt (ITO)

```markdown
# Generate Allocation API

## Task
Create REST API for Resource Allocation in Resource Management Context.

## Domain Context
- Bounded Context: Resource Management (Core Domain)
- Aggregate Root: Resource
- Key Entities: Resource, Allocation (within aggregate)
- Key VOs: AllocationPercentage, Skill, DateRange

## Business Rules
1. totalAllocation ≤ 100% per Resource
2. Allocation percentage: 10-100% (increments of 10%)
3. DateRange must be within Project timeline
4. Resource must have ≥80% skill match for Project

## Events
- Success: ResourceAllocated { resourceId, projectId, percentage, dateRange }
- Failure: AllocationRejected { resourceId, reason }

## Constraints
- ADR-001: Use percentage (not Man-Day)
- Terminology: "Resource" not "Staff", "Allocation" not "Assignment"

## Output
1. OpenAPI spec for POST /resources/{resourceId}/allocations
2. AllocateResourceUseCase (Application Service)
3. Resource.allocate() method (Domain logic)
4. Unit tests for invariant validation
```

### Ví dụ — Review Domain Model Prompt (Logistics)

```markdown
# Review Trip Aggregate Design

## Task
Review the Trip aggregate design against DDD tactical patterns.

## Current Design
[paste Trip aggregate code]

## Review Criteria
1. Is the Aggregate Root correctly identified?
2. Are invariants properly enforced?
3. Are references by ID (not object)?
4. Is the aggregate small enough?
5. Are Value Objects used where appropriate?

## Domain Knowledge
- Glossary: Trip, Stop, Route, Vehicle, Driver
- Business Rules: capacity constraints, time windows
- Events: TripStarted, StopCompleted, TripCompleted

## Output
- List of issues found (with severity)
- Suggested improvements
- Code examples for fixes
```

### Chain-of-Thought with DDD

```markdown
# Prompt: Design Feature — Step by Step

Before implementing, think through:

1. **Domain Discovery:** What domain does this feature belong to?
2. **Language Check:** Are we using Ubiquitous Language correctly?
3. **Strategic Check:** Is this Core, Supporting, or Generic?
4. **Tactical Design:** What Entity/VO/Aggregate patterns apply?
5. **Events:** What events should this produce/consume?
6. **Integration:** How does this interact with other Bounded Contexts?
7. **Knowledge Update:** What glossary/ADR/event-storm needs updating?

Then generate the implementation.
```

---

## 🏋️ Exercise — Tạo Prompt Library

### Phần A: Viết 1 Structured Prompt (15 phút)

Chọn 1 task và viết structured prompt:
- Option 1: "Generate Event Handler cho ResourceBenched"
- Option 2: "Create Integration Test cho Opportunity → Contract flow"
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

Viết structured prompt cho: "Generate Route Optimization API"

### Phần C: Test prompt effectiveness (5 phút)

So sánh output AI khi dùng:
- (A) "Tạo API cho allocation" (ad-hoc)
- (B) Structured prompt từ Phần A

Ghi nhận sự khác biệt.

---

## 🪞 Reflect

1. **Prompt Library nên version-controlled?** Gợi ý: Có! Prompts thay đổi khi domain thay đổi. Cần review như code.

2. **Structured Prompts có risk gì?** Gợi ý: over-constraining AI — quá nhiều rules → AI "sợ" sai → output quá safe.

3. **Khi nào dùng RAG (auto-retrieve) vs Structured Prompt (manual template)?** Gợi ý: RAG cho exploratory tasks. Structured Prompt cho repetitive tasks.

---

## ✅ Hoàn thành lesson khi
- [ ] Viết 1 Structured Prompt cho ITO
- [ ] Viết 1 Structured Prompt cho Logistics
- [ ] So sánh ad-hoc vs structured prompt output
- [ ] Trả lời ≥2/3 câu hỏi reflection
