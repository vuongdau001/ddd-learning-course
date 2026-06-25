---
type: lesson
module: 2
lesson: 3
title: "Viết Glossary chuẩn"
duration: "35 phút"
prerequisites: ["module-2/lesson-2"]
---

# Lesson 2.3: Viết Glossary chuẩn

## 🎓 Concept — "Glossary không phải từ điển"

### Sai lầm phổ biến

```
❌ Glossary kiểu từ điển:
   "Resource: Nhân sự trong công ty"
   → Quá ngắn, không giải quyết xung đột, code không dùng được

✅ Glossary kiểu DDD:
   Resource: Một nhân sự thuộc biên chế ITO, có kỹ năng chuyên môn xác định,
   và có thể được phân bổ (allocate) vào dự án để tạo ra giá trị billable.
   
   Context: Resource Management
   Attributes: employee_id, skills[], seniority, current_allocations[]
   Rules: Tổng allocation ≤ 100%. Allocation < 20% > 5 ngày → Bench.
   NOT: Employee (HR context), User (Auth context), Assignee (Jira context)
```

### Template chuẩn — 8 thành phần

Mỗi Glossary entry cần **8 phần** để thực sự hữu ích:

| # | Phần | Tại sao cần | Ai dùng |
|:---:|---|---|---|
| 1 | **Definition** | 1 câu không mơ hồ | Tất cả |
| 2 | **Bounded Context** | Term này thuộc context nào | Architect |
| 3 | **Attributes** | Data model cơ bản | Developer |
| 4 | **Business Rules** | Logic nghiệp vụ gắn với term | BA, Dev |
| 5 | **States** (nếu có) | Lifecycle diagram | BA, QA |
| 6 | **Phân biệt với** | Tránh nhầm với term khác | Tất cả |
| 7 | **Đồng nghĩa nguy hiểm** | Từ KHÔNG được dùng | Dev, Writer |
| 8 | **Câu hỏi mở** | Cần stakeholders chốt | PM, BA |

### Tại sao cần mỗi phần?

**Definition:** Nếu thiếu → mọi người vẫn hiểu khác nhau.

**Attributes:** Nếu thiếu → developer tự thiết kế database schema → mỗi người 1 kiểu.

**Business Rules:** Nếu thiếu → logic nằm trong đầu BA → không ai biết ngoài BA → bus factor = 1.

**Phân biệt với:** Nếu thiếu → vẫn nhầm "Allocation" với "Assignment" → bug.

**Đồng nghĩa nguy hiểm:** Nếu thiếu → developer vẫn dùng "Staff" thay vì "Resource" → code inconsistent.

### Ví dụ: Glossary entry tốt vs tệ

#### ❌ Tệ — "Shipment" trong Logistics:

```
Shipment: Hàng hóa được vận chuyển.
```

Thiếu: context nào? attributes gì? khác "Order" thế nào? trạng thái gì?

#### ✅ Tốt — "Shipment" trong Logistics:

```
Shipment: Một lô hàng cụ thể được giao cho 1 tài xế/xe, 
vận chuyển từ điểm A đến điểm B, trong 1 chuyến (trip) xác định.

Context: Transportation Management
Attributes: shipment_id, origin, destination, vehicle, driver, 
            weight, volume, status, estimated_arrival
Rules: 
  - 1 Shipment thuộc 1 Trip (nhưng 1 Trip có thể có nhiều Shipments)
  - Tổng weight ≤ Vehicle capacity
  - Status: Created → Loaded → InTransit → Delivered → Confirmed
Phân biệt:
  - Order: Yêu cầu từ khách (có thể gồm nhiều Shipments)
  - Package: Đơn vị vật lý nhỏ nhất. 1 Shipment có thể có nhiều Packages
  - Trip: Chuyến đi của xe. 1 Trip gom nhiều Shipments
❌ Không gọi: "Đơn hàng" (nhầm với Order), "Kiện hàng" (nhầm với Package)
```

### Quy trình viết Glossary

```
1. Chọn term có xung đột cao nhất (từ Conflict Matrix)
2. Draft definition (1 câu)
3. Liệt kê attributes (từ Domain Map + System Inventory)
4. Viết business rules (từ Pain Points + stakeholder interview)
5. Tìm "khác gì" và "đồng nghĩa nguy hiểm" (từ Conflict Matrix)
6. Ghi câu hỏi mở (cần stakeholders confirm)
7. Review với team → merge vào Glossary chính thức
```

---

## 🏋️ Exercise — Viết 2 Glossary entries

### Phần A: Viết "Shipment" cho Logistics (15 phút)

Dùng template 8 phần, viết Glossary entry cho "Shipment":

```markdown
# Shipment

## Definition
[1 câu — không mơ hồ]

## Bounded Context
[Context nào?]

## Attributes
| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|

## Business Rules
1. [Rule 1]
2. [Rule 2]

## States
[State diagram]

## Phân biệt với
| Hay bị nhầm | Khác ở đâu |
|---|---|

## Đồng nghĩa nguy hiểm ⚠️
| ❌ Không dùng | Tại sao |
|---|---|

## Câu hỏi mở ⚠️
- [ ] [Câu hỏi cần stakeholder confirm]
```

### Phần B: Viết "Trip" cho Logistics (15 phút)

Tương tự — viết Glossary cho "Trip" (chuyến đi của xe):

```markdown
# Trip

## Definition
[...]
```

### Self-check

| Tiêu chí | Shipment | Trip |
|---|:---:|:---:|
| Definition ≤ 2 câu, rõ ràng? | ☐ | ☐ |
| Có Bounded Context? | ☐ | ☐ |
| ≥ 4 attributes? | ☐ | ☐ |
| ≥ 2 business rules? | ☐ | ☐ |
| Phân biệt với ≥ 2 terms khác? | ☐ | ☐ |
| ≥ 1 đồng nghĩa nguy hiểm? | ☐ | ☐ |
| ≥ 1 câu hỏi mở? | ☐ | ☐ |

---

## 🪞 Reflect

1. **Phần nào trong template khó viết nhất? Tại sao?** Thường là "Business Rules" — vì rules nằm trong đầu domain expert, không ở đâu khác.

2. **Glossary nên do ai viết? Developer? BA? Domain Expert?** Gợi ý: ai viết, ai review, ai approve — 3 vai trò khác nhau.

3. **Glossary có phải "xong 1 lần" không? Hay cần update liên tục?** Nghĩ về khi nào business rules thay đổi.

---

## ✅ Hoàn thành lesson khi
- [ ] Viết Glossary "Shipment" đủ 8 phần
- [ ] Viết Glossary "Trip" đủ 8 phần
- [ ] Self-check đạt ≥5/7 tiêu chí cho mỗi entry
- [ ] Trả lời ≥2/3 câu hỏi reflection

---

## 🏁 Hoàn thành Module 2

Bạn đã học:
- **Lesson 2.1:** 3 loại xung đột ngôn ngữ — tại sao chúng tốn tiền thật
- **Lesson 2.2:** Conflict Detection Matrix — phát hiện xung đột hệ thống
- **Lesson 2.3:** Glossary template 8 phần — viết entry chuẩn

**Deliverable:** `practice/glossary/` — ≥6 entries hoàn chỉnh

→ Tiếp: **Quiz Module 2** rồi sang **Module 3: Strategic Design**
