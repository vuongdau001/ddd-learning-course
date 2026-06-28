---
type: lesson
module: 4
lesson: 1
title: "Events và Commands"
duration: "30 phút"
prerequisites: ["module-3"]
---

# Lesson 4.1: Events và Commands

## 🎓 Concept — "Mọi thứ bắt đầu từ sự kiện"

### Tại sao Event Storming?

Bạn đã có Domain Map, Glossary, Priority Matrix. Nhưng những thứ đó mô tả **cấu trúc tĩnh** — domain nào, gồm gì, ưu tiên nào.

Event Storming mô tả **quy trình động** — trong domain đó, chuyện gì xảy ra theo thứ tự nào?

```
Domain Map  = bản đồ thành phố (tĩnh)
Event Storm = video traffic flow (động)
```

### 5 Building Blocks

| # | Block | Màu Sticky Note | Ý nghĩa | Ví dụ |
|:---:|---|:---:|---|---|
| 1 | **Domain Event** | 🟧 Orange | "Cái gì đã xảy ra" — past tense | ResourceAllocated, OpportunityWon |
| 2 | **Command** | 🟦 Blue | "Ai đó yêu cầu làm gì" — imperative | AllocateResource, SubmitProposal |
| 3 | **Actor** | 🟨 Yellow (small) | "Ai" gửi Command | Resource Manager, Sales Person |
| 4 | **Read Model** | 🟩 Green | "Dữ liệu cần xem trước khi quyết định" | Available Resources List, Pipeline Dashboard |
| 5 | **External System** | 🟪 Purple | Hệ thống bên ngoài tác động | SAP, Email, Google Calendar |

### Flow cơ bản

```
Actor → (xem Read Model) → gửi Command → tạo ra Event

VD:
Resource Manager → (xem Available Resources) → AllocateResource → ResourceAllocated
```

### Quy tắc đặt tên

| Block | Quy tắc | ✅ Đúng | ❌ Sai |
|---|---|---|---|
| Event | Past tense (đã xảy ra) | ResourceAllocated | AllocateResource |
| Command | Imperative (yêu cầu) | AllocateResource | ResourceAllocated |
| Actor | Vai trò, không phải tên người | Resource Manager | Anh Hùng |

**Tại sao Event phải ở past tense?**

Event = đã xảy ra, không thể đảo ngược. Khi bạn viết "ResourceAllocated" → mọi người hiểu: *việc này đã xong, các hệ thống khác có thể phản ứng*.

### Ví dụ — ITO: Resource Allocation Flow

```
1. PM             → (xem Project Needs)
                  → RequestResource           → ResourceRequested
                                                     ↓
2. Resource Mgr   → (xem Available Resources)
                  → AllocateResource          → ResourceAllocated
                                                     ↓
3. Resource        → (nhận notification)
                  → ConfirmAllocation         → AllocationConfirmed
                                                     ↓
4. PM             → (xem Updated Team)
                  → StartSprint               → SprintStarted
```

### Ví dụ — Logistics: Shipment Delivery Flow

```
1. Customer       → PlaceOrder               → OrderPlaced
                                                   ↓
2. Dispatcher     → (xem Available Vehicles)
                  → AssignVehicle             → VehicleAssigned
                                                   ↓
3. Warehouse      → (xem Packing List)
                  → LoadShipment              → ShipmentLoaded
                                                   ↓
4. Driver         → StartDelivery             → DeliveryStarted
                                                   ↓
5. Driver         → ConfirmDelivery           → DeliveryConfirmed
                                                   ↓
6. Customer       → (nhận notification + POD)
```

### Cách "đào" Events — 3 approaches

| Approach | Cách làm | Khi nào dùng |
|---|---|---|
| **Timeline-first** | Viết events theo thứ tự thời gian, từ đầu đến cuối | Khi process rõ ràng |
| **Pivot Events** | Bắt đầu từ event quan trọng nhất, mở rộng ra 2 bên | Khi process phức tạp |
| **Pain-first** | Bắt đầu từ pain point, hỏi "trước/sau pain này có event gì?" | Khi chưa rõ process |

---

## 🏋️ Exercise — Viết Events và Commands

> 📂 **Mở `practice/glossary/` trước khi bắt đầu.** Event và Command names phải dùng đúng thuật ngữ trong Glossary (Module 2). VD: nếu Glossary định nghĩa "Opportunity" thì event phải là `OpportunityQualified`, không phải `DealQualified`.

### Phần A: ITO — Opportunity Lifecycle (15 phút)

Viết Event Storm cho "1 Opportunity từ khi có Lead đến khi Won":

| # | Actor | Command | Event |
|:---:|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |
| 6 | | | |

### Phần B: Logistics — Order to Delivery (10 phút)

Viết Event Storm cho "1 Order từ khi khách đặt đến khi giao xong":

| # | Actor | Command | Event |
|:---:|---|---|---|
| 1 | | | |
| 2 | | | |
| 3 | | | |
| 4 | | | |
| 5 | | | |

### Self-check

| Tiêu chí | Phần A | Phần B |
|---|:---:|:---:|
| Events đúng past tense? | ☐ | ☐ |
| Commands đúng imperative? | ☐ | ☐ |
| Mỗi event có actor? | ☐ | ☐ |
| ≥ 5 events? | ☐ | ☐ |
| Tên khớp với `practice/glossary/`? | ☐ | ☐ |

---

## 🪞 Reflect

1. **"ResourceAllocated" là event. Nhưng nếu allocation bị reject thì sao?** Event nào được tạo? Gợi ý: "AllocationRejected" cũng là event.

2. **Sự khác biệt giữa Command và Event: ai có quyền nói "không"?** Command có thể bị reject. Event thì không — nó đã xảy ra rồi.

3. **Trong Exercise Phần A, bạn viết bao nhiêu events? Theo kinh nghiệm, 1 Core Domain thường có 15-30 events. Bạn có thiếu event nào không?**

---

## ✅ Hoàn thành lesson khi
- [ ] Phân biệt Event vs Command vs Actor vs Read Model
- [ ] Viết ≥5 events đúng naming convention
- [ ] Hoàn thành Event Storm cho Opportunity lifecycle
- [ ] Hoàn thành Event Storm cho Order-to-Delivery
