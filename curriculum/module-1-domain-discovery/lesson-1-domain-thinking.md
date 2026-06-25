---
type: lesson
module: 1
lesson: 1
title: "Tư duy Domain-First"
duration: "20 phút"
prerequisites: []
---

# Lesson 1.1: Tư duy Domain-First

## 🎓 Concept — "Nghĩ từ nghiệp vụ, không phải từ màn hình"

### Câu chuyện mở đầu

Hãy tưởng tượng bạn được thuê xây hệ thống CRM cho một công ty ITO. Phản ứng đầu tiên của hầu hết developer là gì?

```
Developer thông thường:
"OK, CRM thì cần:
 - Trang Dashboard
 - Trang quản lý khách hàng (CRUD)
 - Trang quản lý nhân sự (CRUD)
 - Trang báo cáo
 - Login/Register
 → Ước lượng: 10 màn hình, 4 tháng"
```

Người này đang nghĩ từ **hệ thống** — UI, database, CRUD. Họ sẽ xây nhanh, nhưng 6 tháng sau sẽ gặp vấn đề: hệ thống không phản ánh đúng cách công ty hoạt động.

```
Người nghĩ theo DDD:
"Trước khi code, tôi cần hiểu:
 - Công ty kiếm tiền bằng cách nào?
 - Tiền đi qua bao nhiêu bước?
 - Bước nào đang bị nghẽn?
 - Ai đang đau nhất?
 → Trả lời xong → mới biết cần build gì"
```

### Sự khác biệt cốt lõi

| | System-Centric (thông thường) | Domain-Centric (DDD) |
|---|---|---|
| **Bắt đầu từ** | UI mockup, database schema | Nghiệp vụ, quy trình, người dùng |
| **Đơn vị thiết kế** | Màn hình, API endpoint | Domain, Capability, Business Rule |
| **Ngôn ngữ** | Kỹ thuật (CRUD, table, endpoint) | Nghiệp vụ (Opportunity, Allocation, Staffing) |
| **Khi thay đổi yêu cầu** | Phải sửa nhiều chỗ vì coupling | Thay đổi nằm gọn trong 1 domain |
| **AI có đọc được?** | Khó — vì thông tin nằm trong code | Dễ — vì thông tin nằm trong tài liệu có cấu trúc |

### Domain là gì?

**Domain** = một lĩnh vực nghiệp vụ có ranh giới rõ ràng, có quy tắc riêng, có người chịu trách nhiệm riêng.

Kiểm tra nhanh: nếu bạn có thể trả lời 3 câu này → đó là 1 domain:
1. **Ai** chịu trách nhiệm? (Actor)
2. Có **quy tắc riêng** không? (Business Rules)
3. Nếu **tách ra** khỏi hệ thống, nó có ý nghĩa độc lập không?

### Ví dụ — ITO CRM

| Kiểm tra | Resource Management | "Trang Dashboard" |
|---|---|---|
| Ai chịu trách nhiệm? | Resource Manager | ...Ai? Dev? PM? |
| Có quy tắc riêng? | Allocation ≤ 100%, Bench > 5 ngày → alert | Không — chỉ show data |
| Tách ra có ý nghĩa? | Có — vẫn cần quản lý resource dù không có CRM | Không — dashboard phụ thuộc mọi thứ |

→ Resource Management = Domain ✅  
→ "Trang Dashboard" = UI component, không phải domain ❌

### Ví dụ — Logistics

| Kiểm tra | Warehouse Management | "Trang quản lý đơn hàng" |
|---|---|---|
| Ai chịu trách nhiệm? | Warehouse Manager | ...Dev? |
| Có quy tắc riêng? | FIFO xuất kho, capacity limit, zone mapping | Không — CRUD đơn thuần |
| Tách ra có ý nghĩa? | Có — warehouse vận hành dù không có phần mềm | Không — chỉ là UI wrapper |

→ Warehouse Management = Domain ✅  
→ "Trang quản lý đơn hàng" = UI, không phải domain ❌

---

## 🏋️ Exercise — Nhận diện Domain vs Không-phải-Domain

Phân loại các mục sau: đâu là Domain, đâu không phải?

| # | Tên | Domain? | Lý do |
|:---:|---|:---:|---|
| 1 | Order Management | ? | ? |
| 2 | Trang Login | ? | ? |
| 3 | Fleet Management (quản lý xe) | ? | ? |
| 4 | API Gateway | ? | ? |
| 5 | Pricing (định giá) | ? | ? |
| 6 | Notification System | ? | ? |
| 7 | Route Optimization (tối ưu tuyến đường) | ? | ? |
| 8 | Trang Settings | ? | ? |

<details>
<summary>💡 Gợi ý: Dùng 3 câu hỏi kiểm tra</summary>

Với mỗi mục, tự hỏi:
1. Ai chịu trách nhiệm nghiệp vụ này?
2. Có business rules riêng không?
3. Tách ra khỏi hệ thống, có ý nghĩa độc lập không?

Nếu trả lời "Có" cho cả 3 → Domain.
Nếu bất kỳ câu nào "Không rõ" → Không phải domain (là component kỹ thuật).
</details>

<details>
<summary>📝 Đáp án tham khảo</summary>

| # | Tên | Domain? | Lý do |
|:---:|---|:---:|---|
| 1 | Order Management | ✅ | Sales team sở hữu, có rules (order lifecycle, payment terms) |
| 2 | Trang Login | ❌ | Không có business owner riêng, chỉ là UI component |
| 3 | Fleet Management | ✅ | Fleet Manager sở hữu, có rules (bảo trì, fuel, driver assignment) |
| 4 | API Gateway | ❌ | Infrastructure component, không phải nghiệp vụ |
| 5 | Pricing | ✅ | Pricing Manager sở hữu, rules rất phức tạp (discount, tier, contract) |
| 6 | Notification | ⚠️ | Ranh giới: nếu chỉ gửi email → Generic (không phải domain). Nếu có rules phức tạp (escalation, preferences) → có thể là Supporting domain |
| 7 | Route Optimization | ✅ | Operations team sở hữu, algorithms + constraints phức tạp |
| 8 | Trang Settings | ❌ | UI component, không có business logic riêng |
</details>

---

## 🪞 Reflect — Câu hỏi phản tư

1. **Trong dự án gần đây nhất của bạn, bạn đã bắt đầu thiết kế từ đâu — UI hay nghiệp vụ?** Nếu từ UI, điều gì xảy ra khi yêu cầu thay đổi?

2. **"Authentication" là domain hay không?** Tại sao nhiều người tranh cãi về câu này?

3. **Tại sao AI đọc "Domain Map" dễ hơn đọc "Database Schema"?** Gợi ý: nghĩ về cách bạn prompt AI.

---

## ✅ Hoàn thành lesson khi
- [ ] Phân biệt được System-centric vs Domain-centric
- [ ] Áp dụng 3 câu hỏi kiểm tra để nhận diện domain
- [ ] Hoàn thành exercise (≥6/8 đúng)
- [ ] Trả lời ≥2/3 câu hỏi reflection
