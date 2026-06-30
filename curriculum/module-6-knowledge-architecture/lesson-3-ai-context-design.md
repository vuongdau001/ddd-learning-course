---
type: lesson
module: 6
lesson: 3
title: "AI Context Design"
duration: "30 phút"
prerequisites: ["module-6/lesson-2"]
narrative_phase: "kiến trúc tri thức"
migration_phase: "Phase 1: Tối ưu knowledge cho AI consumption"
business_invariant: "YAML frontmatter bắt buộc cho mọi Knowledge Object; 3 cấp AI-readability (Readable → Actionable → Autonomous); AI Agent config = scope + permissions"
---

# Lesson 6.3: AI Context Design — "Viết tài liệu cho AI Agent đọc"

## 📍 Context — Bạn đang ở đây

> Lesson 6.1 cho bạn Repository Structure (nơi chứa). Lesson 6.2 cho bạn ADR + Traceability (nối knowledge lại). Bây giờ: **ai đọc knowledge này?** Người đọc → OK, Markdown readable. Nhưng AI Agent cũng cần đọc — để generate code aligned với domain model. Và AI đọc khác người: nó cần **structured metadata** (frontmatter), **concise format** (atomic files), và **explicit rules** (invariants dạng testable assertions).

## 🔥 Tension — "AI generate code sai vì không hiểu domain"

Sprint 10. ITO team bắt đầu dùng AI coding assistant:

> **Dev A:** *"AI, generate API for Resource allocation."*
>
> **AI:** *"Here's a generic CRUD API: POST /resources, GET /resources/:id, PUT /resources/:id, DELETE /resources/:id."*
>
> **Dev A:** *"Sai! Allocation không phải CRUD. Nó có rules: total ≤ 100%, không allocate resource đang bench > 5 ngày mà chưa approved, và cần cross-check với Finance billing cycle. AI không biết rules này."*

**Minh:**
> *"AI không có lỗi — nó không BIẾT rules của chúng ta. Glossary viết rồi nhưng bằng plain text — AI không parse được 'total ≤ 100%' như một invariant cần validate. Chúng ta cần viết documentation cho CẢ người lẫn AI đọc được."*

> 💭 **Câu hỏi:** DDD tạo structured knowledge. Nhưng AI cần format đặc biệt: YAML frontmatter (metadata), explicit invariants (testable rules), cross-references (navigable links). Làm sao optimize Knowledge Objects để AI generate code **chính xác theo domain model** thay vì generic CRUD?

## 🎓 Explanation — 3 Cấp độ AI-Readability

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"YAML frontmatter bắt buộc cho mọi Knowledge Object — giúp AI parse metadata mà không cần đọc full text. 3 cấp AI-readability: Readable (trả lời câu hỏi) → Actionable (generate code) → Autonomous (tự quyết định trong scope). AI Agent config = scope + permissions (AI KHÔNG được sửa Glossary mà không qua review)."*

### Tại sao DDD + AI = combo mạnh nhất?

```
TRƯỚC DDD:
  Developer: "AI, generate API for our CRM"
  AI: "OK, đây là generic CRUD API..."  ← generic, vô domain

SAU DDD (với AI Context Design):
  Developer: "AI, generate API for Resource Context.
              See: docs/domain/glossary/resource.md"
  AI: *đọc frontmatter → parse invariants → check events*
      "OK, đây là API with domain-specific validation,
       allocation ≤ 100%, ResourceAllocated event emitted,
       integration via Partnership with Sales Context."  ← precise
```

### Level 1: AI-Readable — "AI trả lời câu hỏi"

**Yêu cầu:**
- Glossary entries với definition + attributes rõ ràng
- Domain Map liệt kê domains + capabilities
- Cross-links giữa files

**AI có thể:**
```
User: "Utilization nghĩa là gì trong ITO?"
AI: *đọc glossary/utilization.md*
    "Utilization = Billable Hours / Available Hours.
     Target ≥ 75%. Thuộc Resource Management context.
     Liên quan: [Allocation], [Bench], [Resource]."
```

### Level 2: AI-Actionable — "AI generate code theo domain rules"

**Yêu cầu thêm:**
- YAML frontmatter với metadata có cấu trúc
- Business Rules viết dạng testable assertions
- Event Storm outputs với Commands + Events

**YAML Frontmatter — Biến plain text thành structured data:**

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
  - "resource.status != 'on_leave'"
events:
  - ResourceAllocated
  - AllocationEnded
  - AllocationRejected
  - OverAllocationDetected
commands:
  - AllocateResource
  - EndAllocation
  - TransferAllocation
---

# Allocation

## Definition
Allocation = % thời gian làm việc mà Resource được phân bổ cho 1 Project.

## Business Rules
1. **Total ≤ 100%:** Tổng allocation cho mọi project ≤ 100%
2. **Positive only:** Allocation % > 0 (không có "negative allocation")
3. **Valid range:** start_date < end_date
4. **Status check:** Không allocate resource đang on_leave

## Cross-references
- [Resource](./resource.md) — Allocation thuộc Resource
- [Utilization](./utilization.md) — Allocation ảnh hưởng Utilization
- [ADR-001](../../decisions/001-allocation-use-percentage.md) — Tại sao dùng %
```

**AI có thể:**
```
User: "Generate validation for Allocation entity"
AI: *parse YAML frontmatter → extract invariants*

    function validateAllocation(allocation, existingAllocations) {
      if (allocation.percentage <= 0) throw "Must be positive";
      if (allocation.startDate >= allocation.endDate) throw "Invalid date range";
      if (allocation.resource.status === 'on_leave') throw "Resource on leave";

      const totalAllocation = existingAllocations
        .filter(a => overlaps(a, allocation))
        .reduce((sum, a) => sum + a.percentage, 0);

      if (totalAllocation + allocation.percentage > 100)
        throw "Total allocation exceeds 100%";

      return new AllocationCreated(allocation);  // emit domain event
    }
```

### Level 3: AI-Autonomous — "AI tự quyết định trong scope"

**Yêu cầu thêm:**
- ADRs cho mọi quyết định quan trọng
- Context Map với integration patterns
- Policies từ Event Storm
- AI Agent config file

**AI Agent Config:**

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
  can_read: ["glossary/*", "events/*", "decisions/*", "context-map.md"]
  can_suggest: ["code changes", "new ADRs", "test cases"]
  cannot: ["modify glossary without PR review", "change business rules", "cross context boundary"]

behaviors:
  on_new_feature_request:
    1: "Check if term exists in glossary → if not, ask developer to define"
    2: "Identify which Bounded Context → stay within scope"
    3: "Reference relevant Events and Commands"
    4: "Check ADRs for constraints and prior decisions"
    5: "Generate code following Context patterns + invariants"
    6: "Generate tests from invariants in frontmatter"
```

**AI có thể (Level 3):**
```
User: "Add feature: auto-match resource to project based on skills"
AI:
  1. Check glossary → "Resource" ✅, "Allocation" ✅, "Skill" ✅
  2. Context → Resource Management (Core) ✅ — within scope
  3. Events → ResourceRequested → MatchingResourcesFound ✅
  4. ADR-002 → AI-Augmented matching (Build + AI, not Buy) ✅
  5. Integration → Resource ↔ Sales = Partnership → sync needed ✅
  6. Generate:
     - MatchingService with semantic skill matching
     - API endpoint POST /resources/match
     - Event handler for ResourceRequested → emit MatchingResourcesFound
     - Tests: "match returns resources with ≥80% skill overlap"
     - Test: "total allocation of matched resource ≤ 100%"
```

### ⚖️ Trade-offs — 3 Levels

| | Level 1 (Readable) | Level 2 (Actionable) | Level 3 (Autonomous) |
|---|---|---|---|
| **Effort** | Low (Markdown + links) | Medium (YAML + invariants) | High (ADRs + config + policies) |
| **AI value** | Q&A about domain | Generate domain-specific code | Full feature development |
| **Risk** | AI answers generic | AI misses edge cases | AI acts outside scope |
| **When** | MVP, small team | Production code generation | Mature DDD, trusted AI |
| **ITO Phase** | Sprint 1-3 | Sprint 4-8 | Sprint 9+ |

---

## 🏋️ Exercise — Thiết kế AI Context

### Phần A: Thêm YAML frontmatter cho Glossary (15 phút)

Chọn 2 glossary entries, thêm YAML frontmatter:

**Entry 1: Resource**
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
commands:
  - ___
---
```

**Entry 2: Shipment (Logistics)**
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
commands:
  - ___
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

behaviors:
  on_new_feature_request:
    1: "___"
    2: "___"
    3: "___"
```

### Phần C: Test AI-Readability (5 phút)

Đưa Glossary entry bạn vừa viết (với YAML frontmatter) cho AI và hỏi: "Generate validation code for this entity." So sánh kết quả với/không có frontmatter:

| | Không frontmatter | Có frontmatter |
|---|---|---|
| Invariants detected | ? | ? |
| Events referenced | ? | ? |
| Code quality | ? | ? |

---

## 🪞 Reflect

1. **Level 2 (Actionable) đã đủ chưa?** → Cho hầu hết teams, Level 2 = đủ. Level 3 cần maturity cao: DDD artifacts đầy đủ + team discipline maintain. **Start Level 2, evolve to Level 3** khi ADRs và Event Storms ổn định.

2. **AI Agent KHÔNG được sửa Glossary mà không review — tại sao?** → Glossary = Ubiquitous Language = source of truth (M2). Nếu AI tự sửa → inconsistency giữa code, docs, và team understanding. **AI suggest, human approve** = safety gate.

3. **DDD artifacts nào giá trị nhất cho AI?** → Ranking: **Glossary (invariants) > Event Storm (behavior) > ADR (constraints) > Context Map (scope) > Domain Map (overview).** Invariants = AI có thể trực tiếp convert thành validation code. Event Storm = AI biết flow. ADR = AI biết giới hạn.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 3 cấp AI-readability + tại sao YAML frontmatter quan trọng
- [ ] **Apply:** Thêm YAML frontmatter cho ≥2 glossary entries + viết AI Agent Config
- [ ] **Analyze:** So sánh AI output với/không có frontmatter — liên hệ với "Dev A nhận generic CRUD thay vì domain-specific API"

---

## 🏁 Hoàn thành Module 6 — Knowledge Architecture

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 6.1 Repository Structure | Docs-as-Code, 3 nguyên tắc (Atomic/Versioned/Cross-linked) | ITO + Logistics repo structure |
| 6.2 ADR + Traceability | Architecture Decision Records + Traceability Chain | ADRs + Knowledge Graph traversal |
| 6.3 AI Context Design | 3 levels AI-readability + YAML frontmatter + Agent Config | AI-optimized Knowledge Objects |

**Deliverable:** `practice/knowledge-repo/` — Repository structure + ≥2 ADRs + YAML-enhanced glossary entries + AI Agent Config

### 🔮 Module 7 Preview: Tactical DDD

Bạn hoàn thành **Strategic Design (Modules 1-6)** — quyết định "WHAT" và "WHERE":
- **What:** Domain Map, Glossary, Event Storm → biết business cần gì
- **Where:** Bounded Contexts, Context Map → biết code tổ chức thế nào

Module 7 bắt đầu **Tactical Design** — quyết định "HOW":
- **How:** Entity, Value Object, Aggregate → biết code viết thế nào **bên trong** 1 Bounded Context

> **Câu hỏi dẫn dắt:** Khi Tuấn implement Resource Context, `Resource` là Entity (có identity, mutable) hay Value Object (chỉ value, immutable)? `Allocation` là Entity riêng hay thuộc Aggregate nào? Aggregate boundary ở đâu để đảm bảo `total_allocation ≤ 100%`? → Module 7 trả lời.
