---
type: lesson
module: 7
lesson: 1
title: "Entities & Value Objects"
duration: "35 phút"
prerequisites: ["module-6"]
narrative_phase: "tactical design"
migration_phase: "Phase 2: Implement domain model bên trong Bounded Context"
business_invariant: "Entity = có identity + lifecycle; Value Object = immutable + so sánh bằng value; Prefer VOs over Entities; VO KHÔNG CÓ repository riêng"
---

# Lesson 7.1: Entities & Value Objects — "Cùng là object, nhưng khác bản chất"

## 📍 Context — Bạn đang ở đây

> Module 1-6 = **Strategic Design** — quyết định "WHAT" (Domain Map, Glossary, Event Storm) và "WHERE" (Bounded Contexts, Context Map, Knowledge Architecture). Bạn biết ITO có 6 Bounded Contexts, biết integration patterns, biết invariants.
>
> Bây giờ bắt đầu **Tactical Design** — quyết định "HOW": viết code **bên trong** 1 Bounded Context thế nào? Khi Tuấn tạo `Resource` object — nó có identity riêng (Entity) hay chỉ là giá trị (Value Object)? `60% allocation` có cần ID không? `Money(50000, "VND")` có khác `Money(50000, "VND")` khác không?

## 🔥 Tension — "Tại sao Skill có ID?"

Sprint 5. Dev A implement Resource Context:

> **Dev A:** *"Tôi tạo class cho mọi thứ: Resource, Skill, AllocationPercentage, DateRange, Money. Mỗi class đều có ID (UUID) vì tôi cần lưu DB."*

Code review. Tuấn phát hiện bug:

> **Tuấn:** *"Dev A, Skill có ID `skill-001`. Nhưng khi 2 resource cùng có 'Java Senior' — bạn tạo 2 records trong DB: `skill-001` (Java Senior) và `skill-002` (Java Senior). 2 records, cùng giá trị. Khi query 'ai biết Java Senior?' — bạn query thế nào?"*
>
> **Dev A:** *"Join bằng skill_id... nhưng skill-001 và skill-002 cùng là Java Senior... Tôi phải so sánh bằng name thay vì ID. Vậy ID vô nghĩa?"*

**Minh:**
> *"Không phải mọi object đều cần ID. 'Java Senior' = 'Java Senior' — bất kể nó thuộc resource nào. Đó là **Value Object**. Resource thì khác — Nguyễn Văn A (R001) ≠ Nguyễn Văn B (R002) dù cả hai đều biết Java. Đó là **Entity**. Phân biệt 2 loại này = bước đầu tiên của Tactical Design."*

> 💭 **Câu hỏi:** Mọi thứ đều là class — nhưng KHÔNG phải mọi thứ đều cần ID. Khi nào cần identity (Entity)? Khi nào chỉ cần value (Value Object)? Và tại sao phân biệt sai → bug + complexity?

## 🎓 Explanation — 2 loại Object trong Domain

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Entity = có identity (ID) + lifecycle (tạo/sửa/xóa) → so sánh bằng ID. Value Object = immutable + so sánh bằng value → KHÔNG có ID, KHÔNG có repository riêng. Prefer Value Objects over Entities — VO đơn giản hơn, ít bug hơn, dễ test hơn."*

### Entity — Có Identity

Entity là object có **danh tính riêng** — dù thay đổi thuộc tính, nó vẫn là chính nó.

```
Resource (Entity):
  id: R001                        ← IDENTITY
  name: "Nguyễn Văn A"           ← đổi tên → vẫn là R001
  email: "a@ito.com"             ← đổi email → vẫn là R001
  skills: [Java, React]          ← thêm skill → vẫn là R001

→ R001 đổi tên thành "Nguyễn Văn Alpha" → VẪN LÀ R001
→ 2 resource trùng tên → KHÁC resource (R001 ≠ R002)
→ Identity = ID, KHÔNG PHẢI tập hợp thuộc tính
```

**3 tiêu chí nhận biết Entity:**

| Tiêu chí | Câu hỏi | Nếu "Có" |
|---|---|---|
| **Distinguish** | Cần phân biệt 2 instance dù giá trị giống nhau? | → Entity |
| **Lifecycle** | Có lifecycle (tạo → sửa → xóa)? | → Entity |
| **Tracking** | Cần tracking theo thời gian? | → Entity |

### Value Object — Không có Identity

Value Object **bằng nhau khi giá trị bằng nhau** — không cần ID.

```
Money (Value Object):
  amount: 100000
  currency: "VND"

→ Money(100000, "VND") == Money(100000, "VND")  ← SO SÁNH BẰNG GIÁ TRỊ
→ Không cần phân biệt "đồng 100K này" với "đồng 100K kia"
→ IMMUTABLE: muốn đổi → tạo object MỚI
→ KHÔNG có lifecycle riêng
```

**3 tiêu chí nhận biết Value Object:**

| Tiêu chí | Câu hỏi | Nếu "Có" |
|---|---|---|
| **Equality** | 2 instance cùng giá trị = giống hệt nhau? | → VO |
| **Immutable** | Không đổi, thay bằng object mới? | → VO |
| **No lifecycle** | Không tạo/sửa/xóa riêng? | → VO |

### Apply cho ITO CRM

| Object | Entity hay VO? | Tại sao | Ví dụ |
|---|---|---|---|
| Resource | **Entity** | R001 ≠ R002 dù cùng skill | Nguyễn Văn A ≠ Nguyễn Văn B |
| Skill | **Value Object** | "Java Senior" = "Java Senior" | Không cần ID, so sánh bằng (name, level) |
| Allocation | **Entity** | Cần track: ai → dự án nào → bao lâu | Alloc-001: R001 → Project X, 60% |
| AllocationPercentage | **Value Object** | 60% = 60%, immutable | Muốn đổi → tạo VO mới |
| Money | **Value Object** | 50M VND = 50M VND | `Money(50000000, "VND")` |
| DateRange | **Value Object** | 01/01-31/03 = 01/01-31/03 | `DateRange(start, end)` |
| Project | **Entity** | Mỗi project có ID, lifecycle | PRJ-001: "CRM Migration" |
| Opportunity | **Entity** | Sales pipeline tracking | OPP-001: "TechCorp CRM Deal" |

### Apply cho Logistics

| Object | Entity hay VO? | Tại sao | Ví dụ |
|---|---|---|---|
| Vehicle | **Entity** | Mỗi xe có biển số riêng | VH-001: "51F-12345" |
| Route | **Entity** | Mỗi tuyến có lifecycle (planned → active → completed) | RT-001: HCM → Đà Nẵng |
| GeoLocation | **Value Object** | (10.762, 106.660) = (10.762, 106.660) | Tọa độ không cần ID |
| Weight | **Value Object** | 5 tấn = 5 tấn | `Weight(5000, "kg")` |
| Driver | **Entity** | Cần phân biệt từng tài xế | DRV-001: "Trần Văn C" |
| TimeWindow | **Value Object** | 8:00-12:00 = 8:00-12:00 | `TimeWindow(08:00, 12:00)` |

### Code Implementation

**Entity — Resource:**
```typescript
class Resource {
  readonly id: ResourceId          // Identity — BẤT BIẾN
  private name: string             // Có thể thay đổi
  private email: string            // Có thể thay đổi
  private skills: Skill[]          // Có thể thêm/bớt
  private status: ResourceStatus   // Có lifecycle

  addSkill(skill: Skill): void {
    if (this.skills.some(s => s.equals(skill))) return  // VO comparison
    this.skills.push(skill)
  }

  equals(other: Resource): boolean {
    return this.id === other.id  // SO SÁNH BẰNG ID — không quan tâm name, email
  }
}
```

**Value Object — AllocationPercentage:**
```typescript
class AllocationPercentage {
  private readonly value: number   // IMMUTABLE — không setter

  constructor(value: number) {
    if (value <= 0) throw new Error("Must be positive")
    if (value > 100) throw new Error("Cannot exceed 100%")
    this.value = value
  }

  add(other: AllocationPercentage): AllocationPercentage {
    return new AllocationPercentage(this.value + other.value)  // TẠO MỚI, không sửa
  }

  equals(other: AllocationPercentage): boolean {
    return this.value === other.value  // SO SÁNH BẰNG GIÁ TRỊ
  }
}
```

**Value Object — Money:**
```typescript
class Money {
  private readonly amount: number
  private readonly currency: string

  constructor(amount: number, currency: string) {
    if (amount < 0) throw new Error("Cannot be negative")
    this.amount = amount
    this.currency = currency
  }

  add(other: Money): Money {
    if (this.currency !== other.currency) throw new Error("Currency mismatch")
    return new Money(this.amount + other.amount, this.currency)  // IMMUTABLE
  }

  equals(other: Money): boolean {
    return this.amount === other.amount && this.currency === other.currency
  }
}
```

### Quy tắc thiết kế

```
Entity:                              Value Object:
  ✅ Có ID (UUID hoặc meaningful)      ✅ KHÔNG CÓ ID
  ✅ Mutable (thay đổi theo TG)        ✅ Immutable (tạo mới thay vì sửa)
  ✅ So sánh bằng ID                   ✅ So sánh bằng TẤT CẢ thuộc tính
  ✅ Có Repository để lưu/lấy          ✅ KHÔNG có Repository riêng
  ✅ Ít nhất 1 per context             ✅ Nên có NHIỀU hơn Entity
```

### ⚖️ Trade-offs — Entity vs Value Object

| | Dùng Entity khi không cần | Dùng VO khi nên Entity |
|---|---|---|
| **Hậu quả** | Skill có ID vô nghĩa, DB bloat, query phức tạp | Mất tracking, mất history, không distinguish |
| **Dấu hiệu** | "2 records giống nhau nhưng ID khác" (Dev A bug) | "Cần tìm lại record cụ thể nhưng không có ID" |
| **Sửa** | Chuyển thành VO, bỏ ID | Thêm ID, tạo Repository |
| **Rule** | **Prefer VO** — default mọi thứ là VO, chỉ promote lên Entity khi cần | Promote khi cần distinguish/track/lifecycle |

---

## 🏋️ Exercise — Phân loại Entity vs Value Object

### Phần A: ITO CRM (10 phút)

| Object | Entity hay VO? | Lý do (dùng 3 tiêu chí) |
|---|---|---|
| Account (khách hàng) | | |
| ContactInfo (email, phone) | | |
| Opportunity | | |
| WinProbability (0-100%) | | |
| Contract | | |
| ContractValue (số tiền) | | |
| BenchStatus (bench hay không) | | |
| SkillLevel (Junior/Mid/Senior) | | |

### Phần B: Logistics (10 phút)

| Object | Entity hay VO? | Lý do |
|---|---|---|
| Shipment | | |
| DeliveryAddress | | |
| Trip | | |
| TimeWindow (8:00-12:00) | | |
| ProofOfDelivery | | |
| VehicleCapacity (tải trọng) | | |

### Phần C: Code sketch (10 phút)

Viết code cho 1 Entity và 1 Value Object từ ITO:

```typescript
// Entity — Opportunity
class Opportunity {
  readonly id: OpportunityId
  // viết thuộc tính...
  // viết equals() — so sánh bằng ID...
}

// Value Object — WinProbability
class WinProbability {
  private readonly value: number
  // viết constructor (validation)...
  // viết equals() — so sánh bằng value...
}
```

---

## 🪞 Reflect

1. **"Address" thường là VO — nhưng khi nào là Entity?** → Nếu bạn là công ty **bất động sản** — mỗi address cần tracking riêng (giá, lịch sử giao dịch) → Entity. Nếu address chỉ là "nơi giao hàng" → VO. **Context quyết định** — cùng concept, khác context, khác classification.

2. **Value Object PHẢI immutable — tại sao?** → Nếu 2 Entity share cùng 1 VO instance và VO bị sửa → **side effect** — Entity A sửa VO → Entity B bị ảnh hưởng mà không biết. Immutable = safe sharing. Muốn thay đổi → tạo VO mới.

3. **Nên có nhiều VOs hơn Entities — tại sao?** → VO: đơn giản hơn (no ID, no lifecycle), ít bug (immutable, no side effect), dễ test (equals = value comparison), DB nhẹ (embedded, no separate table). **Rule of thumb:** 70% VO, 30% Entity.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Entity vs VO bằng 3 tiêu chí + giải thích tại sao VO immutable
- [ ] **Apply:** Phân loại ≥6 objects cho ITO + ≥4 cho Logistics
- [ ] **Analyze:** Viết code sketch + giải thích tại sao Skill có ID = bug (Dev A scenario)

---

> 🔗 **Tiếp theo:** Entity và VO = building blocks cơ bản. Nhưng chúng không sống đơn lẻ — chúng tạo thành **nhóm** (Aggregate). Bài tiếp — *Aggregates & Roots* — sẽ trả lời: `Allocation` thuộc Aggregate nào? Ai là Aggregate Root? Và tại sao invariant `total_allocation ≤ 100%` phải enforce **bên trong** Aggregate boundary?
