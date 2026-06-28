---
type: practice
module: 4
title: "Event Storm Output"
case_study: "ITO CRM"
---

# Event Storm — Hướng dẫn & Template

> Hướng dẫn: Sau Module 4, bạn sẽ có Event Storm cho Core Domain. Dùng format dưới đây.

---

## Quy ước màu (Color Convention)

| Màu | Loại | Ví dụ |
|:---:|---|---|
| 🟠 | **Domain Event** (đã xảy ra) | ResourceAllocated |
| 🔵 | **Command** (hành động trigger) | AllocateResource |
| 🟣 | **Policy/Rule** (automation) | "Khi bench > 5 ngày → thông báo PM" |
| 🟡 | **Actor** (ai trigger) | Resource Manager |
| 🔴 | **Hot Spot** (chưa rõ/tranh cãi) | "Resource từ chối allocation?" |
| 🟢 | **Read Model** (UI hiển thị) | Allocation Dashboard |

---

## Template Event Storm — ITO Resource Management

> Core Domain — vẽ flow chính từ khi có yêu cầu staffing đến khi resource hoàn thành dự án.

### Timeline (trái → phải)

```
🟡 Project Manager
    ↓
🔵 RequestResource
    ↓
🟠 ResourceRequested
    ↓
🟣 Policy: "Auto-search matching skills"
    ↓
🟠 MatchingResourcesFound
    ↓
🟡 Resource Manager
    ↓
🔵 AllocateResource
    ↓
🟣 Rule: "total_allocation ≤ 100%"
    ↓
🟠 ResourceAllocated / 🔴 AllocationRejected
    ↓
🟣 Policy: "Khi allocation = 0% > 5 ngày → Bench"
    ↓
🟠 ResourceBenched
```

### Hot Spots (vấn đề chưa giải quyết)

| # | Hot Spot | Câu hỏi | Trạng thái |
|:---:|---|---|---|
| 1 | Resource từ chối | Resource có quyền reject allocation? | ❓ Chưa rõ |
| 2 | Partial allocation | 60% project A + 50% project B = 110%? | ❓ Cần rule |
| 3 | Skill mismatch | Match 80% skill có chấp nhận được không? | ❓ Cần threshold |

---

## Bài làm của bạn — Event Storm cho _______________

### Timeline

```
🟡 _______________
    ↓
🔵 _______________
    ↓
🟠 _______________
    ↓
(tiếp tục...)
```

### Hot Spots

| # | Hot Spot | Câu hỏi | Trạng thái |
|:---:|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |

---

### Checklist tự kiểm tra

- [ ] Có ≥5 Domain Events
- [ ] Mỗi Event có Command trigger
- [ ] Có ≥2 Policies/Rules
- [ ] Có ≥2 Hot Spots được ghi nhận
- [ ] Events đặt tên PascalCase quá khứ (ResourceAllocated, không phải AllocateResource)
