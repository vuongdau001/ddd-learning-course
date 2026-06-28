---
type: lesson
module: 7
lesson: 2
title: "Aggregates & Aggregate Roots"
duration: "40 phút"
prerequisites: ["module-7/lesson-1"]
---

# Lesson 7.2: Aggregates & Aggregate Roots

## 🎓 Concept — "Không phải mọi Entity đều bình đẳng"

### Vấn đề: Object graph quá phức tạp

```
Resource → Allocation → Project → Contract → Account → ...
             ↓
          Skill → SkillCategory → ...

→ Load 1 Resource kéo theo cả database?
→ Sửa Allocation cần lock Resource + Project?
```

### Aggregate = "Cluster có boundary rõ ràng"

Aggregate là **nhóm Entities + VOs** được xử lý như 1 đơn vị:
- Có **1 Aggregate Root** (entry point duy nhất)
- **Consistency boundary** = mọi thay đổi trong aggregate phải consistent
- Bên ngoài chỉ reference qua **Root's ID**

```
┌─────────── Aggregate ──────────┐
│                                 │
│  Resource (ROOT)                │
│    ├── skills: [Skill VO, ...]  │
│    ├── allocations: [Alloc, .]  │
│    └── benchStatus: VO          │
│                                 │
└─────────────────────────────────┘
        ↑
    Bên ngoài chỉ biết Resource.id
    KHÔNG được access Allocation trực tiếp
```

### Aggregate Root = "Người gác cổng"

```
✅ ĐÚNG:
  resource.allocate(projectId, percentage, dateRange)
  → Resource kiểm tra: tổng allocation ≤ 100%
  → Resource tạo Allocation
  → Invariant LUÔN ĐÚNG

❌ SAI:
  allocation = new Allocation(resource, project, 60%)
  allocationRepo.save(allocation)
  → Ai kiểm tra tổng? Có thể vượt 100%!
```

### Ví dụ — ITO CRM

**Aggregate 1: Resource**
```
Resource (Root)
├── skills: List<Skill>              ← VO
├── allocations: List<Allocation>     ← Entity (có ID riêng)
├── benchStatus: BenchStatus          ← VO
└── Invariants:
    - totalAllocation ≤ 100%
    - bench = (totalAllocation == 0 && duration > 5 days)
```

**Aggregate 2: Opportunity**
```
Opportunity (Root)
├── stages: List<StageTransition>     ← Entity
├── winProbability: Percentage        ← VO
├── contactInfo: ContactInfo          ← VO
├── accountId: AccountId              ← Reference (không chứa Account)
└── Invariants:
    - stage chỉ đi tiến, không lùi (Lead→Qualify→Proposal→Won)
    - winProbability phải update khi đổi stage
```

### Ví dụ — Logistics

**Aggregate 1: Trip**
```
Trip (Root)
├── stops: List<Stop>                 ← Entity
├── vehicle: VehicleId                ← Reference
├── driver: DriverId                  ← Reference
├── route: Route                      ← VO
└── Invariants:
    - tổng weight stops ≤ vehicle capacity
    - thứ tự stops phải hợp lý (theo route)
```

### 4 quy tắc thiết kế Aggregate

| # | Quy tắc | Ví dụ |
|:---:|---|---|
| 1 | **Protect invariants** | Resource: totalAllocation ≤ 100% |
| 2 | **Prefer small aggregates** | Resource KHÔNG chứa Project (chỉ projectId) |
| 3 | **Reference by ID** | Allocation có projectId, không phải Project object |
| 4 | **Eventual consistency giữa aggregates** | Khi Allocation thay đổi → notify Project (event) |

---

## 🏋️ Exercise — Thiết kế Aggregates

### Phần A: ITO — Contract Aggregate (15 phút)

Thiết kế aggregate cho Contract:

```
Contract (Root)
├── _______________        ← ___
├── _______________        ← ___
├── _______________        ← ___
└── Invariants:
    - _______________
    - _______________
```

Câu hỏi: Contract có chứa Account object không? Hay chỉ accountId? Tại sao?

### Phần B: Logistics — Shipment Aggregate (15 phút)

```
Shipment (Root)
├── _______________        ← ___
├── _______________        ← ___
├── _______________        ← ___
└── Invariants:
    - _______________
    - _______________
```

### Phần C: Boundary check (5 phút)

Mỗi aggregate bạn thiết kế, kiểm tra:

| Kiểm tra | Contract | Shipment |
|---|:---:|:---:|
| Có Root rõ ràng? | ☐ | ☐ |
| Invariants rõ ràng? | ☐ | ☐ |
| Reference bằng ID (không object)? | ☐ | ☐ |
| Aggregate đủ nhỏ? | ☐ | ☐ |

---

## 🪞 Reflect

1. **Allocation là Entity bên trong Resource aggregate — tại sao không phải aggregate riêng?** Gợi ý: invariant "tổng ≤ 100%" cần cả Resource lẫn Allocation → phải cùng aggregate.

2. **"Prefer small aggregates" — nhưng nhỏ quá thì sao?** Nếu tách Resource và Allocation thành 2 aggregates → ai bảo vệ invariant?

3. **Eventual consistency giữa aggregates — chấp nhận được khi nào?** Gợi ý: business nói "OK nếu data hơi trễ 5 giây" → eventual OK.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Aggregate, Aggregate Root, Consistency Boundary
- [ ] Thiết kế Contract aggregate (ITO)
- [ ] Thiết kế Shipment aggregate (Logistics)
- [ ] Áp dụng 4 quy tắc thiết kế
- [ ] Trả lời ≥2/3 câu hỏi reflection
