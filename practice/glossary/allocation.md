# Allocation

## Definition

Việc phân bổ một **Resource** vào một **Project** trong một khoảng thời gian xác định, được đo bằng **phần trăm (%) thời gian làm việc**.

## Bounded Context

**Resource Management**

## Attributes

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| allocation_id | string | ✅ | ID duy nhất |
| resource | Resource | ✅ | Ai được phân bổ? |
| project | Project | ✅ | Vào dự án nào? |
| percentage | integer | ✅ | % thời gian (0–100) |
| start_date | date | ✅ | Ngày bắt đầu |
| end_date | date | ✅ | Ngày kết thúc |
| status | enum | ✅ | Planned · Active · Completed · Cancelled |
| billable | boolean | ✅ | Có tính vào revenue không? |

## Business Rules

1. **Tổng allocation của 1 Resource ≤ 100%** — Tại mọi thời điểm, tổng % qua tất cả Project ≤ 100
2. **Allocation chỉ được tạo khi có Staffing Request đã approve**
3. **Allocation < 20% trong > 10 ngày → cảnh báo Resource Manager** — Gợi ý tăng allocation hoặc assign project khác
4. **Allocation billable = true phải có Contract tương ứng** — Không thể bill nếu chưa có HĐ
5. **Thay đổi allocation > 20% phải được PM approve** — Tránh rút người khỏi project đột ngột

## States

```
Planned → Active → Completed
   ↓                    
Cancelled          
```

- **Planned:** Đã dự kiến nhưng chưa bắt đầu (VD: project chưa kick off)
- **Active:** Resource đang thực sự làm việc trong project
- **Completed:** Hết thời hạn allocation hoặc project kết thúc
- **Cancelled:** Hủy trước khi bắt đầu (VD: deal bị hủy)

## ⚠️ Quyết định quan trọng: Đơn vị đo

Hiện tại ITO đang dùng lẫn lộn 3 đơn vị:

| Đơn vị | Ưu điểm | Nhược điểm | Ai hay dùng |
|---|---|---|---|
| **% thời gian** | Trực quan, dễ tính tổng (≤100%) | Không chính xác khi part-time | Resource Manager |
| **Man-Day** | Rõ ràng (10 ngày/tháng) | Khó aggregation, tháng có số ngày khác nhau | PM |
| **Giờ (Hours)** | Chính xác nhất | Micromanagement, overhead tracking | Finance |

> **Đề xuất:** Dùng **% thời gian** làm đơn vị chính (đơn giản, match với cách Resource Manager vận hành). Convert sang Man-Day/Hours chỉ khi cần báo cáo Finance.

- [x] **Quyết định: % thời gian** ← Đề xuất, cần stakeholders confirm

## Phân biệt với

| Hay bị nhầm với | Khác ở đâu |
|---|---|
| **Staffing Request** | Staffing Request = yêu cầu "cần 2 Senior Java cho Project X". Allocation = kết quả: "Nguyễn Văn A, 80%, từ 01/07 đến 31/12". |
| **Assignment (Jira)** | Assignment = gán task cụ thể cho người. Allocation = phân bổ thời gian ở level project. 1 Allocation → nhiều Assignments. |
| **Utilization** | Allocation = kế hoạch (planned). Utilization = thực tế (actual hours worked). |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Assign" | Quá chung — assign task ≠ allocate resource to project |
| "Book" / "Booking" | Nghe như đặt phòng, gây nhầm trong context resource |

## Related Concepts

- [[resource]] — Ai được allocate
- [[project]] — Allocate vào đâu
- [[utilization]] — KPI đo thực tế so với allocation
- [[bench]] — Trạng thái khi không có allocation
- [[staffing-request]] — Trigger tạo allocation

## Owner

**Resource Management Team**

---

## 💡 Tại sao term này quan trọng?

Allocation là **"khớp nối"** giữa 2 Core Domain:

```
Opportunity Won → Staffing Request → ALLOCATION → Project Delivery
                                      ↑
                               Đây là điểm nối
                     Resource Mgmt ↔ Delivery Mgmt
```

Nếu Allocation không rõ ràng:
- PM không biết resource available bao nhiêu %
- Resource Manager không biết project cần đến khi nào
- Finance không biết tính revenue thế nào
- → **Toàn bộ pipeline bị nghẽn tại đây**
