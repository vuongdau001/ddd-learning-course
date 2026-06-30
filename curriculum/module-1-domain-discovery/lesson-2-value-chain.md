---
type: lesson
module: 1
lesson: 2
title: "Value Chain Analysis"
duration: "30 phút"
prerequisites: ["module-1/lesson-1"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Khảo sát"
business_invariant: "Tiền đi từ Lead → Deal → Contract → Invoice — mỗi bước phải traceable"
---

# Lesson 1.2: Value Chain Analysis — "Đi theo dòng tiền"

## 📍 Context — Bạn đang ở đây

> Sau khi chứng kiến **Khoa** mất 600 triệu vì bắt đầu từ màn hình (Lesson 1.1), bạn đã thuyết phục CTO **Minh** cho team 2 tuần khảo sát nghiệp vụ trước khi code. Minh đồng ý, nhưng nói: *"2 tuần là giới hạn. Sau 2 tuần tôi cần biết: build cái gì trước, cái gì sau, tại sao."*
>
> Bạn bắt đầu tuần đầu tiên bằng câu hỏi: **Tiền vào ITO Corp từ đâu? Qua bao nhiêu bước để ra tiền?**

## 🔥 Tension — "Ai cũng nói mình quan trọng nhất"

Bạn setup 3 buổi phỏng vấn với stakeholders chính:

**Hà** (VP Sales) nói:
> *"Sales pipeline là trung tâm mọi thứ. Không có deal → không có tiền. Nhưng hiện tại tôi track deal bằng Excel, mỗi Sales Manager có file riêng. Tôi không thấy tổng pipeline value. Tuần trước mất 1 deal 2 tỷ vì 2 người cùng approach cùng 1 khách."*

**Tuấn** (Resource Manager) phản bác:
> *"Sales ký deal nhưng tôi mới là người tìm resource. Staffing mất 3-5 ngày vì không biết ai đang rảnh. Có lần Sales ký deal cần 10 Java Senior → tôi chỉ có 3 trên bench → deal bị delay 2 tháng → khách phạt penalty."*

**CFO** (qua email) nói:
> *"Hai bạn đừng cãi nhau. Vấn đề lớn nhất là: từ lúc ký contract đến lúc nhận tiền mất 90 ngày trung bình. Invoice gửi trễ, thanh toán theo kiểu thủ công. Cash flow bị ảnh hưởng nghiêm trọng."*

> 💭 **Câu hỏi:** Cả 3 người đều nói đúng. Vậy bạn sẽ liệt kê "chức năng" cho từng người? Hay có cách nào thấy **tổng thể** — biết phần nào liên quan đến phần nào?

## 🎓 Explanation — Value Chain Analysis

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Tiền đi từ Lead → Deal → Contract → Invoice → Payment — mỗi bước phải traceable. Nếu mất dấu ở bước nào, ITO Corp mất tiền ở bước đó."*

Thay vì liệt kê chức năng riêng lẻ theo yêu cầu từng người, bạn cần vẽ **dòng chảy giá trị** xuyên suốt tổ chức.

### Nguyên lý

```
Mọi tổ chức đều có 1 chuỗi:

[Đầu vào] → [Bước 1] → [Bước 2] → ... → [Bước N] → [Doanh thu]
                                                          ↓
                                                    [Khách quay lại?]
```

Mỗi bước lớn trong chuỗi này = ít nhất 1 Domain tiềm năng.

### Câu hỏi dẫn dắt (dùng khi interview stakeholders)

| # | Câu hỏi | Mục đích | Bạn hỏi ai ở ITO? |
|:---:|---|---|---|
| 1 | "Khách hàng là ai?" | Xác định đầu vào | Hà (VP Sales) |
| 2 | "Tiền vào từ đâu?" | Xác định nguồn revenue | CFO |
| 3 | "Từ khi tiếp cận khách đến khi nhận tiền — qua bao nhiêu bước?" | Vẽ value chain | Hà + Tuấn + CFO |
| 4 | "Bước nào hay bị nghẽn/chậm nhất?" | Xác định Core Domain tiềm năng | Tuấn (Staffing bottleneck) |
| 5 | "Sau khi nhận tiền lần đầu, làm gì để khách quay lại?" | Phát hiện domain bị thiếu | Hà (Customer Success?) |

### Vẽ Value Chain cho ITO Corp

Từ 3 buổi phỏng vấn, bạn tổng hợp được dòng chảy:

```
Khách hàng: Doanh nghiệp Hàn/Nhật cần outsource IT

Lead → Qualify → Meeting → Proposal → Negotiate → Contract → Staffing → Delivery → Support
 ↑       ↑         ↑                                  ↑          ↑
 Hà      Hà        Hà                                CFO       Tuấn
                                                    (90 ngày!) (3-5 ngày
                                                               tìm người)
```

Mỗi bước → 1 domain tiềm năng:

| Value Chain Step | Domain tiềm năng | Stakeholder | Đau nhất? |
|---|---|---|---|
| Lead → Qualify → Meeting → Proposal → Negotiate | **Sales Pipeline** | Hà | ⚠️ (deal trùng, mất 2 tỷ) |
| Contract | **Contract Management** | CFO | ⚠️ (invoice trễ 90 ngày) |
| Staffing | **Resource Management** | Tuấn | 🔴 (3-5 ngày tìm người, penalty) |
| Delivery | **Delivery Management** | PM Team | Trung bình |
| Support | **Customer Success** | (Chưa có ai!) | ❓ (domain bị thiếu) |

> **Pattern phát hiện:** Bottleneck thường = Core Domain. Domain chưa có người phụ trách = cơ hội lớn nhất.

### Ví dụ — Logistics (so sánh)

```
Khách hàng: Doanh nghiệp cần vận chuyển hàng

Đơn hàng → Định tuyến → Phân xe → Xếp hàng → Vận chuyển → Giao hàng → Xác nhận → Thanh toán
              ↑                                                ↑
        Bottleneck #1                                   Bottleneck #2
     (tối ưu tuyến phức tạp)                       (POD delay, thiếu ký)
```

Mỗi bước → 1 domain:
- Đơn hàng → **Order Management**
- Định tuyến → **Route Optimization**
- Phân xe → **Fleet Management**
- Xếp hàng → **Warehouse Management**
- Vận chuyển + Giao hàng → **Transportation Management**
- Xác nhận → **Proof of Delivery (POD)**
- Thanh toán → **Billing**

### So sánh 2 tổ chức

| | ITO CRM | Logistics |
|---|---|---|
| Đầu vào | Lead (quan tâm outsource IT) | Đơn hàng (cần vận chuyển) |
| Bottleneck | Staffing (3-5 ngày tìm resource) | Route Optimization (tuyến phức tạp) |
| Domain bị thiếu | Customer Success (chưa có ai!) | Real-time Tracking |
| Core Domain | Resource Management | Route Optimization |

### ⚖️ Trade-offs — Value Chain vs Feature List

| | Value Chain Analysis | Feature List từ stakeholders |
|---|---|---|
| **Được** | Thấy dependencies giữa domains; phát hiện domain bị thiếu; có cơ sở ưu tiên | Stakeholders thấy quen, nhanh lấy input |
| **Mất** | Cần interview 3-5 người, mất 2-3 ngày; stakeholders phải tư duy end-to-end | Feature list không thấy liên kết; mỗi người chỉ thấy phần mình |
| **Khi nào dùng** | Hệ thống cross-department, nhiều domain | App đơn giản, 1-2 domain, 1 stakeholder |

---

## 🏋️ Exercise — Quyết định thiết kế dưới ràng buộc

### Phần A: Tự vẽ Value Chain cho ITO (5 phút)

Không nhìn lại ví dụ ở trên. Tự viết lại value chain của ITO Corp từ bộ nhớ:

```
[        ] → [        ] → [        ] → [        ] → [        ] → [        ]
```

Trả lời:
- Bước nào bottleneck nhất? _______________
- Ai sẽ phàn nàn trước tiên khi hệ thống mới không có bước này? _______________

### Phần B: Phân tích xung đột giữa stakeholders (15 phút)

Hà muốn Sales Pipeline lên trước. Tuấn muốn Resource Allocation lên trước. CFO muốn sửa invoice flow.

| Stakeholder | Domain ưu tiên | Lý do kinh doanh | Impact nếu delay |
|---|---|---|---|
| Hà | Sales Pipeline | ? | ? |
| Tuấn | Resource Allocation | ? | ? |
| CFO | Contract & Billing | ? | ? |

**Ràng buộc:** Bạn chỉ có 1 team dev, 4 tháng cho Phase 1.

> 💭 Bạn sẽ đề xuất deploy domain nào trước trong Phase 1? Dùng value chain để bảo vệ quyết định — domain nào là "upstream" của domain nào?

<details>
<summary>📝 Gợi ý phân tích</summary>

Nhìn value chain: Lead → ... → **Contract** → **Staffing** → Delivery

- Sales Pipeline nằm **upstream** — nếu không có deal, Tuấn và CFO cũng không có gì để làm.
- Nhưng Tuấn đang gây **penalty** (mất tiền thật) — tác động trực tiếp đến revenue hiện tại.
- CFO: 90 ngày invoice → cash flow problem, nhưng không mất deal.

**Gợi ý kết luận:** Sales Pipeline trước (vì upstream), Resource Allocation ngay sau (vì đang gây thiệt hại). Contract & Billing ở Phase 2. Đây chính là thứ tự migration mà bạn sẽ gặp lại ở Module 3 (Strategic Design).
</details>

### Phần C: Vẽ cho 1 tổ chức bất kỳ (5 phút)

Chọn 1 công ty/dự án bạn đã từng làm việc. Vẽ value chain:

```
Tổ chức: _______________
[        ] → [        ] → [        ] → [        ] → [        ]

Bottleneck: _______________
Core Domain tiềm năng: _______________
```

---

## 🪞 Reflect

1. **Tại sao "đi theo dòng tiền" phát hiện domain tốt hơn "liệt kê chức năng"?** Gợi ý: dòng tiền cho thấy **thứ tự dependency** — bạn không thể staffing nếu chưa có contract, không thể contract nếu chưa có deal.

2. **Bottleneck có phải luôn luôn là Core Domain không?** Nghĩ về trường hợp bottleneck nằm ở Generic domain (VD: login quá chậm, server quá tải). Bottleneck ≠ Core nếu đó là vấn đề infrastructure.

3. **Customer Success domain ở ITO Corp chưa có ai phụ trách — đây là red flag hay bình thường?** Gợi ý: Hà ký deal mới dễ hơn giữ khách cũ? Nếu churn rate cao → cần Customer Success domain. Nếu khách ký lại tự động → có thể defer.

---

## ✅ Completion Checklist
- [ ] **Recall:** Vẽ Value Chain cho ITO (≥6 bước) và Logistics (≥6 bước)
- [ ] **Apply:** Xác định bottleneck và domain bị thiếu cho cả 2
- [ ] **Analyze:** Bảo vệ quyết định domain nào deploy trước Phase 1, dùng value chain dependency làm luận cứ

---

> 🔗 **Tiếp theo:** Bạn đã vẽ được dòng tiền. Nhưng mỗi bước trong chuỗi này có bao nhiêu "nỗi đau" cụ thể? Bao nhiêu triệu đang bị mất mỗi tháng? Lesson tiếp — *Kỹ thuật Khám phá* — sẽ giúp bạn **đo lường** từng pain point bằng số, để khi trình bày với CTO Minh, bạn nói: *"Staffing chậm gây mất 500 triệu/quý"* thay vì *"Staffing hơi chậm"*.
