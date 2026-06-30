---
type: lesson
module: 2
lesson: 3
title: "Viết Glossary chuẩn"
duration: "35 phút"
prerequisites: ["module-2/lesson-2"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Glossary là contract"
business_invariant: "Glossary entry phải đủ 8 phần: nếu thiếu Attributes → dev tự thiết kế schema → drift"
---

# Lesson 2.3: Viết Glossary chuẩn — "Glossary không phải từ điển"

## 📍 Context — Bạn đang ở đây

> Workshop Conflict Matrix (Lesson 2.2) cho kết quả: 5 thuật ngữ xung đột — "Resource", "Customer", "Project" ở mức 🔴 (cần Bounded Context riêng), "Utilization" và "Allocation" ở mức 🟡 (cần Glossary). CTO **Minh** phê duyệt giải pháp: *"OK, tách context cho 3 thuật ngữ đỏ. Nhưng tôi cần 1 tài liệu chính thức — mọi người, kể cả dev mới onboard tuần sau, phải hiểu thuật ngữ giống nhau. Viết ra đi."*
>
> Bạn cần viết Glossary — nhưng Glossary kiểu nào? Từ điển 1 dòng thì ai cũng bỏ qua. Bạn cần format mà cả PM, dev, **và AI Agent** đều đọc được.

## 🔥 Tension — "Tôi đã viết Glossary rồi mà!"

Bạn nhớ lại dự án trước. Team BA cũng viết Glossary. Nó trông thế này:

```
❌ Glossary kiểu từ điển:
   "Resource: Nhân sự trong công ty"
   "Customer: Khách hàng của ITO"
   "Project: Dự án mà ITO thực hiện"
```

3 dòng. Ai cũng gật đầu. Rồi sprint bắt đầu:

- Dev A đọc "Resource: Nhân sự" → tạo bảng `employees` với cột `name, email, phone`
- Dev B đọc cùng 1 definition → tạo bảng `resources` với cột `employee_id, skills[], seniority`

Cả hai đều **đúng** theo definition "Nhân sự trong công ty". Nhưng schema **khác nhau hoàn toàn**.

> **Tuấn** (RM) review 2 tuần sau: *"'employees' table không có skills. 'resources' table không có contract info. Tôi cần CẢ HAI. Bây giờ gộp hay tách?"*

**Kết quả:** Merge 2 bảng mất 3 ngày. Nếu Glossary entry ban đầu ghi rõ attributes + rules → **0 ngày**.

> 💭 **Câu hỏi:** Glossary 1 dòng = vô nghĩa. Glossary 10 trang = không ai đọc. Bao nhiêu là đủ?

## 🎓 Explanation — Glossary Template 8 phần

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Glossary entry phải đủ 8 phần. Nếu thiếu Attributes → dev tự thiết kế schema → drift. Nếu thiếu Business Rules → logic nằm trong đầu BA → bus factor = 1."*

### Template chuẩn

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

| Thiếu phần | Hậu quả tại ITO |
|---|---|
| **Definition** | Dev A hiểu khác Dev B → schema drift (vụ Resource ở trên) |
| **Attributes** | Dev tự thiết kế → merge conflict khi tích hợp |
| **Business Rules** | Logic nằm trong đầu BA → bus factor = 1 → BA nghỉ phép = team stop |
| **Phân biệt với** | Dev nhầm "Allocation" với "Assignment" → query sai table |
| **Đồng nghĩa** | Code review vẫn chấp nhận `getStaff()` thay vì `getResource()` → inconsistent |

### Ví dụ so sánh

#### ❌ Tệ — "Resource" bản cũ:

```
Resource: Nhân sự trong công ty.
```

→ Thiếu: context nào? attributes gì? khác "Employee" thế nào? rules gì?

#### ✅ Tốt — "Resource" bản CTEER:

```markdown
# Resource

## Definition
Một nhân sự thuộc biên chế ITO, có kỹ năng chuyên môn xác định,
và có thể được phân bổ (allocate) vào dự án để tạo ra giá trị billable.

## Bounded Context
Resource Management

## Attributes
| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| employee_id | string | ✅ | ID nhân sự (FK → HR system) |
| skills | Skill[] | ✅ | Danh sách kỹ năng + cấp độ |
| seniority | enum | ✅ | Junior / Mid / Senior / Principal |
| current_allocations | Allocation[] | ✅ | Các dự án đang tham gia + % |
| bench_since | date? | — | Ngày bắt đầu không có allocation |
| billable_rate | money | ✅ | Đơn giá/giờ (theo seniority + skill) |

## Business Rules
1. Tổng allocation ≤ 100% (1 resource không thể > full-time)
2. Allocation < 20% liên tục > 5 ngày → trạng thái "Bench"
3. Resource mới (< 3 tháng) → seniority "Junior" mặc định
4. Khi resource vào Bench → alert RM + Sales (để tìm project)

## States
Available → Allocated → Bench → Available (loop)
             └→ On Leave (tạm dừng allocation, giữ assignment)

## Phân biệt với
| Hay bị nhầm | Khác ở đâu |
|---|---|
| Employee (HR) | Employee = hợp đồng lao động. Resource = khả năng delivery. 1 Employee có thể không phải Resource (VD: kế toán) |
| User (Auth) | User = tài khoản đăng nhập. Resource = nhân sự allocatable |
| Team Member (PM) | Team Member = resource đã được assign vào project cụ thể |

## Đồng nghĩa nguy hiểm ⚠️
| ❌ Không dùng | Tại sao |
|---|---|
| Staff | Quá chung — bao gồm cả non-technical |
| Dev / Engineer | Quá hẹp — Resource bao gồm cả BA, QA, PM |
| Headcount | Thuật ngữ HR, tính theo FTE, không phải allocatable |

## Câu hỏi mở ❓
- [ ] Contractor (freelancer) có phải Resource không? → Cần Minh confirm
- [ ] Resource bán part-time cho 2 công ty con → tổng allocation tính thế nào?
```

### Quy trình viết Glossary

```
1. Chọn term xung đột cao nhất (từ Conflict Matrix — Lesson 2.2)
2. Draft definition (1-2 câu)
3. Liệt kê attributes (từ Domain Map + System Inventory — Module 1)
4. Viết business rules (từ Pain Points + stakeholder interview)
5. Tìm "phân biệt" + "đồng nghĩa nguy hiểm" (từ Conflict Matrix)
6. Ghi câu hỏi mở (cần stakeholders confirm)
7. Review với team → merge vào Glossary chính thức
```

### ⚖️ Trade-offs — Glossary Depth

| | Glossary 1 dòng (dictionary) | Glossary 8 phần (DDD) | Glossary 20+ phần (Enterprise) |
|---|---|---|---|
| **Được** | 5 phút viết, dễ scan | Balance: đủ chi tiết cho dev, không quá dài | Cực kỳ chi tiết, cover mọi edge case |
| **Mất** | Không giải quyết xung đột, schema drift | 30-45 phút/entry, cần domain expert time | 2+ giờ/entry, không ai đọc hết, stale nhanh |
| **Khi nào** | Internal tool nhỏ, 1 dev | Hầu hết dự án DDD | Regulated industry (banking, healthcare) |

---

## 🏋️ Exercise — Viết Glossary entries

### Phần A: Viết "Allocation" cho ITO (15 phút)

Dùng template 8 phần, viết Glossary entry cho **Allocation** trong Resource Management context:

```markdown
# Allocation

## Definition
[1-2 câu — không mơ hồ. Gợi ý: Allocation = việc gán % thời gian...]

## Bounded Context
[Context nào?]

## Attributes
| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|

## Business Rules
1. [Rule 1 — gợi ý: tổng % ≤ ?]
2. [Rule 2]

## States
[Lifecycle?]

## Phân biệt với
| Hay bị nhầm | Khác ở đâu |
|---|---|

## Đồng nghĩa nguy hiểm ⚠️
| ❌ Không dùng | Tại sao |
|---|---|

## Câu hỏi mở ❓
- [ ] [Câu hỏi cần Tuấn/Minh confirm]
```

### Phần B: Viết "Shipment" cho Logistics (15 phút)

Viết Glossary entry cho **Shipment** trong Transportation Management context:

```markdown
# Shipment

## Definition
[1-2 câu. Gợi ý: Shipment = 1 lô hàng cụ thể được giao cho...]

## Bounded Context
[?]

## Attributes
| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|

## Business Rules
1. [Rule 1 — gợi ý: weight vs vehicle capacity?]
2. [Rule 2]

## States
[Created → ? → ? → Delivered → ?]

## Phân biệt với
| Hay bị nhầm | Khác ở đâu |
|---|---|

## Đồng nghĩa nguy hiểm ⚠️
| ❌ Không dùng | Tại sao |
|---|---|

## Câu hỏi mở ❓
- [ ] [?]
```

### Self-check

| Tiêu chí | Allocation | Shipment |
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

1. **Phần nào trong template khó viết nhất? Tại sao?** Gợi ý: thường là "Business Rules" — vì rules nằm trong đầu domain expert (Tuấn, Hà), chưa bao giờ được ghi ra. Kỹ thuật: interview Tuấn với câu hỏi *"Khi nào thì X không được phép xảy ra?"* — mỗi câu trả lời = 1 rule.

2. **Glossary nên do ai viết? Developer? BA? Domain Expert?** Gợi ý 3 vai trò khác nhau: BA **viết draft** (có kỹ năng document), Domain Expert **review content** (biết rules thật), Developer **review attributes** (biết data model).

3. **Glossary có phải "xong 1 lần"?** Không. Rules thay đổi → Glossary phải update. Ví dụ: ITO đổi policy "Bench = 5 ngày" thành "Bench = 3 ngày" → Glossary entry "Resource" phải cập nhật Rule #2. **Ai** sẽ trigger update? Gợi ý: cần process, không chỉ file.

---

## ✅ Completion Checklist
- [ ] **Recall:** Liệt kê 8 phần của Glossary template, giải thích tại sao thiếu "Attributes" gây schema drift
- [ ] **Apply:** Viết "Allocation" (ITO) + "Shipment" (Logistics) đủ 8 phần, self-check ≥5/7
- [ ] **Analyze:** So sánh Glossary 1 dòng vs 8 phần — dùng ví dụ "Resource: Nhân sự" gây 3 ngày merge

---

## 🏁 Hoàn thành Module 2 — Ubiquitous Language

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 2.1 Tại sao ngôn ngữ quan trọng | Phát hiện 3 loại xung đột ngôn ngữ | Biết chi phí thực: 7.5h/tuần, 100 triệu/vụ |
| 2.2 Phát hiện xung đột | Xây Conflict Matrix cho ITO + Logistics | Bảng 5 term × 5 vai trò, rank 🟢🟡🔴 |
| 2.3 Viết Glossary chuẩn | Viết Glossary entry 8 phần | Template chuẩn, ≥2 entries hoàn chỉnh |

**Deliverable:** `practice/glossary/` — ≥6 Glossary entries

> 🔗 **Tiếp theo — Module 3: Event Storming.** Bạn có Domain Map (Module 1) + Glossary (Module 2). Nhưng cả hai đều **tĩnh** — chúng mô tả "có gì" chứ không mô tả "xảy ra gì". Khi Hà ký deal, chuyện gì xảy ra theo trình tự? Ai nhận thông báo? Hệ thống nào phải cập nhật? Module 3 sẽ dạy bạn **Event Storming** — kỹ thuật khám phá luồng sự kiện nghiệp vụ, biến Domain Map tĩnh thành **bản đồ hành vi sống** của tổ chức.
