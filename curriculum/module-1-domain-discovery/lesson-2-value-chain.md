---
type: lesson
module: 1
lesson: 2
title: "Value Chain Analysis"
duration: "30 phút"
prerequisites: ["module-1/lesson-1"]
---

# Lesson 1.2: Value Chain Analysis

## 🎓 Concept — "Đi theo dòng tiền"

### Tại sao bắt đầu từ Value Chain?

Khi bạn đến một công ty mới và hỏi "có những domain nào?", sẽ không ai trả lời được. Nhưng nếu bạn hỏi **"Tiền vào từ đâu? Qua bao nhiêu bước để ra tiền?"** — ai cũng trả lời được.

Value Chain Analysis là kỹ thuật **biến câu hỏi kinh doanh thành Domain Map**.

### Nguyên lý

```
Mọi tổ chức đều có 1 chuỗi:

[Đầu vào] → [Bước 1] → [Bước 2] → ... → [Bước N] → [Doanh thu]
                                                          ↓
                                                    [Khách quay lại?]
```

Mỗi bước lớn trong chuỗi này = ít nhất 1 Domain tiềm năng.

### Câu hỏi dẫn dắt (dùng khi interview stakeholders)

| # | Câu hỏi | Mục đích |
|:---:|---|---|
| 1 | "Khách hàng/người dùng của bạn là ai?" | Xác định đầu vào |
| 2 | "Tiền vào từ đâu?" | Xác định nguồn revenue |
| 3 | "Từ khi tiếp cận khách đến khi nhận tiền — qua bao nhiêu bước?" | Vẽ value chain |
| 4 | "Bước nào hay bị nghẽn/chậm nhất?" | Xác định Core Domain tiềm năng |
| 5 | "Sau khi nhận tiền lần đầu, làm gì để khách quay lại?" | Phát hiện domain bị thiếu |

### Ví dụ — ITO CRM

```
Khách hàng: Doanh nghiệp Hàn/Nhật cần outsource IT

Lead → Qualify → Meeting → Proposal → Negotiate → Contract → Staffing → Delivery → Support
                                                                  ↑
                                                          Bottleneck #1
                                                       (3-5 ngày tìm người)
```

Mỗi bước → 1 domain:
- Lead, Qualify, Meeting → **Opportunity Management**
- Proposal → **Proposal Management**
- Contract → **Contract Management**
- Staffing → **Resource Management**
- Delivery → **Delivery Management**
- Support → **Customer Success** (nếu có)

### Ví dụ — Logistics (vận tải hàng hóa)

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

### So sánh 2 case study

| | ITO CRM | Logistics |
|---|---|---|
| Đầu vào | Lead (quan tâm) | Đơn hàng (cần vận chuyển) |
| Bottleneck | Staffing (tìm người) | Route Optimization (tuyến đường) |
| Domain bị thiếu | Customer Success | Real-time Tracking |
| Core Domain | Resource Management | Route Optimization |

→ **Pattern:** Bottleneck thường = Core Domain. Domain bị thiếu = cơ hội lớn nhất.

---

## 🏋️ Exercise — Vẽ Value Chain

### Phần A: Tự vẽ cho ITO (5 phút)
Không nhìn ví dụ ở trên. Tự viết lại value chain của ITO từ bộ nhớ:

```
[        ] → [        ] → [        ] → [        ] → [        ] → [        ]
```

Trả lời: Bước nào bottleneck nhất? _______________

### Phần B: Vẽ cho Logistics (10 phút)
Tưởng tượng bạn là consultant mới vào công ty vận tải. Vẽ value chain:

```
[        ] → [        ] → [        ] → [        ] → [        ] → [        ]
```

Trả lời:
- Bottleneck nhất: _______________
- Domain nào có thể là Core? _______________
- Domain nào chưa có hệ thống hỗ trợ? _______________

### Phần C: Vẽ cho 1 tổ chức bất kỳ (10 phút)
Chọn 1 công ty/dự án bạn đã từng làm việc. Vẽ value chain:

```
Tổ chức: _______________
[        ] → [        ] → [        ] → [        ] → [        ]

Bottleneck: _______________
Core Domain tiềm năng: _______________
```

---

## 🪞 Reflect

1. **Tại sao "đi theo dòng tiền" lại phát hiện domain tốt hơn "liệt kê chức năng"?**

2. **Bottleneck có phải luôn luôn là Core Domain không?** Nghĩ về trường hợp bottleneck nằm ở bước Generic (VD: login quá chậm).

3. **Nếu công ty của bạn không có revenue trực tiếp (VD: tổ chức phi lợi nhuận, nội bộ) — bạn sẽ hỏi câu gì thay cho "tiền vào từ đâu"?**

---

## ✅ Hoàn thành lesson khi
- [ ] Vẽ được Value Chain cho ITO (≥6 bước)
- [ ] Vẽ được Value Chain cho Logistics (≥6 bước)
- [ ] Xác định được bottleneck cho cả 2
- [ ] Trả lời ≥2/3 câu hỏi reflection
