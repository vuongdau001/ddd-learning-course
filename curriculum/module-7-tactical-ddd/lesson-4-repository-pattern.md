---
type: lesson
module: 7
lesson: 4
title: "Repository Pattern"
duration: "30 phút"
prerequisites: ["module-7/lesson-3"]
---

# Lesson 7.4: Repository Pattern

## 🎓 Concept — "Domain không biết database tồn tại"

### Vấn đề: Domain code bị ô nhiễm bởi database

```typescript
// ❌ Domain code phụ thuộc database
class ResourceService {
  async allocate(resourceId: string, projectId: string, pct: number) {
    const conn = await mysql.getConnection();
    const rows = await conn.query("SELECT * FROM resources WHERE id = ?", [resourceId]);
    const resource = this.mapToEntity(rows[0]);
    
    // business logic xen lẫn SQL
    const totalAlloc = await conn.query(
      "SELECT SUM(percentage) FROM allocations WHERE resource_id = ?", [resourceId]
    );
    if (totalAlloc + pct > 100) throw new Error("Over-allocated");
    
    await conn.query("INSERT INTO allocations...");
  }
}
```

### Repository = Interface giữa Domain và Database

```typescript
// ✅ Domain chỉ biết interface
interface ResourceRepository {
  findById(id: ResourceId): Promise<Resource | null>;
  save(resource: Resource): Promise<void>;
  findAvailableWithSkills(skills: Skill[]): Promise<Resource[]>;
}

// Domain code sạch — không có SQL, không có DB
class AllocateResourceUseCase {
  constructor(private resourceRepo: ResourceRepository) {}
  
  async execute(resourceId: ResourceId, projectId: ProjectId, pct: Percentage) {
    const resource = await this.resourceRepo.findById(resourceId);
    if (!resource) throw new ResourceNotFound(resourceId);
    
    resource.allocate(projectId, pct);  // ← pure domain logic
    
    await this.resourceRepo.save(resource);
  }
}
```

### Persistence Ignorance

```
Domain Layer:         Application Layer:       Infrastructure Layer:
                      
ResourceRepository    AllocateResourceUseCase   PostgresResourceRepository
(interface)          (orchestrator)            (implementation)

→ Domain không biết dùng PostgreSQL, MongoDB, hay file system
→ Có thể swap database mà KHÔNG sửa domain code
```

### Quy tắc Repository

| # | Quy tắc | Ví dụ |
|:---:|---|---|
| 1 | **1 Repository per Aggregate** | ResourceRepo, OpportunityRepo — KHÔNG AllocationRepo |
| 2 | **Interface ở Domain layer** | `interface ResourceRepository` |
| 3 | **Implementation ở Infra layer** | `class PostgresResourceRepository implements ResourceRepository` |
| 4 | **Save/Load toàn bộ Aggregate** | `save(resource)` lưu cả allocations, skills |
| 5 | **Query methods = domain language** | `findAvailableWithSkills()`, KHÔNG `findByColumn()` |

### Ví dụ — ITO CRM Repositories

```typescript
// Resource Aggregate Repository
interface ResourceRepository {
  findById(id: ResourceId): Promise<Resource | null>;
  save(resource: Resource): Promise<void>;
  findAvailableWithSkills(skills: Skill[]): Promise<Resource[]>;
  findBenched(threshold: Days): Promise<Resource[]>;
}

// Opportunity Aggregate Repository  
interface OpportunityRepository {
  findById(id: OpportunityId): Promise<Opportunity | null>;
  save(opportunity: Opportunity): Promise<void>;
  findByStage(stage: OpportunityStage): Promise<Opportunity[]>;
  findByAccountId(accountId: AccountId): Promise<Opportunity[]>;
}
```

### Ví dụ — Logistics Repositories

```typescript
interface TripRepository {
  findById(id: TripId): Promise<Trip | null>;
  save(trip: Trip): Promise<void>;
  findActiveByDriver(driverId: DriverId): Promise<Trip[]>;
  findByDateRange(from: Date, to: Date): Promise<Trip[]>;
}

interface ShipmentRepository {
  findById(id: ShipmentId): Promise<Shipment | null>;
  save(shipment: Shipment): Promise<void>;
  findPendingPickup(area: GeoArea): Promise<Shipment[]>;
}
```

### Anti-patterns

```
❌ Generic Repository:
  interface Repository<T> { find(id); save(entity); delete(entity); }
  → Quá generic, mất domain language

❌ Repository cho non-root entity:
  interface AllocationRepository { ... }
  → Allocation nằm trong Resource aggregate, chỉ access qua ResourceRepository

❌ Business logic trong Repository:
  class ResourceRepoImpl {
    findAndAllocate(resourceId, projectId, pct) { ... }
    → Business logic phải ở Domain, không ở Repository
  }
```

---

## 🏋️ Exercise — Thiết kế Repository Interfaces

### Phần A: ITO — Contract Repository (10 phút)

```typescript
interface ContractRepository {
  // Viết ≥4 methods dùng domain language
  // Hint: findById, save, findByAccountId, findExpiring...
}
```

### Phần B: Logistics — Vehicle Repository (10 phút)

```typescript
interface VehicleRepository {
  // Viết ≥4 methods
  // Hint: findById, save, findAvailableInArea, findByCapacity...
}
```

### Phần C: Full stack trace (10 phút)

Vẽ flow từ API → Application → Domain → Repository:

```
API: POST /resources/{id}/allocate
     ↓
Application: AllocateResourceUseCase
     ↓ uses
Domain: resource.allocate(projectId, pct)
     ↓ enforces
Invariant: totalAllocation ≤ 100%
     ↓ persisted via
Repository: resourceRepo.save(resource)
     ↓ implemented by
Infra: PostgresResourceRepository
```

Làm tương tự cho 1 use case của bạn: _______________

---

## 🪞 Reflect

1. **"1 Repository per Aggregate" — nếu cần query across aggregates thì sao?** Gợi ý: dùng Read Model / Query Service riêng, tách khỏi Repository.

2. **Repository nên return Entity hay DTO?** Gợi ý: Repository return Entity (domain object). Nếu cần DTO cho API → Application Service chuyển đổi.

3. **ORM (Sequelize, TypeORM) có phải là Repository?** Gợi ý: ORM = data access tool. Repository = abstraction TRÊN ORM.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Persistence Ignorance
- [ ] Thiết kế Repository cho Contract aggregate
- [ ] Thiết kế Repository cho Vehicle aggregate
- [ ] Vẽ full stack trace cho 1 use case
- [ ] Trả lời ≥2/3 câu hỏi reflection
