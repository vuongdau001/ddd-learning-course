---
type: lesson
module: 8
lesson: 1
title: "CQRS — Command Query Responsibility Segregation"
duration: "35 phút"
prerequisites: ["module-7"]
narrative_phase: "advanced patterns"
migration_phase: "Phase 3: Tách read/write paths khi performance cần"
business_invariant: "CQRS = 2 models riêng: Command (write, domain model) + Query (read, denormalized); Events đồng bộ 2 sides; Eventual consistency giữa Write→Read; Chỉ dùng khi read ≠ write model"
---

# Lesson 8.1: CQRS — "Đọc và Ghi là 2 bài toán khác nhau"

## 📍 Context — Bạn đang ở đây

> Module 7 cho bạn tactical building blocks: Entity/VO → Aggregate → Domain Service → Repository. Resource aggregate enforce invariants, Repository persist aggregates. Nhưng khi dashboard ITO cần hiển thị "resource nào available + skills + bench days + project names" — query phải JOIN 5 bảng từ 3 aggregates. Write model (Aggregate) tối ưu cho invariants, không tối ưu cho hiển thị. Giải pháp: **CQRS** — tách Command (write) và Query (read) thành 2 models riêng.

## 🔥 Tension — "Dashboard chậm vì phải join quá nhiều"

Sprint 9. Hà (PM trưởng) complain:

> **Hà:** *"Resource Dashboard load mất 8 giây. Tôi cần xem: tên resource, skills, allocation hiện tại, tên project đang làm, bench bao lâu. Mỗi lần refresh, hệ thống join Resources + Allocations + Projects + Skills + BenchHistory."*

> **Dev B:** *"Query phức tạp vì write model normalize — Resource table, Allocation table, Project table, Skill table đều tách riêng. Join 5 bảng + GROUP BY + subquery cho bench days = slow."*

**Tuấn:**
> *"Write model tối ưu cho business rules (normalize, invariant). Read model tối ưu cho hiển thị (denormalize, pre-join). Đây là 2 bài toán KHÁC NHAU — đừng ép 1 model làm cả 2. CQRS: Command side giữ Aggregate (7.1-7.4). Query side tạo Read Model riêng — denormalized view, no joins, instant."*

> 💭 **Câu hỏi:** 1 model cho cả read lẫn write → trade-off: normalize (tốt cho write, chậm cho read) hoặc denormalize (nhanh cho read, khó enforce invariant). CQRS giải quyết bằng cách dùng **2 models khác nhau** cho 2 mục đích khác nhau.

## 🎓 Explanation — CQRS

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"CQRS = 2 models: Command side (Domain Aggregate — enforce invariants) + Query side (Read Model — denormalized, fast). Events đồng bộ Write→Read. Eventual consistency acceptable khi business cho phép delay (vài ms). Chỉ dùng CQRS khi read model ≠ write model — CRUD đơn giản thì KHÔNG cần."*

### Architecture

```
Command Side (Write):              Query Side (Read):
┌─────────────────────┐            ┌─────────────────────┐
│ Resource Aggregate  │            │ ResourceDashboardView│
│ - allocate()        │            │ - name              │
│ - updateSkills()    │──event──►  │ - skills[]          │
│ - markBench()       │            │ - totalAllocation   │
│ (normalized)        │            │ - benchDays         │
│ (enforce invariant) │            │ - projectNames[]    │
└─────────────────────┘            │ (denormalized, fast) │
                                   │ (NO joins needed)    │
    WRITE: 1 aggregate             └─────────────────────┘
    ↓                                  ↑
    Repository                     READ: single table scan
    ↓                                  ↑
    PostgreSQL (normalized)        Read DB / Materialized View
```

### Command Side — Write Path

```typescript
// Command = ý định thay đổi state
class AllocateResourceCommand {
  readonly resourceId: ResourceId
  readonly projectId: ProjectId
  readonly percentage: AllocationPercentage
  readonly dateRange: DateRange
}

// Application Service = orchestrate
class AllocateResourceUseCase {
  async execute(cmd: AllocateResourceCommand): Promise<void> {
    const resource = await this.resourceRepo.findById(cmd.resourceId)
    resource.allocate(cmd.projectId, cmd.percentage, cmd.dateRange)
    // ↑ Aggregate Root enforce: totalAllocation ≤ 100%

    await this.resourceRepo.save(resource)
    this.eventBus.publish(new ResourceAllocated(
      resource.id, cmd.projectId, cmd.percentage
    ))
  }
}
```

### Query Side — Read Path

```typescript
// Read Model = denormalized view, tối ưu cho hiển thị
interface ResourceDashboardView {
  resourceId: string
  name: string
  skills: string[]                  // Pre-joined
  totalAllocation: number           // Pre-calculated
  benchDays: number                 // Pre-calculated
  currentProjects: string[]         // Pre-joined project names
  status: "Available" | "Allocated" | "Benched"
}

// Event Handler = sync Write → Read
class ResourceDashboardProjection {
  on(event: ResourceAllocated): void {
    // Update denormalized view — NO complex joins
    await this.readDb.update('resource_dashboard', {
      resourceId: event.resourceId,
      totalAllocation: event.newTotalAllocation,
      currentProjects: [...existing, event.projectName],
      status: "Allocated"
    })
  }

  on(event: ResourceDeallocated): void {
    // Update when allocation removed
  }
}

// Query = instant, no joins
class ResourceDashboardQuery {
  async getAvailableResources(): Promise<ResourceDashboardView[]> {
    return this.readDb.find('resource_dashboard', { status: "Available" })
    // → Single table scan, 5ms instead of 8s join
  }
}
```

### Events kết nối 2 sides

```
Write side                 Events                    Read side
─────────────────────────────────────────────────────────────────
resource.allocate()    →  ResourceAllocated       →  Update dashboard view
resource.endAlloc()    →  ResourceDeallocated     →  Update dashboard view
resource.addSkill()    →  SkillAdded              →  Update dashboard view
resource.markBench()   →  ResourceBenched         →  Update bench report view
```

### Apply cho ITO CRM

| Aggregate | Command side | Events | Read Models |
|---|---|---|---|
| **Resource** | allocate(), endAlloc(), addSkill() | ResourceAllocated, Deallocated, SkillAdded | ResourceDashboardView, UtilizationReportView |
| **Opportunity** | qualify(), submitProposal(), win() | OpportunityQualified, ProposalSubmitted, Won | SalesPipelineView (kanban), RevenueReportView |
| **Contract** | activate(), addLineItem() | ContractActivated, LineItemAdded | ContractListView, RevenueProjectionView |

### Apply cho Logistics

| Aggregate | Command side | Events | Read Models |
|---|---|---|---|
| **Trip** | addStop(), assignDriver() | StopAdded, DriverAssigned | ActiveTripTrackingView, DriverScheduleView |
| **Shipment** | pickup(), deliver() | ShipmentPickedUp, Delivered | ShipmentTrackingView, DeliveryReportView |

### ⚖️ Trade-offs — Khi nào CQRS?

| Dùng CQRS | KHÔNG dùng CQRS |
|---|---|
| Read ≠ Write model (dashboard cần join 5 bảng) | CRUD đơn giản (read ≈ write) |
| Read traffic >> Write (100 view / 1 write) | Read/Write tương đương |
| Cần scale read/write độc lập | Team nhỏ, domain đơn giản |
| Cần nhiều read models (dashboard, report, search) | 1 view đủ |
| **Ví dụ:** ITO Resource Dashboard | **Ví dụ:** Address management CRUD |

---

## 🏋️ Exercise — Thiết kế CQRS

### Phần A: ITO — Opportunity Dashboard (15 phút)

**Command model (Opportunity Aggregate):**
```
Opportunity:
  - commands: _______________
  - invariants: _______________
```

**Read model (Opportunity Dashboard View):**
```
SalesPipelineView:
  - fields: _______________
  - denormalized from: _______________
```

**Events kết nối:**
```
1. _______________  →  Update _______________
2. _______________  →  Update _______________
```

### Phần B: Logistics — Tracking Dashboard (10 phút)

**Command model:** Trip aggregate: _______________
**Read model:** ActiveTripView: _______________
**Events:** _______________

---

## 🪞 Reflect

1. **CQRS có BẮT BUỘC Event Sourcing không?** → **Không!** CQRS = tách models. Event Sourcing = cách lưu data. Có thể CQRS + state-based write (phổ biến). ES + CQRS = combo mạnh nhưng phức tạp.

2. **Eventual consistency Write→Read — user chấp nhận?** → *"Bạn vừa allocate, nhưng dashboard chưa update."* Delay 100ms → OK. Delay 5s → có thể confusing. **Solution:** optimistic UI update + eventual sync. Hỏi business: delay bao lâu acceptable?

3. **Có thể CQRS cho 1 context, không dùng cho context khác?** → **Có!** Core Domain (complex reads: Resource Dashboard) dùng CQRS. Generic Subdomain (simple CRUD: Address management) không cần. **Context-level decision.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích CQRS + phân biệt Command/Query side
- [ ] **Apply:** Thiết kế Command + Read model cho Opportunity + Trip
- [ ] **Analyze:** Giải thích khi nào dùng CQRS, khi nào over-engineering — liên hệ Dashboard 8s load

---

> 🔗 **Tiếp theo:** CQRS tách Read/Write — nhưng Write side vẫn lưu STATE (latest snapshot). Có cách khác: lưu **EVENTS** (lịch sử mọi thay đổi). Bài tiếp — *Event Sourcing* — sẽ trả lời: tại sao lưu events thay vì state? Khi nào cần audit trail? Và làm sao rebuild state từ event stream?
