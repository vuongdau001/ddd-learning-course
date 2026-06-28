---
type: lesson
module: 6
lesson: 2
title: "ADR và Traceability"
duration: "30 phút"
prerequisites: ["module-6/lesson-1"]
---

# Lesson 6.2: ADR và Traceability

## 🎓 Concept — "Ghi nhớ TẠI SAO, không chỉ CÁI GÌ"

### Vấn đề: "Tại sao code viết thế này?"

```
6 tháng sau, dev mới vào hỏi:
"Tại sao Allocation dùng % thay vì Man-Day?"
"Tại sao Resource Matching dùng AI thay vì rule-based?"
"Tại sao Customer Success là Core chứ không phải Supporting?"

Không ai nhớ. Người quyết định đã nghỉ.
→ Dev mới đoán → đoán sai → refactor sai → bug
```

### Architecture Decision Record (ADR)

ADR = tài liệu **ngắn gọn** ghi lại 1 quyết định kiến trúc: **bối cảnh, lựa chọn, quyết định, hệ quả.**

### Template ADR

```markdown
# ADR-001: [Tên quyết định]

## Status
Accepted / Proposed / Deprecated / Superseded by ADR-XXX

## Date
YYYY-MM-DD

## Context
[Tình huống gì dẫn đến quyết định này?]

## Decision Drivers
- [Yếu tố 1]
- [Yếu tố 2]

## Options Considered
1. [Lựa chọn A] — ưu/nhược
2. [Lựa chọn B] — ưu/nhược
3. [Lựa chọn C] — ưu/nhược

## Decision
Chọn [Option X] vì [lý do].

## Consequences
### Positive
- [Hệ quả tốt]
### Negative
- [Hệ quả xấu / tradeoff chấp nhận]

## References
- [Link đến glossary, event storm, meeting notes]
```

### Ví dụ ADR — ITO

```markdown
# ADR-001: Allocation sử dụng % thay vì Man-Day

## Status
Accepted

## Date
2026-06-25

## Context
Allocation là concept dùng xuyên suốt nhiều Bounded Contexts 
(Resource, Delivery, Finance). Cần chọn đơn vị đo thống nhất.

## Decision Drivers
- Resource có thể allocate cho nhiều project cùng lúc (partial)
- Finance cần convert ra tiền (billing)
- PM cần biết "resource này bận bao nhiêu"

## Options Considered
1. **% Time** — "Resource A: 60% cho Project X, 40% cho Project Y"
   - ✅ Trực quan, dễ validate (tổng ≤ 100%)
   - ✅ Cross-context dễ dùng
   - ❌ Không chính xác giờ (60% ≈ bao nhiêu giờ/tuần?)

2. **Man-Day** — "Resource A: 3 Man-Day/tuần cho Project X"
   - ✅ Chính xác giờ
   - ❌ Phải biết "1 tuần = bao nhiêu Man-Day?" (4? 5? 5.5?)
   - ❌ Part-time, nghỉ phép → Man-Day thay đổi → phức tạp

3. **Hours** — "Resource A: 24 giờ/tuần cho Project X"
   - ✅ Rất chính xác
   - ❌ Quá granular, over-engineering cho CRM
   - ❌ Tracking giờ = micromanagement → resource ghét

## Decision
Chọn **% Time** vì:
1. Trực quan nhất cho cross-context communication
2. Validation đơn giản (sum ≤ 100%)
3. Convert sang hours khi cần: hours = % × available_hours

## Consequences
### Positive
- Business rule đơn giản: if totalAllocation > 100% → reject
- Mọi context dùng cùng đơn vị

### Negative
- Mất precision: "60%" không nói rõ bao nhiêu giờ
- Cần convention: "available_hours = 40 giờ/tuần" (trừ nghỉ phép)

## References
- [Glossary: Allocation](../domain/glossary/allocation.md)
- [Glossary: Utilization](../domain/glossary/utilization.md)
```

### Ví dụ ADR — Logistics

```markdown
# ADR-001: Route Optimization dùng AI-Augmented thay vì Pure OR

## Status
Accepted

## Context
Route Optimization là Core Domain. Cần quyết định approach.

## Options Considered
1. Pure Operations Research (Linear Programming)
2. AI-only (ML predict optimal routes)
3. Hybrid: Hard constraints (OR) + Soft optimization (AI)

## Decision
Chọn Hybrid vì:
- Hard constraints (capacity, time windows) PHẢI deterministic
- Soft optimization (minimize distance) AI làm tốt hơn
- Team có OR experience nhưng không có ML team → AI-Augment = feasible

## Consequences
### Positive
- Deterministic cho safety-critical constraints
- AI improves over time with data

### Negative
- 2 systems to maintain (OR engine + AI model)
- AI output needs human review initially
```

### Traceability — Kết nối ADR với DDD artifacts

```
Glossary entry (resource.md)
  → references ADR-001 (allocation unit)
  → referenced by Event Storm (ResourceAllocated event)
  → informs Code (Resource aggregate, allocation ≤ 100% invariant)

ADR-002 (AI-augmented matching)
  → changes Event Storm (thêm MatchingResourcesFound event)
  → references Domain Priority (Resource Mgmt = Core)
  → informs Code (AI service integration)
```

---

## 🏋️ Exercise — Viết ADR

### Phần A: Viết ADR cho ITO (15 phút)

Chọn 1 quyết định từ quá trình học (VD: "Customer Success là Core Domain") và viết ADR đầy đủ:

```markdown
# ADR-___: _______________

## Status

## Date

## Context

## Decision Drivers

## Options Considered

## Decision

## Consequences
### Positive
### Negative

## References
```

### Phần B: Viết ADR cho Logistics (10 phút)

Viết ADR cho: "Chọn tech stack cho Real-time Tracking (WebSocket vs Server-Sent Events vs Polling)":

```markdown
# ADR-001: _______________
(viết đầy đủ)
```

### Phần C: Traceability Chain (15 phút)

Chọn 1 Knowledge Object bạn đã tạo (VD: Glossary entry "Allocation") và trace xuyên suốt các DDD layers:

| Layer | Loại Knowledge Object | Ví dụ với "Allocation" | File |
|---|---|---|---|
| **Business Term** | Glossary entry | Allocation = % time phân bổ cho project | `glossary/allocation.md` |
| **Business Rule** | Invariant | total_allocation ≤ 100% | `glossary/allocation.md#invariants` |
| **Domain Event** | Event Storm | ResourceAllocated, AllocationRejected | `event-storms/resource-management.md` |
| **Context** | Context Map | Resource Management (Core) | `context-map.md` |
| **Decision** | ADR | Dùng % thay vì Man-Day (ADR-001) | `decisions/adr-001.md` |

**Bây giờ, làm tương tự cho 1 term khác:**

| Layer | Loại Knowledge Object | Term của bạn: ___________ | File |
|---|---|---|---|
| Business Term | Glossary entry | | |
| Business Rule | Invariant | | |
| Domain Event | Event Storm | | |
| Context | Context Map | | |
| Decision | ADR | | |

> **Tại sao exercise này quan trọng?** Khi AI Agent nhận được câu hỏi "Generate API for Allocation", nó không chỉ đọc 1 file — nó traverse toàn bộ chain này. Traceability Chain chính là thứ biến "đống files" thành "Knowledge Graph" mà AI có thể reasoning.

---

## 🪞 Reflect

1. **ADR có "hết hạn" không?** Khi nào cần viết "Superseded by ADR-XXX"?

2. **Ai nên viết ADR — Architect? Developer? PM?** Gợi ý: người quyết định viết, team review.

3. **ADR quá dài (>2 trang) có tốt không?** Gợi ý: ADR nên đọc xong trong 5 phút. Nếu dài hơn → tách thành nhiều ADRs.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích ADR là gì và tại sao cần
- [ ] Viết 1 ADR hoàn chỉnh cho ITO
- [ ] Viết 1 ADR hoàn chỉnh cho Logistics
- [ ] Hiểu Traceability giữa ADR ↔ Glossary ↔ Event Storm
- [ ] Hoàn thành Traceability Chain cho ≥1 Knowledge Object

