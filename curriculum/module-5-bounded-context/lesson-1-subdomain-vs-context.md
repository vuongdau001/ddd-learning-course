---
type: lesson
module: 5
lesson: 1
title: "Subdomain vs Bounded Context"
duration: "30 phút"
prerequisites: ["module-4"]
---

# Lesson 5.1: Subdomain vs Bounded Context

## 🎓 Concept — "Vấn đề ≠ Giải pháp"

### Sự nhầm lẫn phổ biến nhất trong DDD

90% người học DDD lần đầu nhầm Subdomain và Bounded Context là một. Đây là sai lầm nguy hiểm nhất.

```
Subdomain = Problem Space  → "Vấn đề gì cần giải quyết?"
Bounded Context = Solution Space → "Giải pháp được tổ chức thế nào?"
```

### Analogy — Bản đồ thành phố

```
Subdomain    = Khu vực địa lý (Quận 1, Quận 7, Thủ Đức)
               → Tồn tại khách quan, bạn không tạo ra được
               → Được định nghĩa bởi thực tế (dân cư, kinh tế)

Bounded Context = Đơn vị hành chính (UBND Quận 1, Sở Giao thông)
                  → Do con người tạo ra, có thể thay đổi
                  → Được thiết kế để quản lý hiệu quả
```

Quận 1 luôn ở đó (Subdomain), nhưng UBND Quận 1 có thể sáp nhập với Quận 3 (Bounded Context thay đổi).

### Định nghĩa chính thức

| | Subdomain | Bounded Context |
|---|---|---|
| **Thuộc về** | Problem Space | Solution Space |
| **Ai quyết định?** | Business (tồn tại tự nhiên) | Team kỹ thuật (thiết kế) |
| **Thay đổi?** | Ít khi (business thay đổi chậm) | Có thể (refactor, tách/gộp) |
| **Mối quan hệ** | 1:1 (lý tưởng) hoặc 1:N hoặc N:1 | N/A |
| **Ví dụ** | "Resource Management" (domain) | "Resource Service" (microservice) |

### 3 kiểu mapping Subdomain → Bounded Context

#### Kiểu 1: 1 Subdomain = 1 Bounded Context (Lý tưởng)
```
[Resource Management] ──── [Resource Context]
[Opportunity Management] ── [Opportunity Context]
```
Mỗi domain có 1 solution riêng. Clean nhất.

#### Kiểu 2: 1 Subdomain = N Bounded Context (Tách)
```
[Resource Management] ──┬── [Skill Inventory Context]
                        ├── [Allocation Context]  
                        └── [Utilization Context]
```
Khi domain quá lớn → tách thành nhiều contexts. Dấu hiệu: >30 events, >2 teams.

#### Kiểu 3: N Subdomain = 1 Bounded Context (Gộp — thường là xấu)
```
[Resource Management] ──┐
[Delivery Management] ──┼── [Monolith CRM Context]
[Opportunity Management]┘
```
Khi team nhỏ "gộp hết cho nhanh". Ban đầu OK, nhưng dần thành Big Ball of Mud.

### Ví dụ — ITO

| Subdomain (Problem) | Bounded Context (Solution) | Kiểu | Lý do |
|---|---|:---:|---|
| Resource Management | Resource Context | 1:1 | Core, cần isolation |
| Opportunity Management | Sales Context | 1:1 | Core, cần isolation |
| Contract + Billing | Finance Context | N:1 | Team nhỏ, gộp OK (Supporting) |
| Auth + Notification | Shared Context | N:1 | Generic, dùng SaaS |

### Ví dụ — Logistics

| Subdomain (Problem) | Bounded Context (Solution) | Kiểu | Lý do |
|---|---|:---:|---|
| Route Optimization | Route Context | 1:1 | Core, phức tạp, cần isolation |
| Fleet + Vehicle | Fleet Context | N:1 | Gộp vì closely related |
| Order + POD | Order Context | N:1 | Gộp vì share lifecycle |
| Warehouse | Warehouse Context | 1:1 | Đội riêng, WMS riêng |
| Real-time Tracking | Tracking Context | 1:1 | Tech stack khác (real-time) |

### Dấu hiệu cần TÁCH 1 Bounded Context

| # | Dấu hiệu | Hành động |
|:---:|---|---|
| 1 | 2 teams làm trên cùng 1 codebase, hay conflict | Tách |
| 2 | Deploy 1 feature → break feature khác | Tách |
| 3 | Cùng entity nhưng 2 nghĩa khác nhau (VD: "Resource") | Tách |
| 4 | >30 events trong 1 Event Storm | Xem xét tách |
| 5 | Một phần cần scale, phần khác không | Tách |

### Dấu hiệu nên GỘP Bounded Context

| # | Dấu hiệu | Hành động |
|:---:|---|---|
| 1 | 2 contexts luôn deploy cùng nhau | Gộp |
| 2 | Cross-context calls quá nhiều (chatty) | Gộp hoặc redesign |
| 3 | Team chỉ có 1-2 người quản lý cả hai | Gộp (tạm thời) |

---

## 🏋️ Exercise — Mapping Subdomain → Bounded Context

### Phần A: ITO (10 phút)

Từ Domain Map (Module 1), map mỗi Subdomain → Bounded Context:

| Subdomain | → Bounded Context(s) | Kiểu (1:1/1:N/N:1) | Lý do |
|---|---|:---:|---|
| Resource Management | | | |
| Opportunity Management | | | |
| Delivery Management | | | |
| Contract Management | | | |
| Customer Success | | | |
| Billing | | | |
| Knowledge Management | | | |

Tổng bao nhiêu Bounded Contexts? ___

### Phần B: Logistics (10 phút)

| Subdomain | → Bounded Context(s) | Kiểu | Lý do |
|---|---|:---:|---|
| Route Optimization | | | |
| Fleet Management | | | |
| Real-time Tracking | | | |
| Warehouse | | | |
| Order Management | | | |
| POD | | | |

---

## 🪞 Reflect

1. **Tại sao N:1 mapping (gộp nhiều domain vào 1 context) thường là "nợ kỹ thuật"?** Khi nào nó acceptable?

2. **Team size ảnh hưởng thế nào đến quyết định tách/gộp?** Amazon's "Two Pizza Rule" nói gì?

3. **Nếu phát hiện Context quá lớn SAU KHI đã code — refactor thế nào?** Gợi ý: Strangler Fig Pattern.

---

## ✅ Hoàn thành lesson khi
- [ ] Phân biệt Subdomain vs Bounded Context
- [ ] Giải thích 3 kiểu mapping (1:1, 1:N, N:1)
- [ ] Map ≥5 subdomains cho ITO
- [ ] Map ≥5 subdomains cho Logistics
