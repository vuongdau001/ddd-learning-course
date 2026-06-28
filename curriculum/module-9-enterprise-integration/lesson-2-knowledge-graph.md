---
type: lesson
module: 9
lesson: 2
title: "Knowledge Graph & Traceability"
duration: "35 phút"
prerequisites: ["module-9/lesson-1"]
---

# Lesson 9.2: Knowledge Graph & Traceability

## 🎓 Concept — "Tri thức không phải file rời, mà là mạng lưới"

### Vấn đề: File-based knowledge = isolated islands

```
glossary/resource.md          ← Ai đọc file này?
event-storm/resource-mgmt.md  ← Liên quan gì tới glossary?
decisions/adr-001.md           ← ADR này ảnh hưởng event nào?
context-map.md                 ← Context nào dùng resource?

→ Files tồn tại nhưng KHÔNG LIÊN KẾT
→ Developer tìm 1 file, không biết có files liên quan
→ AI Agent không biết traverse → output kém
```

### Knowledge Graph = Mạng lưới quan hệ

```
                    ┌─────────────────┐
                    │  Business Goal  │
                    │ "Tăng win rate" │
                    └────────┬────────┘
                             │ drives
                    ┌────────▼────────┐
                    │    Capability   │
                    │"Resource Match" │
                    └────────┬────────┘
                             │ requires
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
     ┌────────────┐  ┌────────────┐  ┌────────────┐
     │  Glossary   │  │   Event    │  │    ADR     │
     │ "Resource"  │  │"Allocated" │  │ "ADR-001"  │
     └────────────┘  └─────┬──────┘  └────────────┘
                           │ triggers
                    ┌──────▼──────┐
                    │    API      │
                    │POST /alloc  │
                    └──────┬──────┘
                           │ tested by
                    ┌──────▼──────┐
                    │  Test Case  │
                    │"alloc_test" │
                    └─────────────┘
```

### Traceability Chain = Business Goal → Test Case

```
Layer 1: Business Goal
  "Tăng win rate từ 30% → 50%"
    ↓ decomposes into

Layer 2: Domain Capability
  "AI-powered Resource Matching"
    ↓ defined by

Layer 3: Domain Knowledge
  Glossary: Resource, Skill, Allocation
  Business Rules: totalAllocation ≤ 100%, skill match ≥ 80%
    ↓ modeled in

Layer 4: Design Artifacts
  Event Storm: ResourceRequested → MatchesFound → ResourceAllocated
  ADR: ADR-001 (Allocation %, not Man-Day)
    ↓ implemented as

Layer 5: Code
  API: POST /resources/{id}/allocate
  Service: ResourceMatchingService
    ↓ verified by

Layer 6: Test
  Unit: ResourceMatchingServiceTest
  Integration: AllocationAPITest
  E2E: StaffingFlowTest
```

### Cross-linking trong Markdown

```markdown
<!-- glossary/resource.md -->
# Resource

## Related
- **Events:** [ResourceAllocated](../event-storms/resource-mgmt.md#resourceallocated)
- **ADRs:** [ADR-001: Allocation %](../decisions/adr-001.md)
- **API:** [POST /resources/{id}/allocate](../api/resource-api.md#allocate)
- **Tests:** [ResourceMatchingServiceTest](../../tests/resource-matching.test.ts)
```

### Ví dụ — ITO Traceability Matrix

| Business Goal | Capability | Glossary Terms | Events | ADR | API | Test |
|---|---|---|---|---|---|---|
| Tăng win rate | Resource Matching | Resource, Skill | MatchesFound | ADR-001 | POST /match | MatchingTest |
| Giảm bench rate | Allocation Tracking | Allocation, Bench | ResourceBenched | ADR-002 | GET /bench | BenchReportTest |
| Faster staffing | Auto-qualification | Opportunity | OpportunityQualified | — | POST /qualify | QualifyTest |

### Ví dụ — Logistics Traceability

| Business Goal | Capability | Events | Test |
|---|---|---|---|
| Giảm delivery time | Route Optimization | RoutePlanned | RouteOptTest |
| Tăng POD rate | Real-time Tracking | DeliveryConfirmed | PODFlowTest |

---

## 🏋️ Exercise — Xây dựng Knowledge Graph

### Phần A: ITO — Full Traceability Chain (15 phút)

Chọn 1 Business Goal và trace đến Test Case:

| Layer | Artifact | Cụ thể |
|---|---|---|
| Business Goal | | |
| Capability | | |
| Glossary Terms | | |
| Business Rules | | |
| Events | | |
| ADR | | |
| API | | |
| Test | | |

### Phần B: Logistics (10 phút)

Làm tương tự cho 1 Business Goal khác.

### Phần C: Cross-linking exercise (5 phút)

Chọn 1 glossary entry đã viết (Module 2) và thêm `## Related` section:

```markdown
## Related
- Events: _______________
- ADRs: _______________
- Bounded Context: _______________
- Business Goal: _______________
```

---

## 🪞 Reflect

1. **Knowledge Graph có cần tool đặc biệt (Neo4j, Obsidian)?** Gợi ý: bắt đầu bằng Markdown cross-links. Chỉ cần tool khi graph > 100 nodes.

2. **Ai maintain Knowledge Graph — Developer? BA? Architect?** Gợi ý: mọi người contribute, Architect review.

3. **Khi xóa/thay đổi 1 glossary term, làm sao biết ảnh hưởng gì?** Gợi ý: trace Related links → biết impact.

---

## ✅ Hoàn thành lesson khi
- [ ] Vẽ Traceability Chain từ Business Goal → Test Case
- [ ] Tạo Traceability Matrix cho ITO (≥3 rows)
- [ ] Thêm cross-links vào 1 glossary entry
- [ ] Trả lời ≥2/3 câu hỏi reflection
