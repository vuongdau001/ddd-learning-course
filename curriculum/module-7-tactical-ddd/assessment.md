---
type: assessment
module: 7
title: "Quiz Module 7: Tactical DDD"
total_questions: 10
passing_score: 70
---

# Quiz Module 7: Tactical DDD

> **Hướng dẫn:** Trả lời trực tiếp dưới mỗi câu hỏi.
> Có 3 cấp độ: 🟢 Recall · 🟡 Apply · 🔴 Analyze.
> Đạt: ≥7/10 câu đúng.

---

## 🟢 Câu 1 — Recall
**Entity và Value Object khác nhau ở điểm nào? Cho 3 tiêu chí.**

Trả lời:

---

## 🟢 Câu 2 — Recall
**Aggregate Root có vai trò gì? Kể 2 quy tắc thiết kế Aggregate.**

Trả lời:

---

## 🟢 Câu 3 — Recall
**Domain Service khác Application Service ở điểm nào?**

Trả lời:

---

## 🟡 Câu 4 — Apply
**Trong ITO CRM, "Skill" là Entity hay Value Object? Giải thích. Nếu ITO muốn tracking skill proficiency theo thời gian (Junior→Mid→Senior), câu trả lời có thay đổi không?**

Trả lời:

---

## 🟡 Câu 5 — Apply
**Thiết kế Aggregate cho "Proposal" trong ITO CRM. Xác định Root, các Entity/VO bên trong, và ≥1 invariant.**

Trả lời:

---

## 🟡 Câu 6 — Apply
**Một developer tạo `AllocationRepository` riêng để CRUD allocations. Đây có phải anti-pattern không? Tại sao?**

Trả lời:

---

## 🟡 Câu 7 — Apply
**Viết interface Repository cho Trip aggregate (Logistics) với ≥4 methods dùng domain language.**

Trả lời:

---

## 🔴 Câu 8 — Analyze
**Team muốn đặt Resource + Allocation + Project vào cùng 1 aggregate để "đơn giản." Phân tích ưu/nhược của quyết định này. Quy tắc DDD nào bị vi phạm?**

Trả lời:

---

## 🔴 Câu 9 — Analyze
**So sánh 2 cách thiết kế "Delivery Address" — (A) Value Object trong Shipment aggregate vs (B) Entity riêng với AddressRepository. Khi nào dùng A? Khi nào dùng B?**

Trả lời:

---

## 🔴 Câu 10 — Analyze
**Trong Logistics, RouteOptimizer cần data từ Vehicle, Shipment, và TrafficCondition (3 aggregates). Nó nên là Domain Service hay Application Service? Ai cung cấp data cho nó? Vẽ sequence diagram đơn giản.**

Trả lời:

---

## Sau khi làm xong

Gửi lại cho instructor để review. Instructor sẽ:
1. Chấm từng câu
2. Cho feedback chi tiết
3. Ghi score vào `practice/progress.md`
4. Đề xuất ôn lại lesson nào (nếu cần)
