---
type: lesson
module: 11
lesson: 3
title: "AI-Native Workflow"
duration: "35 phút"
prerequisites: ["module-11/lesson-2"]
narrative_phase: "knowledge-driven development"
migration_phase: "Phase 5: Full cycle — Knowledge → Code → Test → Knowledge Update"
business_invariant: "AI-Native Workflow = Knowledge Objects → Context Engineering → AI generates → Human reviews → Knowledge updates → feedback loop; Developer role shift: code writer → knowledge curator + AI reviewer; Virtuous cycle: better knowledge → better AI output → better knowledge"
---

# Lesson 11.3: AI-Native Workflow — "Từ Knowledge đến Code — Full Circle"

## 📍 Context — Bạn đang ở đây

> Module 11.1 AGENT.md (identity) + Module 11.2 Orchestration (multi-agent coordination). Infrastructure xong. Bài cuối này kết nối **toàn bộ 11 modules** thành 1 workflow hoàn chỉnh: Knowledge → Context → AI → Code → Review → Knowledge Update. **Đây là tại sao bạn học DDD.**

## 🔥 Tension — "AI generate code nhưng không match business"

Sprint 20 — cuối dự án migration. Team reflect:

> **Dev A:** *"AI generate Allocation API nhanh lắm — 5 phút thay vì 2 giờ. Nhưng lần đầu, nó dùng Man-Day thay vì %. Lần hai, thiếu ResourceAllocated event. Lần ba mới đúng."*

> **PM Loan:** *"3 iterations = 15 phút. Manual code = 2 giờ. Vẫn tiết kiệm. Nhưng nếu 1 shot đúng luôn thì 5 phút = 24x faster."*

> **Dev B:** *"Vấn đề: mỗi developer setup context khác nhau. Tôi copy 3 glossary files, A copy 5 files khác. Không consistent."*

**Tuấn:**
> *"Chúng ta đã xây tất cả building blocks: Glossary (M2), Events (M4), ADRs (M6), Knowledge Graph (M9), Context Window (M10.1), RAG (M10.2), Prompts (M10.3), AGENT.md (M11.1), Orchestration (M11.2). Bây giờ — connect them thành **1 automated workflow**: Knowledge → AI → Code + Tests → Review → Knowledge Update. Developer role: từ 'viết code' → 'curate knowledge + review AI output'. Higher value work."*

> 💭 **Câu hỏi:** 11 modules = building blocks rời rạc nếu không connect. AI-Native Workflow = glue nối tất cả. Kết quả: developer productivity tăng 10-20x cho routine tasks, trong khi business accuracy tăng nhờ structured knowledge.

## 🎓 Explanation — AI-Native Workflow

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"AI-Native Workflow = Knowledge Objects (M1-M9) → Context Engineering (M10) → AI Agent generates (M11) → Human reviews → Knowledge updates → feedback loop. Developer shift: code writer → knowledge curator + AI reviewer. Virtuous cycle: better knowledge → better AI → discovers gaps → updates knowledge → even better AI."*

### Bức tranh toàn cảnh: 11 Modules → 1 Workflow

```
┌─────────────────────────────────────────────────────────────┐
│ KNOWLEDGE FOUNDATION (Module 1-6)                           │
│   M1: Domain Discovery → Domain Map                        │
│   M2: Ubiquitous Language → Glossary                       │
│   M3: Strategic Design → Priority Matrix                   │
│   M4: Event Storming → Event Flows                         │
│   M5: Context Mapping → Bounded Contexts                   │
│   M6: Knowledge Architecture → OKF Repository              │
├─────────────────────────────────────────────────────────────┤
│ IMPLEMENTATION PATTERNS (Module 7-9)                        │
│   M7: Tactical DDD → Entity, VO, Aggregate code            │
│   M8: Advanced → CQRS, Event Sourcing, Saga code           │
│   M9: Enterprise → Knowledge Graph, Traceability Chain     │
├─────────────────────────────────────────────────────────────┤
│ AI ENABLEMENT (Module 10-11)                                │
│   M10: Context Engineering → Context Window, RAG, Prompts  │
│   M11: Knowledge-Driven Dev → AGENT.md, Orchestration,     │
│         THIS WORKFLOW ← BẠN ĐANG Ở ĐÂY                    │
└─────────────────────────────────────────────────────────────┘
```

### 5-Step AI-Native Workflow

```
┌─────────────────────────────────────────────────┐
│ Step 1: KNOWLEDGE CHECK                         │
│   "What do I know about this feature?"          │
│                                                 │
│   Check: glossary/*.md, event-storms/*.md,      │
│          decisions/*.md, context-map.md          │
│   Missing? → Create Knowledge Objects FIRST     │
│              ↓                                   │
│ Step 2: CONTEXT ASSEMBLY                        │
│   Context Window (M10.1) selects layers          │
│   RAG (M10.2) auto-retrieves relevant KOs       │
│   Structured Prompt (M10.3) provides template   │
│   AGENT.md (M11.1) provides persistent identity │
│              ↓                                   │
│ Step 3: AI GENERATES                            │
│   ├── Domain Model (Entity, VO, Aggregate)      │
│   ├── API Specification (OpenAPI)                │
│   ├── Service Implementation                    │
│   ├── Unit Tests (from Business Rules)          │
│   ├── Integration Tests (from Events)           │
│   └── E2E Tests (from Event Storm flow)         │
│              ↓                                   │
│ Step 4: HUMAN REVIEW                            │
│   Developer/Architect reviews:                   │
│   ├── Business rule accuracy                    │
│   ├── Ubiquitous Language compliance            │
│   ├── ADR conformance                           │
│   ├── Event completeness                        │
│   └── Cross-context boundary respect            │
│              ↓                                   │
│ Step 5: KNOWLEDGE UPDATE (Feedback Loop)        │
│   Implementation reveals new insights →          │
│   ├── Update glossary (new terms discovered)    │
│   ├── Create ADR (new decisions made)           │
│   ├── Update event storm (new events found)     │
│   └── Update AGENT.md (new rules added)         │
│              ↓                                   │
│   → BACK TO Step 1 for next feature             │
│   → Virtuous cycle: each iteration IMPROVES     │
│     knowledge base AND AI output quality        │
└─────────────────────────────────────────────────┘
```

### ITO — Full Workflow Example

```
Feature: "Soft Allocation" (tentative resource reservation)

Step 1: KNOWLEDGE CHECK
  ✓ glossary/resource.md — has Resource definition
  ✓ glossary/allocation.md — has allocation rules
  ✗ glossary/soft-allocation.md — DOES NOT EXIST!
  ✓ decisions/adr-001.md — % allocation
  ✗ decisions/adr-003.md — soft allocation rules → DOES NOT EXIST!
  ✓ event-storms/resource-mgmt.md — has allocation events

  → STOP! Create missing Knowledge Objects FIRST:
  → Create glossary/soft-allocation.md
  → Create decisions/adr-003.md

Step 2: CONTEXT ASSEMBLY
  AGENT.md: Resource Management (Core) context
  RAG retrieves: resource.md, allocation.md, soft-allocation.md (NEW),
                 adr-001.md, adr-003.md (NEW), resource-mgmt.md
  Structured Prompt: "Generate Soft Allocation feature..."

Step 3: AI GENERATES
  → Resource.softAllocate() method
  → SoftAllocation Value Object (percentage, expirationDate)
  → POST /resources/{id}/soft-allocations API
  → SoftAllocationCreated, SoftAllocationExpired events
  → Unit tests: max 30 days, auto-expire, convert-to-hard
  → Integration test: SoftAllocationExpired → notification

Step 4: HUMAN REVIEW
  → Developer: "What if soft allocation + hard allocation > 100%?"
  → Decision: soft allocations are SEPARATE from hard allocation cap
  → This is a NEW business rule not in any Knowledge Object!

Step 5: KNOWLEDGE UPDATE
  → UPDATE glossary/soft-allocation.md: add "does not count toward 100% cap"
  → UPDATE adr-003.md: add "soft allocation ≠ capacity reservation"
  → UPDATE event-storms/resource-mgmt.md: add new events
  → UPDATE AGENT.md: add soft allocation invariants

→ Next time AI generates soft allocation code → correct from 1st shot
→ VIRTUOUS CYCLE: code → knowledge → better code
```

### Logistics — Full Workflow Example

```
Feature: "Automatic Rescheduling when delivery fails"

Step 1: KNOWLEDGE CHECK
  glossary/delivery.md ✓, glossary/route.md ✓
  adr-l03-reschedule-policy.md ✗ → CREATE FIRST
  event-storms/transportation.md ✓ (DeliveryFailed event exists)

Step 2: CONTEXT ASSEMBLY
  AGENT.md: Route Optimization Context
  RAG: delivery.md, route.md, time-window.md, adr-l03.md, transportation.md

Step 3: AI GENERATES
  → ReschedulingService (Domain Service)
  → RescheduleDelivery command
  → DeliveryRescheduled event
  → Tests: next-day policy, max 2 retries, customer notification

Step 4: HUMAN REVIEW
  → "What about perishable goods? Max 1 retry, not 2."
  → New business rule discovered!

Step 5: KNOWLEDGE UPDATE
  → glossary/delivery.md: add perishable goods category
  → adr-l03.md: differentiate retry limits by goods type
```

### Developer Role Shift

```
Traditional Developer:                AI-Native Developer:
  Requirement → Design → CODE →       Requirement → CURATE KNOWLEDGE →
  Test → Deploy                        Review AI Output → UPDATE KNOWLEDGE

Time allocation:
  Traditional:                         AI-Native:
    40% Writing code                     10% Writing code (complex logic only)
    20% Testing                          10% Testing (review AI-generated tests)
    20% Debugging                        30% Knowledge curation
    10% Documentation                    30% AI output review + refinement
    10% Meetings                         20% Architecture + design decisions

→ Developer becomes "Knowledge Engineer" + "AI Reviewer"
→ Higher value work: decisions > keystrokes
```

### ⚖️ Trade-offs

| | Traditional Dev | AI-Native Workflow |
|---|---|---|
| **Setup cost** | Low (just code) | High (build knowledge base first) |
| **Per-feature cost** | High (manual) | Low (automated) |
| **Break-even** | - | ~5th feature |
| **Knowledge debt** | Accumulates silently | Explicit + managed |
| **Onboarding** | Read code + ask people | Read Knowledge Objects |
| **Bus factor** | High risk | Low risk (knowledge codified) |
| **AI accuracy** | Unpredictable | Improves over time (virtuous cycle) |

---

## 🏋️ Exercise — AI-Native Workflow

### Phần A: ITO — Simulate Full Workflow (20 phút)

Pick 1 feature: (gợi ý: "Resource skill gap analysis" hoặc tự chọn)

| Step | Action | Your Output |
|---|---|---|
| 1. Knowledge Check | List needed KOs + identify gaps | |
| 2. Context Assembly | Which files for context? | |
| 3. Expected AI Output | What should AI generate? | |
| 4. Human Review | What would you check? | |
| 5. Knowledge Update | What KOs need creating/updating? | |

### Phần B: Logistics — Simulate Full Workflow (10 phút)

Feature: "Automatic rescheduling khi delivery fail"
Complete the same 5-step workflow.

### Phần C: Feedback Loop Detection (5 phút)

In your Phần A workflow, if AI generates code that uses wrong terminology:
1. Root cause: _______________
2. Fix location: _______________
3. Prevention: _______________

---

## 🪞 Reflect

1. **"AI-Native" = developer không cần code?** → **KHÔNG.** Developer shifts from writing code → curating knowledge + reviewing AI output. Complex business logic, architectural decisions, edge cases — **human still leads**. AI excels at boilerplate, tests, documentation, routine implementations.

2. **Feedback loop risk?** → **Knowledge inflation** — quá nhiều updates → noise. **Curation principle:** only update KOs when you discover GENUINE new business insight. Not every code review comment = knowledge update.

3. **Where does AI save most time?** → Boilerplate code (CRUD, DTOs, mappers), test generation from events, documentation from code, routine implementations following established patterns. **Where human leads:** complex domain logic, strategic decisions, cross-context coordination, stakeholder negotiation.

---

## 🏁 Hoàn thành Module 11 — Kết thúc DDD Learning Course

```
Stage 1: DDD Foundation (M1-M6)
  → Domain Discovery → Language → Strategy → Events → Contexts → Knowledge Architecture

Stage 2: Knowledge Engineering (M7-M9)
  → Tactical DDD → Advanced Patterns (CQRS, ES, Saga) → Enterprise Integration

Stage 3: AI-Native Development (M10-M11)
  → Context Engineering (Window, RAG, Prompts) → Knowledge-Driven Development
```

### Bạn đã có gì?

| Kỹ năng | Module | Deliverable |
|---|---|---|
| Tìm domain từ business | M1 | Domain Map |
| Xây ngôn ngữ chung | M2 | Glossary |
| Phân loại ưu tiên (Core/Supporting/Generic) | M3 | Priority Matrix |
| Mô hình hóa quy trình bằng events | M4 | Event Storm |
| Vẽ ranh giới & tích hợp | M5 | Context Map |
| Tổ chức tri thức theo OKF | M6 | Knowledge Repository |
| Code domain model (Entity, VO, Aggregate) | M7 | Tactical Model |
| Xử lý phức tạp (CQRS, ES, Saga) | M8 | Advanced Patterns |
| Tích hợp enterprise (EA, Knowledge Graph) | M9 | Traceability Chain |
| Tối ưu AI context (Window, RAG, Prompts) | M10 | Context Engineering |
| AI-Native workflow (AGENT.md, Orchestration) | M11 | Full AI Workflow |

### Tiếp theo?

→ **Áp dụng vào dự án thực tế.** Bắt đầu từ Module 1 (Domain Discovery) cho dự án mới.
→ **Iterate.** Knowledge base grows → AI output improves → developer productivity compounds.
→ **Share.** Dạy team, build culture of Knowledge-Driven Development.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 5-Step AI-Native Workflow + Virtuous Cycle
- [ ] **Apply:** Simulate full workflow cho 1 ITO feature + 1 Logistics feature
- [ ] **Analyze:** Identify feedback loop points + root cause analysis khi AI sai
- [ ] 🎓 **Hoàn thành toàn bộ DDD Learning Course!**

---

> 🔗 **Full Circle:** Module 1 hỏi "Domain là gì?" Module 11 trả lời: "Domain knowledge = nền tảng cho AI-Native Development." Mọi thứ bạn học — glossary, events, ADRs, context maps, aggregates, sagas, knowledge graphs — tất cả là **building blocks cho 1 AI-Native Workflow** nơi knowledge drives code, không phải ngược lại.
