---
type: lesson
module: 1
lesson: 1
title: "Tư duy Domain-First"
duration: "30 phút"
prerequisites: []
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Nhận diện vấn đề"
business_invariant: "Hệ thống phải phản ánh cách công ty kinh doanh, không phải cách DB lưu trữ"
---

# Lesson 1.1: Tư duy Domain-First — "Nghĩ từ nghiệp vụ, không phải từ màn hình"

## 📍 Context — Bạn đang ở đây

> Bạn là **Tech Lead** mới được CTO **Minh** giao nhiệm vụ: xây hệ thống CRM mới cho **ITO Corp** — công ty IT Outsourcing 2.000 nhân sự. Hệ thống hiện tại bị phân mảnh nghiêm trọng: mỗi team tự dùng tool riêng, Excel rải rác, không có nguồn sự thật chung. CTO Minh nói: *"Cần 1 hệ thống CRM tự chủ, phản ánh đúng cách chúng ta kinh doanh."*
>
> Đây là ngày đầu tiên. Bạn chưa viết dòng code nào. Câu hỏi đầu tiên là: **Bắt đầu từ đâu?**

## 🔥 Tension — "Chuyện gì xảy ra nếu bạn bắt đầu từ màn hình?"

Bạn mở cuộc họp đầu tiên với team. Một dev Senior tên **Khoa** hào hứng mở Figma:

```
Khoa: "CRM thì đơn giản mà. Tôi ước lượng rồi:
  - Trang Dashboard
  - Trang quản lý khách hàng (CRUD)
  - Trang quản lý nhân sự (CRUD)
  - Trang báo cáo
  - Login/Register
  → 10 màn hình, 4 tháng là xong."
```

Team đồng ý. Sprint 1 bắt đầu. Tháng thứ 3, **Hà** — VP of Sales — gọi điện giận dữ:

> *"Hệ thống bạn build hoàn toàn SAI. Tôi cần biết deal nào đang ở stage Proposal, resource nào available tuần này để demo cho khách, nhưng hệ thống chỉ có CRUD khách hàng? Team Sales tôi vẫn phải dùng Excel track pipeline!"*

**Tuấn** — Resource Manager — cũng phản hồi:

> *"Trang nhân sự chỉ là danh sách nhân viên. Tôi cần biết: ai đang rảnh (bench), ai sắp hết dự án, tổng utilization rate bao nhiêu. Không có gì cả. Tôi vẫn phải dùng tool nội bộ cũ."*

**Kết quả:** 3 tháng code → bỏ 70% vì hệ thống không phản ánh cách công ty vận hành. Mất **~600 triệu đồng** (3 dev × 4 tháng × lương).

> 💭 **Câu hỏi:** Khoa đã sai ở đâu? Anh ấy thiếu bước gì trước khi mở Figma?

## 🎓 Explanation — Tư duy Domain-First

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Hệ thống phải phản ánh cách ITO Corp kinh doanh — cách họ kiếm tiền, cách resource được phân bổ, cách deal đi qua pipeline — chứ không phải cách database lưu trữ."*

Đây chính là sự khác biệt cốt lõi:

| | System-Centric (Khoa đã làm) | Domain-Centric (DDD) |
|---|---|---|
| **Bắt đầu từ** | UI mockup, database schema | Nghiệp vụ, quy trình, người dùng |
| **Câu hỏi đầu tiên** | "Cần mấy bảng? Mấy màn hình?" | "Công ty kiếm tiền bằng cách nào?" |
| **Đơn vị thiết kế** | Màn hình, API endpoint | Domain, Capability, Business Rule |
| **Ngôn ngữ** | CRUD, table, endpoint | Opportunity, Allocation, Staffing |
| **Khi yêu cầu thay đổi** | Sửa nhiều chỗ vì coupling | Thay đổi gọn trong 1 domain |
| **AI có đọc được?** | Khó — thông tin nằm rải trong code | Dễ — thông tin có cấu trúc rõ ràng |

**Nếu Khoa dùng DDD**, anh ấy sẽ hỏi trước:

```
"Trước khi code, tôi cần hiểu:
  - Công ty kiếm tiền bằng cách nào? → Outsourcing, bán resource theo project
  - Tiền đi qua bao nhiêu bước? → Lead → Deal → Contract → Invoice → Payment
  - Bước nào đang bị nghẽn? → Sales pipeline không ai nhìn thấy tổng
  - Ai đang đau nhất? → Hà (VP Sales): mất deal vì không biết resource availability
  → Trả lời xong → MỚI biết cần build gì trước"
```

### Domain là gì?

**Domain** = một lĩnh vực nghiệp vụ có ranh giới rõ ràng, có quy tắc riêng, có người chịu trách nhiệm.

Kiểm tra nhanh bằng **3 câu hỏi**:
1. **Ai** chịu trách nhiệm? (Business Owner)
2. Có **quy tắc riêng** không? (Business Rules)
3. Nếu **tách ra** khỏi hệ thống, nó có ý nghĩa độc lập không?

### Ví dụ — ITO CRM

CTO Minh cho bạn danh sách "chức năng cần build". Hãy phân biệt đâu là Domain, đâu chỉ là component kỹ thuật:

| Kiểm tra | Resource Management | "Trang Dashboard" |
|---|---|---|
| Ai chịu trách nhiệm? | **Tuấn** — Resource Manager | ...Ai? Dev? PM? |
| Có quy tắc riêng? | Allocation ≤ 100%, Bench > 5 ngày → alert | Không — chỉ show data |
| Tách ra có ý nghĩa? | Có — vẫn cần quản lý resource dù không có CRM | Không — phụ thuộc mọi thứ |

→ Resource Management = **Domain** ✅
→ "Trang Dashboard" = UI component, không phải domain ❌

### Ví dụ — Logistics (so sánh)

| Kiểm tra | Warehouse Management | "Trang quản lý đơn hàng" |
|---|---|---|
| Ai chịu trách nhiệm? | Warehouse Manager | ...Dev? |
| Có quy tắc riêng? | FIFO xuất kho, capacity limit, zone mapping | Không — CRUD đơn thuần |
| Tách ra có ý nghĩa? | Có — warehouse vận hành dù không có phần mềm | Không — chỉ là UI wrapper |

→ Warehouse Management = **Domain** ✅
→ "Trang quản lý đơn hàng" = UI, không phải domain ❌

### ⚖️ Trade-offs — Được gì, Mất gì

| | Domain-First (DDD) | System-First (CRUD) |
|---|---|---|
| **Được** | Hệ thống phản ánh đúng business; dễ thay đổi khi yêu cầu đổi; AI-readable | Code nhanh ngay sprint 1; team quen workflow |
| **Mất** | Cần thời gian khảo sát trước khi code (1-2 tuần); team phải học cách tư duy mới | 3-6 tháng sau phải rewrite vì không khớp business |
| **Khi nào chấp nhận** | Hệ thống phức tạp, business rules nhiều, cần scale | Prototype nhanh, internal tool đơn giản, ít business logic |

---

## 🏋️ Exercise — Quyết định thiết kế

### Scenario A: ITO CRM (15 phút)

CTO Minh gửi cho bạn danh sách "chức năng cần build". Phân loại: đâu là Domain, đâu không phải?

| # | Tên | Domain? | Lý do |
|:---:|---|:---:|---|
| 1 | Sales Pipeline Management | ? | ? |
| 2 | Trang Login | ? | ? |
| 3 | Resource Allocation | ? | ? |
| 4 | API Gateway | ? | ? |
| 5 | Contract & Payment Tracking | ? | ? |
| 6 | Notification System | ? | ? |
| 7 | Reporting & Analytics | ? | ? |
| 8 | Trang Settings | ? | ? |

**Ràng buộc migration:** Hà (VP Sales) yêu cầu Sales Pipeline phải lên trước vì đang mất deal mỗi tuần. Tuấn cần Resource Allocation ngay vì utilization rate bị tính sai 15%. Nhưng bạn chỉ có 1 team dev.

> 💭 Bạn sẽ đề xuất build domain nào trước? Tại sao? Business impact của từng lựa chọn?

<details>
<summary>💡 Gợi ý: Dùng 3 câu hỏi kiểm tra</summary>

Với mỗi mục, tự hỏi:
1. Ai chịu trách nhiệm nghiệp vụ này?
2. Có business rules riêng không?
3. Tách ra khỏi hệ thống, có ý nghĩa độc lập không?

Nếu cả 3 "Có" → Domain. Nếu bất kỳ câu nào "Không rõ" → Không phải domain.
</details>

<details>
<summary>📝 Đáp án tham khảo</summary>

| # | Tên | Domain? | Lý do |
|:---:|---|:---:|---|
| 1 | Sales Pipeline Management | ✅ | Hà sở hữu, rules: deal lifecycle, stage transitions, territory assignment |
| 2 | Trang Login | ❌ | Không có business owner riêng, chỉ là authentication component |
| 3 | Resource Allocation | ✅ | Tuấn sở hữu, rules: allocation ≤ 100%, bench tracking, skill matching |
| 4 | API Gateway | ❌ | Infrastructure component, không phải nghiệp vụ |
| 5 | Contract & Payment | ✅ | CFO sở hữu, rules: payment terms, revenue recognition, invoice lifecycle |
| 6 | Notification | ⚠️ | Nếu chỉ gửi email → Generic. Nếu có escalation rules → Supporting domain |
| 7 | Reporting & Analytics | ⚠️ | Phụ thuộc vào data từ domains khác, nhưng có rules riêng (KPI thresholds, alert triggers) |
| 8 | Trang Settings | ❌ | UI component, không có business logic riêng |

**Thứ tự ưu tiên:** Sales Pipeline trước (Hà đang mất deal mỗi tuần → impact trực tiếp đến revenue). Resource Allocation sau (Tuấn cần, nhưng sai 15% utilization chưa gây mất tiền ngay).
</details>

### Scenario B: Logistics (10 phút)

Một công ty vận tải muốn xây hệ thống quản lý. Phân loại:

| # | Tên | Domain? | Lý do |
|:---:|---|:---:|---|
| 1 | Fleet Management (quản lý xe) | ? | ? |
| 2 | Route Optimization (tối ưu tuyến) | ? | ? |
| 3 | Trang tracking đơn hàng | ? | ? |
| 4 | Pricing (định giá cước) | ? | ? |

---

## 🪞 Reflect

1. **Trong dự án gần đây nhất, bạn đã bắt đầu từ đâu — UI hay nghiệp vụ?** Nếu từ UI: điều gì xảy ra khi yêu cầu thay đổi? Nếu bạn dùng DDD, kết quả có khác không?

2. **Khoa mất 600 triệu vì bắt đầu từ màn hình. Nhưng nếu bạn mất 2 tuần khảo sát domain trước khi code, CTO Minh có đồng ý không?** Bạn sẽ thuyết phục Minh bằng cách nào? (Gợi ý: trade-offs table ở trên là vũ khí tốt nhất)

3. **Tại sao AI đọc "Domain Map" dễ hơn đọc "Database Schema"?** Gợi ý: khi bạn prompt AI: *"Generate API for Resource Management"* — AI cần hiểu Resource là gì, có rules gì, liên quan tới domain nào. Database schema chỉ nói `table: resources, columns: id, name, email` — không đủ context.

---

## ✅ Completion Checklist
- [ ] **Recall:** Phân biệt System-centric vs Domain-centric, nêu 3 câu hỏi kiểm tra domain
- [ ] **Apply:** Phân loại ≥6/8 mục cho ITO CRM, ≥3/4 cho Logistics
- [ ] **Analyze:** Giải thích tại sao chọn Sales Pipeline build trước, bảo vệ quyết định bằng trade-offs

---

> 🔗 **Tiếp theo:** Bạn đã biết cách nhận diện Domain. Nhưng mỗi domain đó tạo ra giá trị gì cho công ty? Tiền đi từ đâu đến đâu qua bao nhiêu bước? Bài tiếp theo — *Value Chain Analysis* — sẽ giúp bạn vẽ "dòng chảy tiền" của ITO Corp, để hiểu domain nào nằm ở vị trí then chốt nhất.
