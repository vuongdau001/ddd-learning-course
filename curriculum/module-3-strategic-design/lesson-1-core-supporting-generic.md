---
type: lesson
module: 3
lesson: 1
title: "Core / Supporting / Generic"
duration: "30 phút"
prerequisites: ["module-1", "module-2"]
---

# Lesson 3.1: Core / Supporting / Generic

## 🎓 Concept — "Không phải domain nào cũng bằng nhau"

### Câu chuyện mở đầu

CEO giao cho team: *"Build CRM hệ thống quản lý toàn bộ — từ lead tới delivery, từ resource tới billing."*

Team estimate: 18 tháng, 10 người.

Nhưng nếu bạn hỏi: *"18 tháng để build cái gì? Login system? Email notifications? Hay Resource Matching Algorithm?"*

Login bạn có thể mua hoặc dùng Keycloak trong 2 ngày. Nhưng Resource Matching? Không ai bán thuật toán match resource theo skill + seniority + availability + cost cho đúng model ITO của bạn.

→ **Không phải domain nào cũng đáng để build từ đầu.**

### Core Domain không tự nhiên sinh ra

Trước khi phân loại domain, hãy nhớ: **Core Domain bắt nguồn từ chiến lược kinh doanh**, không phải từ kỹ thuật.

```
    Business Goal           ← CEO: "Trở thành ITO #1 về talent matching"
         │
         ▼
  Competitive Advantage     ← "Chúng ta match resource nhanh hơn, chính xác hơn"
         │
         ▼
    Core Domain             ← Resource Management = Core
         │
         ▼
  Investment Priority       ← Đội giỏi nhất, ngân sách lớn nhất cho Core
```

> 💡 **Nếu không rõ Business Goal → không thể xác định đúng Core Domain.** Hai công ty ITO cùng ngành nhưng chiến lược khác nhau sẽ có Core Domain khác nhau. VD: ITO chuyên giá rẻ → Core là Cost Optimization, không phải Resource Matching.

### 3 loại Domain

| Loại | Định nghĩa | Chiến lược | Ví dụ |
|---|---|---|---|
| **Core** | Tạo ra lợi thế cạnh tranh. Đây là thứ khiến bạn **khác biệt** so với đối thủ. | Build in-house, đội giỏi nhất, đầu tư nhiều nhất | Resource Matching, Route Optimization |
| **Supporting** | Cần thiết cho hoạt động, nhưng không tạo lợi thế cạnh tranh. Mọi đối thủ đều có. | Build đơn giản hoặc customize tool có sẵn | Reporting, Contract Management |
| **Generic** | Giống nhau ở mọi công ty. Không liên quan đến nghiệp vụ riêng. | Mua / dùng SaaS / dùng open-source | Authentication, Email, File Storage |

### The Copy Test — Phép thử quyết định

> *"Nếu đối thủ copy y nguyên hệ thống của domain này, bạn có mất lợi thế không?"*

| Nếu trả lời | Thì đó là | Ví dụ |
|---|---|---|
| **"Chết, mất toàn bộ lợi thế!"** | **Core** | Thuật toán matching resource của ITO |
| **"Phiền, nhưng không chết"** | **Supporting** | Dashboard báo cáo |
| **"Kệ, cái này ai cũng có"** | **Generic** | Login system |

### The Competitive Test — Phép thử bổ sung

> *"Nếu domain này tốt hơn đối thủ 10x, khách hàng có chọn bạn không?"*

| Nếu trả lời | Thì đó là |
|---|---|
| **"Có! Khách chọn ITO vì match resource nhanh hơn"** | **Core** |
| **"Không ai chọn ITO vì dashboard đẹp hơn"** | **Supporting hoặc Generic** |

### Ví dụ — ITO CRM

| Domain | Loại | Lý do | Copy Test |
|---|:---:|---|---|
| **Resource Management** | 🔴 Core | Matching skill + availability + cost → lợi thế cạnh tranh | Copy → mất lợi thế |
| **Opportunity Management** | 🔴 Core | Pipeline quality → win rate → revenue | Copy → mất win rate |
| Delivery Management | 🟡 Supporting | Cần thiết nhưng mọi ITO đều quản lý dự án giống nhau | Copy → phiền nhưng OK |
| Contract Management | 🟡 Supporting | HĐ quan trọng nhưng không tạo lợi thế | Copy → kệ |
| Billing | 🟡 Supporting | Cần nhưng standard | Copy → kệ |
| **Customer Success** | 🔴 Core | Khách quay lại = revenue recurring, ít ITO làm tốt | Copy → mất retention |
| Authentication | ⚪ Generic | Giống mọi nơi | Dùng Keycloak |
| Notification | ⚪ Generic | Gửi email/SMS, standard | Dùng SendGrid |

### Ví dụ — Logistics

| Domain | Loại | Lý do | Copy Test |
|---|:---:|---|---|
| **Route Optimization** | 🔴 Core | Tối ưu chi phí vận chuyển = lợi thế cạnh tranh | Copy → mất lợi thế giá |
| **Real-time Tracking** | 🔴 Core | Khách chọn vì biết hàng ở đâu real-time | Copy → mất UX advantage |
| Fleet Management | 🟡 Supporting | Quản lý xe cần nhưng standard | Copy → phiền nhưng OK |
| Warehouse | 🟡 Supporting | Cần nhưng dùng WMS có sẵn được | Copy → kệ |
| Order Management | 🟡 Supporting | Standard CRUD | Dùng ERP module |
| POD (Proof of Delivery) | 🟡 Supporting | Cần nhưng logic đơn giản | Build đơn giản |
| Billing | ⚪ Generic | Giống mọi nơi | Dùng accounting software |
| Auth | ⚪ Generic | Standard | Dùng SaaS |

### Bẫy thường gặp

#### Bẫy 1: "Mọi thứ đều Core"
```
CEO: "Tất cả đều quan trọng!"
Thực tế: Nếu mọi thứ đều Core → không có gì Core → phân tán resource → thua
Quy tắc: ≤ 2-3 Core Domains. Không hơn.
```

#### Bẫy 2: "Core vĩnh viễn"
```
Ngày xưa: "GPS Tracking" là Core Domain cho Logistics
Ngày nay: Google Maps API → Generic (mua được dễ dàng)
→ Core có thể trở thành Generic theo thời gian khi technology commoditize
```

#### Bẫy 3: "Build tất cả"
```
Team nhỏ 5 người build cả Auth + Notification + Dashboard + Core
→ 80% effort cho Generic/Supporting, 20% cho Core
→ Core bị chậm → đối thủ có Core trước → thua

Đúng: Mua/dùng SaaS cho Generic → 80% effort cho Core
```

---

## 🏋️ Exercise — Phân loại Domain

### Phần A: ITO — The Copy Test (10 phút)

Áp dụng Copy Test cho từng domain. Điền:

| Domain | Copy → mất lợi thế? | Core / Supporting / Generic | Lý do |
|---|:---:|:---:|---|
| Resource Management | | | |
| Opportunity Management | | | |
| Delivery Management | | | |
| Contract Management | | | |
| Customer Success | | | |
| Billing | | | |
| Knowledge Management | | | |

Đếm: bao nhiêu Core? ___  Nếu > 3 → xem lại.

### Phần B: Logistics — The Copy Test (10 phút)

| Domain | Copy → mất lợi thế? | Core / Supporting / Generic | Lý do |
|---|:---:|:---:|---|
| Route Optimization | | | |
| Fleet Management | | | |
| Real-time Tracking | | | |
| Warehouse | | | |
| Order Management | | | |
| POD | | | |
| Billing | | | |

### Phần C: Kiểm tra chéo (5 phút)

So sánh ITO và Logistics:

| Câu hỏi | ITO | Logistics |
|---|---|---|
| Core Domain #1 | | |
| Tại sao nó là Core? | | |
| Generic Domain dễ nhận nhất | | |
| Domain nào bạn phân vân nhất? | | |

---

## 🪞 Reflect

1. **"Customer Success" trong ITO: Core hay Supporting?** Ít công ty ITO đầu tư vào Customer Success. Nếu bạn là một trong số ít làm tốt → đó là lợi thế. Nhưng nếu bạn mới bắt đầu → nó có phải Core ngay không?

2. **Khi nào 1 domain "lên hạng" từ Supporting → Core?** Gợi ý: nghĩ về thị trường thay đổi — VD: AI khiến "Knowledge Management" trở nên quan trọng hơn.

3. **Nếu team chỉ có 5 người và 3 Core Domains — bạn chọn domain nào build trước? Tiêu chí nào?**

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích được Core / Supporting / Generic và khác nhau ở đâu
- [ ] Áp dụng Copy Test cho ≥6 domains
- [ ] Xác định ≤3 Core Domains cho ITO
- [ ] Xác định ≤3 Core Domains cho Logistics
- [ ] Trả lời ≥2/3 câu hỏi reflection
