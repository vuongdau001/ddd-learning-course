---
type: lesson
module: 7
lesson: 4
title: "Repository Pattern"
duration: "30 phút"
prerequisites: ["module-7/lesson-3"]
narrative_phase: "tactical design"
migration_phase: "Phase 2: Implement persistence abstraction"
business_invariant: "Repository = 1 per Aggregate Root; Interface ở Domain layer; Implementation ở Infra layer; Save/Load toàn bộ Aggregate; Domain KHÔNG biết database tồn tại"
---

# Lesson 7.4: Repository Pattern — "Domain không biết database tồn tại"

## 📍 Context — Bạn đang ở đây

> Lesson 7.1-7.3 xây dựng: Entity/VO → Aggregate → Domain Service. Resource aggregate enforce `totalAllocation ≤ 100%`, ResourceMatchingService tính toán cross-aggregate. Nhưng Resource aggregate ở **memory** — cần lưu vào database. Ai chịu trách nhiệm? Nếu domain code chứa SQL → coupling chết. **Repository** = abstraction layer: Domain nói "save(resource)" — Infra decide save vào Postgres, Mongo, hay file.

## 🔥 Tension — "Domain code bị ô nhiễm bởi SQL"

Sprint 8. Code review. Tuấn phát hiện:

> **Dev A:** *"Tôi implement AllocateResource — hoạt động perfect!"*
>
> **Tuấn:** *"Cho tôi xem code..."*

```typescript
// Dev A's code
class AllocateResourceUseCase {
  async execute(resourceId: string, projectId: string, pct: number) {
    const conn = await mysql.getConnection()
    const rows = await conn.query(
      "SELECT * FROM resources WHERE id = ?", [resourceId]
    )
    const resource = this.mapToEntity(rows[0])

    // Business logic XEN LẪN database queries
    const totalAlloc = await conn.query(
      "SELECT SUM(percentage) FROM allocations WHERE resource_id = ?",
      [resourceId]
    )
    if (totalAlloc + pct > 100) throw new Error("Over-allocated")

    await conn.query(
      "INSERT INTO allocations (resource_id, project_id, percentage) VALUES (?, ?, ?)",
      [resourceId, projectId, pct]
    )
  }
}
```

> **Tuấn:** *"3 vấn đề:*
> 1. *Business logic (`totalAlloc ≤ 100%`) nằm trong SQL — bypass aggregate root!*
> 2. *Domain code phụ thuộc MySQL — đổi sang Postgres = rewrite toàn bộ.*
> 3. *Không test được business logic riêng — test cần DB thật."*

**Minh:**
> *"Domain layer phải 'persistence ignorant' — không biết database tồn tại. Cách làm: Repository pattern. Domain define INTERFACE (what I need), Infrastructure define IMPLEMENTATION (how I get it)."*

> 💭 **Câu hỏi:** Làm sao domain code sạch (no SQL, no DB dependency) mà vẫn lưu/load data? Repository = contract: Domain nói "save this aggregate", Infra làm phần còn lại.

## 🎓 Explanation — Repository Pattern

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Repository = 1 interface per Aggregate Root, nằm ở Domain layer. Implementation nằm ở Infrastructure layer. Save/Load TOÀN BỘ aggregate (Root + children). Domain KHÔNG biết database tồn tại — Persistence Ignorance."*

### Persistence Ignorance — 3 Layer

```
Domain Layer:            Application Layer:          Infrastructure Layer:

ResourceRepository       AllocateResourceUseCase     PostgresResourceRepository
(INTERFACE)              (ORCHESTRATOR)              (IMPLEMENTATION)
- findById()              1. Load aggregate            - SQL queries
- save()                  2. Call domain logic         - ORM mapping
- findAvailable()         3. Save aggregate            - Connection pooling

→ Domain không biết Postgres, MongoDB, hay file system
→ Swap database = chỉ sửa Infrastructure
→ Test business logic = mock Repository interface
```

### Code — Đúng cách

```typescript
// ── DOMAIN LAYER ──
// Interface: Domain chỉ biết WHAT, không biết HOW
interface ResourceRepository {
  findById(id: ResourceId): Promise<Resource | null>
  save(resource: Resource): Promise<void>
  findAvailableWithSkills(skills: Skill[]): Promise<Resource[]>
  findBenched(threshold: Days): Promise<Resource[]>
}

// ── APPLICATION LAYER ──
// Use Case: Orchestrate workflow, KHÔNG chứa business logic
class AllocateResourceUseCase {
  constructor(
    private resourceRepo: ResourceRepository,    // Inject INTERFACE
    private eventBus: EventBus
  ) {}

  async execute(cmd: AllocateResourceCommand): Promise<void> {
    const resource = await this.resourceRepo.findById(cmd.resourceId)
    if (!resource) throw new ResourceNotFoundError(cmd.resourceId)

    resource.allocate(cmd.projectId, cmd.percentage, cmd.dateRange)
    // ↑ Domain logic: AGGREGATE ROOT enforces totalAllocation ≤ 100%

    await this.resourceRepo.save(resource)
    // ↑ Save TOÀN BỘ aggregate (resource + allocations + skills)

    this.eventBus.publish(new ResourceAllocated(resource.id, cmd.projectId))
  }
}

// ── INFRASTRUCTURE LAYER ──
// Implementation: HOW to persist
class PostgresResourceRepository implements ResourceRepository {
  async findById(id: ResourceId): Promise<Resource | null> {
    const row = await this.db.query(
      `SELECT r.*, a.*, s.*
       FROM resources r
       LEFT JOIN allocations a ON a.resource_id = r.id
       LEFT JOIN resource_skills s ON s.resource_id = r.id
       WHERE r.id = $1`, [id.value]
    )
    return row ? this.toDomain(row) : null  // Map DB → Domain object
  }

  async save(resource: Resource): Promise<void> {
    await this.db.transaction(async (tx) => {
      await tx.upsert('resources', this.toRow(resource))
      await tx.deleteWhere('allocations', { resource_id: resource.id.value })
      await tx.insertMany('allocations', resource.allocations.map(this.allocToRow))
      // Save TOÀN BỘ aggregate trong 1 transaction
    })
  }
}
```

### 5 Quy tắc Repository

| # | Quy tắc | Ví dụ | Vi phạm |
|:---:|---|---|---|
| 1 | **1 Repository per Aggregate Root** | ResourceRepo, OpportunityRepo | ❌ AllocationRepo (Allocation = child, not root) |
| 2 | **Interface ở Domain layer** | `interface ResourceRepository` | ❌ Interface ở Infrastructure |
| 3 | **Impl ở Infrastructure layer** | `PostgresResourceRepository` | ❌ SQL trong Domain layer |
| 4 | **Save/Load toàn bộ Aggregate** | `save(resource)` = lưu cả allocations, skills | ❌ Save allocation riêng |
| 5 | **Query = domain language** | `findAvailableWithSkills()`, `findBenched()` | ❌ `findByColumn()`, `findWhere()` |

### ITO CRM Repositories

```typescript
// Resource Aggregate Repository
interface ResourceRepository {
  findById(id: ResourceId): Promise<Resource | null>
  save(resource: Resource): Promise<void>
  findAvailableWithSkills(skills: Skill[]): Promise<Resource[]>    // Domain language!
  findBenched(threshold: Days): Promise<Resource[]>                 // Domain language!
}

// Opportunity Aggregate Repository
interface OpportunityRepository {
  findById(id: OpportunityId): Promise<Opportunity | null>
  save(opportunity: Opportunity): Promise<void>
  findByStage(stage: OpportunityStage): Promise<Opportunity[]>     // Domain language
  findByAccountId(accountId: AccountId): Promise<Opportunity[]>
}

// Contract Aggregate Repository
interface ContractRepository {
  findById(id: ContractId): Promise<Contract | null>
  save(contract: Contract): Promise<void>
  findActiveByAccount(accountId: AccountId): Promise<Contract[]>   // Domain language
  findExpiringSoon(withinDays: Days): Promise<Contract[]>          // Domain language
}
```

### Logistics Repositories

```typescript
// Trip Aggregate Repository
interface TripRepository {
  findById(id: TripId): Promise<Trip | null>
  save(trip: Trip): Promise<void>
  findActiveByDriver(driverId: DriverId): Promise<Trip[]>
  findByDateRange(from: Date, to: Date): Promise<Trip[]>
}

// Shipment Aggregate Repository
interface ShipmentRepository {
  findById(id: ShipmentId): Promise<Shipment | null>
  save(shipment: Shipment): Promise<void>
  findPendingPickup(area: GeoArea): Promise<Shipment[]>            // Domain language
  findByStatus(status: ShipmentStatus): Promise<Shipment[]>
}
```

### Anti-patterns

```
❌ Generic Repository:
  interface Repository<T> {
    find(id: string): T
    save(entity: T): void
    delete(entity: T): void
  }
  → Quá generic → mất domain language
  → findAvailableWithSkills() biến thành findByColumn("skill", ...)

❌ Repository cho non-root entity:
  interface AllocationRepository { ... }
  → Allocation nằm TRONG Resource aggregate
  → Access qua ResourceRepository.save(resource)
  → Vi phạm: bypass Root → invariant broken (110% bug lại!)

❌ Business logic trong Repository:
  class ResourceRepoImpl {
    findAndAllocate(resourceId, projectId, pct) {
      // Query + business logic + save trong 1 method
    }
  }
  → Business logic phải ở Domain (Entity/Aggregate Root)
  → Repository chỉ read/write

❌ Returning DTOs from Repository:
  interface ResourceRepository {
    findById(id): ResourceDTO    // ← DTO instead of Entity
  }
  → Repository return DOMAIN OBJECTS (Entity/Aggregate)
  → DTO conversion ở Application layer
```

### Full Stack Trace

```
API:          POST /resources/{id}/allocate { projectId, percentage, dateRange }
                ↓
Controller:   Extract params, validate format
                ↓
Application:  AllocateResourceUseCase.execute(command)
                ↓ uses ResourceRepository (INTERFACE)
Domain:       resource = repo.findById(id)
              resource.allocate(projectId, pct, range)   ← BUSINESS LOGIC
              → Aggregate Root ENFORCE: totalAlloc ≤ 100%
                ↓
Application:  repo.save(resource)                        ← PERSIST
              eventBus.publish(ResourceAllocated)         ← SIDE EFFECT
                ↓ implementation
Infra:        PostgresResourceRepository
              → Transaction: upsert resource + insert allocation
```

### ⚖️ Trade-offs — Repository Design

| | Repository quá chi tiết | Repository quá generic |
|---|---|---|
| **Vấn đề** | Quá nhiều methods, maintenance burden | Mất domain language, findByColumn() everywhere |
| **Dấu hiệu** | >10 methods trên 1 repository | Chỉ có find/save/delete |
| **Fix** | Extract Read Model cho complex queries | Thêm domain-specific query methods |

---

## 🏋️ Exercise — Thiết kế Repository Interfaces

### Phần A: ITO — Contract Repository (10 phút)

```typescript
interface ContractRepository {
  // Viết ≥4 methods dùng domain language
  // Hint: findById, save, findActiveByAccount, findExpiringSoon...
}
```

### Phần B: Logistics — Vehicle Repository (10 phút)

```typescript
interface VehicleRepository {
  // Viết ≥4 methods dùng domain language
  // Hint: findById, save, findAvailableInArea, findByCapacity...
}
```

### Phần C: Full Stack Trace (10 phút)

Vẽ complete flow từ API → Application → Domain → Repository cho use case:

> **"PM Alpha tạo mới Opportunity từ Lead"**

```
API:          POST /opportunities/from-lead { leadId }
                ↓
Controller:   _______________
                ↓
Application:  _______________UseCase.execute(command)
                ↓
Domain:       lead = ___Repo.findById(leadId)
              opportunity = ___Factory.createFromLead(lead)
                ↓
Application:  ___Repo.save(opportunity)
              eventBus.publish(_______________)
                ↓
Infra:        _______________Repository
```

---

## 🪞 Reflect

1. **"1 Repository per Aggregate" — nếu cần query across aggregates?** → Dùng **Read Model / Query Service** riêng — tách khỏi Repository. Repository = write side (lưu/load aggregate). Query Service = read side (dashboard, reports). Đây là bước đầu tiên hướng tới **CQRS** (Module 8).

2. **Repository nên return Entity hay DTO?** → Repository return **Domain Object** (Entity/Aggregate). Nếu API cần DTO → **Application Service** chuyển đổi. Repository = Domain layer, DTO = Application/Presentation layer.

3. **ORM (TypeORM, Prisma) có phải Repository?** → **Không.** ORM = data access tool. Repository = **abstraction TRÊN ORM.** Repository ẩn ORM details, expose domain language. VD: TypeORM `find({ where: { status: 'active' }})` được wrap thành `findActive()`.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Persistence Ignorance + 5 quy tắc Repository
- [ ] **Apply:** Thiết kế Contract + Vehicle repository interfaces
- [ ] **Analyze:** Vẽ full stack trace cho Opportunity use case — identify layer nào chứa logic gì

---

> 🔗 **Tiếp theo:** Module 7 hoàn tất tactical building blocks: Entity/VO → Aggregate → Domain Service → Repository. Bạn biết cách model domain objects và persist chúng. Nhưng aggregates cần **giao tiếp** — khi Resource allocated → Delivery cần biết. Module 8 — *Domain Events & Application Services* — sẽ trả lời: Events = glue giữa aggregates. How to publish, subscribe, and maintain eventual consistency?
