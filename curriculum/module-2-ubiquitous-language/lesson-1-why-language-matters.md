---
type: lesson
module: 2
lesson: 1
title: "Tại sao ngôn ngữ quan trọng"
duration: "25 phút"
prerequisites: ["module-1"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Phát hiện xung đột"
business_invariant: "Cùng từ 'Khách hàng' — Sales nghĩ Account, Finance nghĩ Payer → vỡ nếu gộp"
---

# Lesson 2.1: Tại sao ngôn ngữ quan trọng — "Cùng 1 từ, 5 nghĩa khác nhau"

## 📍 Context — Bạn đang ở đây

> Module 1 kết thúc với Domain Map hoàn chỉnh cho ITO Corp: 8 domains, phân pha MVP → Phase 2 → Phase 3. CTO **Minh** duyệt blueprint. Bạn bắt đầu sprint đầu tiên — Sales Pipeline (domain ưu tiên 1) và Resource Management (ưu tiên 2). Dev team ngồi cùng phòng với Sales (**Hà**) và Resource Manager (**Tuấn**) để hiểu nghiệp vụ.
>
> Tuần đầu sprint: mọi thứ bắt đầu vỡ — không phải vì code, mà vì **ngôn ngữ**.

## 🔥 Tension — "Mọi người đang nói gì vậy?"

Sprint Planning thứ hai. 5 người trong phòng, cùng nói "Resource", nhưng mỗi người hiểu khác:

```
PM:              "Resource cho project Alpha chưa ready"
Hà (Sales):      "Resource nào? Tôi đã confirm với khách rồi"
Tuấn (RM):       "Resource nào? Tôi có 3 người available"
HR:              "Resource nào? Headcount report tháng này đã gửi"
CTO Minh:        "...Các bạn đang nói về cái gì vậy?"
```

5 người, cùng 1 từ, 5 nghĩa:
- PM: "người trong team tôi" (team member)
- Hà: "capability tôi đã bán cho khách" (deliverable)
- Tuấn: "kỹ sư có skill cụ thể" (allocatable person)
- HR: "nhân viên trong biên chế" (employee)
- Minh: "???"

→ Buổi họp mất **30 phút** chỉ để clarify ai đang nói gì. Nhân 30 phút × 5 người × 3 buổi họp/tuần = **~7.5 giờ/tuần** lãng phí chỉ vì ngôn ngữ không thống nhất.

Nhưng tệ hơn nữa — sau buổi họp, dev viết code:

```typescript
// Dev A (nghe PM):
userService.getStaff()

// Dev B (nghe Tuấn):
resourceService.getEngineers()

// Dev C (nghe HR):
employeeService.getMembers()

// → 3 function, CÓ THỂ trả về cùng data hoặc khác → BUG
```

> 💭 **Câu hỏi:** Đây không phải lỗi code — 3 dev đều code đúng. Nhưng hệ thống sẽ lỗi vì **ngôn ngữ không thống nhất**. Làm sao fix?

## 🎓 Explanation — 3 loại xung đột ngôn ngữ

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mỗi thuật ngữ phải có 1 định nghĩa duy nhất trong mỗi context. Nếu cùng từ 'Khách hàng', Sales nghĩ Account (công ty ký hợp đồng), Finance nghĩ Payer (đối tượng thanh toán) — thì KHÔNG ĐƯỢC gộp vào 1 bảng `customers`."*

### Loại 1: Đồng nghĩa nguy hiểm (Dangerous Synonyms)

Nhiều từ khác nhau chỉ **cùng 1 thứ:**

```
"Resource" = "Dev" = "Staff" = "Member" = "Engineer" = "Người"

→ Trong database: column tên gì? resource_id? staff_id? employee_id?
→ Trong API: parameter tên gì?
→ Trong code: variable tên gì?

Nếu mỗi developer chọn 1 từ khác nhau:
  userService.getStaff()         // Dev A
  projectService.getMembers()    // Dev B
  resourceService.getEngineers() // Dev C
  
→ 3 function, có thể trả về cùng 1 data hoặc khác nhau → bug
```

### Loại 2: Đồng âm nguy hiểm (Dangerous Homonyms)

1 từ chỉ **nhiều thứ khác nhau** tùy context:

```
"Project" trong Sales context   = Opportunity (deal chưa ký)
"Project" trong Delivery context = Dự án đang chạy (đã ký contract)
"Project" trong Finance context  = Work Order (đơn vị billing)

→ Hà: "Project ABC đang rất tốt" (đang negotiate, chưa ký)
→ PM: "OK, tôi setup sprint cho Project ABC" (tưởng đã ký!)
→ Minh: "Tại sao chúng ta delivery mà chưa ký hợp đồng???"
```

**Đây chính là bug ngôn ngữ đã xảy ra thật tại ITO Corp** — PM setup team 5 người cho deal chưa ký, mất 2 tuần allocation sai = **~100 triệu.**

### Loại 3: Thiếu từ (Missing Terms)

Concept tồn tại nhưng **không có tên chính thức:**

```
Hiện tượng: "Resource không có project trong 5+ ngày"
Ai cũng biết, nhưng:
- Tuấn gọi: "rảnh"
- PM gọi: "idle"
- Hà gọi: "available"
- CFO gọi: "non-billable"

→ Không ai đo lường → không ai quản lý → mất hàng trăm triệu/năm

Giải pháp DDD: Đặt tên → "Bench"
→ Có tên → Có KPI (Bench Rate ≤ 5%) → Có action (alert khi > 5 ngày)
```

> **Pattern:** Nếu concept quan trọng nhưng không có tên chính thức → nó không được đo lường → không được quản lý → gây thiệt hại âm thầm.

### Ví dụ — Logistics

```
"Delivery" trong công ty vận tải:
- Dispatcher: "chuyến hàng" (shipment)
- Warehouse: "giao hàng cho tài xế" (handover)
- Tài xế: "giao hàng cho khách" (last mile delivery)
- Sales: "dịch vụ giao hàng" (service offering)

→ "Delivery scheduled for tomorrow" → giao hàng NÀO? cho AI? ở đâu trong chuỗi?
```

### Eric Evans nói gì?

> *"Within a Bounded Context, use the same language in code, conversation, and documentation. If the team doesn't share a language, there is no shared understanding."*

**Ubiquitous Language** = ngôn ngữ **được thống nhất** và **dùng ở mọi nơi** — meeting, code, database, API, tài liệu, Jira ticket.

### ⚖️ Trade-offs — Enforcing UL

| | Enforce Ubiquitous Language nghiêm ngặt | Để mọi người tự dùng từ quen |
|---|---|---|
| **Được** | Code nhất quán, giảm bug ngôn ngữ, AI prompt chính xác | Team không bị gián đoạn, không cần training |
| **Mất** | Mất 1-2 tuần đầu để thống nhất; một số stakeholders resist | 3-6 tháng sau: code base rời rạc, onboarding dev mới chậm 3× |
| **Khi nào** | Dự án >3 dev, >2 stakeholder groups | Solo dev, internal tool nhỏ |

---

## 🏋️ Exercise — Phát hiện xung đột tại ITO

### Phần A: Tìm Dangerous Synonyms (10 phút)

Trong ITO, những từ nào đang dùng thay thế nhau nhưng gây nhầm lẫn?

| Từ chuẩn (bạn đề xuất) | Các từ đồng nghĩa đang dùng | Ai dùng từ nào? | Risk nếu không thống nhất? |
|---|---|---|---|
| Resource | ? | ? | ? |
| Customer | ? | ? | ? |
| Project | ? | ? | ? |

### Phần B: Tìm Dangerous Homonyms (10 phút)

Từ nào trong ITO + Logistics có >1 nghĩa?

| Từ | Nghĩa 1 (Context A) | Nghĩa 2 (Context B) | Hậu quả nếu nhầm? |
|---|---|---|---|
| "Project" | ? (Sales context) | ? (Delivery context) | ? |
| "Delivery" | ? (Logistics: warehouse) | ? (Logistics: last mile) | ? |
| "Status" | ? | ? | ? |

### Phần C: Tìm Missing Terms (5 phút)

Concept nào ở ITO tồn tại nhưng chưa có tên chính thức? (Gợi ý: ngoài "Bench", còn gì?)

| Hiện tượng | Mọi người gọi là... | Tên chuẩn bạn đề xuất |
|---|---|---|
| Resource không có project >5 ngày | "rảnh", "idle", "available" | **Bench** |
| ? | ? | ? |
| ? | ? | ? |

---

## 🪞 Reflect

1. **Trong dự án gần nhất, có từ nào 2 team dùng khác nghĩa không?** Hậu quả là gì? Bao nhiêu giờ meeting lãng phí chỉ để clarify?

2. **Tại sao code nên dùng đúng thuật ngữ Glossary?** Nếu Glossary gọi là "Allocation" mà code gọi là `assignment` — khi onboard dev mới, họ phải "dịch" 2 lần: business term → glossary term → code term. Điều gì xảy ra khi có 50 term như vậy?

3. **AI có bị ảnh hưởng bởi xung đột ngôn ngữ?** Nếu bạn prompt: "Generate API for Resource" mà không có Glossary — AI sẽ generate generic CRUD. Nếu bạn prompt: "Generate API for Resource. Resource = allocatable engineer with skills, identity = employee ID, rules: allocation ≤ 100%" — kết quả khác hoàn toàn.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 3 loại xung đột ngôn ngữ (Synonym, Homonym, Missing) + ví dụ mỗi loại
- [ ] **Apply:** Tìm ≥3 Dangerous Synonyms + ≥2 Dangerous Homonyms trong ITO/Logistics
- [ ] **Analyze:** Ước tính chi phí thực (giờ/tiền) của xung đột ngôn ngữ tại ITO — dùng số liệu trong bài

---

> 🔗 **Tiếp theo:** Bạn đã biết 3 loại xung đột ngôn ngữ. Nhưng làm sao **phát hiện một cách có hệ thống** thay vì tình cờ gặp trong meeting? Bài tiếp — *Phát hiện xung đột* — sẽ dạy bạn kỹ thuật **so sánh ngôn ngữ cross-department**, để biết chắc: Hà và Tuấn đang nói cùng hay khác thứ, trước khi bạn viết dòng code đầu tiên.
