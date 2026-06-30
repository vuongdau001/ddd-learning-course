---
type: lesson
module: 11
lesson: 1
title: "Agent Instructions — AGENT.md"
duration: "35 phút"
prerequisites: ["module-10"]
narrative_phase: "knowledge-driven development"
migration_phase: "Phase 5: AI Agent identity + scope boundaries per Bounded Context"
business_invariant: "AGENT.md = Identity + Scope + Knowledge Sources + Rules + Conventions cho AI; 1 Agent per Bounded Context = focused, accurate, no 'lấn sân'; NOT scope = explicit boundary → AI refuses + redirects"
---

# Lesson 11.1: Agent Instructions — "Mỗi AI Agent cần biết 'mình là ai'"

## 📍 Context — Bạn đang ở đây

> Module 10 Context Engineering — Context Window (chọn lọc knowledge), RAG (auto-retrieve), Structured Prompts (templates). Nhưng tất cả là per-request — mỗi task phải setup context lại. **AGENT.md** = persistent identity cho AI Agent — nó biết mình là ai, scope gì, rules gì, TRƯỚC KHI nhận bất kỳ task nào. Giống Job Description cho AI.

## 🔥 Tension — "AI sửa Payment bug nhưng break Resource rules"

Sprint 18. Team dùng AI assistant cho nhiều contexts:

> **Dev A:** *"AI, fix bug trong Payment service — invoice tính sai VAT."*
>
> **AI:** *Sửa Payment code. Trong quá trình, cần reference Allocation data → truy cập trực tiếp Resource database → sửa cả Resource model để lấy billing info.*
>
> **Dev B:** *"Chờ — tại sao Payment AI sửa Resource model? Nó thêm field 'billingRate' vào Resource entity, nhưng Resource context dùng 'costRate'. Bây giờ Resource aggregate có 2 rate fields mâu thuẫn."*

> **Tuấn:** *"AI không biết scope. Nó 'lấn sân' — Payment task mà sửa Resource model. Giống developer không có context map — code across boundaries gây bugs. AI cần **AGENT.md** = Job Description: 'Tôi là Resource Agent, tôi CHỈ quản lý Resource + Allocation. Payment thì KHÔNG PHẢI việc tôi — redirect sang Finance Agent.'"*

> 💭 **Câu hỏi:** AI powerful nhưng không hiểu organizational boundary. AGENT.md = Bounded Context cho AI — explicit scope, explicit NOT-scope, explicit rules. Giống Context Map (M5) nhưng cho AI agents thay vì teams.

## 🎓 Explanation — AGENT.md

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"AGENT.md = persistent AI identity file (Identity + Scope + NOT Scope + Knowledge Sources + Rules + Conventions). 1 Agent per Bounded Context = focused domain expert. NOT scope = explicit boundary → AI refuses task + redirects to correct agent. AGENT.md prevents cross-context contamination."*

### AGENT.md Template

```markdown
# AGENT.md — [Bounded Context Name]

## Identity
You are an AI developer working on the [CONTEXT_NAME] bounded context
of the [PROJECT_NAME] system. This is a [CORE/SUPPORTING/GENERIC] domain.

## Scope
### You ARE responsible for:
- [Entity/Aggregate 1]
- [Domain Service 1]
- [Domain Events]

### You are NOT responsible for:
- [Other context 1] (→ redirect to [AGENT_NAME])
- [Other context 2] (→ redirect to [AGENT_NAME])

## Knowledge Sources
Before ANY task, read:
1. [glossary file 1] — [what it contains]
2. [glossary file 2]
3. [ADR file]
4. [event storm file]

## Rules
1. ALWAYS use Ubiquitous Language:
   - [Correct term] (not [Wrong term 1, Wrong term 2])
2. ENFORCE business invariants:
   - [Invariant 1]
   - [Invariant 2]
3. PUBLISH events for state changes:
   - [Event 1, Event 2, Event 3]

## Conventions
- Events: PascalCase past tense (ResourceAllocated)
- Commands: PascalCase imperative (AllocateResource)
- Files: kebab-case (resource-matching.service.ts)
- Tests: co-located (*.test.ts next to source)
```

### ITO — Resource Agent (AGENT.md)

```markdown
# AGENT.md — Resource Management Context

## Identity
You are an AI developer working on the Resource Management bounded context
of the ITO CRM system. This is a CORE domain — our competitive advantage.

## Scope
### You ARE responsible for:
- Resource entity and its aggregate (Allocation, Skill VOs)
- AllocationPercentage, DateRange, Skill (Value Objects)
- ResourceMatchingService (Domain Service)
- Resource Repository interface
- Events: ResourceAllocated, AllocationRejected, ResourceBenched, ResourceFreed

### You are NOT responsible for:
- Project management, timelines (→ Delivery Context Agent)
- Financial calculations, billing (→ Finance Context Agent)
- Opportunity management, leads (→ Sales Context Agent)
- User authentication (→ Identity Context Agent)
- If asked about these: REFUSE and REDIRECT to the correct agent

## Knowledge Sources
Before ANY task, read these files:
1. `docs/glossary/resource.md` — Core entity definition
2. `docs/glossary/allocation.md` — Allocation rules + soft allocation
3. `docs/glossary/bench.md` — Bench detection rules
4. `docs/decisions/adr-001.md` — Allocation uses % (NOT Man-Day)
5. `docs/decisions/adr-003.md` — Soft allocation feature
6. `docs/event-storms/resource-mgmt.md` — Complete event flow
7. `docs/policies/pol-001-allocation-policy.md` — Business policy

## Rules
1. ALWAYS use Ubiquitous Language:
   - "Resource" (not Staff, Employee, Consultant, Worker)
   - "Allocation" (not Assignment, Booking, Staffing)
   - "Bench" (not Idle, Available, Free, Unassigned)

2. ENFORCE business invariants:
   - totalAllocation ≤ 100% per Resource
   - benchThreshold = 5 business days
   - AllocationPercentage: 10-100%, increments of 10%
   - Soft allocation: max 30 days before confirm/expire

3. PUBLISH events for ALL state changes:
   - ResourceAllocated, AllocationRejected, ResourceBenched, ResourceFreed

4. REFERENCE by ID for external aggregates:
   - Use projectId (not Project object) from Delivery Context
   - Use opportunityId (not Opportunity object) from Sales Context

## Conventions
- Events: PascalCase past tense (ResourceAllocated)
- Commands: PascalCase imperative (AllocateResource)
- Files: kebab-case (resource-matching.service.ts)
- Tests: co-located (*.test.ts next to source file)
- Aggregate access: through Repository only (never direct DB query)
```

### 1 Agent per Bounded Context

```
ITO CRM Project:
├── contexts/
│   ├── resource-management/
│   │   └── AGENT.md          ← Resource Agent (Core)
│   ├── opportunity-management/
│   │   └── AGENT.md          ← Sales Agent (Core)
│   ├── delivery-management/
│   │   └── AGENT.md          ← Delivery Agent (Core)
│   └── finance/
│       └── AGENT.md          ← Finance Agent (Supporting)

Tại sao 1 Agent per Context?
- Scope rõ ràng → AI không "lấn sân" (cross-context contamination)
- Knowledge sources focused → smaller context = more accurate
- Rules specific → mỗi context có invariants riêng
- Mapping 1:1 với team ownership → align with Conway's Law
```

### Logistics — Route Optimization Agent

```markdown
# AGENT.md — Route Optimization Context

## Identity
Core domain AI agent for Smart Logistics System.

## Scope
### Responsible for:
- Route planning and optimization
- Vehicle capacity validation
- ETA calculation
- Stop sequence optimization

### NOT responsible for:
- Driver management, scheduling (→ Fleet Context Agent)
- Customer communication, POD (→ Customer Context Agent)
- Warehouse operations (→ Warehouse Context Agent)

## Knowledge Sources
1. `glossary/route.md`, `glossary/vehicle.md`, `glossary/time-window.md`
2. `decisions/adr-l02-route-algo.md` — Hybrid optimization approach
3. `event-storms/transportation.md` — Event flow

## Rules
- Routes MUST respect time windows (hard constraint)
- Vehicle capacity is a HARD constraint (never exceed)
- AI optimization for soft constraints only (ADR-L02)
- Terminology: "Trip" not "Journey", "Stop" not "Waypoint"
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

Developer request: *"Add payment tracking to Opportunity."*

Theo AGENT.md, Opportunity Agent nên:
- **(A)** Implement it → tại sao?
- **(B)** Refuse + redirect → redirect đến context nào?

Giải thích dựa trên AGENT.md scope.

---

## 🪞 Reflect

1. **AGENT.md quá restrictive → AI từ chối quá nhiều?** → Thêm **"gray area" escalation rule**: "If task touches your scope AND another context, implement YOUR part and note the cross-context dependency for the other agent." Không quá strict cũng không quá loose.

2. **AGENT.md replace team documentation?** → **KHÔNG.** AGENT.md = subset cho AI. Team docs vẫn cần cho người (onboarding, meeting records, policies). AGENT.md references team docs nhưng không replace chúng.

3. **2 contexts overlap — Resource cần biết Project timeline?** → **Reference by ID** (M7.2 rule). Resource Agent queries Project API for timeline — KHÔNG access Project database trực tiếp. AGENT.md enforces this boundary = same as Context Map integration pattern (M5).

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 5 sections của AGENT.md + tại sao 1 Agent per Context
- [ ] **Apply:** Viết AGENT.md cho Opportunity + Fleet context
- [ ] **Analyze:** Xử lý scope test scenario — justify refuse + redirect decision

---

> 🔗 **Tiếp theo:** 1 Agent per Context OK. Nhưng khi user request cần **nhiều contexts** — "Staff 3 resources cho Project Alpha" = Resource + Delivery + Notification — cần nhiều agents phối hợp. **Agent Orchestration** sẽ trả lời: Multi-Agent = Context Map cho AI. Orchestrator routes, Domain Agents execute.
