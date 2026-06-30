---
type: lesson
module: 10
lesson: 1
title: "Context Window Design"
duration: "35 phút"
prerequisites: ["module-9"]
narrative_phase: "context engineering"
migration_phase: "Phase 5: AI-ready documentation — Context Window cho Agentic AI"
business_invariant: "Context Window = AI's working memory; 3 Layers: Static (always) + Domain (per BC) + Task (per request); Context budget: focused 10K tokens > noisy 50K tokens; DDD Knowledge Objects = perfect AI context source"
---

# Lesson 10.1: Context Window Design — "AI chỉ biết những gì bạn cho nó đọc"

## 📍 Context — Bạn đang ở đây

> Module 9 hoàn tất Enterprise Integration — EA mapping, Knowledge Graph, Organizational Knowledge. Tất cả knowledge đã codified thành Knowledge Objects (Glossary, ADR, Event Storm, Policy, MR). Câu hỏi tiếp: **AI Agent đọc knowledge đó thế nào?** AI có context window = "não ngắn hạn" — bạn phải chọn lọc đúng knowledge cho đúng task. Sai context = AI generate sai code.

## 🔥 Tension — "AI generate CRUD vì thiếu domain context"

Sprint 15. Dev A dùng AI coding assistant:

> **Dev A:** *"AI, tạo API cho Resource Allocation."*
>
> **AI:** *"OK, đây là CRUD endpoint: POST /resources, GET /resources/:id, PUT /resources/:id, DELETE /resources/:id. Standard REST."*
>
> **Dev A:** *"Không! Allocation dùng %, tổng ≤ 100%. Không phải CRUD resource."*
>
> **AI (round 2):** *Sửa nhưng thiếu events, dùng sai term "Assignment" thay vì "Allocation".*
>
> **Dev A (round 3):** *"Cần publish ResourceAllocated event! Và dùng 'Allocation' không phải 'Assignment'!"*
>
> **Dev A:** *"3 rounds mới đúng. Thôi tự code cho nhanh 😤"*

**Tuấn:**
> *"AI không có tribal knowledge. Nó không biết ADR-001 (% thay vì Man-Day), không biết totalAllocation ≤ 100%, không biết ResourceAllocated event. Bạn phải **cho nó đọc** đúng Knowledge Objects. Đó là Context Window Design."*

> 💭 **Câu hỏi:** AI powerful nhưng "mù" nếu thiếu domain context. Bạn cho nó đọc quá ít → generic output. Quá nhiều → noise, confused. Context Window Design = chọn lọc đúng knowledge, đúng lượng.

## 🎓 Explanation — Context Window Design

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Context Window = AI's working memory (limited). 3 Layers: Static (always load — project overview, terminology), Domain (per Bounded Context — entities, rules, events), Task (per request — specific glossary, ADR). Context budget: focused 10K tokens > noisy 50K tokens. DDD Knowledge Objects từ M2-M9 = perfect context source."*

### 3 Layers of Context

```
Context Window (128K tokens, optimal ~10-20K):
┌────────────────────────────────────────────────────────┐
│ Layer 1 — STATIC (luôn có, ~2,000 tokens):             │
│   ├── Project overview (ITO CRM Transformation)        │
│   ├── Core terminology (Resource, Allocation, ...)     │
│   └── Architecture constraints (ADR-001, ADR-002)      │
│                                                        │
│ Layer 2 — DOMAIN (per BC, ~3,000 tokens):              │
│   ├── Key Entities + VOs (Resource, AllocationPct)     │
│   ├── Business Rules (totalAllocation ≤ 100%)          │
│   └── Domain Events (ResourceAllocated, Benched)       │
│                                                        │
│ Layer 3 — TASK (per request, ~5,000 tokens):           │
│   ├── Relevant glossary entries                        │
│   ├── Relevant ADR (adr-001: use %)                    │
│   └── Relevant event storm section                     │
│                                                        │
│ → Total: ~10,000 tokens → AI has room to reason        │
└────────────────────────────────────────────────────────┘
```

### Layer 1 — Static Context (System Prompt)

```markdown
# ITO CRM — Project Context

You are working on the ITO CRM Transformation project.
Core domains: Resource Management (Core), Opportunity Management (Core),
Finance (Supporting), HR (Generic).

## Terminology Rules
- Use "Resource" (not Staff, Employee, Consultant)
- Use "Allocation" (not Assignment, not Booking)
- Allocation measured in % (not Man-Day) — refer ADR-001
- Use "Opportunity" (not Deal, not Lead after qualification)

## Architecture Constraints
- ADR-001: Allocation uses % (totalAllocation ≤ 100%)
- ADR-002: Event Sourcing for Opportunity (audit requirement)
- Each Bounded Context has its own database schema
- Inter-context: Domain Events (async), not direct calls
```

### Layer 2 — Domain Context (per Bounded Context)

```markdown
# Resource Management Context

## Key Entities & VOs
- Resource (Aggregate Root): id, name, skills[], allocations[]
- Allocation (Entity within aggregate): resourceId, projectId, percentage, dateRange
- AllocationPercentage (VO): value 10-100%, increment 10%
- Skill (VO): name, level (Junior/Mid/Senior)

## Business Rules
- totalAllocation ≤ 100% per Resource (enforced by Aggregate)
- Bench = allocation == 0% for > 5 business days
- Skill match threshold ≥ 80% for staffing
- Soft allocation: tentative, max 30 days before confirm

## Domain Events
- ResourceAllocated { resourceId, projectId, percentage, dateRange }
- AllocationRejected { resourceId, reason, requestedPercentage }
- ResourceBenched { resourceId, benchStartDate, reason }
- ResourceFreed { resourceId, previousProjectId }
```

### Layer 3 — Task Context (per request)

```markdown
# Current Task: Create Allocation API

## Relevant Glossary
- Allocation: see glossary/allocation.md — percentage-based, within aggregate
- Utilization: see glossary/utilization.md — calculated from allocations

## Relevant ADR
- ADR-001: Allocation uses % (alternatives considered: Man-Day, Hours)

## Relevant Events
- ResourceAllocated: publish on success
- AllocationRejected: publish when totalAllocation would exceed 100%

## Relevant Aggregate
- Resource is the Aggregate Root
- Allocation is WITHIN Resource aggregate (not separate)
- Access via: resource.allocate(projectId, percentage, dateRange)
```

### Context Budget — Quality > Quantity

```
ITO knowledge base: ~50,000 tokens total
Context window: 128K tokens available (but optimal at ~10-20K)

→ Phải CHỌN LỌC:
  Layer 1 (always):      ~2,000 tokens  ← 1 file: system-prompt.md
  Layer 2 (per context): ~3,000 tokens  ← 1 file: resource-context.md
  Layer 3 (per task):    ~5,000 tokens  ← 3-4 relevant Knowledge Objects
  ─────────────────────────────────
  Total:                ~10,000 tokens  → 90%+ window free for reasoning

Why not dump everything?
  50,000 tokens all at once → AI "drowning in context"
  → Miss critical rules buried in noise
  → Hallucinate connections between unrelated domains
  → 10K focused > 50K noisy
```

### ITO vs Logistics — Context Design

| | ITO CRM | Logistics |
|---|---|---|
| Layer 1 | "ITO CRM, Resource Mgmt + Opp Mgmt" | "Smart Logistics, Route + Fleet Mgmt" |
| Layer 2 | Resource aggregate, allocation rules | Vehicle capacity, time window constraints |
| Layer 3 | Per-task: glossary + ADR + events | Per-task: route rules + driver constraints |

---

## 🏋️ Exercise — Thiết kế Context Window

> 📂 **Mở artifacts từ Module 1-9:**
> - `practice/glossary/` → Terminology cho Layer 1
> - `practice/domain-map.md` → Core Domains cho Layer 1
> - `practice/context-map.md` → Integration constraints
> - `practice/knowledge-repo/` → Source cho Layer 2 + 3

### Phần A: ITO — System Prompt (15 phút)

Viết Layer 1 (Static Context):
```markdown
# [Project Name] — Project Context

## Core Domains
- _______________

## Terminology Rules
- _______________

## Architecture Constraints
- _______________
```

### Phần B: Domain Context (10 phút)

Viết Layer 2 cho Resource Management:
```markdown
# [Context Name]

## Key Entities & VOs
## Business Rules
## Domain Events
```

### Phần C: Logistics System Prompt (5 phút)

Viết Layer 1 cho Logistics — tối đa 15 dòng.

---

## 🪞 Reflect

1. **Context Window lớn hơn có tốt hơn?** → **KHÔNG!** "Noise" trong context làm AI confused. Focused 10K tokens > noisy 50K. Giống developer: đọc 3 trang relevant > 50 trang unrelated. **Quality of context > quantity.**

2. **Ai maintain Context config?** → Developer + Architect cùng maintain. Domain thay đổi → update Layer 2. ADR mới → update Layer 1 constraints. **Rule:** PR thay đổi business rules → PHẢI update context config.

3. **Module 6 (Knowledge Architecture) → Module 10 liên quan thế nào?** → M6 tạo Knowledge Objects (structured, atomic). M10 **chọn lọc** objects phù hợp cho AI context. M6 = create, M10 = consume. **Good Knowledge Objects = good AI context.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 3 Layers of Context + Context Budget concept
- [ ] **Apply:** Viết System Prompt (Layer 1) + Domain Context (Layer 2) cho ITO
- [ ] **Analyze:** So sánh focused vs noisy context — giải thích tại sao quality > quantity

---

> 🔗 **Tiếp theo:** Layer 1-2 = manual selection. Nhưng Layer 3 (task context) thay đổi mỗi request — developer phải tự chọn relevant files mỗi lần. **RAG (Retrieval-Augmented Generation)** sẽ tự động tìm Knowledge Objects phù hợp. Bài tiếp — *RAG from DDD Artifacts* — sẽ trả lời: làm sao AI tự tìm đúng glossary, ADR, event storm cho mỗi task?
