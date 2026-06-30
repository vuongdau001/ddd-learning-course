---
type: lesson
module: 6
lesson: 1
title: "Repository Structure"
duration: "25 phút"
prerequisites: ["module-5"]
narrative_phase: "kiến trúc tri thức"
migration_phase: "Phase 1: Setup Knowledge Repository"
business_invariant: "Docs sống trong code repo (Docs-as-Code); 1 file = 1 concept (Atomic); Cross-linked → Knowledge Graph"
---

# Lesson 6.1: Repository Structure — "Knowledge sống trong code repo, không trong Confluence"

## 📍 Context — Bạn đang ở đây

> Module 1-5 cho bạn blueprint hoàn chỉnh: Domain Map → Glossary → Strategic Classification → Event Storm → Bounded Contexts → Context Map → Integration Patterns. ITO Corp migration Phase 1 đang chạy. Nhưng bạn nhận ra: toàn bộ knowledge này **nằm rải rác** — Domain Map trong Miro, Glossary trong Google Sheets, Event Storm chụp ảnh whiteboard, Context Map vẽ trên Figma.
>
> 6 tháng nữa, khi onboard dev mới, họ sẽ hỏi: *"Business rules của Resource ở đâu? Ai quyết định dùng AI-Augment? Event flow nào trigger Billing?"* — Và không ai trả lời được vì knowledge bị mất trong 15 tools khác nhau.

## 🔥 Tension — "Confluence đã chết 3 tháng trước"

Sprint 6. ITO onboard 2 dev mới. Ngày đầu tiên:

> **Dev G:** *"Tôi cần hiểu business rules của Resource allocation. Documentation ở đâu?"*
>
> **Tuấn:** *"Tôi viết trong Confluence. Tìm page 'Resource Management Rules'."*
>
> **Dev G (sau 30 phút):** *"Page đó update lần cuối 3 tháng trước. Từ đó team đã thêm AI matching, thay đổi allocation % rule, và thêm bench threshold mới. Confluence không khớp với code."*

**Minh** kiểm tra thêm:
> - Domain Map: trong Miro (link expired, cần invite)
> - Event Storm: ảnh chụp whiteboard (hình mờ, sticky note rơi)
> - ADRs: trong email chains (ai biết thread nào?)
> - Glossary: Google Sheets (3 copies, không biết version nào đúng)
>
> *"Knowledge của chúng ta đang **chết**. Developer mới mất 2 tuần đọc docs sai, rồi mất thêm 1 tuần hỏi lại. AI Agent cũng không đọc được Miro hay Confluence. Chúng ta cần knowledge sống CÙNG code."*

> 💭 **Câu hỏi:** Glossary, Domain Map, Event Storm, ADRs — tất cả đều là "tri thức". Nhưng tri thức nằm ngoài code repo → sẽ drift, lỗi thời, mất. Làm sao tổ chức tri thức để nó **sống cùng code**?

## 🎓 Explanation — Docs-as-Code

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Docs sống trong code repo (Docs-as-Code) — cùng version, cùng review, cùng deploy. 1 file = 1 concept (Atomic) — AI chỉ cần đọc 50 dòng, không phải 500 dòng. Cross-linked → tạo Knowledge Graph — traversable bởi cả người và AI."*

### Vấn đề với documentation truyền thống

```
Truyền thống:
- Requirements → Confluence wiki (page 1 trong 500 pages)
- Design → Google Docs (link nào mới nhất?)
- Decisions → Email chains (mất sau 6 tháng)
- Glossary → Excel (3 copies, version nào đúng?)

→ 6 tháng sau: không ai tìm được, AI đọc không được, knowledge chết
```

### Docs-as-Code — Tri thức sống trong repo

```
DDD Approach:
project-repo/
├── src/                    ← Code (solution)
├── docs/                   ← Knowledge (SỐNG cùng code)
│   ├── domain/             ← Domain knowledge
│   │   ├── glossary/       ← Ubiquitous Language
│   │   ├── domain-map.md   ← Domain Map
│   │   └── context-map.md  ← Context Map
│   ├── decisions/          ← ADRs (Architecture Decision Records)
│   │   ├── 001-allocation-use-percentage.md
│   │   └── 002-resource-matching-ai-augmented.md
│   └── event-storms/       ← Event Storm outputs
│       └── resource-management.md
├── tests/
└── README.md
```

### 3 Nguyên tắc

#### 1. Atomic Files — 1 file = 1 concept

```
❌  glossary.md (30 terms trong 1 file → 500 dòng, unmaintainable)
✅  glossary/
     ├── resource.md     ← 1 file per term (50 dòng)
     ├── allocation.md
     └── utilization.md
```

**Tại sao?** AI Agent chỉ cần đọc `resource.md` (50 dòng) khi cần hiểu "Resource" — không phải load cả file 500 dòng. Dev tìm nhanh hơn. Git diff rõ ràng hơn.

#### 2. Version-Controlled — Tri thức có lịch sử

```
git log docs/domain/glossary/resource.md
→ Day 1:  Created basic definition (Minh + Tuấn)
→ Day 15: Updated allocation rule (allocation ≤ 100%) (Dev A)
→ Day 30: Added freelancer sub-type decision (PM Alpha + HR)
→ Day 45: Added AI matching semantic rules (Dev C)

→ Biết chính xác: ai thay đổi gì, khi nào, tại sao
→ Rollback được nếu sai
→ PR review = domain expert review
```

#### 3. Cross-Linked — Tri thức liên kết thành Knowledge Graph

```
resource.md:
  "Xem thêm: [Allocation](./allocation.md), [Bench](./bench.md)"
  "Thuộc domain: [Resource Management](../domain-map.md#resource-management)"
  "Strategic: [Core Domain - Build + AI-Augment](../../decisions/002-resource-matching.md)"

allocation.md:
  "Đo cho [Resource](./resource.md)"
  "Event flow: [ResourceAllocated](../../event-storms/resource-management.md#allocation-cycle)"

→ Tạo Knowledge Graph — AI có thể traverse links, người có thể navigate
```

### ITO Knowledge Repository

```
ito-crm/
├── docs/
│   ├── domain/
│   │   ├── glossary/
│   │   │   ├── README.md          ← Index + conflict severity (từ M2.2)
│   │   │   ├── resource.md        ← 🔴 High conflict (4 contexts)
│   │   │   ├── allocation.md      ← 🔴 High conflict
│   │   │   ├── utilization.md     ← 🔴 High conflict
│   │   │   ├── bench.md           ← 🟠 Medium
│   │   │   ├── opportunity.md     ← 🟠 Medium
│   │   │   ├── lead.md            ← 🟡 Low
│   │   │   ├── account.md         ← 🔴 High (4 contexts)
│   │   │   └── project.md         ← 🟠 Medium
│   │   ├── domain-map.md          ← Domain Map + Capabilities (M1)
│   │   ├── domain-priority.md     ← Core/Supporting/Generic + Priority Matrix (M3)
│   │   └── context-map.md         ← Bounded Contexts + Integration patterns (M5)
│   ├── decisions/
│   │   ├── 001-allocation-use-percentage.md    ← Tại sao % thay vì hours
│   │   ├── 002-resource-matching-ai-augmented.md ← Tại sao Build + AI
│   │   ├── 003-opportunity-build-not-salesforce.md ← Tại sao không Salesforce
│   │   └── 004-contract-billing-gop-finance.md ← Tại sao gộp N:1
│   └── event-storms/
│       ├── resource-management.md  ← Full Event Storm (M4.3)
│       └── opportunity-lifecycle.md ← Opportunity flow (M4.1)
├── src/
│   ├── resource-context/
│   │   ├── domain/          ← Entities, Value Objects, Aggregates
│   │   ├── application/     ← Use Cases, Services
│   │   ├── infrastructure/  ← DB, External APIs, AI integration
│   │   └── api/             ← REST endpoints
│   ├── sales-context/
│   ├── finance-context/
│   ├── delivery-context/
│   └── shared-kernel/       ← ResourceId, Money, DateRange, SkillTag
└── README.md
```

### Logistics Knowledge Repository

```
logistics-platform/
├── docs/
│   ├── domain/
│   │   ├── glossary/
│   │   │   ├── shipment.md, trip.md, route.md, vehicle.md
│   │   ├── domain-map.md
│   │   └── context-map.md
│   ├── decisions/
│   │   ├── 001-route-optimization-ai-augmented.md
│   │   └── 002-tracking-tech-stack-websocket.md
│   └── event-storms/
│       └── order-to-delivery.md
├── src/
│   ├── route-context/
│   ├── fleet-context/
│   ├── tracking-context/
│   ├── order-context/
│   └── warehouse-context/
└── README.md
```

### 💡 Insight: Mọi DDD artifact = Knowledge Object

Nhìn lại 5 modules, bạn đã tạo ra nhiều loại artifact:

| Module | Artifact | Ví dụ | Sống ở đâu |
|---|---|---|---|
| 1 | Domain Map | `domain-map.md` — 8 domains + capabilities | `docs/domain/` |
| 2 | Glossary entries | `glossary/resource.md` — 8 fields/term | `docs/domain/glossary/` |
| 3 | Priority Matrix | `domain-priority.md` — Core/Supporting/Generic + Investment Map | `docs/domain/` |
| 4 | Event Storms | `event-storms/resource-management.md` | `docs/event-storms/` |
| 5 | Context Map + Integration | `context-map.md` | `docs/domain/` |
| 6 | ADRs | `decisions/001-*.md` — quyết định + lý do | `docs/decisions/` |

Tất cả đều là **Knowledge Objects**: atomic, cross-linked, machine-readable. Khi tổ chức repository, bạn đang **xây Knowledge Graph**.

### ⚖️ Trade-offs — Docs-as-Code

| | Docs-as-Code (repo) | Confluence/Wiki | Mixed (repo + wiki) |
|---|---|---|---|
| **Được** | Version controlled, AI readable, sống cùng code, PR review | Non-tech stakeholders dễ đọc/edit, rich formatting | Best of both worlds |
| **Mất** | Non-tech phải học Git/Markdown | Drift vs code, no version history, AI khó đọc | 2 nơi maintain, risk desync |
| **Khi nào** | Team kỹ thuật, DDD artifacts | Stakeholder-facing docs (user guides, training) | Large org, separate audiences |

---

## 🏋️ Exercise — Thiết kế Repository Structure

### Phần A: ITO — Mapping `src/` theo Bounded Context (10 phút)

Từ Context Map (Module 5), thiết kế folder `src/`:

```
src/
├── _______________-context/
│   ├── domain/          ← Entities, Value Objects
│   ├── application/     ← Use Cases, Services
│   ├── infrastructure/  ← DB, External APIs
│   └── api/             ← REST endpoints
├── _______________-context/
├── _______________-context/
├── _______________-context/
└── shared-kernel/
    └── (liệt kê types: ≤10)
```

### Phần B: Thiết kế `docs/` folder (10 phút)

```
docs/
├── domain/
│   ├── glossary/
│   │   ├── (liệt kê ≥8 terms)
│   ├── domain-map.md
│   ├── domain-priority.md
│   └── context-map.md
├── decisions/
│   ├── (liệt kê ≥3 ADRs cần viết)
└── event-storms/
    ├── (liệt kê)
```

### Phần C: Cross-linking (5 phút)

Cho Glossary entry `resource.md`, viết ≥3 cross-links:

```
## See Also
- [___](link) — ___
- [___](link) — ___
- [___](link) — ___
```

---

## 🪞 Reflect

1. **Tại sao glossary sống trong repo thay vì Confluence?** → Dev thay đổi allocation rule trong code nhưng không update Confluence → **drift.** Trong repo: cùng PR = cùng review. Rule thay đổi → docs phải update trong cùng PR → reviewer enforce.

2. **Docs-as-Code có nhược điểm gì?** → Non-technical stakeholders (Hà, CFO) không đọc Markdown trong Git. **Mitigation:** generate HTML site từ Markdown (MkDocs, Docusaurus) hoặc maintain high-level summaries trong Confluence, detail trong repo.

3. **100 glossary terms = 100 files — quá nhiều?** → **Không.** Mỗi file 50-80 dòng. Git hiểu diff tốt. IDE search instant. AI Agent load chính xác file cần. 1 file 5000 dòng → AI hallucinate vì context window overload.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 3 nguyên tắc Docs-as-Code (Atomic, Version-controlled, Cross-linked)
- [ ] **Apply:** Thiết kế `src/` folder theo Bounded Contexts + `docs/` folder cho ITO
- [ ] **Analyze:** Giải thích tại sao Confluence drift xảy ra — liên hệ với "Dev G mất 30 phút tìm docs sai"

---

> 🔗 **Tiếp theo:** Repository structure = nơi chứa knowledge. Nhưng quyết định kiến trúc cần **traceability** — tại sao chọn Build + AI cho Resource Matching? Tại sao không Salesforce? Bài tiếp — *ADR và Traceability* — sẽ dạy bạn cách ghi lại quyết định bằng Architecture Decision Records, tạo audit trail từ business need → technical decision.
