---
type: lesson
module: 7
lesson: 3
title: "Domain Services & Factories"
duration: "30 phút"
prerequisites: ["module-7/lesson-2"]
---

# Lesson 7.3: Domain Services & Factories

## 🎓 Concept — "Khi logic không thuộc về Entity nào"

### Vấn đề: Đặt logic ở đâu?

```
Matching Resource cho Project:
- Cần biết Project requirements (skills, level, timeline)
- Cần scan tất cả Resources (availability, skills)
- Cần thuật toán matching (scoring, ranking)

Logic này thuộc Resource? → Không, Resource không biết Project requirements.
Logic này thuộc Project? → Không, Project không biết Resource availability.
→ Logic này thuộc "giữa 2 aggregates" → Domain Service
```

### Domain Service — Logic liên aggregate

Domain Service là **operation thuộc domain nhưng không thuộc Entity/VO nào cụ thể:**

```typescript
// ❌ SAI: Resource biết quá nhiều về Project
class Resource {
  matchForProject(project: Project): MatchScore { ... }
}

// ✅ ĐÚNG: Domain Service kết nối 2 aggregates
class ResourceMatchingService {
  findBestMatches(
    requirements: ProjectRequirements,
    availableResources: List<Resource>
  ): List<MatchResult> {
    // Scoring: skills match + availability + location
  }
}
```

**3 dấu hiệu cần Domain Service:**
1. Logic cần data từ **nhiều aggregates**
2. Logic là **stateless** (không giữ state riêng)
3. Logic là **domain concept** (business nói về nó, có tên trong Glossary)

### Ví dụ — ITO CRM

| Domain Service | Mô tả | Tại sao không để trong Entity? |
|---|---|---|
| `ResourceMatchingService` | Matching resource → project | Cần cả Resource lẫn ProjectRequirements |
| `UtilizationCalculator` | Tính utilization rate | Cần tổng hợp từ nhiều Allocations across Resources |
| `OpportunityQualifier` | Đánh giá win probability | Cần data từ Account + Contact + Market |

### Ví dụ — Logistics

| Domain Service | Mô tả | Tại sao không để trong Entity? |
|---|---|---|
| `RouteOptimizer` | Tìm route tối ưu | Cần tất cả Stops + Vehicle capacity + Traffic |
| `CapacityChecker` | Kiểm tra xe đủ tải? | Cần tổng weight từ nhiều Shipments |
| `ETACalculator` | Tính thời gian dự kiến | Cần Route + Traffic + Vehicle speed |

### Factory — Tạo complex objects

Factory pattern dùng khi **việc tạo object phức tạp:**

```typescript
// ❌ Constructor quá phức tạp
const resource = new Resource(
  generateId(),
  name, email,
  parseSkills(skillsInput),
  initBenchStatus(),
  validateAndCreateAllocations([])
);

// ✅ Factory ẩn logic tạo
class ResourceFactory {
  static createNew(name: string, email: string, skills: string[]): Resource {
    return new Resource(
      ResourceId.generate(),
      name, email,
      skills.map(s => Skill.parse(s)),
      BenchStatus.initial(),
      []
    );
  }
  
  static reconstitute(data: ResourceData): Resource {
    // Tái tạo từ database — không validate lại
  }
}
```

**Khi nào cần Factory:**
1. Logic tạo phức tạp (>3 bước)
2. Tạo từ nhiều nguồn khác nhau (DB, API, form)
3. Cần đảm bảo invariants ngay khi tạo

### Domain Service ≠ Application Service

```
Domain Service:
  - Logic NGHIỆP VỤ (matching, calculation, qualification)
  - Không biết DB, API, UI
  - Dùng domain language

Application Service:
  - ĐIỀU PHỐI workflow (load → call domain → save)
  - Biết DB (repository), biết transaction
  - Không chứa business logic

VD:
  AllocateResourceUseCase (Application Service):
    1. Load resource từ repo
    2. Call resource.allocate(projectId, %)    ← domain logic
    3. Save resource qua repo
    4. Publish ResourceAllocated event
```

---

## 🏋️ Exercise — Xác định Domain Services

### Phần A: ITO — Liệt kê (10 phút)

Liệt kê ≥3 Domain Services cho ITO CRM:

| Service | Input | Output | Tại sao là Domain Service? |
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

Viết pseudo-code Factory cho Opportunity:

```typescript
class OpportunityFactory {
  static createFromLead(lead: Lead): Opportunity {
    // viết logic tạo Opportunity từ Lead...
  }
}
```

---

## 🪞 Reflect

1. **Domain Service có nên gọi Repository không?** Gợi ý: thuần DDD nói không (Application Service load data, pass cho Domain Service). Thực tế, một số teams cho phép.

2. **"Nếu logic không biết đặt đâu → Domain Service" — pattern này nguy hiểm thế nào?** Gợi ý: lạm dụng → Anemic Domain Model (Entity chỉ có getters/setters, mọi logic ở Service).

3. **Factory có phải Tactical Pattern bắt buộc?** Khi nào constructor đủ dùng?

---

## ✅ Hoàn thành lesson khi
- [ ] Phân biệt Domain Service vs Application Service
- [ ] Liệt kê ≥3 Domain Services cho ITO
- [ ] Liệt kê ≥3 Domain Services cho Logistics
- [ ] Viết 1 Factory pseudo-code
- [ ] Trả lời ≥2/3 câu hỏi reflection
