---
type: lesson
module: 5
lesson: 3
title: "Integration Patterns Deep-Dive"
duration: "30 phút"
prerequisites: ["module-5/lesson-2"]
---

# Lesson 5.3: Integration Patterns Deep-Dive

## 🎓 Concept — "ACL, Shared Kernel, và Open Host Service thực hành"

### 3 patterns quan trọng nhất

Trong 7 patterns, 3 patterns sau xuất hiện nhiều nhất trong thực tế:

### Pattern 1: Anti-Corruption Layer (ACL)

**Khi nào:** Tích hợp với external system hoặc legacy system mà model "bẩn".

```
External System          ACL              Your Context
(model khác bạn)    (translation)      (model sạch của bạn)

SAP: {                                  Your domain:
  BPNR: "10001",    →  Translator  →    {
  BUKRS: "VN01",        (mapping)        customerId: "10001",
  DMBTR: 50000                           company: "VN Office",
}                                         amount: 50000
                                        }
```

**Tại sao cần?**
- SAP dùng German abbreviations (BPNR = Business Partner Number) → team bạn không hiểu
- Nếu để SAP model "leak" vào domain → code đầy "BPNR", "BUKRS" → unmaintainable
- ACL = lớp dịch thuật: SAP model → Your domain model

**Ví dụ ITO — Finance ↔ SAP:**
```
// ACL: SapContractTranslator

function translateSapContract(sapData) {
  return {
    contractId: sapData.VBELN,        // SAP Sales Document Number
    clientName: sapData.KUNNR_NAME,   // SAP Customer Name
    totalValue: sapData.NETWR,        // SAP Net Value
    currency: sapData.WAERS,          // SAP Currency
    startDate: parseSapDate(sapData.VDATU), // SAP format → ISO
  }
}
```

**Ví dụ Logistics — Tracking ↔ Google Maps:**
```
// ACL: GoogleMapsTranslator

function translateGoogleRoute(googleData) {
  return {
    distance: googleData.routes[0].legs[0].distance.value, // meters
    duration: googleData.routes[0].legs[0].duration.value,  // seconds
    waypoints: googleData.routes[0].legs.map(leg => ({
      lat: leg.start_location.lat,
      lng: leg.start_location.lng,
      address: leg.start_address,
    }))
  }
}
```

### Pattern 2: Shared Kernel

**Khi nào:** 2 contexts cần share 1 phần model — nhưng **rất ít**.

```
Context A              Shared Kernel          Context B
                    ┌──────────────┐
                    │ • ResourceId │
                    │ • Money      │
                    │ • DateRange  │
                    └──────────────┘
```

**Quy tắc:** Shared Kernel phải **nhỏ nhất có thể** — chỉ gồm Value Objects cơ bản (ID, Money, Date). KHÔNG share Entity hay Aggregate.

**Ví dụ ITO:** Sales và Resource cùng dùng `ResourceId` và `SkillTag` — nhưng mỗi context có model `Resource` riêng.

**Rủi ro:** Shared Kernel dễ phình to → trở thành "shared monolith". Quy tắc: review Shared Kernel mỗi sprint, nếu > 10 types → cảnh báo.

### Pattern 3: Open Host Service (OHS)

**Khi nào:** 1 context phục vụ nhiều consumers → expose public API.

```
Resource Context (Upstream)
    │
    ├── REST API /resources
    ├── REST API /allocations
    └── REST API /availability
         │
         ├── → Sales Context
         ├── → Delivery Context
         ├── → AI Agent
         └── → Reporting Tool
```

**Luôn đi kèm Published Language:** API contract phải có schema rõ ràng (OpenAPI, Protobuf, JSON Schema).

**Ví dụ Logistics:**
```
Route Context (Core) expose OHS:
  GET  /routes/{routeId}        → Order Context, Tracking Context
  POST /routes/optimize         → Dispatcher UI, AI Optimizer
  GET  /routes/{routeId}/eta    → Customer App, Notification Service
```

### So sánh 3 patterns

| | ACL | Shared Kernel | OHS |
|---|---|---|---|
| **Direction** | Downstream tự bảo vệ | 2 chiều (cùng share) | Upstream expose |
| **Coupling** | Loose (tốt) | Tight (cẩn thận) | Medium |
| **Effort** | Build translation layer | Maintain shared code | Build + document API |
| **Dùng khi** | External/legacy system | 2 closely related contexts | 1 context, nhiều consumers |
| **Risk** | Translator out of sync | Kernel phình to | API breaking changes |

---

## 🏋️ Exercise — Thiết kế Integration

### Phần A: Design ACL cho ITO (15 phút)

ITO cần tích hợp Finance Context với SAP ERP. Thiết kế ACL:

1. SAP trả về: `{ VBELN, KUNNR, NETWR, WAERS, FKDAT }`
2. Domain model cần: `{ contractId, clientName, amount, currency, invoiceDate }`

Viết translation mapping:

```
SAP Field → Domain Field → Transform cần thiết
VBELN     → ___________  → ___________
KUNNR     → ___________  → ___________
NETWR     → ___________  → ___________
WAERS     → ___________  → ___________
FKDAT     → ___________  → ___________
```

### Phần B: Design OHS cho Logistics (10 phút)

Route Context cần expose API cho 3 consumers. Thiết kế:

| Endpoint | Method | Consumer | Trả về gì |
|---|---|---|---|
| | | Order Context | |
| | | Tracking Context | |
| | | Dispatcher UI | |

---

## 🪞 Reflect

1. **"Tại sao không dùng shared database thay vì ACL?"** Shared database là anti-pattern lớn nhất. Tại sao?

2. **ACL có cost. Khi nào cost > benefit?** Gợi ý: nếu external system API stable, ít thay đổi, và model gần giống domain bạn → Conformist có thể đủ.

3. **Trong microservices, mọi service-to-service call đều cần ACL?** Hay chỉ external/legacy?

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích ACL, Shared Kernel, OHS và khi nào dùng
- [ ] Thiết kế ACL mapping cho SAP integration
- [ ] Thiết kế OHS API cho Route Context
- [ ] Trả lời ≥2/3 câu hỏi reflection

---

## 🏁 Hoàn thành Module 5

**Deliverable:** `practice/context-map.md`

→ Tiếp: **Quiz Module 5** rồi sang **Module 6: Knowledge Architecture**
