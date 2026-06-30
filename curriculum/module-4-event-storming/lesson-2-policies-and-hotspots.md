---
type: lesson
module: 4
lesson: 2
title: "Policies và Hot Spots"
duration: "30 phút"
prerequisites: ["module-4/lesson-1"]
narrative_phase: "khám phá hành vi"
migration_phase: "Pre-migration: Phát hiện automation rules + xung đột"
business_invariant: "Policy = automation rule (Whenever Event → Command); Hot Spot = xung đột chưa giải quyết"
---

# Lesson 4.2: Policies và Hot Spots — "Luật tự động và điểm nóng"

## 📍 Context — Bạn đang ở đây

> Lesson 4.1 cho bạn Event Storm cơ bản: Opportunity Lifecycle có 7 events, 5 actors. Dev team bắt đầu implement. Nhưng giữa các events có **quy tắc ẩn** mà Hà coi là "hiển nhiên" nhưng dev không biết: *"Khi deal thắng → Tuấn phải biết NGAY để tìm resource. Nếu deal > 500 triệu → CFO phải approve pricing."*
>
> Những quy tắc này nếu không ghi lại → dev sẽ miss → hệ thống chạy nhưng **thiếu automation** → stakeholders phải làm tay → quay lại Excel.

## 🔥 Tension — "Tôi tưởng hệ thống tự notify?"

Sprint 2. Hà ký deal mới — **Project Delta, 800 triệu**. Nhưng:

> **Hà:** *"Tôi đã mark OpportunityWon trên hệ thống mới 3 ngày rồi. Tại sao Tuấn chưa tìm resource?"*
>
> **Tuấn:** *"Tôi không biết có deal mới! Hệ thống không gửi notification cho tôi!"*
>
> **Dev Lead:** *"Spec không nói gì về notification khi OpportunityWon. Tôi chỉ update status."*

**Minh** hỏi: *"Chúng ta đã làm Event Storm. Tại sao miss notification?"*

Bạn kiểm tra Event Storm board: đúng là chỉ có events và commands. **Không có rules tự động giữa các events.** Hà biết rule này ("khi thắng deal → báo RM"), nhưng nó **nằm trong đầu Hà**, chưa bao giờ được ghi lại.

> 💭 **Câu hỏi:** Có bao nhiêu rules ẩn như vậy? Làm sao đào chúng ra khỏi đầu domain experts trước khi trở thành bug?

## 🎓 Explanation — Policies và Hot Spots

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Policy = automation rule: 'Whenever Event X → tự động Command Y'. Mỗi Policy phải VISIBLE trên Event Storm board — không được nằm trong đầu stakeholder. Hot Spot = xung đột/unknown chưa giải quyết — PHẢI đánh dấu, KHÔNG được bỏ qua."*

### Building Block #6: Policy (🟣 Lilac)

**Policy** = quy tắc tự động: *"Khi Event X xảy ra → tự động gửi Command Y"*

Format: **Whenever [Event] → then [Command]**

### ITO Policies — những rules Hà "quên nói"

```
Policy 1: "Auto-Staffing Request"
  Whenever OpportunityWon → then CreateStaffingRequest
  (Tuấn bắt đầu tìm resource ngay khi deal thắng — rule bị miss ở Sprint 2!)

Policy 2: "CFO Approval Gate"
  Whenever ProposalCreated(amount > 500M) → then RequestCFOApproval
  (Deal lớn cần CFO duyệt pricing trước khi gửi khách)

Policy 3: "Auto-Alert Bench"
  Whenever AllocationEnded → then CheckIfBench
  (Nếu resource không có allocation mới > 5 ngày → trạng thái Bench → alert RM)

Policy 4: "Pipeline Hygiene"
  Whenever OpportunityStalled(> 30 ngày) → then NotifySalesManager
  (Dọn dẹp deal cũ — tránh pipeline "ảo")

Policy 5: "Utilization Warning"
  Whenever UtilizationCalculated(> 95%) → then AlertManager
  (Quá tải → risk burnout → quality giảm)
```

### Logistics Policies

```
Policy 1: "ETA Breach Alert"
  Whenever ETAUpdated(delay > 30 phút) → then NotifyCustomer
  (Khách biết sớm → ít complain)

Policy 2: "Maintenance Trigger"
  Whenever TripCompleted → then CheckMaintenanceSchedule
  (Kiểm tra xe đến hạn bảo trì)

Policy 3: "Auto-Reoptimize"
  Whenever OrderCancelled → then ReoptimizeRoutes
  (1 đơn hủy → optimize lại tuyến cho xe đó)

Policy 4: "POD Escalation"
  Whenever DeliveryConfirmed(without POD photo) → then EscalateToSupervisor
  (Không có ảnh POD → chưa xác nhận giao thành công)
```

**Tại sao Policy quan trọng?**
Policy = nơi **business rules sống**. Dev hay hardcode rules trong if-else ẩn sâu trong code. DDD muốn rules **nhìn thấy được** trên board → ai cũng biết rule tồn tại → dễ thay đổi.

### Building Block #7: Hot Spot 🔥 (🔴 Red, xoay 45°)

**Hot Spot** = chỗ team **không đồng ý, có xung đột, hoặc chưa rõ ràng.**

### 3 loại Hot Spot

| Loại | Dấu hiệu | Ví dụ ITO | Hành động |
|---|---|---|---|
| **Conflict** 💥 | 2 người nói khác nhau | "PM nói PM approve allocation, RM nói RM approve" | Cần decision từ CTO Minh |
| **Unknown** ❓ | Không ai biết đúng | "Nếu resource reject allocation thì flow thế nào?" | Cần discovery thêm |
| **Technical Debt** ⚙️ | Biết đúng nhưng chưa làm | "Nên auto-match resource nhưng đang manual" | Ghi nhận, plan Phase 2 |

### ITO Hot Spots 🔥

```
🔥 CONFLICT: "Resource có quyền reject allocation?"
   → PM: "Không, resource phải theo assignment"
   → Resource: "Tôi cần được chọn project phù hợp"
   → HR: "Labor law cho phép từ chối nếu có lý do chính đáng"
   → ACTION: Cần Minh quyết định + HR review

🔥 CONFLICT: "Khi nào Opportunity chuyển thành Project?"
   → Hà: "Khi khách nói OK verbal"
   → CFO: "Khi Contract signed"
   → Legal: "Khi cả hai bên ký và seal"
   → ACTION: Define rõ trong Glossary (event nào trigger transition?)

🔥 UNKNOWN: "Non-billable work track ở đâu?"
   → PM: "Jira"  → RM: "Excel riêng"  → Finance: "Không track"
   → ACTION: Define Glossary entry "Non-billable Work" + chọn system of record
```

### Logistics Hot Spots 🔥

```
🔥 CONFLICT: "Ai chịu trách nhiệm khi hàng hư hại?"
   → Warehouse: "Tài xế, vì đã ký nhận"
   → Tài xế: "Warehouse, vì đóng gói sai"

🔥 UNKNOWN: "Partial delivery accepted không?"
   → Sales: "Có, khách cần gấp"
   → Operations: "Không, phức tạp invoice"
```

### Event Storm hoàn chỉnh: Events + Policies + Hot Spots

```
ResourceRequested                        (🟧 Event)
     ↓
[Policy: Auto-Match]                     (🟣 Whenever ResourceRequested → FindMatchingResources)
     ↓
MatchingResourcesFound                   (🟧 Event)
     ↓
Tuấn (RM) → (xem Matching Results)      (🟨 Actor → 🟩 Read Model)
          → AllocateResource             (🟦 Command)
     ↓
ResourceAllocated                        (🟧 Event)
     ↓
[Policy: Notify]                         (🟣 Whenever ResourceAllocated → NotifyResource + NotifyPM)
     ↓
🔥 "Resource có quyền reject?"          (🔴 Hot Spot — Conflict)
     ↓
AllocationConfirmed / AllocationRejected (🟧 Event — 2 paths)
```

### ⚖️ Trade-offs — Policy Explicitness

| | Policies trên board (explicit) | Policies trong code (implicit) |
|---|---|---|
| **Được** | Business thấy rules, dễ thay đổi, AI đọc được | Dev kiểm soát logic, nhanh hơn implement |
| **Mất** | Mất time document + maintain sync board↔code | Rules ẩn, Business không biết rules gì đang chạy, sửa = risk |
| **Khi nào** | Core Domain, critical business rules | Supporting/Generic, rules đơn giản (VD: email format validation) |

---

## 🏋️ Exercise — Thêm Policies và Hot Spots

### Phần A: ITO — Mở rộng Event Storm (15 phút)

Lấy Opportunity Lifecycle từ Lesson 4.1, thêm Policies + Hot Spots:

| Event | Policy (Whenever → then) | Hot Spot 🔥 (nếu có) |
|---|---|---|
| OpportunityCaptured | ? | ? |
| OpportunityQualified | ? | ? |
| ProposalCreated | ? | ? |
| ProposalSubmitted | ? | ? |
| OpportunityWon | Whenever Won → CreateStaffingRequest | ? |
| OpportunityLost | ? | ? |

**Tổng:** ≥3 Policies + ≥2 Hot Spots

### Phần B: Logistics — Mở rộng Event Storm (10 phút)

| Event | Policy (Whenever → then) | Hot Spot 🔥 (nếu có) |
|---|---|---|
| OrderPlaced | ? | ? |
| VehicleAssigned | ? | ? |
| ShipmentLoaded | ? | ? |
| DeliveryStarted | ? | ? |
| DeliveryConfirmed | ? | ? |

### Phần C: Phân loại Hot Spots (5 phút)

| Hot Spot | Loại (Conflict/Unknown/Tech Debt) | Ai cần quyết định? | Ảnh hưởng sprint nào? |
|---|---|---|---|
| ? | ? | ? | ? |
| ? | ? | ? | ? |

---

## 🪞 Reflect

1. **Policy nào trong ví dụ trên hiện đang "manual" (con người làm tay)?** → "Auto-Staffing Request" trước Sprint 2 = manual (Hà phải gọi Tuấn). Đó là cơ hội automation có value cao nhất — trực tiếp giải quyết sự cố 3-ngày-delay.

2. **Hot Spot "Resource có quyền reject?" — bạn giải quyết thế nào?** → Không có đáp án đúng sai. Đây là **business decision**: nếu cho reject → team flexibility tăng, nhưng PM khó plan. Nếu không cho → PM dễ plan, nhưng resource unhappy → turnover tăng. **Trade-off cần CTO quyết định + HR review.**

3. **Event Storm không có Hot Spot nào — tốt hay xấu?** → **Xấu.** Không có hot spot = team không đào đủ sâu. Mọi quy trình phức tạp đều có ≥3 hot spots. Nếu 0 → hỏi lại: "Có trường hợp nào edge case? Khi nào quy trình này fail?"

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Policy (format Whenever-Then) + 3 loại Hot Spot (Conflict/Unknown/Tech Debt)
- [ ] **Apply:** Thêm ≥3 Policies + ≥2 Hot Spots cho ITO Event Storm
- [ ] **Analyze:** Phân loại Hot Spots + xác định ai cần quyết định — liên hệ với sự cố "Tuấn không biết deal mới trong 3 ngày"

---

> 🔗 **Tiếp theo:** Bạn có Event Storm hoàn chỉnh: Events + Commands + Actors + Policies + Hot Spots. Bài tiếp — *Guided Workshop* — sẽ hướng dẫn bạn **chạy 1 buổi Event Storming thực tế** từ đầu đến cuối: agenda, facilitation tips, và cách tổng hợp output thành tài liệu kiến trúc.
