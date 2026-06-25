---
type: lesson
module: 5
lesson: 2
title: "Context Mapping"
duration: "35 phút"
prerequisites: ["module-5/lesson-1"]
---

# Lesson 5.2: Context Mapping

## 🎓 Concept — "Bản đồ quan hệ giữa các Context"

### Tại sao cần Context Map?

Bounded Contexts không sống cô lập. Chúng **giao tiếp** với nhau:
- Resource Context cần biết Project nào cần resource (từ Sales Context)
- Sales Context cần biết resource nào available (từ Resource Context)

Context Map = **bản đồ mô tả ai nói chuyện với ai, và theo quy tắc nào**.

### Upstream và Downstream

```
Upstream (U) ──────→ Downstream (D)
 "Cung cấp data"      "Tiêu thụ data"
 "Có power"            "Phải thích nghi"
```

| Vai trò | Nghĩa | Ví dụ |
|---|---|---|
| **Upstream** | Cung cấp data/service, quyết định format | Resource Context cung cấp "available resources" |
| **Downstream** | Tiêu thụ data, phải adapt theo format upstream | Sales Context consume "available resources" để match |

### 7 Integration Patterns

| # | Pattern | Quan hệ | Khi nào dùng |
|:---:|---|---|---|
| 1 | **Partnership** | U ↔ D (ngang hàng) | 2 teams cùng priority, phối hợp chặt |
| 2 | **Shared Kernel** | U ↔ D (chia sẻ code) | 2 contexts share 1 phần model chung |
| 3 | **Customer-Supplier** | U → D (có commitment) | Upstream cam kết phục vụ downstream |
| 4 | **Conformist** | U → D (downstream phải theo) | Downstream chấp nhận model của upstream |
| 5 | **Anti-Corruption Layer (ACL)** | U → [ACL] → D | Downstream tự bảo vệ, translate model |
| 6 | **Open Host Service (OHS)** | U → D (API public) | Upstream expose public API cho mọi consumer |
| 7 | **Published Language** | (Shared format) | JSON Schema, Protobuf — format chuẩn |

### Chọn pattern nào? — Decision Guide

```
Bạn control upstream?
├── Có → Team bạn own cả 2? 
│        ├── Có → Partnership hoặc Shared Kernel
│        └── Không → Customer-Supplier (cam kết SLA)
│
└── Không → Upstream thay đổi thường xuyên?
             ├── Có → ACL (bảo vệ domain bạn)
             └── Không → Conformist (chấp nhận, không fight)

Upstream phục vụ nhiều consumers?
├── Có → Open Host Service + Published Language
└── Không → Customer-Supplier hoặc Partnership
```

### Ví dụ — ITO Context Map

```
┌─────────────────────────────────────────────────┐
│                ITO CRM System                    │
│                                                  │
│  ┌──────────────┐    Partnership    ┌──────────┐│
│  │  Sales       │◄════════════════►│ Resource  ││
│  │  Context     │                   │ Context   ││
│  └──────┬───────┘                   └────┬─────┘│
│         │                                │       │
│    Customer-                        Customer-    │
│    Supplier                         Supplier     │
│         │                                │       │
│  ┌──────▼───────┐                   ┌────▼─────┐│
│  │  Finance     │                   │ Delivery  ││
│  │  Context     │                   │ Context   ││
│  └──────────────┘                   └──────────┘│
│                                                  │
│        ┌──────────┐    ACL    ┌──────────────┐  │
│        │ SAP ERP  │◄════════►│ Finance Ctx   │  │
│        │(External)│           │               │  │
│        └──────────┘           └──────────────┘  │
│                                                  │
│        ┌──────────┐    OHS    ┌──────────────┐  │
│        │ Resource │═══════════►│ AI Agent     │  │
│        │ Context  │  (API)    │ (Future)      │  │
│        └──────────┘           └──────────────┘  │
└─────────────────────────────────────────────────┘
```

**Đọc bản đồ:**
- Sales ↔ Resource: **Partnership** — 2 Core, cùng priority, cần sync chặt
- Sales → Finance: **Customer-Supplier** — Sales provide deal data, Finance consume
- SAP ↔ Finance: **ACL** — SAP là external, Finance tự bảo vệ bằng translation layer
- Resource → AI Agent: **OHS** — Resource expose API cho future AI consumers

### Ví dụ — Logistics Context Map

```
┌──────────────────────────────────────────────────┐
│              Logistics System                     │
│                                                   │
│  ┌──────────┐  Partnership  ┌──────────────────┐ │
│  │  Order   │◄═════════════►│  Route           │ │
│  │  Context │               │  Context (Core)  │ │
│  └────┬─────┘               └────────┬─────────┘ │
│       │                              │            │
│  Customer-Supplier              Partnership       │
│       │                              │            │
│  ┌────▼─────┐               ┌────────▼─────────┐ │
│  │Warehouse │               │  Fleet Context    │ │
│  │ Context  │               │                   │ │
│  └──────────┘               └──────────────────┘ │
│                                                   │
│  ┌──────────┐    ACL    ┌──────────────────────┐ │
│  │Google    │◄═════════►│  Tracking Context    │ │
│  │Maps API  │           │                      │ │
│  └──────────┘           └──────────────────────┘ │
└──────────────────────────────────────────────────┘
```

---

## 🏋️ Exercise — Vẽ Context Map

### Phần A: ITO — Xác định Integration Patterns (15 phút)

Điền pattern cho mỗi cặp context:

| Context A | ↔ | Context B | Pattern | Lý do |
|---|:---:|---|---|---|
| Sales | ↔ | Resource | | |
| Sales | → | Finance | | |
| Resource | → | Delivery | | |
| Finance | ↔ | SAP (external) | | |
| Resource | → | AI Agent | | |

### Phần B: Logistics (10 phút)

| Context A | ↔ | Context B | Pattern | Lý do |
|---|:---:|---|---|---|
| Order | ↔ | Route | | |
| Route | ↔ | Fleet | | |
| Tracking | ↔ | Google Maps | | |
| Order | → | Warehouse | | |
| Order | → | Billing | | |

### Phần C: Vẽ Context Map diagram (10 phút)

Vẽ Context Map bằng ASCII hoặc text cho ITO (giống ví dụ ở trên):

```
(vẽ ở đây)
```

---

## 🪞 Reflect

1. **ACL là pattern phổ biến nhất khi tích hợp external system. Tại sao?** Gợi ý: bạn không control external system, model của họ có thể thay đổi bất cứ lúc nào.

2. **Partnership nghe hay nhưng có cost gì?** Gợi ý: 2 teams phải sync liên tục — meeting, contract changes, deployment coordination.

3. **Nếu 1 context là upstream của 5 downstream contexts — pattern nào phù hợp nhất?** Tại sao?

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Upstream vs Downstream
- [ ] Liệt kê ≥5 integration patterns
- [ ] Xác định pattern cho ≥4 cặp contexts (ITO)
- [ ] Xác định pattern cho ≥4 cặp contexts (Logistics)
- [ ] Vẽ Context Map diagram
