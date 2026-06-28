---
type: lesson
module: 9
lesson: 3
title: "Organizational Knowledge"
duration: "30 phút"
prerequisites: ["module-9/lesson-2"]
---

# Lesson 9.3: Organizational Knowledge

## 🎓 Concept — "Tri thức tổ chức > tri thức cá nhân"

### Vấn đề: Tribal knowledge

```
"Hỏi anh Minh, anh ấy biết"
"Chị Lan thiết kế cái đó, nhưng chị ấy nghỉ rồi"
"Rule này từ 2 năm trước, tôi chỉ biết nó hoạt động"

→ Tribal knowledge = tri thức chỉ ở trong đầu vài người
→ Người nghỉ = tri thức mất
→ Onboarding mất 6 tháng vì "phải hỏi nhiều người"
```

### Knowledge Objects — Chuẩn hóa mọi loại tri thức

Từ Lesson 6.1, bạn đã biết: mọi DDD artifact là Knowledge Object. Bây giờ mở rộng ra **toàn bộ tổ chức:**

| Loại tri thức | Knowledge Object | Format | Ví dụ |
|---|---|---|---|
| **Domain Knowledge** | Glossary, Domain Map | Markdown | `glossary/resource.md` |
| **Design Decisions** | ADR | Markdown template | `decisions/adr-001.md` |
| **Process Knowledge** | Event Storm, Flow | Diagram/Markdown | `event-storms/*.md` |
| **Meeting Decisions** | Meeting Record (MR) | Markdown template | `meetings/2026-06-28.md` |
| **Policy Knowledge** | Policy Document | Markdown | `policies/allocation-policy.md` |
| **Onboarding Knowledge** | Getting Started Guide | Markdown | `onboarding/README.md` |

### Meeting Record (MR) — Template mới

```markdown
# MR-2026-06-28: Resource Allocation Policy Review

## Attendees
- PM Lead, RM Lead, VP Engineering

## Context
Review allocation policy sau 3 tháng triển khai.

## Decisions Made
1. ✅ Giữ % allocation (không đổi sang Man-Day) → Confirmed ADR-001
2. ✅ Thêm "soft allocation" (tentative 30 ngày trước confirm)
3. ❌ Reject: auto-allocate bằng AI (chưa đủ data)

## Action Items
- [ ] @RM: Update glossary/allocation.md thêm "soft allocation"
- [ ] @Dev: Create ADR-003 cho soft allocation feature
- [ ] @PM: Test soft allocation workflow trong Sprint 12

## References
- [ADR-001: Allocation %](../decisions/adr-001.md)
- [Glossary: Allocation](../glossary/allocation.md)
```

### Policy Document — Template

```markdown
# POL-001: Resource Allocation Policy

## Effective Date
2026-07-01

## Scope
Áp dụng cho tất cả Resource Managers và Project Managers.

## Policy Statements
1. Resource allocation tính bằng % (không Man-Day)
2. Tổng allocation không vượt 100%
3. Soft allocation tồn tại tối đa 30 ngày
4. Resource bench > 10 ngày → escalate to VP

## Exceptions
- Intern: có thể allocate > 100% (training overlap)
- Contract resource: tính theo hours, không %

## Related
- [ADR-001](../decisions/adr-001.md)
- [MR-2026-06-28](../meetings/2026-06-28.md)
- [Glossary: Allocation](../glossary/allocation.md)
```

### Ví dụ — ITO Knowledge Structure

```
docs/
├── domain/
│   ├── glossary/           ← Domain Knowledge
│   ├── domain-map.md
│   └── event-storms/
├── decisions/              ← Design Decisions
│   ├── adr-001.md
│   └── adr-002.md
├── policies/               ← Policy Knowledge
│   └── allocation-policy.md
├── meetings/               ← Meeting Decisions
│   └── 2026-06-28.md
└── onboarding/             ← Onboarding Knowledge
    ├── README.md
    ├── developer-guide.md
    └── domain-overview.md
```

### Ví dụ — Logistics

```
docs/
├── domain/glossary/        ← Shipment, Route, POD
├── decisions/              ← ADR: Route Algorithm, Tracking Tech
├── policies/               ← POL: Safety, Compliance, SLA
├── meetings/               ← Weekly ops review
└── onboarding/             ← Driver onboarding, dispatcher guide
```

---

## 🏋️ Exercise — Organizational Knowledge

### Phần A: Meeting Record (10 phút)

Viết 1 Meeting Record cho ITO:
- Topic: quyết định tích hợp hệ thống mới (VD: chọn CRM platform)
- Phải có Decisions Made + Action Items + References

### Phần B: Policy Document (10 phút)

Viết 1 Policy Document cho ITO hoặc Logistics:
- Chọn topic (VD: "SLA Policy cho Customer Success" hoặc "Safety Policy cho Drivers")
- Phải có Policy Statements + Exceptions + Related

### Phần C: Onboarding Guide (5 phút)

Viết outline cho "Developer Onboarding Guide" — new developer vào ITO project cần đọc gì:

```
1. _______________
2. _______________
3. _______________
4. _______________
5. _______________
```

---

## 🪞 Reflect

1. **Meeting Record có cần chi tiết như Meeting Minutes?** Gợi ý: MR chỉ ghi DECISIONS + ACTIONS, không ghi "ai nói gì." Meetings dài → chỉ giữ output.

2. **Policy Document và ADR khác nhau thế nào?** Gợi ý: ADR = 1 quyết định kỹ thuật cụ thể. Policy = quy tắc kinh doanh áp dụng ongoing.

3. **Tribal knowledge có luôn xấu không?** Gợi ý: không — nhưng CRITICAL knowledge phải được codified.

---

## ✅ Hoàn thành lesson khi
- [ ] Viết 1 Meeting Record
- [ ] Viết 1 Policy Document
- [ ] Viết outline Onboarding Guide
- [ ] Hiểu sự khác nhau giữa ADR, MR, Policy
- [ ] Trả lời ≥2/3 câu hỏi reflection
