---
type: lesson
module: 5
lesson: 3
title: "Integration Patterns Deep-Dive"
duration: "30 phút"
prerequisites: ["module-5/lesson-2"]
narrative_phase: "kiến trúc"
migration_phase: "Phase 1: Implement integration giữa contexts"
business_invariant: "ACL bắt buộc cho external/legacy; Shared Kernel ≤ 10 types; OHS + Published Language cho multi-consumer"
---

# Lesson 5.3: Integration Patterns Deep-Dive — "ACL, Shared Kernel, và OHS thực hành"

## 📍 Context — Bạn đang ở đây

> Lesson 5.2 cho bạn Context Map: Sales ↔ Resource (Partnership), Finance ↔ SAP (ACL), Resource → AI Agent (OHS). Bạn đã biết DÙNG pattern nào cho mỗi cặp. Bây giờ: **implement thế nào?** Dev team cần code cụ thể — ACL translator trông ra sao? OHS API design thế nào? Shared Kernel share gì và KHÔNG share gì?
>
> CTO Minh yêu cầu: *"Sprint 4 phải integrate SAP để Finance Context auto-sync invoices. Cho tôi design rõ ràng — không để SAP model leak vào code."*

## 🔥 Tension — "SAP tiếng Đức, code tiếng Anh"

Sprint 4. Team Finance bắt đầu integrate SAP ERP:

> **Dev E:** *"SAP API trả về data như thế này:*
> ```json
> { "VBELN": "0010005321", "KUNNR": "CST001", "NETWR": 50000.00, "WAERS": "VND", "FKDAT": "20260115" }
> ```
> *Tôi viết code: `if (sapData.VBELN === contractId)` — nhưng team không ai biết VBELN là gì."*
>
> **Minh:** *"VBELN = Verkaufsbeleg Nummer. Tiếng Đức = Sales Document Number. Ai sẽ maintain code đầy VBELN, BUKRS, DMBTR? Đó là tại sao chúng ta cần ACL — dịch SAP model sang model của chúng ta."*

Đồng thời, team Sales và Resource tranh cãi:

> **Dev A (Resource):** *"Tôi cần share `ResourceId` type giữa Sales và Resource context. Làm shared library?"*
>
> **Dev F (Sales):** *"Nếu share library → mỗi lần Resource đổi library → Sales phải update. Tôi không muốn phụ thuộc!"*

> 💭 **Câu hỏi:** ACL translate SAP model → domain model. Nhưng share bao nhiêu giữa internal contexts? Shared Kernel phải nhỏ thế nào? OHS expose API — nhưng versioning thế nào để không break downstream?

## 🎓 Explanation — 3 Patterns quan trọng nhất

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"ACL = bắt buộc cho external/legacy (SAP model KHÔNG ĐƯỢC leak vào domain code). Shared Kernel ≤ 10 types (chỉ Value Objects: ID, Money, DateRange — KHÔNG Entity). OHS + Published Language cho multi-consumer (versioned API + JSON Schema)."*

### Pattern 1: Anti-Corruption Layer (ACL)

**Khi nào:** Tích hợp external/legacy system mà model "khác" hoặc "bẩn".

```
External System          ACL              Your Context
(model khác bạn)    (translation)      (model sạch của bạn)

SAP: {                                  Finance Domain:
  VBELN: "10005321",  →  Translator  →    {
  KUNNR: "CST001",       (mapping)        contractId: "10005321",
  NETWR: 50000,                           clientId: "CST001",
  WAERS: "VND",                           amount: Money(50000, "VND"),
  FKDAT: "20260115"                       invoiceDate: 2026-01-15
}                                       }
```

**Tại sao cần?**
- SAP dùng German abbreviations (VBELN = Verkaufsbeleg Nummer) → team không hiểu
- Nếu SAP model "leak" vào domain code → code đầy `BPNR`, `BUKRS` → unmaintainable
- Khi SAP upgrade API → **chỉ sửa ACL**, không sửa Finance domain logic

**ITO — Finance ↔ SAP ACL:**
```javascript
// ACL: SapContractTranslator.ts

class SapContractTranslator {
  translate(sapData) {
    return {
      contractId: sapData.VBELN,           // SAP: Sales Document Number
      clientId: sapData.KUNNR,             // SAP: Customer Number
      amount: new Money(sapData.NETWR, sapData.WAERS),  // SAP: Net Value + Currency
      invoiceDate: this.parseSapDate(sapData.FKDAT),     // SAP: yyyymmdd → Date
      lineItems: sapData.ITEMS?.map(item => ({
        description: item.ARKTX,           // SAP: Material Short Text
        quantity: item.FKIMG,              // SAP: Billing Quantity
        unitPrice: new Money(item.NETWR, sapData.WAERS),
      }))
    }
  }

  parseSapDate(sapDate) {
    // SAP format: "20260115" → 2026-01-15
    return new Date(
      sapDate.slice(0, 4),
      sapDate.slice(4, 6) - 1,
      sapDate.slice(6, 8)
    )
  }
}
```

**Logistics — Tracking ↔ Google Maps ACL:**
```javascript
// ACL: GoogleMapsTranslator.ts

class GoogleMapsTranslator {
  translateRoute(googleResponse) {
    const route = googleResponse.routes[0]
    return {
      totalDistance: Meters(route.legs.reduce((sum, leg) => sum + leg.distance.value, 0)),
      totalDuration: Seconds(route.legs.reduce((sum, leg) => sum + leg.duration.value, 0)),
      waypoints: route.legs.map(leg => ({
        location: new GeoPoint(leg.start_location.lat, leg.start_location.lng),
        address: leg.start_address,
      }))
    }
  }
}
```

### Pattern 2: Shared Kernel

**Khi nào:** 2 contexts cần share 1 phần nhỏ model.

```
Sales Context        Shared Kernel        Resource Context
                  ┌──────────────────┐
                  │ • ResourceId     │  ← ID type
                  │ • Money          │  ← Value Object
                  │ • DateRange      │  ← Value Object
                  │ • SkillTag       │  ← Value Object
                  └──────────────────┘
```

**Quy tắc nghiêm ngặt:**

| ✅ Share | ❌ KHÔNG share |
|---|---|
| Value Objects (ID, Money, Date) | Entities (Resource, Opportunity) |
| Enums (SkillLevel, DealStatus) | Aggregates (ResourceProfile) |
| ≤ 10 types | Business logic |

**Tại sao ≤10 types?**
Shared Kernel dễ phình to → thành "shared monolith in disguise". **Review mỗi sprint:** nếu >10 types → cảnh báo, xem lại có nên dùng Customer-Supplier + ACL thay thế.

**ITO Shared Kernel:**
```typescript
// shared-kernel/types.ts

export type ResourceId = Brand<string, 'ResourceId'>
export type OpportunityId = Brand<string, 'OpportunityId'>

export class Money {
  constructor(public amount: number, public currency: string) {}
}

export class DateRange {
  constructor(public start: Date, public end: Date) {}
}

export type SkillTag = string  // "Java", "React", "DevOps"
export enum SkillLevel { Junior, Mid, Senior, Lead, Principal }

// TOTAL: 5 types — well within limit
```

### Pattern 3: Open Host Service (OHS)

**Khi nào:** 1 context phục vụ nhiều consumers → expose public API + versioning.

```
Resource Context (Upstream)
    │
    ├── v1/resources          ← Sales Context, Delivery Context
    ├── v1/allocations        ← Delivery Context, Reporting
    ├── v1/availability       ← Sales Context (real-time)
    └── v1/skills/search      ← AI Agent (semantic matching)
         │
         ├── → Sales Context
         ├── → Delivery Context
         ├── → AI Agent
         └── → Reporting Dashboard
```

**Luôn đi kèm Published Language:**
```yaml
# openapi.yaml — Published Language cho Resource OHS
openapi: 3.0.0
paths:
  /v1/resources/{id}:
    get:
      responses:
        200:
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/ResourceSummary'

components:
  schemas:
    ResourceSummary:
      type: object
      properties:
        id: { type: string, format: uuid }
        name: { type: string }
        skills: { type: array, items: { $ref: '#/components/schemas/Skill' } }
        currentAllocation: { $ref: '#/components/schemas/AllocationStatus' }
        availability: { type: string, enum: [available, allocated, bench, leave] }
```

**Versioning rule:** Breaking change → new version (`v2/resources`). Non-breaking → add to existing version. Sunset old version after 3 months.

### ⚖️ So sánh 3 patterns

| | ACL | Shared Kernel | OHS |
|---|---|---|---|
| **Direction** | Downstream tự bảo vệ | 2 chiều (cùng share) | Upstream expose |
| **Coupling** | Loose ✅ | Tight ⚠️ | Medium |
| **Effort** | Build translation layer | Maintain shared library | Build API + docs + versioning |
| **Dùng khi** | External/legacy system | 2 closely related contexts (cùng team) | 1 context, nhiều consumers |
| **Risk** | Translator out of sync | Kernel phình to (>10 types) | API breaking changes |
| **ITO ví dụ** | Finance ↔ SAP | Sales ↔ Resource (shared IDs) | Resource → AI Agent + Delivery + Sales |

---

## 🏋️ Exercise — Thiết kế Integration

### Phần A: Design ACL cho SAP Integration (15 phút)

SAP API trả về:
```json
{
  "VBELN": "0010005321",
  "KUNNR": "CST001",
  "NETWR": 50000.00,
  "WAERS": "VND",
  "FKDAT": "20260115"
}
```

Domain model cần:
```json
{
  "contractId": "...",
  "clientId": "...",
  "amount": { "value": 50000, "currency": "VND" },
  "invoiceDate": "2026-01-15"
}
```

Viết translation mapping:

| SAP Field | → Domain Field | Transform |
|---|---|---|
| VBELN | ? | ? |
| KUNNR | ? | ? |
| NETWR + WAERS | ? | ? |
| FKDAT | ? | ? |

**Bonus:** Nếu SAP thêm field `MWSBTR` (VAT amount) → bạn sửa ở đâu? Domain model hay ACL?

### Phần B: Design OHS cho Route Context — Logistics (10 phút)

Route Context cần expose API cho 3 consumers:

| Endpoint | Method | Consumer | Trả về gì | Tại sao consumer cần? |
|---|---|---|---|---|
| /v1/routes/{id} | GET | ? | ? | ? |
| /v1/routes/optimize | POST | ? | ? | ? |
| /v1/routes/{id}/eta | GET | ? | ? | ? |

### Phần C: Shared Kernel Review (5 phút)

Bạn team ITO quyết định Shared Kernel gồm:

| Type | Share? (Yes/No) | Lý do |
|---|---|---|
| ResourceId | ? | ? |
| Money | ? | ? |
| Resource (full entity) | ? | ? |
| SkillTag | ? | ? |
| OpportunityPipeline | ? | ? |
| DateRange | ? | ? |

Tổng types shared: ___ (phải ≤ 10)

---

## 🪞 Reflect

1. **"Tại sao không dùng shared database thay vì ACL?"** → Shared database = **anti-pattern lớn nhất** trong DDD. Tại sao: (1) Coupling — 1 context thay đổi schema → break tất cả, (2) Ownership — ai own schema? (3) Performance — 1 context load nặng → tất cả chậm. **ACL + API = loose coupling. Shared DB = tight coupling trá hình.**

2. **ACL có cost. Khi nào cost > benefit?** → Khi external system API stable, ít thay đổi, model gần giống domain bạn → **Conformist** có thể đủ (chấp nhận model external, không translate). VD: Google Calendar API = stable, model đơn giản → Conformist OK. SAP ERP = thay đổi khi upgrade → ACL bắt buộc.

3. **Microservices: mọi service-to-service call đều cần ACL?** → **Không.** Internal contexts do team mình control → dùng Shared Kernel (cho closely related) hoặc Customer-Supplier (cho khác priority). ACL chỉ cần khi: external system, legacy system, hoặc context do team khác control mà model rất khác.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích ACL, Shared Kernel, OHS + khi nào dùng mỗi pattern
- [ ] **Apply:** Design ACL mapping SAP → Finance domain + Design OHS API cho Route Context
- [ ] **Analyze:** Review Shared Kernel — giải thích tại sao Entity/Aggregate KHÔNG được share — liên hệ với vấn đề "VBELN leak vào domain code"

---

## 🏁 Hoàn thành Module 5 — Bounded Context

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 5.1 Subdomain vs Context | Phân biệt Problem vs Solution Space, 3 kiểu mapping | 8 Subdomains → 6 Bounded Contexts |
| 5.2 Context Mapping | Vẽ quan hệ giữa contexts, chọn integration patterns | Context Map diagram + pattern assignments |
| 5.3 Integration Deep-Dive | Design ACL, Shared Kernel, OHS thực hành | ACL translator + OHS API spec + Shared Kernel manifest |

**Deliverable:** `practice/context-map.md` — Context Map diagram + Integration Pattern assignments + ACL design

> 🔗 **Tiếp theo — Module 6: Tactical Design.** Bạn đã có kiến trúc strategic hoàn chỉnh: Domain Map → Glossary → Strategic Classification → Event Storm → Bounded Contexts → Context Map. Tất cả ở level **strategic** — quyết định "what" và "where". Module 6 sẽ đi vào **tactical** — quyết định "how": **Entity, Value Object, Aggregate** — các building blocks bên trong 1 Bounded Context. Khi Tuấn tạo `Resource` object trong code — nó là Entity (có identity) hay Value Object (chỉ có value)? Allocation gồm những gì? Aggregate boundary ở đâu?
