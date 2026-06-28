---
type: lesson
module: 6
lesson: 1
title: "Repository Structure"
duration: "25 phút"
prerequisites: ["module-5"]
---

# Lesson 6.1: Repository Structure

## 🎓 Concept — "Knowledge sống trong code repo, không trong Confluence"

### Vấn đề với documentation truyền thống

```
Truyền thống:
- Requirements → Confluence wiki (page 1 trong 500 pages)
- Design → Google Docs (ai biết link nào mới nhất?)
- Decisions → Email chains (mất sau 6 tháng)
- Glossary → Excel (version nào là latest?)

→ 6 tháng sau: không ai tìm được, AI đọc không được, knowledge bị mất
```

### Docs-as-Code — Tài liệu sống trong repo

```
DDD Approach:
project-repo/
├── src/                    ← Code
├── docs/                   ← Knowledge (SỐNG trong repo)
│   ├── domain/             ← Domain knowledge
│   │   ├── glossary/       ← Ubiquitous Language
│   │   ├── domain-map.md   ← Domain Map
│   │   └── context-map.md  ← Context Map
│   ├── decisions/          ← ADRs (Architecture Decision Records)
│   │   ├── 001-use-percentage-for-allocation.md
│   │   └── 002-resource-matching-algorithm.md
│   └── architecture/       ← Technical architecture
│       ├── bounded-contexts.md
│       └── integration-patterns.md
├── tests/
└── README.md
```

### 3 nguyên tắc

#### 1. Atomic Files — 1 file = 1 concept
```
❌  glossary.md (30 terms trong 1 file → 500 dòng, unmaintainable)
✅  glossary/
     ├── resource.md     ← 1 file per term
     ├── allocation.md
     └── utilization.md
```

Tại sao? Vì AI Agent chỉ cần đọc `resource.md` (50 dòng) thay vì cả file 500 dòng.

#### 2. Version-Controlled — Tài liệu có lịch sử
```
git log docs/domain/glossary/resource.md
→ Ngày 1: Created with basic definition
→ Ngày 15: Updated business rules (allocation ≤ 100%)
→ Ngày 30: Added Freelancer decision

→ Biết chính xác ai thay đổi gì khi nào
→ Rollback được nếu sai
```

#### 3. Cross-Linked — Tài liệu liên kết với nhau
```
resource.md:
  "Xem thêm: [Allocation](./allocation.md), [Bench](./bench.md)"

allocation.md:
  "Đo cho [Resource](./resource.md), thuộc [Resource Management](../domain-map.md#resource-management)"

→ Tạo thành knowledge graph — AI có thể traverse
```

### Ví dụ — ITO Knowledge Repository

```
ito-crm/
├── docs/
│   ├── domain/
│   │   ├── glossary/
│   │   │   ├── README.md          ← Index + conflict severity
│   │   │   ├── resource.md        ← 🔴 High conflict
│   │   │   ├── allocation.md      ← 🔴 High conflict
│   │   │   ├── utilization.md     ← 🔴 High conflict
│   │   │   ├── bench.md           ← 🟠 Medium
│   │   │   ├── opportunity.md     ← 🟠 Medium
│   │   │   ├── lead.md            ← 🟡 Low
│   │   │   ├── account.md         ← 🔴 High (4 contexts)
│   │   │   └── project.md         ← 🟠 Medium
│   │   ├── domain-map.md          ← Domain Map + Capabilities
│   │   ├── domain-priority.md     ← Core/Supporting/Generic
│   │   └── context-map.md         ← Integration patterns
│   ├── decisions/
│   │   ├── 001-allocation-use-percentage.md
│   │   ├── 002-resource-matching-ai-augmented.md
│   │   └── 003-customer-success-as-core.md
│   └── event-storms/
│       ├── resource-management.md
│       └── opportunity-lifecycle.md
├── src/
│   ├── resource-context/
│   ├── sales-context/
│   └── shared-kernel/
└── README.md
```

### Ví dụ — Logistics Knowledge Repository

```
logistics-platform/
├── docs/
│   ├── domain/
│   │   ├── glossary/
│   │   │   ├── shipment.md
│   │   │   ├── trip.md
│   │   │   ├── route.md
│   │   │   └── vehicle.md
│   │   ├── domain-map.md
│   │   └── context-map.md
│   ├── decisions/
│   │   ├── 001-route-optimization-algorithm.md
│   │   └── 002-real-time-tracking-tech-stack.md
│   └── event-storms/
│       └── order-to-delivery.md
├── src/
│   ├── route-context/
│   ├── fleet-context/
│   └── tracking-context/
└── README.md
```

---

## 🏋️ Exercise — Thiết kế Repository Structure

### Phần A: ITO — Mapping code structure theo Bounded Context (10 phút)

Thiết kế folder `src/` cho ITO dựa trên Context Map (Module 5):

```
src/
├── _______________-context/
│   ├── domain/          ← Entities, Value Objects
│   ├── application/     ← Use Cases, Services
│   ├── infrastructure/  ← DB, External APIs
│   └── api/             ← REST endpoints
├── _______________-context/
├── _______________-context/
└── shared-kernel/       ← Shared Value Objects
```

### Phần B: Thiết kế `docs/` folder (10 phút)

Liệt kê mọi file sẽ có trong `docs/`:

```
docs/
├── domain/
│   ├── glossary/
│   │   ├── (liệt kê)
│   ├── (liệt kê)
├── decisions/
│   ├── (liệt kê ≥3 ADRs cần viết)
└── event-storms/
    ├── (liệt kê)
```

---

## 🪞 Reflect

1. **Tại sao glossary nên sống trong code repo thay vì Confluence?** Gợi ý: dev thay đổi business rule trong code nhưng không update Confluence → drift.

2. **"Docs-as-Code" có nhược điểm gì?** Non-technical stakeholders (Sales, CEO) có đọc Markdown trong Git không?

3. **Atomic files có limit không?** 100 glossary terms = 100 files → quá nhiều? Hay vẫn OK vì mỗi file nhỏ?

### 💡 Insight: Mọi DDD artifact = Knowledge Object

Nhìn lại 5 modules vừa qua, bạn đã tạo ra nhiều loại artifact khác nhau:

| Module | Artifact | Ví dụ |
|---|---|---|
| 1 | Domain Map | `domain-map.md` — 9 domains + capabilities |
| 2 | Glossary entries | `glossary/resource.md` — định nghĩa + business rules |
| 3 | Priority Matrix | `domain-priority.md` — Core/Supporting/Generic |
| 4 | Event Storms | `event-storms/resource-management.md` — Events, Commands, Policies |
| 5 | Context Map | `context-map.md` — Bounded Contexts + integration patterns |
| 6 | ADRs | `decisions/001-*.md` — quyết định + lý do |

Chúng trông khác nhau, nhưng có chung **3 đặc điểm**:
1. **Atomic** — mỗi file = 1 đơn vị tri thức độc lập
2. **Cross-linked** — liên kết với nhau qua references
3. **Machine-readable** — AI Agent có thể đọc, parse, và reasoning

Trong Knowledge Architecture, tất cả đều được gọi chung là **Knowledge Objects** — đơn vị tri thức có thể version, cross-link, và cho cả người lẫn AI đọc. Khi bạn tổ chức repository, bạn không "viết tài liệu" — bạn đang **xây dựng Knowledge Graph** từ các Knowledge Objects.

> Lesson 6.2 và 6.3 tiếp theo sẽ dạy cách liên kết (Traceability) và tối ưu hóa (AI Context Design) cho các Knowledge Objects này.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích 3 nguyên tắc (Atomic, Version-controlled, Cross-linked)
- [ ] Thiết kế `src/` folder theo Bounded Contexts
- [ ] Thiết kế `docs/` folder cho ITO

