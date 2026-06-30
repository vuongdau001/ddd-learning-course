---
type: lesson
module: 7
lesson: 3
title: "Domain Services & Factories"
duration: "30 phút"
prerequisites: ["module-7/lesson-2"]
narrative_phase: "tactical design"
migration_phase: "Phase 2: Extract cross-aggregate logic"
business_invariant: "Domain Service = stateless business logic cần multi-aggregate data; Factory = complex object creation; Domain Service ≠ Application Service"
---

# Lesson 7.3: Domain Services & Factories — "Khi logic không thuộc Entity nào"

## 📍 Context — Bạn đang ở đây

> Lesson 7.2 cho bạn Aggregate: Resource (Root) chứa Allocations, enforce `totalAllocation ≤ 100%`. Root = gác cổng, invariant = law. Nhưng có business logic **không thuộc 1 aggregate nào**: "Match resource cho project dựa trên skills" — logic này cần data từ Resource aggregate LẪN Project requirements. Đặt ở Resource? Không, Resource không biết Project. Đặt ở Project? Không, Project không biết Resource availability.

## 🔥 Tension — "Matching logic ở đâu?"

Sprint 7. Tuấn implement feature matching:

> **Tuấn:** *"Hà cần tính năng 'auto-suggest resources for project'. Tôi cần scan tất cả Resources (skills, availability) + Project requirements (required skills, timeline, budget) + AI scoring. Logic này đặt ở đâu?"*
>
> **Dev A:** *"Đặt trong Resource entity? `resource.matchForProject(project)`?"*
>
> **Tuấn:** *"Sai. Resource entity không nên biết về Project structure. Và matching cần SCAN TẤT CẢ resources — 1 resource instance không có context của các resources khác."*

**Dev B:** *"Vậy đặt trong Application Service?"*

**Tuấn:** *"Cũng sai. Matching = business logic thuần túy (scoring algorithm, skill overlap calculation). Application Service chỉ ĐIỀU PHỐI workflow — load data, gọi logic, save kết quả. Business logic phải ở Domain layer."*

> 💭 **Câu hỏi:** Logic cần multi-aggregate data, stateless, nhưng thuộc domain (business nói về "matching", "qualification", "utilization calculation"). Đó chính là **Domain Service** — concept thứ 3 trong tactical design.

## 🎓 Explanation — Domain Service & Factory

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Domain Service = stateless business logic cần data từ nhiều aggregates — nó sống ở Domain layer (KHÔNG phải Application layer). Factory = tạo complex Aggregate khi constructor quá phức tạp. Domain Service ≠ Application Service — Application Service ĐIỀU PHỐI, Domain Service TÍNH TOÁN."*

### Domain Service — Logic liên aggregate

```typescript
// ❌ SAI: Entity biết quá nhiều về aggregate khác
class Resource {
  matchForProject(project: Project): MatchScore { ... }
  // → Resource coupling với Project, vi phạm aggregate boundary
}

// ❌ SAI: Business logic trong Application Service
class AllocateResourceUseCase {
  execute() {
    // ... SQL queries, scoring calculation, ranking ...
    // → Application Service quá nặng, domain logic bị leak
  }
}

// ✅ ĐÚNG: Domain Service kết nối multi-aggregate data
class ResourceMatchingService {
  findBestMatches(
    requirements: ProjectRequirements,
    availableResources: List<Resource>
  ): List<MatchResult> {
    return availableResources
      .map(r => this.score(r, requirements))
      .filter(m => m.score > 0)
      .sort((a, b) => b.score - a.score)
  }

  private score(resource: Resource, requirements: ProjectRequirements): MatchResult {
    const skillOverlap = this.calculateSkillOverlap(resource.skills, requirements.requiredSkills)
    const availabilityScore = resource.getAvailablePercentage() >= requirements.minPercentage ? 1 : 0
    const seniorityMatch = resource.matchesSeniority(requirements.minLevel) ? 1 : 0
    return new MatchResult(resource.id, skillOverlap * 0.5 + availabilityScore * 0.3 + seniorityMatch * 0.2)
  }
}
```

### 3 Dấu hiệu cần Domain Service

| Dấu hiệu | Câu hỏi | ITO Ví dụ |
|---|---|---|
| **Multi-aggregate** | Logic cần data từ ≥2 aggregates? | Matching: cần Resource + ProjectRequirements |
| **Stateless** | Logic không giữ state riêng — chỉ input → output? | Matching: input (resources, requirements) → output (ranked list) |
| **Domain concept** | Business nói về nó, có tên trong Glossary? | "Resource Matching" = domain concept, Hà nói về nó |

### ITO Domain Services

| Domain Service | Input | Output | Tại sao Domain Service? |
|---|---|---|---|
| `ResourceMatchingService` | ProjectRequirements + List\<Resource\> | List\<MatchResult\> | Cần cả Resource lẫn Project data |
| `UtilizationCalculator` | List\<Resource\> + DateRange | UtilizationReport | Tổng hợp từ nhiều Resource aggregates |
| `OpportunityQualifier` | Opportunity + Account + MarketData | QualificationResult | Cần cross-aggregate + external data |
| `BillingCalculator` | Contract + List\<Allocation\> | Invoice | Cross-aggregate: Contract × Allocations |

### Logistics Domain Services

| Domain Service | Input | Output | Tại sao Domain Service? |
|---|---|---|---|
| `RouteOptimizer` | List\<Stop\> + VehicleCapacity + TrafficData | OptimizedRoute | Multi-aggregate + external data |
| `CapacityPlanner` | List\<Shipment\> + Vehicle | CapacityResult | Cross-aggregate: Shipment × Vehicle |
| `ETACalculator` | Route + CurrentPosition + TrafficData | ETA | Cần Route + real-time data |

### Domain Service ≠ Application Service

```
Domain Service:                     Application Service:
├── Business logic (TÍNH TOÁN)       ├── Workflow (ĐIỀU PHỐI)
├── Không biết DB, API, UI           ├── Biết Repository, Transaction
├── Dùng domain language             ├── Orchestrate: load → call → save
├── Stateless                        ├── Coordinate side effects
└── Sống ở Domain Layer              └── Sống ở Application Layer

VD:
┌──────────────────────────────────────────────────┐
│  Application Layer                                │
│                                                   │
│  AllocateResourceUseCase:                         │
│    1. resource = resourceRepo.findById(id)        │ ← load
│    2. resource.allocate(projectId, pct)            │ ← DOMAIN logic
│    3. resourceRepo.save(resource)                  │ ← save
│    4. eventBus.publish(ResourceAllocated)           │ ← side effect
│                                                   │
│  FindBestMatchUseCase:                             │
│    1. resources = resourceRepo.findAvailable()     │ ← load
│    2. matches = matchingService.findBest(req, res) │ ← DOMAIN SERVICE
│    3. return matches                               │ ← return (no save)
└──────────────────────────────────────────────────┘
```

### Factory — Tạo complex Aggregate

Khi constructor quá phức tạp → Factory ẩn logic tạo:

```typescript
// ❌ Constructor quá phức tạp — caller phải biết quá nhiều
const opportunity = new Opportunity(
  OpportunityId.generate(),
  lead.accountId,
  lead.contactInfo,
  StageTransition.initial(),
  WinProbability.fromLeadScore(lead.score),
  Money.zero("VND"),
  ResourceRequest.fromLeadRequirements(lead.requirements),
  new Date()
)

// ✅ Factory ẩn logic tạo — express domain intent
class OpportunityFactory {
  static createFromLead(lead: Lead): Opportunity {
    return new Opportunity(
      OpportunityId.generate(),
      lead.accountId,
      lead.contactInfo,
      StageTransition.initial(),                          // Start at "Qualification"
      WinProbability.fromLeadScore(lead.score),           // Convert lead score
      Money.zero(lead.currency),                          // No value yet
      ResourceRequest.fromLeadRequirements(lead.requirements),
      new Date()
    )
  }

  static reconstitute(data: OpportunityData): Opportunity {
    // Tái tạo từ database — KHÔNG validate lại invariants
    return new Opportunity(data.id, data.accountId, ...)
  }
}
```

**Khi nào cần Factory:**

| Cần Factory | Constructor đủ |
|---|---|
| Logic tạo > 3 bước | Constructor ≤ 3 params |
| Tạo từ nhiều nguồn (DB, API, form) | Tạo trực tiếp |
| Cần transform data trước khi tạo | Data đã đúng format |
| Cần domain-level validation khi tạo | Invariants đơn giản |

### ⚖️ Trade-offs — Domain Service

| | Dùng Domain Service đúng | Lạm dụng Domain Service |
|---|---|---|
| **Kết quả** | Entity rich (logic inside), Service orchestrate | **Anemic Domain Model** — Entity chỉ getter/setter, MỌI logic ở Service |
| **Dấu hiệu** | Entity có methods, Service chỉ cross-aggregate | Entity = data bag, Service = God class |
| **Rule** | Nếu logic chỉ cần 1 aggregate → ĐẶT TRONG ENTITY | Nếu Entity trở thành data-only → refactor logic VÀO Entity |

---

## 🏋️ Exercise — Xác định Domain Services

### Phần A: ITO — Liệt kê (10 phút)

| Service | Input | Output | Tại sao là Domain Service (không Entity)? |
|---|---|---|---|
| 1. | | | |
| 2. | | | |
| 3. | | | |

### Phần B: Logistics — Liệt kê (10 phút)

| Service | Input | Output | Tại sao là Domain Service? |
|---|---|---|---|
| 1. | | | |
| 2. | | | |
| 3. | | | |

### Phần C: Factory design (5 phút)

Viết Factory cho Trip (Logistics):

```typescript
class TripFactory {
  static createNew(
    stops: StopData[],
    vehicleId: VehicleId,
    driverId: DriverId
  ): Trip {
    // Viết logic: validate stops, calculate route, enforce capacity...
  }
}
```

---

## 🪞 Reflect

1. **Domain Service có nên gọi Repository không?** → Thuần DDD nói **không** — Application Service load data, pass cho Domain Service. Thực tế, một số teams cho phép — nhưng risk: Domain Service coupling với infrastructure. **Recommendation:** pass data vào, không inject repo.

2. **"Không biết đặt đâu → Domain Service" — nguy hiểm thế nào?** → **Anemic Domain Model:** Entity thành data bag (chỉ getter/setter), MỌI logic ở Service. Kết quả: Entity vô nghĩa, Service God class. **Rule:** nếu logic chỉ cần 1 aggregate → đặt TRONG Entity trước. Chỉ dùng Domain Service khi truly cross-aggregate.

3. **Factory có bắt buộc?** → Không. Nếu constructor đơn giản (≤3 params, không transform) → constructor đủ. Factory chỉ cần khi tạo phức tạp hoặc tạo từ nhiều nguồn. **Over-engineering Factory cho simple objects = waste.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Domain Service vs Application Service + 3 dấu hiệu Domain Service
- [ ] **Apply:** Liệt kê ≥3 Domain Services cho ITO + ≥3 cho Logistics
- [ ] **Analyze:** Giải thích tại sao matching logic KHÔNG thuộc Resource entity — liên hệ với Anemic Domain Model anti-pattern

---

> 🔗 **Tiếp theo:** Domain Service orchestrate logic, Entity enforce invariants, Factory tạo objects. Nhưng tất cả cần **persistence** — lưu và load aggregates từ database. Bài tiếp — *Repository Pattern* — sẽ trả lời: Domain code KHÔNG biết database tồn tại. Repository = abstraction layer giữa Domain và Database. 1 Repository per Aggregate.
