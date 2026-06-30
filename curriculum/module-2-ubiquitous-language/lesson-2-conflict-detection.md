---
type: lesson
module: 2
lesson: 2
title: "Phát hiện xung đột thuật ngữ"
duration: "30 phút"
prerequisites: ["module-2/lesson-1"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Workshop"
business_invariant: "Mỗi term = 1 định nghĩa duy nhất mỗi context; xung đột = cần tách context"
---

# Lesson 2.2: Phát hiện xung đột thuật ngữ — "Bảng xung đột"

## 📍 Context — Bạn đang ở đây

> Sau sự cố "5 người nói Resource, 5 nghĩa" (Lesson 2.1), CTO **Minh** yêu cầu: *"Trước khi viết dòng code nào, tôi cần biết: còn bao nhiêu từ đang bị hiểu khác nhau? Tôi không muốn thêm bug ngôn ngữ nào nữa sau vụ PM setup team cho deal chưa ký."*
>
> Bạn cần một phương pháp **có hệ thống** để quét toàn bộ xung đột ngôn ngữ giữa các phòng ban — không thể chỉ dựa vào tình cờ phát hiện trong meeting.

## 🔥 Tension — "Tôi không biết còn bao nhiêu bom nổ chậm"

Bạn ngồi lại với **Hà** (Sales) và **Tuấn** (RM) để chuẩn bị sprint backlog. Hà nói:

> **Hà:** *"Sprint 1, tôi cần: hiển thị tất cả 'Customers' active."*

Bạn viết User Story: "As Sales, I want to see all active Customers."

Nhưng dev hỏi:

> **Dev:** *"'Customer' nghĩa là gì? Company? Contact person? Account? Và 'active' — active deal hay active contract?"*

Bạn hỏi lại Hà. Rồi hỏi CFO cùng câu. Câu trả lời:

| Hỏi | Hà (Sales) | CFO (Finance) |
|---|---|---|
| "Customer" là gì? | Công ty có ≥1 deal trong pipeline | Đối tượng thanh toán trên hóa đơn |
| "Active" nghĩa gì? | Có deal đang open (chưa Won/Lost) | Có contract chưa hết hạn |

**Kết quả:** Nếu dev build 1 bảng `customers` với 1 trường `is_active` — **cả Hà và CFO đều sẽ sai**. Hà thấy "customer active" nhưng chưa ký contract. CFO thấy "customer active" nhưng deal đã lost.

> 💭 **Câu hỏi:** Bạn biết "Resource" và "Customer" đã xung đột. Còn bao nhiêu thuật ngữ nữa đang là bom nổ chậm? Làm sao quét hết?

## 🎓 Explanation — Conflict Detection Matrix

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mỗi thuật ngữ = 1 định nghĩa duy nhất trong mỗi context. Nếu cùng 1 từ mà ≥2 phòng ban hiểu khác nhau → đó là DẤU HIỆU cần tách thành context riêng."*

### Kỹ thuật: Conflict Detection Matrix

Thay vì phỏng đoán, dùng **bảng hệ thống** 4 bước:

**Bước 1:** Liệt kê 5-10 thuật ngữ quan trọng nhất (lấy từ Domain Map — Module 1)
**Bước 2:** Liệt kê 4-5 vai trò/phòng ban chính
**Bước 3:** Điền: mỗi vai trò hiểu thuật ngữ đó nghĩa là gì?
**Bước 4:** Ô nào khác nhau = xung đột cần giải quyết

### ITO Corp Conflict Matrix

Bạn triệu tập 1 buổi workshop 2 giờ với đại diện mỗi phòng ban. Kết quả:

| Thuật ngữ | Sales (Hà) | PM | Resource Mgr (Tuấn) | Finance (CFO) | HR |
|---|---|---|---|---|---|
| **Resource** | Capability tôi bán | Người trong team tôi | Kỹ sư allocatable | Labor cost | Employee |
| **Customer** | Account (công ty có deal) | Customer (daily contact) | — | Billing Entity | — |
| **Project** | Opportunity (chưa ký) | Dự án đang delivery | Engagement | Work Order | — |
| **Utilization** | — | — | Billable/Available hours | Revenue/Capacity ratio | Headcount ratio |
| **Allocation** | — | "Cần N người" | % thời gian 1 resource | Man-Day cost | FTE |

**Đọc bảng:** Mỗi hàng có >1 cách hiểu = xung đột. "Resource" có 5 cách hiểu = xung đột nặng nhất.

### Logistics Conflict Matrix (so sánh)

| Thuật ngữ | Sales | Dispatcher | Tài xế | Warehouse | Finance |
|---|---|---|---|---|---|
| **Delivery** | Dịch vụ vận chuyển | Chuyến hàng assigned | Giao hàng cho khách | Giao hàng cho xe tải | Chi phí vận chuyển |
| **Order** | Đơn đặt hàng khách | Lệnh vận chuyển | Danh sách điểm giao | Phiếu xuất kho | Invoice |
| **Route** | — | Tuyến đường tối ưu | Đường tôi chạy hôm nay | — | Chi phí theo tuyến |
| **Capacity** | Khả năng nhận đơn | Số xe trống hôm nay | — | Sức chứa kho | — |

### Mức độ xung đột → Hành động

| Mức | Dấu hiệu | Hành động | Ví dụ ITO |
|---|---|---|---|
| 🟢 **Thấp** | 2 vai trò giống, 1 khác | Ghi chú, thống nhất dần | "Route" trong Logistics |
| 🟡 **Trung bình** | 3 vai trò khác nhau cùng "loại" | Tạo Glossary entry, chọn 1 definition | "Utilization", "Allocation" |
| 🔴 **Cao** | Mỗi vai trò hiểu hoàn toàn khác | Tạo Glossary + Bounded Context riêng | "Resource", "Customer", "Project" |

### Giải pháp DDD cho xung đột 🔴

Khi xung đột quá lớn, **không ép mọi người dùng 1 từ**. Thay vào đó, cho mỗi context 1 tên riêng:

```
TRƯỚC:  Mọi người gọi "Customer" → tranh cãi, bug
SAU:    Sales Context     → "Account"        (công ty có deal)
        Delivery Context  → "Customer"       (contact hàng ngày)
        Finance Context   → "Billing Entity" (đối tượng thanh toán)
        Contract Context  → "Client"         (bên ký hợp đồng)

Mỗi tên rõ ràng trong context của nó → không ai nhầm
```

Đây chính là tiền đề của **Bounded Context** (sẽ học kỹ ở Module 5) — mỗi context có ngôn ngữ riêng, model riêng, ranh giới rõ ràng.

### ⚖️ Trade-offs — Conflict Matrix Workshop

| | Làm workshop Conflict Matrix | Thống nhất thuật ngữ "bình thường" |
|---|---|---|
| **Được** | Phát hiện hết xung đột 1 lần; có evidence bằng bảng; basis cho Bounded Context | Nhanh, không cần tổ chức meeting |
| **Mất** | 2 giờ workshop + 5 stakeholders = 10 man-hours; stakeholders có thể defensive | Miss xung đột ẩn, phát hiện khi đã code → tốn gấp 10x sửa |
| **Khi nào** | Bắt đầu dự án mới, migration, hoặc khi team gặp bug ngôn ngữ liên tục | Dự án nhỏ, team <5 người, domain đơn giản |

---

## 🏋️ Exercise — Xây Conflict Matrix

### Phần A: Mở rộng Logistics Conflict Matrix (15 phút)

Thêm ít nhất 3 thuật ngữ nữa vào bảng:

| Thuật ngữ | Sales | Dispatcher | Tài xế | Warehouse | Finance |
|---|---|---|---|---|---|
| **Package** | ? | ? | ? | ? | ? |
| **Shipment** | ? | ? | ? | ? | ? |
| **ETA** | ? | ? | ? | ? | ? |
| _____ | ? | ? | ? | ? | ? |

Rank mức xung đột cho mỗi thuật ngữ mới: 🟢 / 🟡 / 🔴

### Phần B: Ranking xung đột ITO (10 phút)

Từ Conflict Matrix ITO ở trên, rank và quyết định hành động:

| Thuật ngữ | Mức xung đột | Cần Glossary? | Cần Bounded Context riêng? | Lý do |
|---|---|---|---|---|
| Resource | ? | ? | ? | ? |
| Customer | ? | ? | ? | ? |
| Project | ? | ? | ? | ? |
| Utilization | ? | ? | ? | ? |
| Allocation | ? | ? | ? | ? |

### Phần C: Đề xuất giải pháp (5 phút)

Chọn 1 thuật ngữ xung đột 🔴 từ ITO. Viết giải pháp kiểu DDD — tách thành tên riêng cho mỗi context:

```
TRƯỚC: Mọi người gọi "___________"
SAU:
  ___________ Context → "___________"
  ___________ Context → "___________"
  ___________ Context → "___________"
```

---

## 🪞 Reflect

1. **Conflict Matrix có giới hạn gì?** Gợi ý: nó chỉ phát hiện xung đột giữa các vai trò bạn liệt kê. Nếu thiếu 1 vai trò quan trọng (VD: khách hàng cuối), có thể miss xung đột. Làm sao giảm rủi ro?

2. **Tại sao giải pháp DDD là "cho mỗi context 1 tên riêng" thay vì "bắt mọi người dùng 1 tên"?** Gợi ý: bạn có thể bắt CFO gọi "Customer" thay vì "Billing Entity" không? CFO sẽ nói: *"Billing Entity phản ánh đúng cách Finance nhìn — tôi không quan tâm Sales gọi là gì."*

3. **Xung đột ngôn ngữ = dấu hiệu gì trong kiến trúc?** Gợi ý: mỗi xung đột 🔴 = 1 ranh giới Bounded Context tiềm năng. Nếu "Customer" có 4 nghĩa → có thể cần 4 context riêng. Đây là bridge sang Module 5.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 4 bước xây Conflict Matrix + 3 mức xung đột
- [ ] **Apply:** Xây Conflict Matrix ≥5 thuật ngữ × ≥4 vai trò cho Logistics, rank mức xung đột
- [ ] **Analyze:** Giải thích tại sao xung đột 🔴 cần Bounded Context riêng thay vì chỉ cần Glossary — liên hệ với vụ "Customer active" ở phần Tension

---

> 🔗 **Tiếp theo:** Bạn đã phát hiện xung đột. Bây giờ cần **ghi lại** chính xác: mỗi thuật ngữ nghĩa là gì, thuộc context nào, rules gì, liên quan đến term nào khác. Bài tiếp — *Viết Glossary chuẩn* — sẽ dạy bạn template Glossary mà cả dev, PM, và AI Agent đều đọc được.
