---
type: lesson
module: 11
lesson: 3
title: "AI-Native Workflow"
duration: "35 phút"
prerequisites: ["module-11/lesson-2"]
---

# Lesson 11.3: AI-Native Workflow — Từ Knowledge đến Code

## 🎓 Concept — "Knowledge Objects → Code + Tests tự động"

### Bức tranh toàn cảnh: 11 Modules → 1 Workflow

```
Module 1-2: Domain Discovery + Language     → Knowledge Foundation
Module 3-4: Strategic Design + Events       → Knowledge Structure
Module 5-6: Context + Architecture          → Knowledge Organization
Module 7-8: Tactical DDD + Advanced         → Implementation Patterns
Module 9:   Enterprise Integration          → Knowledge Graph
Module 10:  Context Engineering             → AI Context Design
Module 11:  Knowledge-Driven Development    → AI-Native Workflow ← BẠN ĐANG Ở ĐÂY
```

### AI-Native Workflow = Knowledge → Code → Test → Deploy

```
┌─────────────────────────────────────────────────┐
│ 1. Knowledge Objects (đã có từ Module 1-9):     │
│    Glossary + Events + ADRs + Context Map       │
│              ↓                                   │
│ 2. Context Engineering (Module 10):              │
│    RAG retrieves → Structured Prompt builds      │
│              ↓                                   │
│ 3. AI Agent generates:                           │
│    ├── Domain Model (Entity, VO, Aggregate)      │
│    ├── API Specification (OpenAPI)                │
│    ├── Service Implementation                    │
│    ├── Unit Tests (from Business Rules)          │
│    ├── Integration Tests (from Events)           │
│    └── E2E Tests (from Event Storm flow)         │
│              ↓                                   │
│ 4. Human Review:                                 │
│    Developer/Architect reviews + adjusts         │
│              ↓                                   │
│ 5. Knowledge Update:                             │
│    Update glossary/ADR if implementation reveals │
│    new insights → feedback loop                  │
└─────────────────────────────────────────────────┘
```

### Code Generation từ Knowledge Objects

**Input: Glossary + Business Rules**
```markdown
# Glossary: Allocation
- Definition: Phân bổ resource cho project theo %
- Invariant: totalAllocation ≤ 100%
- Related: Resource (aggregate root), Project (reference)
```

**AI generates:**
```typescript
// Domain Model
class Resource {
  private allocations: Allocation[] = [];
  
  allocate(projectId: ProjectId, percentage: Percentage): void {
    const total = this.totalAllocation() + percentage.value;
    if (total > 100) {
      throw new OverAllocationError(this.id, total);
    }
    this.allocations.push(new Allocation(projectId, percentage));
    this.addEvent(new ResourceAllocated(this.id, projectId, percentage));
  }
  
  private totalAllocation(): number {
    return this.allocations.reduce((sum, a) => sum + a.percentage.value, 0);
  }
}
```

### Test Generation từ Events

**Input: Event Storm**
```
ResourceRequested → MatchesFound → ResourceAllocated / AllocationRejected
```

**AI generates:**
```typescript
describe("Resource Allocation Flow", () => {
  it("should allocate resource when capacity available", () => {
    const resource = ResourceFactory.createWithAllocation(40);
    resource.allocate(projectId, new Percentage(60));
    expect(resource.totalAllocation()).toBe(100);
    expect(resource.events).toContainEvent("ResourceAllocated");
  });

  it("should reject when over-allocated", () => {
    const resource = ResourceFactory.createWithAllocation(80);
    expect(() => resource.allocate(projectId, new Percentage(30)))
      .toThrow(OverAllocationError);
    expect(resource.events).toContainEvent("AllocationRejected");
  });

  it("should detect bench status", () => {
    const resource = ResourceFactory.createWithAllocation(0);
    resource.checkBenchStatus(Duration.days(6));
    expect(resource.isBenched).toBe(true);
    expect(resource.events).toContainEvent("ResourceBenched");
  });
});
```

### Feedback Loop — Knowledge ← Code

```
AI generates code
  → Developer reviews
  → Discovers: "Oh, 'soft allocation' (tentative) không có trong glossary!"
  → Updates: glossary/allocation.md thêm "soft allocation" definition
  → Creates: ADR-003 cho soft allocation rules
  → AI next time: generates code with soft allocation support

→ Code development IMPROVES knowledge base
→ Knowledge base IMPROVES code generation
→ Virtuous cycle
```

### Ví dụ — Full Workflow cho ITO Feature

```
Feature: "AI-powered Resource Matching"

Step 1: Check Knowledge
  → glossary/resource.md ✓
  → glossary/skill.md ✓
  → event-storms/resource-mgmt.md → has MatchesFound event ✓
  → decisions/adr-002.md → AI-Augmented approach ✓

Step 2: AI generates
  → ResourceMatchingService (Domain Service)
  → POST /matching/search API
  → MatchingAlgorithm (scoring: skills 40%, availability 30%, location 30%)
  → Tests: 5 unit tests + 2 integration tests

Step 3: Human review
  → Developer: "Scoring weights cần configurable, không hardcode"
  → → Update: ADR-004 cho configurable matching weights
  → → AI regenerates with config

Step 4: Knowledge update
  → glossary/resource-matching.md (new term)
  → adr-004.md (new ADR)
  → event-storms/resource-mgmt.md updated
```

### Ví dụ — Logistics Feature

```
Feature: "Real-time ETA Calculation"

Knowledge → AI generates ETACalculator → Tests from Events
→ Human reviews → Updates glossary/eta.md → Next iteration better
```

---

## 🏋️ Exercise — AI-Native Workflow

### Phần A: ITO — Feature Implementation (20 phút)

Chọn 1 feature và simulate full workflow:

**Feature:** _______________

| Step | Action | Output |
|---|---|---|
| 1. Knowledge Check | Liệt kê Knowledge Objects cần | |
| 2. Prompt Design | Viết Structured Prompt | |
| 3. Expected AI Output | Mô tả code + tests AI sẽ generate | |
| 4. Human Review | Điểm nào cần review? | |
| 5. Knowledge Update | Knowledge Objects nào cần update? | |

### Phần B: Logistics — Feature Implementation (10 phút)

Làm tương tự cho: "Automatic rescheduling khi delivery fail"

### Phần C: Feedback loop (5 phút)

Trong workflow Phần A, nếu AI generate code sai business rule:
1. Root cause là gì? (Missing Knowledge Object? Outdated? Ambiguous?)
2. Fix ở đâu? (Update glossary? Add ADR? Improve prompt?)

---

## 🪞 Reflect

1. **"AI-Native" có nghĩa là developer không cần code nữa?** Gợi ý: Không — developer chuyển từ "viết code" sang "review + curate knowledge." Higher value work.

2. **Feedback loop (Code → Knowledge update) có risk gì?** Gợi ý: knowledge inflation — quá nhiều updates → noise. Cần curation.

3. **So sánh AI-Native Workflow với Traditional Development. Ở đâu tiết kiệm thời gian nhất?** Gợi ý: boilerplate code, test generation, documentation — AI excellent. Complex business logic — human still leads.

---

## 🏁 Hoàn thành Module 11 — Kết thúc Stage 3

Bạn đã hoàn thành **11 modules** — toàn bộ DDD Learning Course:

```
Stage 1: DDD Foundation (M1-M6)
  → Domain Discovery → Language → Strategy → Events → Contexts → Knowledge Architecture

Stage 2: Knowledge Engineering (M7-M9)  
  → Tactical DDD → Advanced Patterns → Enterprise Integration

Stage 3: AI-Native Development (M10-M11)
  → Context Engineering → Knowledge-Driven Development
```

### Bạn đã có gì?

| Kỹ năng | Module | Deliverable |
|---|---|---|
| Tìm domain từ business | M1 | Domain Map |
| Xây ngôn ngữ chung | M2 | Glossary |
| Phân loại ưu tiên | M3 | Priority Matrix |
| Mô hình hóa quy trình | M4 | Event Storm |
| Vẽ ranh giới & tích hợp | M5 | Context Map |
| Tổ chức tri thức | M6 | Knowledge Repository |
| Code từ domain model | M7 | Tactical Model |
| Xử lý phức tạp | M8 | CQRS + Event Sourcing |
| Tích hợp enterprise | M9 | EA Bridge + Knowledge Graph |
| Tối ưu AI context | M10 | Context Window + RAG |
| AI-Native workflow | M11 | Agent Config + Workflow |

### Tiếp theo?

→ Áp dụng vào dự án thực tế. Bắt đầu từ Module 1 (Domain Discovery) cho dự án mới, và iterate.

---

## ✅ Hoàn thành lesson khi
- [ ] Simulate full AI-Native Workflow cho 1 feature
- [ ] Xác định feedback loop points
- [ ] Xử lý error scenario (AI output sai)
- [ ] Trả lời ≥2/3 câu hỏi reflection
- [ ] 🎓 Hoàn thành toàn bộ DDD Learning Course!
