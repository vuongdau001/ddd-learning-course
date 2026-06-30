---
type: lesson
module: 7
lesson: 2
title: "Aggregates & Aggregate Roots"
duration: "40 phút"
prerequisites: ["module-7/lesson-1"]
narrative_phase: "tactical design"
migration_phase: "Phase 2: Xác định aggregate boundaries"
business_invariant: "Aggregate = cluster Entities + VOs với consistency boundary; Root = entry point duy nhất; Reference by ID; Invariants enforce bên trong boundary"
---

# Lesson 7.2: Aggregates & Aggregate Roots — "Không phải mọi Entity đều bình đẳng"

## 📍 Context — Bạn đang ở đây

> Lesson 7.1 cho bạn Entity (có identity) và Value Object (chỉ value). Bạn biết Resource = Entity, Skill = VO, Allocation = Entity, Money = VO. Nhưng Entities + VOs không sống đơn lẻ — chúng tạo thành **nhóm có boundary**. Câu hỏi: `Allocation` thuộc nhóm nào? Ai gác cổng để đảm bảo `totalAllocation ≤ 100%`? Đó là Aggregate.

## 🔥 Tension — "Allocation vượt 100% mà không ai biết"

Sprint 6. Dev A deploy Resource Context. Ngày đầu:

> **Dev A:** *"Tôi tạo `AllocationRepository` riêng. Khi cần allocate resource, tôi gọi thẳng: `allocationRepo.save(new Allocation(resourceId, projectId, 60%))`. OK?"*

Ngày 2. PM Alpha allocate Resource R001:
- Allocation 1: R001 → Project X = 60%
- Allocation 2: R001 → Project Y = 50%
- **Tổng = 110%!**

> **PM Alpha:** *"Hệ thống cho tôi allocate 110%. Resource R001 không thể làm 110% thời gian!"*

> **Dev A:** *"Nhưng tôi validate mỗi allocation riêng: 60% ≤ 100% ✅, 50% ≤ 100% ✅. Tôi không kiểm tra TỔNG."*

**Tuấn:**
> *"Vấn đề: Allocation sống riêng lẻ, không ai kiểm tra tổng. Invariant 'totalAllocation ≤ 100%' cần context — cần BIẾT tất cả allocations của 1 resource. Nghĩa là Resource và các Allocations phải nằm CÙNG 1 nhóm — và nhóm đó có 1 người gác cổng kiểm tra invariant."*

> 💭 **Câu hỏi:** Allocation sống riêng → invariant bị vi phạm. Allocation thuộc Resource → Resource kiểm tra tổng trước khi accept. Cách gom Entity + VO thành nhóm có boundary gọi là **Aggregate**. Người gác cổng = **Aggregate Root**.

## 🎓 Explanation — Aggregate & Aggregate Root

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Aggregate = cluster Entities + VOs có consistency boundary rõ ràng. Aggregate Root = entry point duy nhất — mọi thay đổi đi qua Root. Reference giữa aggregates = ID only (không nested objects). Invariants phải enforce bên trong aggregate boundary — nếu invariant cần 2 objects → chúng cùng aggregate."*

### Object Graph vấn đề

```
KHÔNG CÓ Aggregate:
Resource → Allocation → Project → Contract → Account → ...
             ↓
          Skill → SkillCategory → ...

→ Load 1 Resource kéo theo cả database?
→ Sửa Allocation cần lock Resource + Project?
→ Invariant "totalAllocation ≤ 100%" ai kiểm tra?
```

### Aggregate = "Cluster có boundary rõ ràng"

```
┌─────────── Aggregate ──────────┐
│                                 │
│  Resource (ROOT)                │  ← Entry point duy nhất
│    ├── skills: [Skill VO, ...]  │  ← VOs trong aggregate
│    ├── allocations: [Alloc, .]  │  ← Entities trong aggregate
│    └── benchStatus: VO          │  ← VO trong aggregate
│                                 │
│  INVARIANT: totalAllocation ≤ 100% ← Enforce bên trong
│  INVARIANT: bench = (alloc == 0  │
│              && duration > 5d)   │
└─────────────────────────────────┘
        ↑
    Bên ngoài chỉ biết Resource.id
    KHÔNG được access Allocation trực tiếp
```

### Aggregate Root = "Người gác cổng"

```
✅ ĐÚNG — Đi qua Root:
  resource.allocate(projectId, 60%, dateRange)
  → Resource KIỂM TRA: tổng hiện tại + 60% ≤ 100%?
  → Nếu OK → tạo Allocation bên trong
  → Invariant LUÔN ĐÚNG

❌ SAI — Bypass Root:
  allocation = new Allocation(resourceId, projectId, 60%)
  allocationRepo.save(allocation)
  → Ai kiểm tra tổng? KHÔNG AI → 110% bug!
```

### 4 Quy tắc thiết kế Aggregate

| # | Quy tắc | Giải thích | ITO Ví dụ |
|:---:|---|---|---|
| 1 | **Protect invariants** | Invariant cần 2+ objects → cùng aggregate | Resource + Allocation → cùng aggregate vì `sum ≤ 100%` |
| 2 | **Prefer small** | Aggregate nhỏ → load nhanh, lock ít | Resource KHÔNG chứa Project object |
| 3 | **Reference by ID** | Giữa aggregates = ID, không nested object | Allocation chứa `projectId`, không `Project` |
| 4 | **Eventual consistency** | Giữa aggregates = eventually consistent (events) | Allocation changed → emit event → Delivery update |

### Apply cho ITO CRM

**Aggregate 1: Resource**
```
Resource (Root)
├── skills: List<Skill>              ← VO (list, replaceable)
├── allocations: List<Allocation>     ← Entity (có ID, lifecycle)
│   ├── id: AllocationId
│   ├── projectId: ProjectId          ← REFERENCE BY ID (quy tắc 3)
│   ├── percentage: AllocationPercentage ← VO
│   └── dateRange: DateRange          ← VO
├── benchStatus: BenchStatus          ← VO
└── Invariants:
    - totalAllocation ≤ 100%                    ← quy tắc 1
    - bench = (totalAllocation == 0 && noAlloc > 5 days)
```

**Aggregate 2: Opportunity**
```
Opportunity (Root)
├── stages: List<StageTransition>     ← Entity (có timestamp)
├── winProbability: Percentage        ← VO
├── estimatedValue: Money             ← VO
├── accountId: AccountId              ← REFERENCE BY ID
├── resourceRequests: List<ResourceRequest> ← VO
└── Invariants:
    - stage chỉ đi tiến (Lead→Qualify→Proposal→Won/Lost)
    - winProbability phải update khi đổi stage
    - estimatedValue > 0
```

**Aggregate 3: Contract**
```
Contract (Root)
├── lineItems: List<ContractLineItem> ← Entity
│   ├── description: string
│   ├── amount: Money                 ← VO
│   └── billingCycle: BillingCycle    ← VO
├── totalValue: Money                 ← VO
├── accountId: AccountId              ← REFERENCE BY ID
├── status: ContractStatus            ← VO (enum)
└── Invariants:
    - totalValue = sum(lineItems.amount)
    - status transitions: Draft → Active → Completed/Terminated
```

### Apply cho Logistics

**Aggregate 1: Trip**
```
Trip (Root)
├── stops: List<Stop>                 ← Entity (ordered, có sequence)
│   ├── id: StopId
│   ├── location: GeoLocation         ← VO
│   ├── timeWindow: TimeWindow        ← VO
│   └── cargo: Weight                 ← VO
├── vehicleId: VehicleId              ← REFERENCE BY ID
├── driverId: DriverId                ← REFERENCE BY ID
├── route: OptimizedRoute             ← VO (calculated)
└── Invariants:
    - totalWeight(stops) ≤ vehicle.capacity
    - stops thứ tự hợp lý (theo route)
    - driver certified cho vehicle type
```

**Aggregate 2: Shipment**
```
Shipment (Root)
├── packages: List<Package>           ← Entity
├── origin: GeoLocation               ← VO
├── destination: GeoLocation          ← VO
├── status: ShipmentStatus            ← VO
├── tripId: TripId                    ← REFERENCE BY ID (eventual consistency)
└── Invariants:
    - status transitions: Created → Picked → InTransit → Delivered
    - at least 1 package
```

### Code Implementation — Resource Aggregate

```typescript
class Resource {                              // AGGREGATE ROOT
  readonly id: ResourceId
  private skills: Skill[] = []                // VOs
  private allocations: Allocation[] = []      // Entities INSIDE aggregate
  private benchStatus: BenchStatus            // VO

  allocate(projectId: ProjectId, pct: AllocationPercentage, range: DateRange): Allocation {
    // INVARIANT CHECK — Root enforces
    const currentTotal = this.allocations
      .filter(a => a.overlaps(range))
      .reduce((sum, a) => sum + a.percentage.value, 0)

    if (currentTotal + pct.value > 100) {
      throw new OverAllocationError(this.id, currentTotal, pct.value)
    }

    const allocation = new Allocation(
      AllocationId.generate(),
      projectId,
      pct,
      range
    )
    this.allocations.push(allocation)
    this.updateBenchStatus()
    return allocation  // → caller can emit ResourceAllocated event
  }

  endAllocation(allocationId: AllocationId): void {
    const allocation = this.allocations.find(a => a.id === allocationId)
    if (!allocation) throw new AllocationNotFoundError(allocationId)
    allocation.end()
    this.updateBenchStatus()
  }

  private updateBenchStatus(): void {
    const hasActiveAllocation = this.allocations.some(a => a.isActive())
    this.benchStatus = hasActiveAllocation
      ? BenchStatus.allocated()
      : BenchStatus.benched(new Date())
  }
}

// Allocation = ENTITY inside aggregate (NOT accessible directly)
class Allocation {
  readonly id: AllocationId
  readonly projectId: ProjectId      // Reference by ID, NOT Project object
  readonly percentage: AllocationPercentage  // VO
  readonly dateRange: DateRange       // VO
  private ended: boolean = false

  end(): void { this.ended = true }
  isActive(): boolean { return !this.ended && this.dateRange.contains(new Date()) }
  overlaps(range: DateRange): boolean { return this.isActive() && this.dateRange.overlaps(range) }
}
```

### ⚖️ Trade-offs — Aggregate Size

| | Aggregate quá lớn | Aggregate quá nhỏ |
|---|---|---|
| **Vấn đề** | Load chậm, lock nhiều, concurrency conflict | Invariant bị vi phạm (110% bug) |
| **Dấu hiệu** | "Sửa 1 field → lock cả bảng" | "2 thay đổi đồng thời → inconsistent" |
| **ITO ví dụ** | Resource chứa cả Project + Contract | Allocation tách riêng khỏi Resource |
| **Rule** | Aggregate chỉ chứa objects cần cho invariant | Nếu invariant cần 2 objects → cùng aggregate |

---

## 🏋️ Exercise — Thiết kế Aggregates

### Phần A: ITO — Contract Aggregate (15 phút)

```
Contract (Root)
├── _______________        ← ___ (Entity/VO?)
├── _______________        ← ___
├── _______________        ← ___  (reference by ID)
└── Invariants:
    - _______________
    - _______________
```

**Câu hỏi quan trọng:** Contract có chứa Account object không? Hay chỉ `accountId`? → Tại sao? (hint: quy tắc 3)

### Phần B: Logistics — Shipment Aggregate (15 phút)

```
Shipment (Root)
├── _______________        ← ___
├── _______________        ← ___
├── _______________        ← ___  (reference by ID)
└── Invariants:
    - _______________
    - _______________
```

### Phần C: Boundary Check (5 phút)

| Kiểm tra | Contract | Shipment |
|---|:---:|:---:|
| Có Root rõ ràng? | ☐ | ☐ |
| Invariants rõ ràng? | ☐ | ☐ |
| Reference bằng ID (không object)? | ☐ | ☐ |
| Aggregate đủ nhỏ? (chỉ chứa cần cho invariant) | ☐ | ☐ |

---

## 🪞 Reflect

1. **Allocation là Entity bên trong Resource aggregate — tại sao không phải aggregate riêng?** → Invariant `totalAllocation ≤ 100%` cần CẢ Resource lẫn tất cả Allocations. Nếu tách → không ai kiểm tra tổng → 110% bug (Dev A scenario). **Invariant quyết định boundary.**

2. **"Prefer small" — nhưng nhỏ quá thì sao?** → Nếu tách Resource và Allocation → mất invariant. **Rule:** tách cho đến khi invariant bị phá. Nếu 2 objects cần consistent → giữ cùng aggregate. Nếu eventual consistency OK → tách.

3. **Eventual consistency giữa aggregates — chấp nhận khi nào?** → Khi business nói *"OK nếu data hơi trễ vài giây."* VD: khi Resource allocated → Delivery dashboard update trễ 5s → chấp nhận được. Nhưng totalAllocation > 100% → KHÔNG chấp nhận → strong consistency → cùng aggregate.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Aggregate, Root, Consistency Boundary + 4 quy tắc thiết kế
- [ ] **Apply:** Thiết kế Contract aggregate (ITO) + Shipment aggregate (Logistics)
- [ ] **Analyze:** Giải thích tại sao Allocation PHẢI ở trong Resource aggregate — liên hệ với "110% bug"

---

> 🔗 **Tiếp theo:** Aggregate Root enforce invariants — nhưng có business logic KHÔNG thuộc 1 aggregate nào. VD: "Match resources to project based on skills" — logic này nằm ở Resource aggregate? Project aggregate? Cả hai? Bài tiếp — *Domain Services & Factories* — sẽ trả lời: khi nào logic vượt aggregate → Domain Service? Và Factory pattern giúp tạo complex aggregates thế nào?
