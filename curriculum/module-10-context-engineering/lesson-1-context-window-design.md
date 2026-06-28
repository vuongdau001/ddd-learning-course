---
type: lesson
module: 10
lesson: 1
title: "Context Window Design"
duration: "35 phút"
prerequisites: ["module-9"]
---

# Lesson 10.1: Context Window Design

## 🎓 Concept — "AI chỉ biết những gì bạn cho nó đọc"

### Vấn đề: AI không có tribal knowledge

```
Developer: "Tạo API cho Resource Allocation"
AI (không có context):
  → Tạo CRUD endpoint generic
  → Không biết "allocation = %, không phải Man-Day"
  → Không biết invariant "tổng ≤ 100%"
  → Không biết Resource Matching Service

Developer (sau 3 lần sửa): "Thôi tự code cho nhanh 😤"
```

### Context Window = "Não ngắn hạn" của AI

```
Context Window (128K tokens):
┌─────────────────────────────────────────────┐
│ System Prompt (luôn có):                     │
│   ├── Project overview                       │
│   ├── Core terminology (5-10 glossary terms) │
│   └── Coding conventions                     │
│                                              │
│ Dynamic Context (theo task):                 │
│   ├── Relevant glossary entries              │
│   ├── Relevant business rules               │
│   ├── Related events & commands              │
│   └── Relevant ADRs                          │
│                                              │
│ User Request:                                │
│   "Tạo API cho Resource Allocation"          │
│                                              │
│ AI Response:                                 │
│   → API với đúng business rules              │
│   → Dùng đúng terminology                   │
│   → Enforce invariants                       │
└─────────────────────────────────────────────┘
```

### 3 Layers of Context

**Layer 1: Static Context (System Prompt)**
```markdown
# ITO CRM — Project Context

You are working on the ITO CRM Transformation project.
Core domains: Resource Management, Opportunity Management.

## Terminology Rules
- Use "Resource" (not Staff, Employee, Consultant)
- Use "Allocation" (not Assignment, not Booking)
- Allocation is measured in % (not Man-Day)

## Architecture Constraints
- ADR-001: Allocation uses % (totalAllocation ≤ 100%)
- ADR-002: Resource Matching uses AI-Augmented approach
- Each Bounded Context has its own database schema
```

**Layer 2: Domain Context (per Bounded Context)**
```markdown
# Resource Management Context

## Key Entities
- Resource: id, name, skills[], allocations[]
- Allocation: resourceId, projectId, percentage, dateRange

## Business Rules
- totalAllocation ≤ 100%
- bench = allocation == 0% for > 5 days
- skill match threshold ≥ 80%

## Events
- ResourceAllocated, AllocationRejected, ResourceBenched
```

**Layer 3: Task Context (per request)**
```markdown
# Current Task: Create Allocation API

## Relevant Glossary
- Allocation: see glossary/allocation.md
- Utilization: see glossary/utilization.md

## Relevant Events
- ResourceAllocated: trigger khi allocation thành công
- AllocationRejected: trigger khi totalAllocation > 100%
```

### Ví dụ — Logistics Context Window

```
Layer 1 (Static):
  "You work on Smart Logistics System. Core: Route Optimization."

Layer 2 (Domain):
  "Vehicle has capacity constraint. Trip has time windows."

Layer 3 (Task):
  "Create Route Optimization API. See events: RoutePlanned, RouteOptimized."
```

### Context Budget — Không thể cho AI đọc hết

```
Toàn bộ ITO knowledge base: ~50,000 tokens
Context window limit: 128K tokens (nhưng tối ưu ở ~20-30K)

→ Phải CHỌN LỌC:
  Layer 1 (always): ~2,000 tokens
  Layer 2 (per context): ~3,000 tokens  
  Layer 3 (per task): ~5,000 tokens
  
  Total context: ~10,000 tokens → dư cho AI reasoning
```

---

## 🏋️ Exercise — Thiết kế Context Window

### Phần A: ITO — System Prompt (15 phút)

Viết System Prompt (Layer 1) cho ITO CRM project:

```markdown
# [Project Name] — Project Context

You are working on _______________

## Core Domains
- _______________

## Terminology Rules
- _______________
- _______________

## Architecture Constraints  
- _______________
```

### Phần B: Domain Context (10 phút)

Viết Domain Context (Layer 2) cho Resource Management:

```markdown
# [Context Name]

## Key Entities
## Business Rules
## Events
```

### Phần C: Logistics System Prompt (5 phút)

Viết Layer 1 cho Logistics — 10 dòng hoặc ít hơn.

---

## 🪞 Reflect

1. **Context Window lớn hơn có tốt hơn không?** Gợi ý: Không — "noise" trong context làm AI confused. Focused context > Large context.

2. **Ai maintain Context Window config?** Gợi ý: Developer + Architect cùng maintain. Khi domain thay đổi → update context.

3. **Context Window Design và Knowledge Architecture (Module 6) liên quan thế nào?** Gợi ý: Module 6 tạo Knowledge Objects. Module 10 chọn lọc objects phù hợp cho AI.

---

## ✅ Hoàn thành lesson khi
- [ ] Viết System Prompt (Layer 1) cho ITO
- [ ] Viết Domain Context (Layer 2) cho Resource Management
- [ ] Viết System Prompt cho Logistics
- [ ] Hiểu context budget concept
