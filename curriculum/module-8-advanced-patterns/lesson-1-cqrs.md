---
type: lesson
module: 8
lesson: 1
title: "CQRS — Command Query Responsibility Segregation"
duration: "35 phút"
prerequisites: ["module-7"]
---

# Lesson 8.1: CQRS — Tách Read và Write

## 🎓 Concept — "Đọc và Ghi là 2 bài toán khác nhau"

### Vấn đề: 1 model cho cả Read và Write

```
Truyền thống: 1 model duy nhất

Resource model:
  - Write: allocate(), updateSkills(), markBench()
  - Read: hiển thị dashboard, report, search

Khi dashboard cần join 5 bảng + aggregate + filter
→ Query phức tạp
→ Ảnh hưởng performance của Write
→ Model bị "kéo" 2 hướng: write cần normalize, read cần denormalize
```

### CQRS = 2 models riêng biệt

```
Command Side (Write):              Query Side (Read):
┌─────────────────────┐            ┌─────────────────────┐
│ Resource Aggregate  │            │ ResourceDashboardView│
│ - allocate()        │            │ - name, skills      │
│ - updateSkills()    │───event──▶│ - totalAllocation   │
│ - markBench()       │            │ - benchDays         │
│ (normalized)        │            │ - projectNames      │
└─────────────────────┘            │ (denormalized, fast) │
                                   └─────────────────────┘
```

**Command Side:** Domain model thuần (Entity, VO, Aggregate) — tối ưu cho business rules.
**Query Side:** Read model — denormalized, tối ưu cho hiển thị, join sẵn.

### Ví dụ — ITO CRM

**Command:** `AllocateResource(resourceId, projectId, 60%)`
```
→ Load Resource aggregate
→ Check invariant: totalAllocation ≤ 100%
→ Create Allocation
→ Save Resource
→ Publish event: ResourceAllocated
```

**Query:** `GET /dashboard/resources?status=available`
```
→ Đọc từ ResourceDashboardView (read model)
→ Đã denormalize sẵn: name, skills, allocation%, bench status
→ Không cần join, không cần aggregate — nhanh!
```

**Sync giữa Write → Read:**
```
ResourceAllocated event
  → Event Handler → Update ResourceDashboardView
  → Eventual consistency (vài ms delay)
```

### Ví dụ — Logistics

**Command:** `AssignDriverToTrip(tripId, driverId)`
**Query:** `GET /tracking/active-trips` → Read từ ActiveTripView (denormalized)

### Khi nào dùng CQRS?

| Tín hiệu | Ví dụ |
|---|---|
| Read và Write models rất khác nhau | Dashboard cần join 5 bảng, Write chỉ cần 1 aggregate |
| Read traffic >> Write traffic | 100 người xem dashboard / 1 người allocate |
| Cần scale read và write độc lập | Read replica cho dashboard, write master cho transactions |
| Cần nhiều read models khác nhau | Dashboard view, Report view, Search view — từ cùng data |

### Khi nào KHÔNG cần CQRS?

```
- CRUD đơn giản (read ≈ write model)
- Team nhỏ, domain đơn giản
- Không có performance issue
→ CQRS thêm complexity, chỉ dùng khi cần
```

---

## 🏋️ Exercise — Thiết kế CQRS

### Phần A: ITO — Opportunity Dashboard (15 phút)

Thiết kế Command model vs Read model:

**Command model (Opportunity Aggregate):**
```
Opportunity:
  - commands: _______________
  - invariants: _______________
```

**Read model (Opportunity Dashboard View):**
```
OpportunityDashboardView:
  - fields: _______________
  - denormalized from: _______________
```

**Events kết nối 2 sides:**
```
1. _______________
2. _______________
```

### Phần B: Logistics — Tracking Dashboard (10 phút)

**Command model:**
```
Trip aggregate: _______________
```

**Read model:**
```
ActiveTripView: _______________
```

---

## 🪞 Reflect

1. **CQRS có BẮT BUỘC Event Sourcing không?** Gợi ý: Không! CQRS = tách model. Event Sourcing = cách lưu data. Có thể dùng riêng.

2. **Eventual consistency giữa Write → Read — user có chấp nhận không?** Gợi ý: "Bạn vừa allocate resource, nhưng dashboard chưa update" — delay 100ms OK? 5s?

3. **Có thể dùng CQRS cho 1 Bounded Context và không dùng cho context khác?** Gợi ý: Có! Core Domain (complex reads) dùng CQRS. Generic (simple CRUD) không cần.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích CQRS bằng ví dụ cụ thể
- [ ] Thiết kế Command model + Read model cho Opportunity
- [ ] Thiết kế Command model + Read model cho Trip
- [ ] Biết khi nào nên/không nên dùng CQRS
