---
type: lesson
module: 11
lesson: 2
title: "Agent Orchestration"
duration: "35 phút"
prerequisites: ["module-11/lesson-1"]
narrative_phase: "knowledge-driven development"
migration_phase: "Phase 5: Multi-Agent = Context Map cho AI"
business_invariant: "Multi-Agent = 1 Agent per Bounded Context + Orchestrator; Context Map patterns = Agent communication patterns; Orchestrator = 'dumb router' (delegates), Domain Agents = 'smart experts' (execute); Conflict resolution = Core Domain agent priority"
---

# Lesson 11.2: Agent Orchestration — "Multi-Agent = Multi-Context"

## 📍 Context — Bạn đang ở đây

> Lesson 11.1 — AGENT.md cho 1 agent = 1 Bounded Context. Nhưng user request thường cần **nhiều contexts**: "Staff 3 resources cho Project Alpha" = Resource Agent + Delivery Agent + Notification Agent. Ai điều phối? Ai quyết khi agents disagree? **Agent Orchestration** = Context Map (M5) cho AI agents.

## 🔥 Tension — "1 Agent biết hết → biết lờ mờ hết"

Sprint 19. Team thử 1 AI agent cho toàn bộ CRM:

> **Dev A:** *"Tôi cho 1 AI Agent đọc tất cả 9 domain glossaries, 8 ADRs, 5 event storms. Context window = 40K tokens."*
>
> **AI Agent:** *Nhầm Resource "Allocation" (% allocation) với Finance "Allocation" (cost allocation). Dùng delivery terminology cho Sales context. Generate code with mixed concepts.*
>
> **Dev A:** *"Agent 'biết' về mọi thứ nhưng KHÔNG CHUYÊN SÂU gì. Jack of all trades, master of none."*

**Minh:**
> *"Giống tổ chức: 1 người làm hết = bottleneck + shallow knowledge. Team chuyên biệt = deep expertise + clear handoff. Multi-Agent = 1 domain agent per context, Orchestrator điều phối. Chính xác như Context Map — nhưng cho AI."*

> 💭 **Câu hỏi:** DDD Context Map = team boundaries + integration patterns. Multi-Agent = agent boundaries + communication patterns. Mapping 1:1: Shared Kernel = shared knowledge base. Customer-Supplier = request-response. ACL = translation layer. **Same patterns, different consumers.**

## 🎓 Explanation — Agent Orchestration

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Multi-Agent = 1 Agent per Bounded Context + 1 Orchestrator (router). DDD Context Map patterns = Agent communication patterns. Orchestrator = 'dumb router' (understands intent, delegates execution). Domain Agents = 'smart experts' (deep domain knowledge, execute tasks). Conflict = Core Domain agent has priority."*

### Context Map → Agent Architecture

```
Context Map (DDD, M5):               Multi-Agent Architecture:
┌──────────────┐                      ┌──────────────┐
│ Resource Mgmt│                      │ Resource Agent│
│ (Core)       │                      │ (AGENT.md)    │
└──────┬───────┘                      └──────┬───────┘
       │ Customer-Supplier                    │ Request-Response
┌──────▼───────┐                      ┌──────▼───────┐
│ Delivery Mgmt│                      │ Delivery Agent│
│ (Core)       │                      │ (AGENT.md)    │
└──────┬───────┘                      └──────┬───────┘
       │ ACL                                  │ Translation Layer
┌──────▼───────┐                      ┌──────▼───────┐
│ Finance      │                      │ Finance Agent │
│ (Supporting) │                      │ (AGENT.md)    │
└──────────────┘                      └──────────────┘

→ Same boundaries, same integration patterns
→ DDD Context Map = AI Agent Orchestration Map
```

### Agent Communication Patterns

| DDD Pattern (M5) | Agent Pattern | Ví dụ ITO |
|---|---|---|
| **Shared Kernel** | Shared knowledge base | Resource + Delivery agents share `glossary/shared/` |
| **Customer-Supplier** | Request-Response | Resource Agent provides data to Delivery Agent |
| **ACL** | Translation layer | Finance Agent translates "allocation %" → "billable hours" |
| **Published Language** | Shared event schema | All agents understand `ResourceAllocated` event format |
| **Conformist** | Follow upstream model | HR Agent follows SuccessFactors terminology |

### Orchestrator Pattern — ITO

```
User: "Staff 3 resources cho Project Alpha"

Orchestrator Agent (dumb router):
  1. Parse intent: Staffing request → needs Resource + Delivery + Notification
  2. Delegate to Resource Agent:
     "Find 3 matching resources for Project Alpha requirements"
     → Resource Agent: searches matching algorithm → returns [R1, R2, R3]

  3. Delegate to Resource Agent:
     "Allocate R1(40%), R2(60%), R3(50%) for Project Alpha"
     → Resource Agent: validates invariants → allocates → publishes events

  4. Delegate to Delivery Agent:
     "Create staffing plan for Project Alpha with R1, R2, R3"
     → Delivery Agent: creates plan → returns staffing timeline

  5. Delegate to Notification Agent:
     "Notify PM Loan about staffing complete for Project Alpha"
     → Notification Agent: sends email + Slack notification

Key: Orchestrator doesn't know business rules
     → Domain Agents know rules
     → Separation of concerns = DDD principle applied to AI
```

### ITO Multi-Agent Architecture

```
┌──────────────────────────────────────────────────┐
│                 Orchestrator                      │
│  "Understands user intent, delegates to experts"  │
│  NOT responsible for business rules               │
└──────┬──────┬──────┬──────┬──────────────────────┘
       │      │      │      │
  ┌────▼──┐ ┌─▼────┐ ┌▼────┐ ┌▼──────┐
  │Resource│ │Sales │ │Deliv│ │Finance│
  │ Agent  │ │Agent │ │Agent│ │ Agent │
  │(Core)  │ │(Core)│ │(Core)│ │(Supp)│
  │        │ │      │ │     │ │       │
  │Scope:  │ │Scope:│ │Scope│ │Scope: │
  │Resource│ │Opp,  │ │Proj,│ │Billing│
  │Alloc,  │ │Lead, │ │Track│ │Invoice│
  │Skill,  │ │Acct, │ │Plan │ │Cost   │
  │Bench   │ │Win   │ │     │ │       │
  └────────┘ └──────┘ └─────┘ └───────┘
```

### Logistics Multi-Agent Architecture

```
Orchestrator
  ├── Route Agent (optimization, planning, ETA)
  ├── Fleet Agent (vehicle assignment, driver scheduling)
  ├── Tracking Agent (real-time location, status updates)
  └── Customer Agent (notifications, POD, complaints)

Flow: "Customer changes delivery address after dispatch"
  Orchestrator → Customer Agent (validate request)
    → Route Agent (recalculate route)
    → Tracking Agent (update ETA)
    → Customer Agent (notify new ETA)
```

### Handoff Protocol

```
Khi Agent A cần data từ Agent B:

Option 1: Query via API (synchronous)
  Resource Agent → GET /projects/{id}/requirements → Delivery Agent responds
  → Use when: need immediate response

Option 2: Event-based (asynchronous)
  Delivery Agent publishes ProjectCreated event
  → Resource Agent listens → prepares matching candidates
  → Use when: eventual consistency OK

Option 3: Orchestrator mediates
  User → Orchestrator → asks both agents → combines responses
  → Use when: need coordination between agents
```

### Conflict Resolution

```
Scenario: Resource Agent says "R1 available, allocate OK"
          Finance Agent says "R1 too expensive, budget insufficient"

Orchestrator Resolution Rules:
  1. Core Domain agent has priority on DOMAIN questions
     → Resource Agent decides availability (domain expertise)
  2. Supporting Domain agent has priority on CONSTRAINT questions
     → Finance Agent decides budget (financial constraint)
  3. When conflict: ESCALATE to human
     → Present both perspectives to PM/VP
     → Human decides trade-off

→ Same as real org: domain expert advises, business leader decides
```

---

## 🏋️ Exercise — Thiết kế Multi-Agent

### Phần A: ITO — Agent Map (15 phút)

| Agent | Scope (BC) | Knowledge Sources | Talks to |
|---|---|---|---|
| Resource Agent | | | |
| Sales Agent | | | |
| | | | |
| Orchestrator | User intent routing | All AGENT.md files | All agents |

### Phần B: Logistics — Communication Flow (10 phút)

"Customer thay đổi delivery address sau khi dispatch":
```
User → Orchestrator → _____ Agent → _____ Agent → _____ Agent
                          ↓              ↓              ↓
                    [action]       [action]       [action]
```

### Phần C: Conflict Resolution (5 phút)

Resource Agent: "R1 available, allocate OK"
Finance Agent: "R1 quá đắt, budget insufficient"

Orchestrator xử lý thế nào? Viết resolution logic.

---

## 🪞 Reflect

1. **Multi-Agent overhead — khi nào không cần?** → Team < 5 devs, < 3 contexts → **1 agent với good AGENT.md đủ**. Multi-Agent justifiable khi > 5 contexts, > 10 devs, hoặc contexts have conflicting terminology.

2. **Orchestrator "smart" vs "dumb"?** → **Dumb orchestrator + smart domain agents** = better separation. Smart orchestrator = God Object anti-pattern. Orchestrator chỉ parse intent + route — KHÔNG biết business rules.

3. **2 agents disagree — ai win?** → Core Domain agent priority cho domain questions. Supporting agent priority cho constraint questions. Unresolvable → escalate to human. **Same principle as real organization.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Map Context Map patterns → Agent communication patterns
- [ ] **Apply:** Thiết kế Agent Map cho ITO (≥4 agents) + Logistics communication flow
- [ ] **Analyze:** Resolve conflict scenario — justify decision with DDD priority

---

> 🔗 **Tiếp theo:** AGENT.md (identity) + Orchestration (multi-agent) = infrastructure. Bài cuối — *AI-Native Workflow* — brings everything together: Knowledge Objects → Context Engineering → AI generates Code + Tests → Human reviews → Knowledge updates. **Full circle: 11 modules → 1 integrated workflow.**
