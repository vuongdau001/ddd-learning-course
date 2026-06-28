---
type: lesson
module: 7
lesson: 1
title: "Entities & Value Objects"
duration: "35 phút"
prerequisites: ["module-6"]
---

# Lesson 7.1: Entities & Value Objects

## 🎓 Concept — "Cùng là object, nhưng khác nhau về bản chất"

### Vấn đề: Mọi thứ đều là class?

Khi code, dev thường tạo class cho mọi thứ: `Resource`, `Skill`, `Money`, `Address`. Nhưng trong DDD, có 2 loại object khác nhau cơ bản:

```
Entity = "Tôi là AI, dù đổi tên thì vẫn là TÔI" → CÓ identity
Value Object = "100.000 VND nào cũng như nhau" → KHÔNG CÓ identity, so bằng giá trị
```

### Entity — Có Identity

Entity là object có **danh tính riêng** — dù thay đổi thuộc tính, nó vẫn là chính nó.

```
Resource (Entity):
  id: R001
  name: "Nguyễn Văn A"       ← đổi tên vẫn là R001
  email: "a@ito.com"          ← đổi email vẫn là R001
  skills: [Java, React]       ← thêm skill vẫn là R001

→ Identity = id, KHÔNG PHẢI tập hợp thuộc tính
```

**Tiêu chí nhận biết Entity:**
1. Cần phân biệt 2 instance dù giá trị giống nhau? → Entity
2. Có lifecycle (tạo → sửa → xóa)? → Entity
3. Cần tracking theo thời gian? → Entity

### Value Object — Không có Identity

Value Object là object **bằng nhau khi giá trị bằng nhau** — không cần ID.

```
Money (Value Object):
  amount: 100000
  currency: "VND"

→ Money(100000, "VND") == Money(100000, "VND")
→ Không cần id, không cần phân biệt "đồng 100K này" với "đồng 100K kia"
```

**Tiêu chí nhận biết Value Object:**
1. 2 instance có cùng giá trị = giống hệt nhau? → VO
2. Immutable (không đổi, thay bằng object mới)? → VO
3. Không có lifecycle riêng? → VO

### So sánh — ITO CRM

| Object | Entity hay VO? | Tại sao |
|---|---|---|
| Resource | **Entity** | Cần phân biệt từng người (R001 ≠ R002) |
| Skill | **Value Object** | "Java Senior" = "Java Senior", không cần ID |
| Allocation | **Entity** | Cần track: ai, dự án nào, bao lâu |
| AllocationPercentage | **Value Object** | 60% = 60%, immutable |
| DateRange | **Value Object** | 01/01-31/01 = 01/01-31/01 |
| Project | **Entity** | Mỗi project có identity riêng |

### So sánh — Logistics

| Object | Entity hay VO? | Tại sao |
|---|---|---|
| Vehicle | **Entity** | Mỗi xe có biển số riêng |
| Route | **Entity** | Mỗi tuyến đường có lifecycle |
| GeoLocation | **Value Object** | (10.762, 106.660) = (10.762, 106.660) |
| Weight | **Value Object** | 5 tấn = 5 tấn |
| Driver | **Entity** | Cần phân biệt từng tài xế |
| FuelConsumption | **Value Object** | 8L/100km = 8L/100km |

### Quy tắc thiết kế

```
Entity:
  ✅ Có ID (UUID hoặc meaningful ID)
  ✅ Mutable (thay đổi theo thời gian)
  ✅ So sánh bằng ID
  ✅ Có repository để lưu/lấy

Value Object:
  ✅ KHÔNG CÓ ID
  ✅ Immutable (tạo mới thay vì sửa)
  ✅ So sánh bằng tất cả thuộc tính
  ✅ KHÔNG CÓ repository riêng (nằm trong Entity)
```

---

## 🏋️ Exercise — Phân loại Entity vs Value Object

### Phần A: ITO CRM (10 phút)

Phân loại các object sau:

| Object | Entity hay VO? | Lý do |
|---|---|---|
| Account (khách hàng) | | |
| ContactInfo (email, phone) | | |
| Opportunity | | |
| WinProbability (0-100%) | | |
| Contract | | |
| ContractValue (số tiền) | | |
| BenchStatus (đang bench hay không) | | |
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

Viết pseudo-code cho 1 Entity và 1 VO từ ITO:

```typescript
// Entity
class Resource {
  // viết thuộc tính + phương thức...
}

// Value Object
class AllocationPercentage {
  // viết thuộc tính + equals()...
}
```

---

## 🪞 Reflect

1. **Tại sao "Address" thường là Value Object nhưng đôi khi là Entity?** Gợi ý: nếu công ty bất động sản, mỗi address cần tracking riêng → Entity.

2. **Value Object PHẢI immutable — tại sao?** Gợi ý: nếu 2 Entity share cùng 1 VO và VO bị sửa → side effect.

3. **Trong DDD, nên có nhiều Value Objects hơn Entities — tại sao?** Gợi ý: VO đơn giản hơn, ít bug hơn, dễ test hơn.

---

## ✅ Hoàn thành lesson khi
- [ ] Phân biệt Entity vs Value Object bằng 3 tiêu chí
- [ ] Phân loại ≥6 objects cho ITO CRM
- [ ] Phân loại ≥4 objects cho Logistics
- [ ] Viết pseudo-code cho 1 Entity + 1 VO
- [ ] Trả lời ≥2/3 câu hỏi reflection
