---
type: lesson
module: 4
lesson: 1
title: "Events và Commands"
duration: "30 phút"
prerequisites: ["module-3"]
narrative_phase: "khám phá hành vi"
migration_phase: "Pre-migration: Khám phá luồng sự kiện"
business_invariant: "Event = đã xảy ra (past tense), không thể đảo ngược; Command = yêu cầu (imperative), có thể bị reject"
---

# Lesson 4.1: Events và Commands — "Mọi thứ bắt đầu từ sự kiện"

## 📍 Context — Bạn đang ở đây

> Module 1-3 cho bạn blueprint hoàn chỉnh cho ITO Corp: Domain Map (8 domains) + Glossary (thống nhất ngôn ngữ) + Strategic Classification (3 Core, 3 Supporting, 2 Generic) + Investment Map (Build/Buy/AI-Augment). CTO **Minh** duyệt xong, phase 1 bắt đầu: Opportunity Mgmt MVP + Resource Mgmt MVP.
>
> Nhưng khi dev team bắt đầu code, câu hỏi đầu tiên: *"Khi Hà ký deal, chuyện gì xảy ra? Theo thứ tự nào? Ai nhận notification? Hệ thống nào phải cập nhật?"* — Domain Map trả lời "có gì" chứ không trả lời "xảy ra gì".

## 🔥 Tension — "Tôi không biết flow"

Sprint 1, dev team bắt đầu code Opportunity Management. Dev lead hỏi:

> **Dev Lead:** *"Hà, flow của 1 deal từ đầu đến cuối là gì?"*
>
> **Hà:** *"Đơn giản: có Lead → qualify → gửi proposal → ký contract → done."*
>
> **Dev Lead:** *"OK. Nhưng khi qualify, Tuấn có cần biết không? Khi gửi proposal, CFO phải approve pricing không? Khi ký contract, hệ thống billing phải tạo invoice không? Và nếu lead bị reject thì flow ngược thế nào?"*
>
> **Hà:** *"Ừ... Tuấn cần biết sớm để chuẩn bị resource. CFO approve nếu deal > 500 triệu. Billing tự động. Reject thì... tùy."*

Dev lead viết lên bảng:

```
Lead → ??? → Qualify → ??? → Proposal → ??? → Contract → ??? → Done
       ^         ^              ^              ^
       |         |              |              |
    Ai biết?  Tuấn cần    CFO approve?    Billing auto?
              biết khi nào?
```

15 dấu hỏi chấm. Hà liệt kê xong flow "đơn giản" → thực ra có **12 bước, 5 actors, 3 hệ thống external**.

> 💭 **Câu hỏi:** Domain Map tĩnh cho bạn bức tranh "có gì". Nhưng dev cần **bức tranh "xảy ra gì"** — theo thứ tự, với actors cụ thể. Kỹ thuật nào giúp khám phá điều này?

## 🎓 Explanation — Event Storming Basics

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Event = đã xảy ra, past tense, KHÔNG thể đảo ngược (OpportunityWon đã xảy ra → các hệ thống khác phản ứng). Command = yêu cầu, imperative, CÓ THỂ bị reject (ApproveProposal → có thể bị CFO reject → ProposalRejected)."*

### Domain Map vs Event Storm

```
Domain Map  = bản đồ thành phố (tĩnh) — đường nào, nhà nào, khu nào
Event Storm = video traffic flow (động) — xe chạy đi đâu, theo thứ tự nào
```

### 5 Building Blocks

| # | Block | Màu Sticky | Ý nghĩa | Ví dụ ITO |
|:---:|---|:---:|---|---|
| 1 | **Domain Event** | 🟧 Orange | "Cái gì đã xảy ra" — past tense | OpportunityQualified, ResourceAllocated |
| 2 | **Command** | 🟦 Blue | "Ai đó yêu cầu làm gì" — imperative | QualifyOpportunity, AllocateResource |
| 3 | **Actor** | 🟨 Yellow | "Ai" gửi Command | Sales Person (Hà), Resource Manager (Tuấn) |
| 4 | **Read Model** | 🟩 Green | "Dữ liệu cần xem trước khi quyết định" | Available Resources List, Pipeline Dashboard |
| 5 | **External System** | 🟪 Purple | Hệ thống bên ngoài | SAP Billing, Email, Google Calendar |

### Flow cơ bản

```
Actor → (xem Read Model) → gửi Command → tạo ra Event

VD:
Tuấn (RM) → (xem Available Resources) → AllocateResource → ResourceAllocated
```

### Quy tắc đặt tên

| Block | Quy tắc | ✅ Đúng | ❌ Sai |
|---|---|---|---|
| Event | Past tense (đã xảy ra) | OpportunityQualified | QualifyOpportunity |
| Command | Imperative (yêu cầu) | QualifyOpportunity | OpportunityQualified |
| Actor | Vai trò, không phải tên cụ thể | Sales Person | Hà |

**Tại sao Event ở past tense?**
Event = **đã xảy ra, không thể đảo ngược**. Khi viết "ResourceAllocated" → mọi người hiểu: *việc này đã xong, các hệ thống khác có thể phản ứng* (notification, billing, calendar update).

### Ví dụ — ITO: Opportunity Lifecycle (mở rộng từ flow "đơn giản" của Hà)

```
1. Sales (Hà)  → (xem Market Leads)
               → CaptureOpportunity         → OpportunityCaptured
                                                    ↓
2. Sales (Hà)  → (xem Qualification Criteria)
               → QualifyOpportunity         → OpportunityQualified
                                                    ↓
3. [POLICY: Nếu deal > 500M → CFO phải approve]
   CFO         → (xem Deal Details)
               → ApproveProposal            → ProposalApproved
                                                    ↓
4. Sales (Hà)  → SubmitProposal             → ProposalSubmitted
                                                    ↓
5. Customer    → (xem Proposal)
               → AcceptProposal             → ProposalAccepted
                                                    ↓
6. Legal       → SignContract               → ContractSigned
                                                    ↓
7. [EXTERNAL: SAP Billing] → Invoice tự tạo
   [POLICY: Notify RM] → Tuấn bắt đầu tìm resource
```

→ Flow "đơn giản" của Hà thực ra có **7 events, 5 actors, 1 external system, 2 policies**.

### Ví dụ — Logistics: Order to Delivery

```
1. Customer     → PlaceOrder                → OrderPlaced
                                                  ↓
2. Dispatcher   → (xem Available Vehicles)
                → AssignVehicle              → VehicleAssigned
                                                  ↓
3. Warehouse    → (xem Packing List)
                → LoadShipment               → ShipmentLoaded
                                                  ↓
4. Driver       → StartDelivery              → DeliveryStarted
                                                  ↓
5. Driver       → ConfirmDelivery            → DeliveryConfirmed
                                                  ↓
6. [EXTERNAL: Notification] → Customer nhận POD
```

### 3 Cách "đào" Events

| Approach | Cách làm | Khi nào dùng |
|---|---|---|
| **Timeline-first** | Viết events theo thứ tự thời gian, từ đầu đến cuối | Process rõ ràng (Logistics) |
| **Pivot Events** | Bắt đầu từ event quan trọng nhất (ContractSigned), mở rộng 2 bên | Process phức tạp (Sales) |
| **Pain-first** | Bắt đầu từ pain point, hỏi "trước/sau pain này có event gì?" | Chưa rõ process |

### ⚖️ Trade-offs — Event Storming Workshop

| | Full Event Storming (1 ngày) | Quick Event Storm (2 giờ) | Không làm Event Storming |
|---|---|---|---|
| **Được** | Khám phá hết flows, policies, external systems | Đủ cho 1-2 Core flows | Bắt đầu code ngay |
| **Mất** | 8 giờ × 6 người = 48 man-hours | Miss flows phụ, edge cases | Dev tự sáng tạo flow → inconsistent giữa features |
| **Khi nào** | Dự án mới, migration lớn, nhiều stakeholders | Sprint Planning cho 1 domain cụ thể | Bug fix, feature nhỏ |

---

## 🏋️ Exercise — Viết Events và Commands

> 📂 **Mở `practice/glossary/` trước khi bắt đầu.** Event và Command names phải dùng đúng thuật ngữ Glossary (Module 2). VD: nếu Glossary định nghĩa "Opportunity" → event = `OpportunityQualified`, KHÔNG phải `DealQualified`.

### Phần A: ITO — Opportunity Lifecycle (15 phút)

Viết Event Storm cho "1 Opportunity từ khi có Lead đến khi Won":

| # | Actor | Read Model | Command | Event |
|:---:|---|---|---|---|
| 1 | ? | ? | ? | ? |
| 2 | ? | ? | ? | ? |
| 3 | ? | ? | ? | ? |
| 4 | ? | ? | ? | ? |
| 5 | ? | ? | ? | ? |
| 6 | ? | ? | ? | ? |

External Systems liên quan: _______________

### Phần B: Logistics — Order to Delivery (10 phút)

| # | Actor | Read Model | Command | Event |
|:---:|---|---|---|---|
| 1 | ? | ? | ? | ? |
| 2 | ? | ? | ? | ? |
| 3 | ? | ? | ? | ? |
| 4 | ? | ? | ? | ? |
| 5 | ? | ? | ? | ? |

### Self-check

| Tiêu chí | Phần A | Phần B |
|---|:---:|:---:|
| Events đúng past tense? | ☐ | ☐ |
| Commands đúng imperative? | ☐ | ☐ |
| Mỗi event có actor? | ☐ | ☐ |
| ≥ 5 events? | ☐ | ☐ |
| Tên khớp với Glossary? | ☐ | ☐ |

---

## 🪞 Reflect

1. **"ResourceAllocated" là event. Nhưng nếu allocation bị reject thì sao?** → `AllocationRejected` CŨNG là event. Mọi kết quả (success hay fail) đều là event. Pattern: mỗi Command → ≥2 events tiềm năng (success + failure).

2. **Command vs Event — ai có quyền nói "không"?** Command CÓ THỂ bị reject (CFO nói không approve proposal). Event KHÔNG THỂ bị đảo ngược — nó đã xảy ra. **Command = ý định, Event = sự thật.**

3. **Bạn viết bao nhiêu events cho Phần A?** Kinh nghiệm: 1 Core Domain thường có **15-30 events**. Nếu bạn viết <10 → chắc chắn thiếu. Thiếu ở đâu? Edge cases: reject, timeout, partial success.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt Event (past tense) vs Command (imperative) vs Actor vs Read Model vs External System
- [ ] **Apply:** Viết Event Storm ≥5 events cho ITO Opportunity + ≥5 events cho Logistics Order-to-Delivery
- [ ] **Analyze:** Giải thích tại sao flow "đơn giản" của Hà thực ra có 7 events + 5 actors — liên hệ với vấn đề "15 dấu hỏi chấm"

---

> 🔗 **Tiếp theo:** Bạn đã khám phá events và commands. Nhưng giữa các events có **rules tự động** (khi OpportunityQualified → tự động notify RM) và **điểm nóng** (pain points trên flow). Bài tiếp — *Policies và Hotspots* — sẽ dạy bạn cách phát hiện automation rules và bottleneck trên event flow.
