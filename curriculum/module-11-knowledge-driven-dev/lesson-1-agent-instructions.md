---
type: lesson
module: 11
lesson: 1
title: "Agent Instructions"
duration: "35 phút"
prerequisites: ["module-10"]
---

# Lesson 11.1: Agent Instructions — AGENT.md

## 🎓 Concept — "Mỗi AI Agent cần biết 'mình là ai'"

### Vấn đề: AI Agent không có identity

```
Developer dùng AI assistant:
  - "Fix bug trong Payment service" → AI sửa, nhưng dùng sai terminology
  - "Add feature to Resource API" → AI tạo, nhưng vi phạm ADR-001
  - "Refactor Customer module" → AI refactor, nhưng break integration

→ AI không biết mình đang làm việc trong context nào
→ Không biết rules, constraints, conventions
→ Mỗi request phải nhắc lại từ đầu
```

### AGENT.md = "Bản mô tả công việc" cho AI

```markdown
# AGENT.md — Resource Management Context

## Identity
You are an AI developer working on the Resource Management bounded context
of the ITO CRM system. This is a CORE domain.

## Scope
### You ARE responsible for:
- Resource entity and its aggregate (Allocation, Skill VOs)
- ResourceMatchingService
- Resource-related events (ResourceAllocated, ResourceBenched)

### You are NOT responsible for:
- Project management (→ Delivery Context)
- Financial calculations (→ Finance Context)
- User authentication (→ Identity Context)

## Knowledge Sources
Before any task, read:
1. `docs/glossary/resource.md` — Core terminology
2. `docs/glossary/allocation.md` — Allocation rules
3. `docs/decisions/adr-001.md` — Allocation uses %
4. `docs/event-storms/resource-mgmt.md` — Event flow

## Rules
1. ALWAYS use Ubiquitous Language:
   - "Resource" (not Staff, Employee, Consultant)
   - "Allocation" (not Assignment, Booking)
   - "Bench" (not Idle, Available, Unassigned)
   
2. ENFORCE business invariants:
   - totalAllocation ≤ 100%
   - benchThreshold = 5 days
   
3. PUBLISH events for state changes:
   - ResourceAllocated, AllocationRejected, ResourceBenched

## Conventions
- Events: PascalCase past tense (ResourceAllocated)
- Commands: PascalCase imperative (AllocateResource)
- Files: kebab-case (resource-matching.service.ts)
- Tests: co-located (*.test.ts next to source)
```

### 1 Agent per Bounded Context

```
ITO CRM Project:
├── contexts/
│   ├── resource-management/
│   │   └── AGENT.md          ← Agent cho Resource context
│   ├── opportunity-management/
│   │   └── AGENT.md          ← Agent cho Sales context
│   ├── delivery-management/
│   │   └── AGENT.md          ← Agent cho Delivery context
│   └── finance/
│       └── AGENT.md          ← Agent cho Finance context
```

**Tại sao 1 Agent per Context?**
- Scope rõ ràng → AI không "lấn sân"
- Knowledge sources focused → context nhỏ, accurate hơn
- Rules specific → mỗi context có invariants riêng
- Mapping 1:1 với team ownership

### Ví dụ — Logistics AGENT.md

```markdown
# AGENT.md — Route Optimization Context

## Identity
Core domain AI agent for Smart Logistics System.

## Scope
- Route planning and optimization
- Vehicle capacity validation
- ETA calculation

## NOT in scope
- Driver management (→ Fleet Context)
- Customer communication (→ CRM Context)

## Knowledge Sources
1. `glossary/route.md`, `glossary/vehicle.md`
2. `decisions/adr-002.md` — Hybrid optimization
3. `event-storms/transportation.md`

## Rules
- Routes must respect time windows
- Vehicle capacity is a HARD constraint
- AI optimization for soft constraints only (ADR-002)
```

---

## 🏋️ Exercise — Viết AGENT.md

### Phần A: ITO — Opportunity Context (15 phút)

Viết AGENT.md cho Opportunity Management context:

```markdown
# AGENT.md — Opportunity Management Context

## Identity

## Scope
### Responsible for:

### NOT responsible for:

## Knowledge Sources

## Rules

## Conventions
```

### Phần B: Logistics — Fleet Context (10 phút)

Viết AGENT.md cho Fleet Management (Vehicle + Driver).

### Phần C: Scope test (5 phút)

Developer request: "Add payment tracking to Opportunity."
Theo AGENT.md của bạn, AI agent nên:
- (A) Implement it → tại sao?
- (B) Refuse + redirect → redirect đến context nào?

Giải thích quyết định.

---

## 🪞 Reflect

1. **AGENT.md quá restrictive thì sao? AI từ chối quá nhiều requests.** Gợi ý: cân bằng — scope rõ nhưng có "gray area" escalation rule.

2. **AGENT.md có replace team documentation không?** Gợi ý: Không — AGENT.md = subset cho AI. Team docs vẫn cần cho người.

3. **Khi 2 contexts overlap (Resource cần biết Project timeline), AGENT.md xử lý thế nào?** Gợi ý: Reference by ID, query via integration API, không access internal.

---

## ✅ Hoàn thành lesson khi
- [ ] Viết AGENT.md cho Opportunity context
- [ ] Viết AGENT.md cho Fleet context
- [ ] Xử lý scope test scenario
- [ ] Trả lời ≥2/3 câu hỏi reflection
