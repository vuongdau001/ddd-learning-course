---
type: lesson
module: 11
lesson: 2
title: "Agent Orchestration"
duration: "35 phút"
prerequisites: ["module-11/lesson-1"]
---

# Lesson 11.2: Agent Orchestration — Multi-Agent = Multi-Context

## 🎓 Concept — "Nhiều AI Agents, mỗi agent 1 chuyên gia"

### Vấn đề: 1 Agent làm hết

```
1 AI Agent cho toàn bộ ITO CRM:
  - Context window chứa glossary 9 domains → quá nhiều
  - Rules từ Resource xung đột với rules từ Finance
  - Agent "biết" về mọi thứ nhưng KHÔNG CHUYÊN SÂU gì

→ Jack of all trades, master of none
→ Output generic, không đủ depth cho Core Domains
```

### Multi-Agent = Context Map cho AI

```
Context Map (DDD):                Multi-Agent Architecture:
┌──────────────┐                  ┌──────────────┐
│ Resource Mgmt│                  │ Resource Agent│
│ (Core)       │                  │ (deep context)│
└──────┬───────┘                  └──────┬───────┘
       │ Customer-Supplier                │ API call
┌──────▼───────┐                  ┌──────▼───────┐
│ Delivery Mgmt│                  │ Delivery Agent│
│ (Core)       │                  │ (deep context)│
└──────────────┘                  └──────────────┘

→ DDD Context Map = AI Agent orchestration map
→ Integration patterns = Agent communication patterns
```

### Agent Communication Patterns

| DDD Pattern | Agent Pattern | Ví dụ |
|---|---|---|
| **Shared Kernel** | Shared knowledge base | 2 agents cùng đọc `glossary/shared/` |
| **Customer-Supplier** | Request-Response | Resource Agent cung cấp API cho Delivery Agent |
| **ACL** | Translation layer | Finance Agent translate "allocation" → "billable hours" |
| **Published Language** | Shared event schema | Tất cả agents hiểu `ResourceAllocated` event format |

### Orchestrator Pattern

```
User: "Staff 3 resources cho Project Alpha"

Orchestrator Agent:
  1. Ask Resource Agent: "Find 3 matching resources"
     → Resource Agent: searches, returns [R1, R2, R3]
  
  2. Ask Resource Agent: "Allocate R1, R2, R3 cho Project Alpha"
     → Resource Agent: validates, allocates, returns success
  
  3. Ask Delivery Agent: "Create staffing plan for Project Alpha"
     → Delivery Agent: creates plan with R1, R2, R3
  
  4. Ask Notification Agent: "Notify PM about staffing complete"
     → Notification Agent: sends email
```

### Ví dụ — ITO Multi-Agent

```
┌──────────────────────────────────────────────┐
│                Orchestrator                   │
│  "Understands user intent, delegates tasks"   │
└──────┬──────┬──────┬──────┬──────────────────┘
       │      │      │      │
  ┌────▼──┐ ┌─▼────┐ ┌▼────┐ ┌▼──────┐
  │Resource│ │Sales │ │Deliv│ │Finance│
  │ Agent  │ │Agent │ │Agent│ │ Agent │
  │        │ │      │ │     │ │       │
  │Scope:  │ │Scope:│ │Scope│ │Scope: │
  │Resource│ │Opp,  │ │Proj,│ │Billing│
  │Alloc,  │ │Lead, │ │Track│ │Invoice│
  │Skill   │ │Acct  │ │     │ │       │
  └────────┘ └──────┘ └─────┘ └───────┘
```

### Ví dụ — Logistics Multi-Agent

```
Orchestrator
  ├── Route Agent (optimization, planning)
  ├── Fleet Agent (vehicle, driver assignment)
  ├── Tracking Agent (real-time location, ETA)
  └── Customer Agent (notifications, POD)
```

### Handoff Protocol

```
Khi Agent A cần data từ Agent B:

Option 1: Query via API (đồng bộ)
  Resource Agent → GET /projects/{id}/requirements → Delivery Agent
  
Option 2: Event-based (bất đồng bộ)  
  Delivery Agent publishes ProjectCreated event
  → Resource Agent listens → prepares matching

Option 3: Orchestrator mediates
  User → Orchestrator → asks both agents → combines response
```

---

## 🏋️ Exercise — Thiết kế Multi-Agent

### Phần A: ITO — Agent Map (15 phút)

Thiết kế multi-agent architecture cho ITO CRM:

| Agent | Scope (Bounded Context) | Knowledge Sources | Talks to |
|---|---|---|---|
| Resource Agent | | | |
| | | | |
| | | | |
| Orchestrator | | | All agents |

### Phần B: Logistics — Agent Communication (10 phút)

Vẽ flow cho: "Customer thay đổi delivery address sau khi dispatch"

```
User → Orchestrator → _____ Agent → _____ Agent → _____ Agent
                          ↓              ↓              ↓
                    [action]       [action]       [action]
```

### Phần C: Conflict resolution (5 phút)

Resource Agent nói: "R1 available, allocate OK"
Finance Agent nói: "R1 quá đắt, budget insufficient"

Orchestrator xử lý thế nào?

---

## 🪞 Reflect

1. **Multi-Agent có overhead — khi nào không cần?** Gợi ý: team < 5 devs, < 3 contexts → 1 agent với good AGENT.md đủ.

2. **Orchestrator nên "smart" (biết business rules) hay "dumb" (chỉ route)?** Gợi ý: dumb orchestrator + smart domain agents = better separation.

3. **Nếu 2 agents disagree (Resource says yes, Finance says no), ai quyết định?** Gợi ý: Business Priority → Core Domain agent có tiếng nói cuối.

---

## ✅ Hoàn thành lesson khi
- [ ] Thiết kế Agent Map cho ITO (≥3 agents)
- [ ] Vẽ communication flow cho Logistics scenario
- [ ] Xử lý conflict resolution scenario
- [ ] Trả lời ≥2/3 câu hỏi reflection
