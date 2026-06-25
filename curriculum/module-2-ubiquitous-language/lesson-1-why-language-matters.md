---
type: lesson
module: 2
lesson: 1
title: "Tại sao ngôn ngữ quan trọng"
duration: "25 phút"
prerequisites: ["module-1"]
---

# Lesson 2.1: Tại sao ngôn ngữ quan trọng

## 🎓 Concept — "Cùng 1 từ, 5 nghĩa khác nhau"

### Câu chuyện thực tế

Trong một buổi họp Sprint Planning ở công ty ITO:

```
PM:              "Resource cho project Alpha chưa ready"
Sales:           "Resource nào? Tôi đã confirm với khách rồi"
Resource Mgr:    "Resource nào? Tôi có 3 người available"
HR:              "Resource nào? Headcount report tháng này đã gửi"
CEO:             "...Các bạn đang nói về cái gì vậy?"
```

5 người trong phòng, cùng nói "Resource", nhưng:
- PM: "người trong team tôi" (team member)
- Sales: "capability tôi đã bán" (deliverable)
- RM: "kỹ sư có skill cụ thể" (allocatable person)
- HR: "nhân viên trong biên chế" (employee)
- CEO: "???"

→ Buổi họp đó mất 30 phút chỉ để clarify ai đang nói gì. Đây là **chi phí thực** của việc không có Ubiquitous Language.

### 3 loại xung đột ngôn ngữ

#### Loại 1: Đồng nghĩa nguy hiểm (Dangerous Synonyms)
Nhiều từ khác nhau chỉ **cùng 1 thứ:**

```
"Resource" = "Dev" = "Staff" = "Member" = "Engineer" = "Người"

→ Trong database: column tên gì?
→ Trong API: parameter tên gì?
→ Trong code: variable tên gì?

Nếu mỗi developer chọn 1 từ khác nhau:
  userService.getStaff()
  projectService.getMembers()
  resourceService.getEngineers()
  
→ 3 function, có thể trả về cùng 1 data hoặc khác nhau → bug
```

#### Loại 2: Đồng âm nguy hiểm (Dangerous Homonyms)
1 từ chỉ **nhiều thứ khác nhau:**

```
"Project" trong Sales context = Opportunity (chưa ký)
"Project" trong Delivery context = Dự án đang chạy (đã ký)
"Project" trong Finance context = Work Order (đơn vị billing)

→ Sales: "Project ABC đang rất tốt" (đang negotiate)
→ PM: "OK, tôi setup sprint cho Project ABC" (tưởng đã ký)
→ CEO: "Tại sao chúng ta delivery mà chưa ký HĐ???"
```

#### Loại 3: Thiếu từ (Missing Terms)
Concept tồn tại nhưng **không có tên chính thức:**

```
Hiện tượng: "Resource không có project trong 5 ngày"
Ai cũng biết, nhưng:
- RM gọi: "rảnh"
- PM gọi: "idle"
- Sales gọi: "available"
- Finance gọi: "non-billable"

→ Không ai đo lường → không ai quản lý → mất hàng trăm nghìn USD/năm

Giải pháp DDD: Đặt tên → "Bench" → Có tên → Có KPI → Có action
```

### Ví dụ Logistics

```
"Delivery" trong công ty vận tải:
- Dispatcher: "chuyến hàng" (shipment)
- Warehouse: "giao hàng cho tài xế" (handover)
- Tài xế: "giao hàng cho khách" (last mile)
- Sales: "dịch vụ giao hàng" (service offering)

→ "Delivery scheduled for tomorrow" → giao hàng nào? cho ai? ở đâu trong chuỗi?
```

### Eric Evans nói gì?

> *"Within a Bounded Context, use the same language in code, conversation, and documentation. If the team doesn't share a language, there is no shared understanding."*

Ubiquitous Language = ngôn ngữ **được thống nhất** và **dùng ở mọi nơi** — meeting, code, database, API, tài liệu.

---

## 🏋️ Exercise — Phát hiện xung đột

### Phần A: Tìm Dangerous Synonyms (10 phút)

Trong ITO, những từ nào đang dùng thay thế cho nhau nhưng gây nhầm lẫn?

| Từ gốc | Các từ đồng nghĩa đang dùng | Ai dùng từ nào? |
|---|---|---|
| Resource | | |
| Customer | | |
| Project | | |

### Phần B: Tìm Dangerous Homonyms (10 phút)

Từ nào trong Logistics có >1 nghĩa?

| Từ | Nghĩa 1 (Context A) | Nghĩa 2 (Context B) | Nguy hiểm thế nào? |
|---|---|---|---|
| "Delivery" | | | |
| "Order" | | | |
| "Route" | | | |

---

## 🪞 Reflect

1. **Trong dự án gần nhất của bạn, có từ nào 2 team dùng khác nghĩa không?** Hậu quả là gì?

2. **Tại sao code nên dùng đúng thuật ngữ Glossary?** Nếu Glossary gọi là "Allocation" mà code gọi là "assignment" — vấn đề gì xảy ra khi onboard dev mới?

3. **AI có bị ảnh hưởng bởi xung đột ngôn ngữ không?** Nếu bạn prompt: "Generate API for Resource" mà không có Glossary — AI sẽ generate gì?

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích được 3 loại xung đột ngôn ngữ (Synonym, Homonym, Missing)
- [ ] Tìm được ≥3 Dangerous Synonyms trong ITO
- [ ] Tìm được ≥2 Dangerous Homonyms trong Logistics
- [ ] Trả lời ≥2/3 câu hỏi reflection
