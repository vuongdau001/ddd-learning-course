---
type: lesson
module: 6
lesson: 2
title: "ADR và Traceability"
duration: "30 phút"
prerequisites: ["module-6/lesson-1"]
narrative_phase: "kiến trúc tri thức"
migration_phase: "Phase 1: Ghi lại quyết định kiến trúc"
business_invariant: "Mỗi quyết định kiến trúc PHẢI có ADR; ADR liên kết ngược tới Glossary/Event Storm/Context Map; Traceability Chain = audit trail"
---

# Lesson 6.2: ADR và Traceability — "Ghi nhớ TẠI SAO, không chỉ CÁI GÌ"

## 📍 Context — Bạn đang ở đây

> Lesson 6.1 cho bạn Repository Structure: `docs/` sống cùng `src/`, mỗi file = 1 concept, cross-linked thành Knowledge Graph. Nhưng structure chỉ trả lời "CÁI GÌ nằm ở đâu" — không trả lời "TẠI SAO chọn giải pháp này". Khi dev mới hỏi "Tại sao allocation dùng % thay vì Man-Day?" — không ai trả lời được nếu không ghi lại quyết định.

## 🔥 Tension — "Người quyết định đã nghỉ"

Sprint 8. 3 tháng sau khi ITO migration bắt đầu. Dev G đang fix bug allocation:

> **Dev G:** *"Allocation dùng % — tổng ≤ 100%. Nhưng PM nói họ cần phân bổ theo Man-Day vì client billing dùng Man-Day. Tại sao team chọn %? Có lý do gì không?"*
>
> **Tuấn:** *"Hmm... Minh quyết định. Nhưng Minh vừa chuyển team khác tuần trước."*
>
> **Dev G:** *"Không có tài liệu nào giải thích tại sao. Tôi không biết nếu đổi sang Man-Day thì break cái gì."*

Dev G mất 3 ngày:
- 1 ngày đọc code tìm "context" xung quanh allocation
- 1 ngày hỏi team nhưng ai nhớ ai quên
- 1 ngày refactor → phát hiện Finance Context phụ thuộc vào % → **rollback**

> **Minh (remote):** *"Tôi chọn % vì 3 lý do: (1) cross-context dễ dùng, (2) validation đơn giản sum ≤ 100%, (3) convert sang hours khi cần. Đáng lẽ phải viết ADR. Sorry."*

> 💭 **Câu hỏi:** Code nói "CÁI GÌ" — `allocation <= 100%`. Nhưng code KHÔNG nói "TẠI SAO". Và khi người quyết định rời team → knowledge bị mất. Làm sao ghi lại quyết định để **bất kỳ ai, bất kỳ lúc nào** đều hiểu lý do?

## 🎓 Explanation — Architecture Decision Record (ADR)

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mỗi quyết định kiến trúc PHẢI có ADR trong `docs/decisions/`. ADR phải liên kết ngược tới Glossary entry, Event Storm output, và Context Map. Traceability Chain = audit trail từ business need → technical decision → code implementation."*

### ADR là gì?

ADR = tài liệu **ngắn gọn** (đọc xong trong 5 phút) ghi lại 1 quyết định kiến trúc:

```
ADR = Context (tại sao cần quyết định)
    + Options (các lựa chọn + trade-offs)
    + Decision (chọn gì + lý do)
    + Consequences (hệ quả tốt/xấu)
```

### Template ADR

```markdown
# ADR-001: [Tên quyết định — ngắn gọn, hành động]

## Status
Accepted | Proposed | Deprecated | Superseded by ADR-XXX

## Date
YYYY-MM-DD

## Context
[Tình huống business gì dẫn đến quyết định này?
Liên kết với Glossary, Event Storm, hoặc business constraint cụ thể.]

## Decision Drivers
- [Yếu tố 1 — từ business constraint]
- [Yếu tố 2 — từ technical constraint]
- [Yếu tố 3 — từ team constraint]

## Options Considered
1. **[Option A]** — ưu/nhược
2. **[Option B]** — ưu/nhược
3. **[Option C]** — ưu/nhược

## Decision
Chọn **[Option X]** vì [lý do kết nối với Decision Drivers].

## Consequences
### Positive
- [Hệ quả tốt — mapped to business value]
### Negative
- [Trade-off chấp nhận — what you give up]
### Risks
- [Rủi ro cần monitor — trigger để revisit ADR]

## References
- [Link Glossary entry](../domain/glossary/xxx.md)
- [Link Event Storm](../event-storms/xxx.md)
- [Link Context Map](../domain/context-map.md)
```

### ITO ADR — Ví dụ đầy đủ

```markdown
# ADR-001: Allocation sử dụng % thay vì Man-Day

## Status
Accepted

## Date
2026-06-25

## Context
Allocation là concept xuyên suốt 3 Bounded Contexts (Resource, Delivery,
Finance). Resource Context dùng để track "resource bận bao nhiêu",
Finance dùng để tính billing, Delivery dùng để plan sprint capacity.
Cần chọn đơn vị đo thống nhất. Dev G đã thử đổi sang Man-Day →
break Finance Context (dependency on %) → rollback.

Ref: [Glossary: Allocation](../domain/glossary/allocation.md)

## Decision Drivers
- Resource có thể allocate cho nhiều project cùng lúc (partial allocation)
- Finance cần convert ra tiền (billing = % × available_hours × rate)
- PM cần dashboard "resource bận bao nhiêu" → trực quan
- 5-dev team → đơn giản hơn = maintain dễ hơn

## Options Considered
1. **% Time** — "Resource A: 60% Project X, 40% Project Y"
   - ✅ Trực quan, validate đơn giản (sum ≤ 100%)
   - ✅ Cross-context communication dễ
   - ❌ Không chính xác giờ (60% ≈ bao nhiêu giờ/tuần?)

2. **Man-Day** — "Resource A: 3 Man-Day/tuần cho Project X"
   - ✅ Chính xác giờ
   - ❌ "1 tuần = bao nhiêu Man-Day?" (4? 5? 5.5?)
   - ❌ Part-time, nghỉ phép → Man-Day thay đổi → complexity

3. **Hours** — "Resource A: 24 giờ/tuần cho Project X"
   - ✅ Rất chính xác
   - ❌ Quá granular cho CRM, over-engineering
   - ❌ Tracking giờ = micromanagement → resource phản đối

## Decision
Chọn **% Time** vì:
1. Trực quan nhất cho cross-context communication (Resource ↔ Finance ↔ Delivery)
2. Validation đơn giản: `if totalAllocation > 100% → reject`
3. Convert sang hours khi cần: `hours = % × available_hours_per_week`

## Consequences
### Positive
- Business rule đơn giản: 1 invariant duy nhất (sum ≤ 100%)
- Mọi context dùng cùng đơn vị → no translation needed
### Negative
- Mất precision: "60%" không nói rõ bao nhiêu giờ/ngày
- Cần convention: `available_hours = 40 giờ/tuần` (trừ nghỉ phép)
### Risks
- Nếu client yêu cầu billing chính xác giờ → revisit ADR, xem xét Hybrid (% + timesheet)

## References
- [Glossary: Allocation](../domain/glossary/allocation.md)
- [Glossary: Utilization](../domain/glossary/utilization.md)
- [Event Storm: ResourceAllocated](../event-storms/resource-management.md)
- [Context Map: Resource ↔ Finance](../domain/context-map.md)
```

### Logistics ADR — Ví dụ

```markdown
# ADR-001: Route Optimization dùng AI-Augmented (Hybrid)

## Status
Accepted

## Context
Route Optimization là Core Domain (M3 Priority Matrix). Cần chọn approach:
team có OR (Operations Research) experience nhưng không có ML team.

## Options Considered
1. **Pure OR** (Linear Programming, Constraint Solver)
   - ✅ Deterministic, provable optimal
   - ❌ Slow với large-scale, không learn từ historical data
2. **AI-only** (ML predict optimal routes)
   - ✅ Learns, improves over time
   - ❌ Non-deterministic, safety risk cho hard constraints
3. **Hybrid: OR constraints + AI soft-optimization**
   - ✅ Hard constraints deterministic (capacity, time windows)
   - ✅ AI optimizes soft objectives (minimize distance, fuel)
   - ❌ 2 systems to maintain

## Decision
Chọn **Hybrid** vì safety-critical constraints phải deterministic;
AI improves soft optimization over time; team có OR but not ML → AI-Augment feasible.

## References
- [Domain Priority: Route = Core](../domain/domain-priority.md)
- [ADR M3.3: Build + AI-Augment](../decisions/002-build-vs-buy.md)
```

### Traceability Chain — Kết nối mọi thứ

ADR không sống đơn lẻ. Nó là 1 node trong Knowledge Graph:

```
                ┌──────────────────────┐
                │  Business Need       │
                │  "PM cần biết resource│
                │   bận bao nhiêu"     │
                └──────────┬───────────┘
                           │
                ┌──────────▼───────────┐
                │  Glossary Entry      │
                │  allocation.md       │
                │  "% time assigned"   │
                └──────────┬───────────┘
                           │
           ┌───────────────┼───────────────┐
           │               │               │
   ┌───────▼───────┐ ┌────▼────────┐ ┌────▼────────┐
   │  ADR-001      │ │ Event Storm │ │ Context Map │
   │  "Dùng % vì  │ │ "Resource   │ │ "Resource   │
   │   cross-ctx"  │ │  Allocated" │ │  ↔ Finance" │
   └───────┬───────┘ └─────────────┘ └─────────────┘
           │
   ┌───────▼───────────────────┐
   │  Code Implementation      │
   │  allocation.totalPct ≤ 100│
   │  (Resource Aggregate)     │
   └───────────────────────────┘
```

**Cách đọc:** Business Need → sinh ra Glossary term → dẫn đến ADR quyết định % → Event Storm capture event "ResourceAllocated" → Code implement invariant `totalPct ≤ 100%`. **Bất kỳ node nào thay đổi → traverse chain để đánh giá impact.**

### ⚖️ Trade-offs — ADR Practices

| | Viết ADR cho mọi quyết định | Chỉ viết ADR cho "lớn" | Không viết ADR |
|---|---|---|---|
| **Được** | Complete audit trail | Ít effort, focus quan trọng | Nhanh |
| **Mất** | Quá nhiều ADRs → không ai đọc | Miss quyết định "nhỏ nhưng quan trọng" | Knowledge mất khi người rời |
| **Rule of thumb** | Overkill trừ khi regulated | ✅ **Recommended** — viết khi ≥2 options, cross-context impact, hoặc team tranh cãi | Dev G scenario |

---

## 🏋️ Exercise — Viết ADR

### Phần A: Viết ADR cho ITO (15 phút)

Chọn 1 quyết định từ quá trình học và viết ADR đầy đủ. Gợi ý:
- "Customer Success là Core Domain" (M3.1)
- "Resource Matching dùng AI-Augmented" (M3.3)
- "Không mua Salesforce cho Opportunity Management" (M3.3)
- "Tách Resource Management thành Skill Inventory + Allocation" (M5.1)

```markdown
# ADR-___: _______________

## Status

## Date

## Context
(link tới Glossary entry + Event Storm)

## Decision Drivers

## Options Considered

## Decision

## Consequences
### Positive
### Negative
### Risks

## References
```

### Phần B: Viết ADR cho Logistics (10 phút)

Viết ADR cho: "Chọn tech cho Real-time Tracking: WebSocket vs Server-Sent Events vs Polling"

```markdown
# ADR-002: _______________
(viết đầy đủ — context, options, decision, consequences)
```

### Phần C: Traceability Chain (15 phút)

Chọn 1 Knowledge Object bạn đã tạo (VD: Glossary entry "Resource") và trace xuyên suốt:

| Layer | Knowledge Object Type | Ví dụ: "Resource" | File Location |
|---|---|---|---|
| **Business Term** | Glossary entry | Resource = nhân viên kỹ thuật | `glossary/resource.md` |
| **Business Rule** | Invariant | allocation ≤ 100%, bench threshold 70% | `glossary/resource.md#invariants` |
| **Domain Event** | Event Storm | ResourceAllocated, ResourceBenched | `event-storms/resource-management.md` |
| **Strategic** | Context Map | Resource Context (Core) → Sales (Partnership) | `domain/context-map.md` |
| **Decision** | ADR | Dùng % Time (ADR-001), AI matching (ADR-002) | `decisions/adr-001.md` |
| **Code** | Aggregate | Resource aggregate, allocation invariant | `src/resource-context/domain/` |

**Bây giờ, làm tương tự cho 1 term khác (VD: Opportunity, Route, Shipment):**

| Layer | Knowledge Object Type | Term: ___________ | File Location |
|---|---|---|---|
| Business Term | Glossary entry | | |
| Business Rule | Invariant | | |
| Domain Event | Event Storm | | |
| Strategic | Context Map | | |
| Decision | ADR | | |
| Code | Aggregate | | |

---

## 🪞 Reflect

1. **ADR có "hết hạn" không?** → Có. Khi business constraint thay đổi (VD: client yêu cầu giờ billing thay vì %) → viết ADR mới, set ADR cũ = `Superseded by ADR-XXX`. **Không xóa** ADR cũ — nó là lịch sử giải thích tại sao chúng ta từng chọn khác.

2. **Ai nên viết ADR?** → **Người quyết định** viết draft, **team review** trong PR. Không phải chỉ architect — nếu dev quyết định tách context → dev viết ADR. Key: ADR = collective ownership, không phải individual opinion.

3. **ADR quá dài (>2 trang)?** → **Tách.** Mỗi ADR = 1 quyết định. Nếu 1 ADR trả lời 3 câu hỏi khác nhau → tách thành 3 ADRs. Rule: đọc xong ADR trong ≤5 phút.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích ADR template (Context → Options → Decision → Consequences) + tại sao cần
- [ ] **Apply:** Viết 1 ADR hoàn chỉnh cho ITO + 1 ADR cho Logistics
- [ ] **Analyze:** Hoàn thành Traceability Chain cho ≥1 Knowledge Object — liên hệ với "Dev G mất 3 ngày vì không có ADR"

---

> 🔗 **Tiếp theo:** ADR ghi lại quyết định cho **người đọc**. Nhưng AI Agent cũng cần đọc knowledge repository. Bài tiếp — *AI Context Design* — sẽ dạy bạn tối ưu Knowledge Objects cho **AI consumption**: frontmatter metadata, embedding-friendly format, và context window management — để AI Agent có thể tự navigate Knowledge Graph và generate code aligned với domain model.
