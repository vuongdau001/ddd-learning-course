---
type: lesson
module: 2
lesson: 2
title: "Phát hiện xung đột thuật ngữ"
duration: "30 phút"
prerequisites: ["module-2/lesson-1"]
---

# Lesson 2.2: Phát hiện xung đột thuật ngữ

## 🎓 Concept — "Bảng xung đột"

### Kỹ thuật: Conflict Detection Matrix

Thay vì phỏng đoán, dùng **bảng hệ thống** để quét xung đột:

**Bước 1:** Liệt kê 5-10 thuật ngữ quan trọng nhất (từ Domain Map)
**Bước 2:** Liệt kê 4-5 vai trò/phòng ban chính
**Bước 3:** Điền: mỗi vai trò hiểu thuật ngữ đó nghĩa là gì?
**Bước 4:** Ô nào khác nhau = xung đột cần giải quyết

### Ví dụ — ITO Conflict Matrix

| Thuật ngữ | Sales | PM | Resource Mgr | Finance | HR |
|---|---|---|---|---|---|
| **Resource** | Capability tôi bán | Người trong team | Kỹ sư allocatable | Labor cost | Employee |
| **Customer** | Account | Customer (daily contact) | — | Billing Entity | — |
| **Project** | Opportunity | Dự án delivery | Engagement | Work Order | — |
| **Utilization** | — | — | Billable/Available | Revenue/Capacity | Headcount có việc/tổng |
| **Allocation** | — | Cần N người | % thời gian | Man-Day | FTE |

**Đọc bảng:** Mỗi hàng có >1 cách hiểu = **xung đột**. Hàng "Resource" có 5 cách hiểu = xung đột nặng nhất.

### Ví dụ — Logistics Conflict Matrix

| Thuật ngữ | Sales | Dispatcher | Tài xế | Warehouse | Finance |
|---|---|---|---|---|---|
| **Delivery** | Dịch vụ vận chuyển | Chuyến hàng assigned | Giao hàng cho khách | Giao hàng cho xe | Chi phí vận chuyển |
| **Order** | Đơn đặt hàng (của khách) | Lệnh vận chuyển | Danh sách điểm giao | Phiếu xuất kho | Invoice |
| **Route** | — | Tuyến đường tối ưu | Đường tôi phải đi | — | Chi phí theo tuyến |
| **Capacity** | Khả năng nhận đơn | Số xe trống | — | Sức chứa kho | — |

### Mức độ xung đột

| Mức | Dấu hiệu | Hành động |
|---|:---:|---|
| 🟢 **Thấp** | 2 vai trò dùng giống nhau, 1 vai trò dùng khác | Ghi chú, thống nhất dần |
| 🟡 **Trung bình** | 3 vai trò dùng khác nhau nhưng cùng "loại" | Tạo Glossary entry, chọn 1 definition chính |
| 🔴 **Cao** | Mỗi vai trò hiểu hoàn toàn khác nhau | Tạo Glossary + Bounded Context riêng cho mỗi cách hiểu |

### Giải pháp DDD cho xung đột 🔴

Khi xung đột quá lớn, **không ép mọi người dùng 1 từ**. Thay vào đó, cho mỗi context 1 tên riêng:

```
TRƯỚC:  Mọi người gọi "Customer" → tranh cãi
SAU:    Sales → "Account"
        Delivery → "Customer"  
        Finance → "Billing Entity"
        Contract → "Client"

Mỗi tên rõ ràng trong context của nó → không ai nhầm
```

Đây chính là **Bounded Context** (sẽ học kỹ ở Module 5) — mỗi context có ngôn ngữ riêng.

---

## 🏋️ Exercise — Xây Conflict Matrix

### Phần A: Logistics Conflict Matrix (15 phút)

Điền thêm vào bảng dưới — ít nhất 3 thuật ngữ nữa:

| Thuật ngữ | Sales | Dispatcher | Tài xế | Warehouse | Finance |
|---|---|---|---|---|---|
| Package | | | | | |
| Shipment | | | | | |
| ETA | | | | | |

### Phần B: Ranking xung đột (10 phút)

Từ Conflict Matrix ITO ở trên, rank theo mức xung đột:

| Thuật ngữ | Mức xung đột | Cần Glossary? | Cần Bounded Context riêng? |
|---|:---:|:---:|:---:|
| Resource | ? | ? | ? |
| Customer | ? | ? | ? |
| Project | ? | ? | ? |
| Utilization | ? | ? | ? |
| Allocation | ? | ? | ? |

---

## 🪞 Reflect

1. **Conflict Matrix có giới hạn gì?** Gợi ý: nó chỉ phát hiện xung đột giữa các vai trò bạn liệt kê. Nếu thiếu 1 vai trò (VD: khách hàng), có thể miss xung đột.

2. **Tại sao giải pháp DDD là "cho mỗi context 1 tên riêng" thay vì "bắt mọi người dùng 1 tên"?** Nghĩ về tính khả thi: bạn có thể bắt Finance gọi "Customer" thay vì "Billing Entity" không?

3. **Khi nào thì xung đột ngôn ngữ là DẤU HIỆU cần tách Bounded Context?**

---

## ✅ Hoàn thành lesson khi
- [ ] Xây được Conflict Matrix ≥5 thuật ngữ × ≥4 vai trò
- [ ] Rank mức xung đột cho mỗi thuật ngữ
- [ ] Nhận biết khi nào cần Glossary vs khi nào cần Bounded Context riêng
- [ ] Trả lời ≥2/3 câu hỏi reflection
