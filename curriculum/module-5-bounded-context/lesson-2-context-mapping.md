---
type: lesson
module: 5
lesson: 2
title: "Context Mapping"
duration: "35 phút"
prerequisites: ["module-5/lesson-1"]
narrative_phase: "kiến trúc"
migration_phase: "Phase 1: Vẽ bản đồ quan hệ giữa contexts"
business_invariant: "Mỗi cặp context PHẢI có integration pattern rõ ràng; ACL bắt buộc cho external systems; Partnership chỉ giữa 2 Core contexts cùng priority"
---

# Lesson 5.2: Context Mapping — "Bản đồ quan hệ giữa các Context"

## 📍 Context — Bạn đang ở đây

> Lesson 5.1 giúp bạn tách 8 ITO subdomains → 6 Bounded Contexts. Resource Management tách thành Skill Inventory Context + Allocation Context để giải quyết merge conflict. Nhưng bây giờ: **các contexts cần nói chuyện với nhau.** Khi Hà thắng deal (Sales Context) → Tuấn cần biết để tìm resource (Resource Context) → PM cần biết resource nào assigned (Delivery Context).
>
> Câu hỏi: contexts giao tiếp thế nào? Ai phụ thuộc ai? Ai control data format?

## 🔥 Tension — "API thay đổi, tôi không biết"

Phase 1 launch. Sales Context và Resource Context đang chạy. Tuần đầu OK. Tuần 2:

> **Dev C (team Resource):** *"Tôi đổi response format của endpoint `/available-resources`. Thêm field `aiMatchScore` vì AI matching cần."*
>
> **Dev D (team Sales):** *"Hệ thống Sales bị crash! Tôi parse response theo format cũ — không có field `aiMatchScore`. Tại sao không ai báo tôi?"*

**Minh:**
> *"Chúng ta đã tách contexts rồi. Tại sao thay đổi 1 context → break context khác? Quy tắc giao tiếp giữa contexts là gì?"*

Bạn kiểm tra: **không có quy tắc.** 2 teams gọi API trực tiếp, không có contract, không có version, không có notification khi thay đổi. Tách context mà không define relationship = **tệ hơn monolith** (ít nhất monolith compile fail khi sai).

> 💭 **Câu hỏi:** Tách Bounded Context = bước 1. Bước 2 = define relationships giữa contexts. Ai upstream? Ai downstream? Ai control format? Ai phải adapt?

## 🎓 Explanation — Context Map

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mỗi cặp Bounded Context PHẢI có integration pattern rõ ràng. ACL bắt buộc khi tích hợp external system (SAP, Google Maps). Partnership chỉ giữa 2 Core contexts cùng priority — vì cost sync cao."*

### Upstream và Downstream

```
Upstream (U) ──────→ Downstream (D)
 "Cung cấp data"      "Tiêu thụ data"
 "Có power"            "Phải thích nghi"
```

| Vai trò | Nghĩa | ITO Ví dụ |
|---|---|---|
| **Upstream** | Cung cấp data/service, quyết định format | Resource Context: cung cấp "available resources" |
| **Downstream** | Tiêu thụ data, phải adapt theo upstream | Sales Context: consume "available resources" để suggest cho Hà |

### 7 Integration Patterns

| # | Pattern | Quan hệ | Khi nào dùng | ITO Ví dụ |
|:---:|---|---|---|---|
| 1 | **Partnership** | U ↔ D (ngang hàng) | 2 teams cùng priority, sync chặt | Sales ↔ Resource |
| 2 | **Shared Kernel** | Chia sẻ code/model | 2 contexts share 1 phần nhỏ model | ⚠️ Anti-pattern nếu lạm dụng |
| 3 | **Customer-Supplier** | U → D (có SLA) | Upstream cam kết phục vụ downstream | Resource → Delivery |
| 4 | **Conformist** | U → D (downstream theo) | Downstream chấp nhận model upstream, không fight | Dùng Google Calendar API as-is |
| 5 | **ACL** | U → [ACL] → D | Downstream tự bảo vệ, translate model | SAP ↔ Finance Context |
| 6 | **OHS** | U → D (API public) | Upstream expose public API cho N consumers | Resource Context → AI Agent |
| 7 | **Published Language** | Shared format | Format chuẩn mọi người dùng | JSON Schema, Protobuf |

### Decision Guide — Chọn pattern nào?

```
Q1: Bạn control upstream?
├── Có → Q2: Team bạn own cả 2 contexts?
│        ├── Có → Partnership (nếu cùng priority)
│        │        hoặc Customer-Supplier (nếu khác priority)
│        └── Không → Customer-Supplier (cam kết SLA, versioning)
│
└── Không (external system) →
     Q3: Upstream thay đổi thường xuyên?
     ├── Có → ACL (BẮT BUỘC — bảo vệ domain bạn)
     └── Không → Conformist (chấp nhận model, không fight)

Q4: Upstream phục vụ nhiều consumers?
├── Có → Open Host Service + Published Language
└── Không → Customer-Supplier hoặc Partnership
```

### Apply cho ITO — Context Map

```
┌──────────────────────────────────────────────────────┐
│                   ITO CRM System                      │
│                                                       │
│  ┌──────────────┐   Partnership    ┌───────────────┐ │
│  │  Sales       │◄═══════════════►│  Resource      │ │
│  │  Context     │  (2 Core, cùng  │  Context       │ │
│  │  (Hà)       │   priority)     │  (Tuấn)       │ │
│  └──────┬───────┘                 └───┬────┬──────┘ │
│         │                             │    │         │
│    Customer-                    Customer-  OHS       │
│    Supplier                     Supplier   (API)     │
│    (Sales provide               (Resource  │         │
│     deal data)                   provide   │         │
│         │                        allocation)│        │
│  ┌──────▼───────┐          ┌─────▼────┐ ┌──▼──────┐ │
│  │  Finance     │          │ Delivery  │ │AI Agent │ │
│  │  Context     │          │ Context   │ │(Future) │ │
│  └──────┬───────┘          └──────────┘ └─────────┘ │
│         │                                            │
│    ACL (translate                                    │
│     SAP model)                                       │
│         │                                            │
│  ┌──────▼───────┐                                    │
│  │  SAP ERP     │ (External System)                  │
│  └──────────────┘                                    │
└──────────────────────────────────────────────────────┘
```

**Đọc bản đồ:**
- **Sales ↔ Resource: Partnership** — 2 Core domains, cùng priority, cần sync chặt (khi deal won → resource request). Cost: 2 teams sync weekly.
- **Sales → Finance: Customer-Supplier** — Sales provide deal data, Finance consume để tạo invoice. Sales cam kết không break format.
- **Resource → Delivery: Customer-Supplier** — Resource provide allocation data, Delivery consume để plan sprints.
- **Finance ↔ SAP: ACL** — SAP là external, Finance tự bảo vệ bằng translation layer. Nếu SAP đổi API → chỉ sửa ACL, không sửa Finance logic.
- **Resource → AI Agent: OHS** — Resource expose public API cho future AI consumers. Published Language = JSON Schema.

### Logistics Context Map

```
┌───────────────────────────────────────────────────┐
│              Logistics System                      │
│                                                    │
│  ┌──────────┐  Partnership  ┌───────────────────┐ │
│  │  Order   │◄════════════►│  Route Context     │ │
│  │  Context │              │  (Core, AI)        │ │
│  └────┬─────┘              └────────┬───────────┘ │
│       │                             │              │
│  Customer-Supplier           Partnership           │
│       │                             │              │
│  ┌────▼─────┐              ┌────────▼───────────┐ │
│  │Warehouse │              │  Fleet Context      │ │
│  │ Context  │              │                     │ │
│  └──────────┘              └────────────────────┘ │
│                                                    │
│  ┌──────────┐    ACL    ┌───────────────────────┐ │
│  │Google    │◄═════════►│  Tracking Context     │ │
│  │Maps API  │           │                       │ │
│  └──────────┘           └───────────────────────┘ │
└───────────────────────────────────────────────────┘
```

### ⚖️ Trade-offs — Partnership vs Customer-Supplier

| | Partnership | Customer-Supplier |
|---|---|---|
| **Được** | Cả 2 teams có tiếng nói, model evolve cùng nhau | Rõ ràng ai control, downstream có SLA |
| **Mất** | Sync cost cao (weekly meetings, joint planning) | Downstream phụ thuộc, ít influence lên format |
| **Khi nào** | 2 Core contexts, cùng priority, cùng release cycle | Khác priority, khác team size, upstream ổn định |

---

## 🏋️ Exercise — Vẽ Context Map

### Phần A: ITO — Xác định Integration Patterns (15 phút)

| Context A | ↔ | Context B | Pattern | Lý do |
|---|:---:|---|---|---|
| Sales | ↔ | Resource | ? | ? |
| Sales | → | Finance | ? | ? |
| Resource | → | Delivery | ? | ? |
| Finance | ↔ | SAP (external) | ? | ? |
| Resource | → | AI Agent | ? | ? |
| Customer Success | → | Sales | ? | ? |

### Phần B: Logistics (10 phút)

| Context A | ↔ | Context B | Pattern | Lý do |
|---|:---:|---|---|---|
| Order | ↔ | Route | ? | ? |
| Route | ↔ | Fleet | ? | ? |
| Tracking | ↔ | Google Maps | ? | ? |
| Order | → | Warehouse | ? | ? |
| Order | → | Billing | ? | ? |

### Phần C: Vẽ Context Map diagram (10 phút)

Vẽ Context Map bằng ASCII cho ITO (tham khảo ví dụ trên):

```
(vẽ ở đây — bao gồm contexts, arrows, pattern labels)
```

---

## 🪞 Reflect

1. **ACL là pattern phổ biến nhất khi tích hợp external system — tại sao?** → Vì bạn KHÔNG control external system. SAP, Google Maps, Salesforce có thể đổi API bất cứ lúc nào. ACL = translation layer — **khi external đổi, chỉ sửa ACL, không sửa domain logic.** Không có ACL = SAP thay đổi 1 field → toàn bộ Finance Context phải refactor.

2. **Partnership nghe hay nhưng cost gì?** → 2 teams phải sync liên tục: weekly planning, shared API contracts, coordinated deployments. Nếu 1 team chậm → team kia bị block. **Partnership chỉ đáng khi CẢ HAI đều Core, cùng priority.** Nếu 1 bên Supporting → dùng Customer-Supplier rẻ hơn.

3. **1 context upstream của 5 downstream → pattern nào?** → **Open Host Service + Published Language.** Vì: 5 consumers = 5 contracts riêng → quá nặng. OHS = 1 API public, versioned, ai cần thì consume. Published Language = JSON Schema/Protobuf → format chuẩn, backwards compatible.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Upstream/Downstream + ≥5 integration patterns
- [ ] **Apply:** Xác định pattern cho ≥5 cặp ITO contexts + ≥4 cặp Logistics contexts + vẽ diagram
- [ ] **Analyze:** Giải thích tại sao API crash Dev D xảy ra — thiếu gì? Liên hệ với ACL và Customer-Supplier patterns

---

> 🔗 **Tiếp theo:** Bạn đã biết CÁC pattern. Bài tiếp — *Integration Patterns (Deep Dive)* — sẽ đi sâu vào **cách implement** 3 patterns quan trọng nhất: ACL (cho SAP), Partnership Events (cho Sales ↔ Resource), và OHS (cho AI Agent future-proofing).
