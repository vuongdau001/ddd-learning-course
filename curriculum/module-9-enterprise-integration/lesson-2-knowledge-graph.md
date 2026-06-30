---
type: lesson
module: 9
lesson: 2
title: "Knowledge Graph & Traceability"
duration: "35 phút"
prerequisites: ["module-9/lesson-1"]
narrative_phase: "enterprise integration"
migration_phase: "Phase 4: Liên kết mọi artifacts thành knowledge graph"
business_invariant: "Knowledge Graph = mạng lưới quan hệ giữa artifacts; Traceability Chain = Business Goal → Test Case (6 layers); Cross-linking = explicit relationships; Isolated files = useless knowledge"
---

# Lesson 9.2: Knowledge Graph & Traceability — "Tri thức là mạng lưới, không phải file rời"

## 📍 Context — Bạn đang ở đây

> Lesson 9.1 mapping EA ↔ DDD. Bạn có Glossary (M2), Event Storm (M4), Bounded Context (M5), ADR (M6), Capability Map (M9.1). Nhưng chúng = **files rời rạc** — Glossary không link tới ADR, ADR không link tới Event, Event không link tới Test. Khi developer hỏi "ADR-001 ảnh hưởng API nào?" — phải search thủ công. **Knowledge Graph** = liên kết tất cả thành mạng lưới traversable.

## 🔥 Tension — "Files tồn tại nhưng không ai tìm thấy relationships"

Sprint 13. New developer join ITO:

> **New Dev:** *"Tôi đọc `glossary/allocation.md` — hiểu Allocation là gì. Nhưng: ADR nào quyết định dùng % thay vì Man-Day? Event nào fire khi allocate? API endpoint nào? Test nào verify? Tôi phải mở từng folder search."*

> **Minh:** *"Files tồn tại nhưng KHÔNG LIÊN KẾT = islands. Developer tìm 1 file, không biết 10 files liên quan. AI Agent nhận 1 file, không biết context. Knowledge phải là **Graph** — mỗi node (file) có edges (links) tới nodes liên quan."*

> 💭 **Câu hỏi:** Glossary → liên quan ADR nào? ADR → trigger event nào? Event → implemented bằng API nào? API → tested bởi test nào? Nếu trả lời được chain này = **Traceability**. Nếu mọi files linked = **Knowledge Graph**.

## 🎓 Explanation — Knowledge Graph & Traceability

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Knowledge Graph = mạng lưới quan hệ (nodes = artifacts, edges = links). Traceability Chain = Business Goal → Capability → Glossary → Event → ADR → API → Test (6 layers). Cross-linking mandatory: mọi artifact PHẢI có `## Related` section. Isolated file = useless knowledge."*

### Knowledge Graph Structure

```
                    ┌─────────────────┐
                    │  Business Goal  │
                    │ "Tăng win rate" │
                    └────────┬────────┘
                             │ drives
                    ┌────────▼────────┐
                    │   Capability    │
                    │"Resource Match" │
                    └────────┬────────┘
                             │ requires
              ┌──────────────┼──────────────┐
              ▼              ▼              ▼
     ┌────────────┐  ┌────────────┐  ┌────────────┐
     │  Glossary   │  │   Event    │  │    ADR     │
     │ "Resource"  │  │"Allocated" │  │ "ADR-001"  │
     └──────┬─────┘  └─────┬──────┘  └──────┬─────┘
            │               │                │
            └───────────────┼────────────────┘
                            │ implemented by
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

### Traceability Chain — 6 Layers

```
Layer 1: Business Goal
  "Tăng win rate từ 30% → 50%"
    ↓ decomposes into

Layer 2: Domain Capability (EA)
  "AI-powered Resource Matching"
    ↓ defined by

Layer 3: Domain Knowledge
  Glossary: Resource, Skill, Allocation, MatchScore
  Business Rules: totalAllocation ≤ 100%, skillMatch ≥ 80%
    ↓ modeled in

Layer 4: Design Artifacts
  Event Storm: ResourceRequested → MatchesFound → ResourceAllocated
  ADR: ADR-001 (Allocation %, not Man-Day)
  Context Map: Resource Context ↔ Sales Context (Customer-Supplier)
    ↓ implemented as

Layer 5: Code
  API: POST /resources/{id}/allocate
  Domain Service: ResourceMatchingService
  Aggregate: Resource (Root)
    ↓ verified by

Layer 6: Tests
  Unit: ResourceMatchingServiceTest
  Integration: AllocationAPITest
  E2E: StaffingFlowTest
```

### Cross-linking Pattern

```markdown
<!-- glossary/allocation.md -->
# Allocation

Việc gán resource vào project với % nhất định trong khoảng thời gian.

## Business Rules
- totalAllocation ≤ 100% per resource
- Allocation period phải trong contract period

## Related
- **Events:** [ResourceAllocated](../event-storms/resource-mgmt.md#resourceallocated)
- **ADRs:** [ADR-001: Allocation %](../decisions/adr-001.md)
- **Context:** [Resource Context](../domain/context-map.md#resource)
- **API:** [POST /resources/{id}/allocate](../api/resource-api.md#allocate)
- **Tests:** [AllocationTest](../../tests/resource-allocation.test.ts)
- **Policy:** [POL-001: Allocation Policy](../policies/allocation-policy.md)
- **Capability:** [Resource Allocation](../ea/capability-map.md#resource-allocation)
```

### ITO Traceability Matrix

| Business Goal | Capability | Glossary | Events | ADR | API | Test |
|---|---|---|---|---|---|---|
| Tăng win rate | Resource Matching | Resource, Skill, MatchScore | MatchesFound, ResourceAllocated | ADR-001 | POST /match | MatchingServiceTest |
| Giảm bench rate | Allocation Tracking | Allocation, BenchStatus | ResourceBenched, ResourceAllocated | ADR-002 | GET /bench-report | BenchReportTest |
| Faster staffing | Auto-qualification | Opportunity, WinProbability | OpportunityQualified | ADR-003 | POST /qualify | QualifyFlowTest |
| Audit compliance | Event History | DomainEvent, EventStore | All events | ADR-004 | GET /events/{id} | AuditTrailTest |

### Logistics Traceability Matrix

| Business Goal | Capability | Events | ADR | Test |
|---|---|---|---|---|
| Giảm delivery time | Route Optimization | RoutePlanned, TripDispatched | ADR-L01 | RouteOptTest |
| Tăng POD rate | Real-time Tracking | DeliveryConfirmed, PODSigned | ADR-L02 | PODFlowTest |
| Giảm fuel cost | Fleet Management | FuelConsumed, RouteOptimized | ADR-L03 | FleetEfficiencyTest |

### Impact Analysis — Khi thay đổi 1 node

```
Scenario: Business quyết định đổi từ % sang Man-Day allocation

1. ADR-001 SUPERSEDED by ADR-005 (% → Man-Day)
   ↓ trace Related links
2. Glossary: allocation.md → update definition
3. Events: ResourceAllocated → schema change (percentage → manDays)
4. API: POST /allocate → request/response schema change
5. Aggregate: Resource → AllocationPercentage VO → AllocationManDay VO
6. Tests: ALL allocation tests → update assertions
7. Policy: POL-001 → update policy statements
8. Event Sourcing: Upcasting for old events (percentage → manDays conversion)

→ 1 change → trace 8 impacts
→ WITHOUT Knowledge Graph: miss 3-4 impacts → bugs in production
```

---

## 🏋️ Exercise — Xây dựng Knowledge Graph

### Phần A: ITO — Full Traceability Chain (15 phút)

Chọn 1 Business Goal, trace 6 layers:

| Layer | Artifact Type | Cụ thể |
|---|---|---|
| Business Goal | | |
| Capability | | |
| Glossary Terms | | |
| Design Artifacts | | |
| Code | | |
| Tests | | |

### Phần B: Logistics (10 phút)

Làm tương tự cho 1 Logistics Business Goal.

### Phần C: Cross-linking exercise (5 phút)

Chọn 1 glossary entry và viết `## Related` section:

```markdown
## Related
- Events: _______________
- ADRs: _______________
- Bounded Context: _______________
- Business Goal: _______________
- Tests: _______________
```

---

## 🪞 Reflect

1. **Knowledge Graph cần tool đặc biệt (Neo4j, Obsidian)?** → Bắt đầu bằng **Markdown cross-links** (như `## Related` section). Chỉ cần tool khi graph > 100 nodes. Obsidian graph view hữu ích nhưng không bắt buộc. **Content trước, tool sau.**

2. **Ai maintain Knowledge Graph?** → **Mọi người contribute** (dev thêm API links, BA thêm glossary links), **Architect review** (đảm bảo consistency). Automate: CI check broken links. **Rule:** PR thay đổi code → PHẢI update Related links trong affected docs.

3. **Thay đổi 1 glossary term → làm sao biết impact?** → Trace `## Related` links → biết ảnh hưởng ADR nào, API nào, Test nào. **Knowledge Graph = impact analysis tool.** Không có graph → miss impacts → bugs.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 6-layer Traceability Chain + Knowledge Graph concept
- [ ] **Apply:** Tạo Traceability Matrix cho ITO (≥3 rows) + cross-link 1 glossary entry
- [ ] **Analyze:** Thực hiện Impact Analysis — trace 1 thay đổi qua ≥4 layers

---

> 🔗 **Tiếp theo:** Knowledge Graph liên kết artifacts. Nhưng knowledge không chỉ ở files — nó ở trong đầu mọi người (tribal knowledge). Bài tiếp — *Organizational Knowledge* — sẽ trả lời: làm sao codify tribal knowledge thành Knowledge Objects? Meeting Records, Policy Documents, Onboarding Guides = organizational memory.
