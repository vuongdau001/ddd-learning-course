---
type: lesson
module: 6
lesson: 3
title: "AI Context Design"
duration: "30 phút"
prerequisites: ["module-6/lesson-2"]
---

# Lesson 6.3: AI Context Design

## 🎓 Concept — "Viết tài liệu cho AI Agent đọc"

### Tại sao DDD + AI = combo mạnh nhất?

```
TRƯỚC DDD:
  Developer: "AI, generate API for our CRM"
  AI: "OK, đây là generic CRUD API..."  ← generic, không có domain knowledge

SAU DDD:
  Developer: "AI, generate API for Resource Context. 
              Rules: allocation ≤ 100%, bench = no allocation > 5 days.
              See: docs/domain/glossary/resource.md"
  AI: "OK, đây là API with domain-specific validation..."  ← precise, useful
```

DDD tạo ra **structured knowledge** → AI Agent đọc được → generate code chính xác hơn.

### 3 cấp độ AI-readability

| Cấp | Tên | AI có thể | Yêu cầu |
|:---:|---|---|---|
| 1 | **Readable** | Trả lời câu hỏi về domain | Glossary + Domain Map |
| 2 | **Actionable** | Generate code theo domain rules | Event Storm + Business Rules |
| 3 | **Autonomous** | Tự quyết định trong scope | ADRs + Context Map + Policies |

### Thiết kế cho Level 1: AI-Readable

**Yêu cầu tối thiểu:**
- Glossary entries có definition + attributes rõ ràng
- Domain Map liệt kê domains + capabilities
- Cross-links giữa các files

**AI có thể làm:**
```
User: "Utilization nghĩa là gì trong hệ thống?"
AI: *đọc glossary/utilization.md*
    "Utilization = Billable Hours / Available Hours. 
     Target ≥ 75%. Thuộc Resource Management context."
```

### Thiết kế cho Level 2: AI-Actionable

**Yêu cầu thêm:**
- Business Rules rõ ràng, testable
- Event Storm với Commands + Events
- YAML frontmatter cho metadata

**Ví dụ: YAML frontmatter trong Glossary**

```yaml
---
type: definition
term: allocation
context: resource-management
domain-type: core
related: [resource, utilization, bench, project]
invariants:
  - "total_allocation_percentage <= 100"
  - "allocation_percentage > 0"
  - "start_date < end_date"
events:
  - ResourceAllocated
  - AllocationEnded
  - AllocationRejected
---
```

**AI có thể làm:**
```
User: "Generate validation for Allocation entity"
AI: *đọc allocation.md, parse invariants*
    
    function validateAllocation(allocation) {
      if (allocation.percentage <= 0) throw "Must be positive";
      if (allocation.percentage > 100) throw "Cannot exceed 100%";
      if (allocation.startDate >= allocation.endDate) throw "Invalid range";
      
      const totalAllocation = existingAllocations
        .filter(a => overlaps(a, allocation))
        .reduce((sum, a) => sum + a.percentage, 0);
      
      if (totalAllocation + allocation.percentage > 100)
        throw "Total allocation exceeds 100%";
    }
```

### Thiết kế cho Level 3: AI-Autonomous

**Yêu cầu thêm:**
- ADRs cho mọi quyết định quan trọng
- Context Map với integration patterns
- Policies từ Event Storm
- AI Agent config file

**Ví dụ: AI Agent Config**

```yaml
# .ai/agent-config.yaml
agent:
  name: "ITO CRM Knowledge Agent"
  scope: "Resource Management Context"
  
knowledge_sources:
  glossary: "docs/domain/glossary/"
  events: "docs/event-storms/"
  decisions: "docs/decisions/"
  context_map: "docs/domain/context-map.md"

permissions:
  can_read: ["glossary/*", "events/*", "decisions/*"]
  can_suggest: ["code changes", "new ADRs"]
  cannot: ["modify glossary without review", "change business rules"]

behaviors:
  on_new_feature_request:
    1: "Check if term exists in glossary"
    2: "Identify which Bounded Context"
    3: "Reference relevant Events"
    4: "Check ADRs for constraints"
    5: "Generate code following Context patterns"
```

**AI có thể làm:**
```
User: "Add feature: auto-match resource to project based on skills"
AI: 
  1. Check glossary → "Resource", "Allocation", "Project" defined ✅
  2. Context → Resource Management (Core) ✅
  3. Events → ResourceRequested → MatchingResourcesFound ✅
  4. ADR-002 → AI-Augmented approach ✅
  5. Generate:
     - MatchingService with semantic skill matching
     - API endpoint POST /resources/match
     - Event handler for ResourceRequested
     - Tests based on business rules in glossary
```

---

## 🏋️ Exercise — Thiết kế AI Context

### Phần A: Thêm YAML frontmatter cho Glossary (15 phút)

Chọn 2 glossary entries đã viết, thêm YAML frontmatter:

**Entry 1:** Resource
```yaml
---
type: definition
term: ___
context: ___
domain-type: ___
related: [___, ___, ___]
invariants:
  - "___"
  - "___"
events:
  - ___
  - ___
---
```

**Entry 2:** (chọn 1 từ Logistics — VD: Shipment)
```yaml
---
(viết đầy đủ)
---
```

### Phần B: Viết AI Agent Config (10 phút)

Thiết kế `.ai/agent-config.yaml` cho Logistics:

```yaml
agent:
  name: "___"
  scope: "___"
  
knowledge_sources:
  glossary: "___"
  events: "___"
  decisions: "___"

permissions:
  can_read: [___]
  can_suggest: [___]
  cannot: [___]
```

---

## 🪞 Reflect

1. **Level 1 (Readable) đã đủ cho bạn chưa?** Hay bạn cần Level 2 (Actionable) ngay? Phụ thuộc vào gì?

2. **AI Agent config restrict "cannot modify glossary without review" — tại sao?** Glossary = source of truth. Nếu AI tự sửa → mất consistency.

3. **DDD artifacts nào có giá trị cao nhất cho AI?** Rank: Glossary > Event Storm > ADR > Context Map > Domain Map. Bạn đồng ý không?

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích 3 cấp độ AI-readability
- [ ] Thêm YAML frontmatter cho 2 glossary entries
- [ ] Viết AI Agent Config cho Logistics
- [ ] Trả lời ≥2/3 câu hỏi reflection

---

## 🏁 Hoàn thành Module 6 — Kết thúc Stage 1

Bạn đã hoàn thành **6 modules DDD Foundation**:
1. Domain Discovery → Domain Map
2. Ubiquitous Language → Glossary
3. Strategic Design → Priority Matrix
4. Event Storming → Event Storm
5. Bounded Context → Context Map
6. Knowledge Architecture → Knowledge Repository

→ Tiếp: **Quiz Module 6** rồi chuyển sang **Stage 2: Knowledge Engineering**

### 🔮 Preview: Stage 3 — AI-Native Development

Lesson này chạm vào Level 1-3 của AI-readability — cách làm cho AI **đọc được** knowledge. Stage 3 sẽ đi xa hơn: làm cho AI **hành động được** dựa trên knowledge:

| Topic | Bạn sẽ học | Tại sao cần DDD trước? |
|---|---|---|
| **Context Engineering** | Cách tổ chức Knowledge Objects cho LLM context window | Không có Glossary + Domain Map → context lộn xộn |
| **RAG Pipeline** | Retrieval-Augmented Generation từ DDD artifacts | Không có atomic files → retrieval không chính xác |
| **Agent Orchestration** | Multiple AI agents, mỗi agent = 1 Bounded Context | Không có Context Map → agents xung đột scope |
| **Knowledge-driven Dev** | Code generation từ structured domain knowledge | Không có Business Rules + Events → generate CRUD generic |

> **Tại sao phải chờ?** Stage 1 (DDD Foundation) tạo ra **structured knowledge**. Stage 2 (Tactical Design) tạo ra **implementation patterns**. Stage 3 kết hợp cả hai để AI có thể **autonomous development** — viết code chính xác vì hiểu cả business lẫn architecture.

