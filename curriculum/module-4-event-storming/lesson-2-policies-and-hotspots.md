---
type: lesson
module: 4
lesson: 2
title: "Policies và Hot Spots"
duration: "30 phút"
prerequisites: ["module-4/lesson-1"]
---

# Lesson 4.2: Policies và Hot Spots

## 🎓 Concept — "Luật tự động và điểm nóng"

### Building Block #6: Policy

**Policy** = quy tắc tự động: *"Khi Event X xảy ra → tự động gửi Command Y"*

| Màu Sticky | Ý nghĩa | Format |
|:---:|---|---|
| 🟣 Lilac/Pink | Whenever [Event] → then [Command] | "Whenever ResourceAllocated → NotifyPM" |

**Tại sao Policy quan trọng?**

Policy là nơi **business rules sống**. Developer hay hardcode rules trong if-else, nhưng DDD muốn rules **nhìn thấy được** trên board.

### Ví dụ — ITO Policies

```
Policy 1: "Auto-Alert Bench"
  Whenever AllocationEnded → then CheckIfBench
  (Nếu resource không có allocation mới > 5 ngày → alert RM)

Policy 2: "Opportunity Follow-up"  
  Whenever OpportunityStalled(30 ngày) → then NotifySalesManager
  (Pipeline hygiene — dọn dẹp cơ hội cũ)

Policy 3: "Auto-Staffing Request"
  Whenever OpportunityWon → then CreateStaffingRequest
  (Tự động tạo yêu cầu tìm resource khi thắng deal)

Policy 4: "Utilization Warning"
  Whenever UtilizationCalculated(>95%) → then AlertManager
  (Quá tải → risk burnout)
```

### Ví dụ — Logistics Policies

```
Policy 1: "ETA Breach Alert"
  Whenever ETAUpdated(delay > 30 phút) → then NotifyCustomer
  
Policy 2: "Maintenance Trigger"
  Whenever TripCompleted → then CheckMaintenanceSchedule
  (Kiểm tra xe đến hạn bảo trì chưa)
  
Policy 3: "Auto-Reoptimize"
  Whenever OrderCancelled → then ReoptimizeRoutes
  (Khi 1 đơn bị hủy, tối ưu lại tuyến)
  
Policy 4: "POD Escalation"
  Whenever DeliveryConfirmed(without POD) → then EscalateToSupervisor
```

### Building Block #7: Hot Spot 🔥

**Hot Spot** = chỗ team không đồng ý, có xung đột, hoặc chưa rõ ràng.

| Màu Sticky | Ý nghĩa | Ví dụ |
|:---:|---|---|
| 🔴 Red/Pink (rotate 45°) | Vấn đề chưa giải quyết | "Ai approve allocation? PM hay RM?" |

### 3 loại Hot Spot

| Loại | Dấu hiệu | Ví dụ | Hành động |
|---|---|---|---|
| **Conflict** | 2 người nói khác nhau | "PM nói PM approve, RM nói RM approve" | Cần decision từ stakeholder |
| **Unknown** | Không ai biết đúng | "Nếu resource reject allocation thì sao?" | Cần discovery thêm |
| **Technical Debt** | Biết đúng nhưng chưa làm | "Nên có auto-match nhưng đang manual" | Ghi nhận, plan sau |

### Hot Spot thực tế thường gặp

#### ITO Hot Spots 🔥
```
🔥 "Resource nào có quyền reject allocation?"
   → PM: "Không, resource phải theo assignment"
   → Resource: "Tôi cần được chọn"
   → HR: "Labor law cho phép từ chối"
   
🔥 "Khi nào Opportunity chuyển thành Project?"
   → Sales: "Khi khách nói OK verbal"
   → Finance: "Khi Contract signed"
   → Legal: "Khi cả hai bên ký"

🔥 "Non-billable work track ở đâu?"
   → PM: "Jira"
   → RM: "Excel riêng"
   → Finance: "Không track"
```

#### Logistics Hot Spots 🔥
```
🔥 "Ai chịu trách nhiệm khi hàng hư hại?"
   → Warehouse: "Tài xế, vì đã ký nhận"
   → Tài xế: "Warehouse, vì đóng gói sai"

🔥 "Partial delivery có accepted không?"
   → Sales: "Có, khách cần gấp"
   → Operations: "Không, phức tạp invoice"
```

### Event Storm hoàn chỉnh: Flow có Policy + Hot Spot

```
ResourceRequested                    (Event)
     ↓
[Policy: Auto-Match]                 (Whenever ResourceRequested → FindMatchingResources)
     ↓
MatchingResourcesFound               (Event)
     ↓
Resource Manager → AllocateResource  (Actor → Command)
     ↓
ResourceAllocated                    (Event)
     ↓
[Policy: Notify]                     (Whenever ResourceAllocated → NotifyResource + NotifyPM)
     ↓
🔥 "Resource có quyền reject?"      (Hot Spot)
     ↓
AllocationConfirmed / AllocationRejected  (Event — 2 paths)
```

---

## 🏋️ Exercise — Thêm Policies và Hot Spots

### Phần A: ITO — Thêm vào Event Storm từ Lesson 4.1 (15 phút)

Lấy Event Storm "Opportunity Lifecycle" từ Lesson 4.1, thêm:
- ≥3 Policies (Whenever Event → Command)
- ≥2 Hot Spots

| Event | Policy (nếu có) | Hot Spot (nếu có) |
|---|---|---|
| LeadReceived | | |
| LeadQualified | | |
| MeetingScheduled | | |
| ProposalSent | | |
| OpportunityWon | | |
| OpportunityLost | | |

### Phần B: Logistics — Thêm (10 phút)

Tương tự cho "Order to Delivery":

| Event | Policy (nếu có) | Hot Spot (nếu có) |
|---|---|---|
| OrderPlaced | | |
| VehicleAssigned | | |
| ShipmentLoaded | | |
| DeliveryStarted | | |
| DeliveryConfirmed | | |

---

## 🪞 Reflect

1. **Policy nào trong ví dụ trên hiện đang là "manual" (con người làm tay)?** Đó là cơ hội automation. Automation nào giá trị nhất?

2. **Hot Spot "Resource có quyền reject?" — bạn sẽ giải quyết thế nào?** Không có đáp án đúng sai — đây là business decision. Nhưng bạn cần document quyết định.

3. **Nếu Event Storm không có Hot Spot nào — đó là dấu hiệu tốt hay xấu?** Gợi ý: không có hot spot thường = team không đào đủ sâu.

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Policy là gì và format viết
- [ ] Giải thích 3 loại Hot Spot
- [ ] Thêm ≥3 Policies cho ITO Event Storm
- [ ] Thêm ≥2 Hot Spots cho ITO Event Storm
- [ ] Trả lời ≥2/3 câu hỏi reflection
