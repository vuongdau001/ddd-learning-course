---
type: lesson
module: 3
lesson: 1
title: "Core / Supporting / Generic"
duration: "30 phút"
prerequisites: ["module-1", "module-2"]
narrative_phase: "chiến lược"
migration_phase: "Pre-migration: Phân loại đầu tư"
business_invariant: "≤3 Core Domains; Core = lợi thế cạnh tranh, nếu đối thủ copy → mất"
---

# Lesson 3.1: Core / Supporting / Generic — "Không phải domain nào cũng bằng nhau"

## 📍 Context — Bạn đang ở đây

> Module 1 cho bạn Domain Map (8 domains), Module 2 cho bạn Glossary (thống nhất ngôn ngữ). CTO **Minh** gọi bạn vào phòng: *"Tốt lắm, bây giờ tôi hiểu ITO có 8 domains. Nhưng ngân sách 2025 = 4 dev × 12 tháng. Chúng ta KHÔNG THỂ build hết. Tôi cần biết: domain nào build in-house, domain nào mua SaaS, domain nào 'cũng được'. Phân loại cho tôi."*
>
> Bạn cần 1 framework để trả lời: **"Cái nào đáng đầu tư, cái nào không?"**

## 🔥 Tension — "Build hết hay mua hết?"

**Minh** triệu tập meeting ban lãnh đạo. Mỗi người 1 ý:

> **Hà** (VP Sales): *"Chúng ta cần build CRM riêng — Salesforce không fit flow của ITO. Opportunity Management phải custom."*
>
> **Tuấn** (RM): *"Resource Matching là linh hồn của ITO. Không có đối thủ nào match resource nhanh như chúng ta NẾU chúng ta build đúng."*
>
> **CFO:** *"Billing? Dùng Xero. Auth? Dùng Keycloak. Notification? Dùng SendGrid. Đừng build cái mà thế giới đã giải quyết."*
>
> **HR Director:** *"Tuyển dụng + Employee Management — phải build, không phần mềm nào hiểu cách tính KPI của ITO."*

**Minh** tổng kết:
> *"Mỗi người muốn build domain của mình. Budget chỉ đủ 3-4 domain. Nếu build sai thứ tự, chúng ta thua đối thủ 1 năm. Bạn — kiến trúc sư — cho tôi tiêu chí phân loại."*

> 💭 **Câu hỏi:** Hà, Tuấn, CFO, HR — ai đúng? Hay tất cả đều đúng, chỉ là **ưu tiên khác nhau**? Có framework nào giúp chọn?

## 🎓 Explanation — Core / Supporting / Generic

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Công ty chỉ có ≤3 Core Domains. Core = thứ tạo lợi thế cạnh tranh — nếu đối thủ copy y nguyên, bạn mất khách. Nếu mọi thứ đều 'Core' → không gì Core → phân tán resource → thua."*

### Core Domain bắt nguồn từ chiến lược

Core Domain **không phải kỹ thuật quyết định** — mà **chiến lược kinh doanh quyết định**:

```
    Business Goal           ← Minh: "ITO #1 về talent matching speed"
         │
         ▼
  Competitive Advantage     ← "Match resource nhanh hơn, chính xác hơn đối thủ"
         │
         ▼
    Core Domain             ← Resource Management = Core
         │
         ▼
  Investment Priority       ← Đội giỏi nhất, ngân sách lớn nhất cho Core
```

> 💡 **Hai ITO cùng ngành, chiến lược khác → Core khác.** ITO chuyên giá rẻ → Core = Cost Optimization. ITO chuyên chất lượng → Core = Resource Matching. Không có "Core mặc định".

### 3 loại Domain

| Loại | Định nghĩa | Chiến lược | Ví dụ ITO |
|---|---|---|---|
| **🔴 Core** | Tạo lợi thế cạnh tranh. Đây là thứ khiến ITO **khác biệt** so với đối thủ. | Build in-house, đội giỏi nhất, ngân sách lớn nhất | Resource Matching, Opportunity Management |
| **🟡 Supporting** | Cần thiết cho hoạt động, nhưng không tạo lợi thế. Mọi đối thủ đều có. | Build đơn giản hoặc customize tool có sẵn | Reporting, Contract Management, Billing |
| **⚪ Generic** | Giống nhau ở mọi công ty. Không liên quan nghiệp vụ riêng. | Mua SaaS / dùng open-source | Authentication, Email, File Storage |

### The Copy Test — Phép thử quyết định

> *"Nếu đối thủ copy y nguyên hệ thống domain này, ITO có mất lợi thế không?"*

| Nếu trả lời | Thì đó là | Ví dụ |
|---|---|---|
| **"Chết, mất toàn bộ lợi thế!"** | 🔴 **Core** | Thuật toán matching resource (skill + seniority + availability + cost) |
| **"Phiền, nhưng không chết"** | 🟡 **Supporting** | Dashboard báo cáo utilization |
| **"Kệ, cái này ai cũng có"** | ⚪ **Generic** | Login system, email notification |

### The Competitive Test — Phép thử bổ sung

> *"Nếu domain này tốt hơn đối thủ 10×, khách hàng có chọn ITO không?"*

| Trả lời | Kết luận |
|---|---|
| "Có! Khách chọn ITO vì match resource trong 48h thay vì 5 ngày" | 🔴 **Core** |
| "Không ai chọn ITO vì dashboard đẹp hơn" | 🟡 Supporting hoặc ⚪ Generic |

### Áp dụng — Phân loại ITO Corp

| Domain | Loại | Copy Test | Competitive Test |
|---|:---:|---|---|
| **Resource Management** | 🔴 Core | Copy → mất lợi thế matching | 10× tốt hơn → khách chọn |
| **Opportunity Management** | 🔴 Core | Copy → mất win rate pipeline | 10× tốt hơn → revenue tăng |
| **Customer Success** | 🔴 Core | Copy → mất retention (ít ITO đầu tư) | 10× → khách quay lại, recurring revenue |
| Delivery Management | 🟡 Supporting | Copy → phiền nhưng OK | 10× → khách không care |
| Contract Management | 🟡 Supporting | Copy → kệ | 10× → irrelevant |
| Billing | 🟡 Supporting | Copy → kệ | 10× → irrelevant |
| Authentication | ⚪ Generic | Giống mọi nơi | Dùng Keycloak |
| Notification | ⚪ Generic | Standard | Dùng SendGrid |

### Ví dụ — Logistics

| Domain | Loại | Copy Test | Competitive Test |
|---|:---:|---|---|
| **Route Optimization** | 🔴 Core | Copy → mất lợi thế giá vận chuyển | 10× → tiết kiệm 30% chi phí |
| **Real-time Tracking** | 🔴 Core | Copy → mất UX advantage | 10× → khách chọn vì minh bạch |
| Fleet Management | 🟡 Supporting | Copy → phiền nhưng OK | 10× → irrelevant |
| Warehouse | 🟡 Supporting | Dùng WMS có sẵn | 10× → khách không care |
| Billing | ⚪ Generic | Standard | Dùng accounting software |

### 3 Bẫy phổ biến

**Bẫy 1: "Mọi thứ đều Core"**
> HR: *"Employee Management phải Core!"* → Thực tế: nếu dùng BambooHR cũng OK → Supporting.
> **Quy tắc: ≤ 2-3 Core Domains. Nếu >3 → phân tán resource → thua.**

**Bẫy 2: "Core vĩnh viễn"**
> 5 năm trước: GPS Tracking = Core cho Logistics (ít ai làm được).
> Bây giờ: Google Maps API → Generic (mua dễ dàng).
> → **Core có thể thành Generic khi technology commoditize.**

**Bẫy 3: "Build hết cho kiểm soát"**
> Team 5 người build Auth + Notification + Dashboard + Core = 80% effort cho Generic/Supporting, 20% cho Core → **Core bị chậm → đối thủ có Core trước → thua.**
> Đúng: Mua/SaaS cho Generic → 80% effort cho Core.

### ⚖️ Trade-offs

| | Build tất cả in-house | Core in-house, Supporting customize, Generic SaaS | Mua hết SaaS |
|---|---|---|---|
| **Được** | Kiểm soát 100%, không phụ thuộc vendor | Balance: Core khác biệt, Supporting đủ dùng, Generic nhanh | Deploy 1 tuần, zero dev effort |
| **Mất** | 80% effort cho non-Core, chậm 12+ tháng | Cần integration layer giữa các hệ thống | Không customize được Core → giống đối thủ → mất lợi thế |
| **Khi nào** | Regulated industry (banking) hoặc team rất lớn | Hầu hết dự án | Startup MVP, validation phase |

---

## 🏋️ Exercise — Phân loại Domain

### Phần A: ITO — The Copy Test + Competitive Test (10 phút)

| Domain | Copy → mất lợi thế? | 10× tốt hơn → khách chọn? | Core / Supporting / Generic | Lý do |
|---|---|---|---|---|
| Resource Management | ? | ? | ? | ? |
| Opportunity Management | ? | ? | ? | ? |
| Delivery Management | ? | ? | ? | ? |
| Contract Management | ? | ? | ? | ? |
| Customer Success | ? | ? | ? | ? |
| Billing | ? | ? | ? | ? |
| Knowledge Management | ? | ? | ? | ? |

**Check:** Bao nhiêu Core? ___ (Nếu >3 → xem lại)

### Phần B: Logistics — The Copy Test (10 phút)

| Domain | Copy → mất lợi thế? | Core / Supporting / Generic | Lý do |
|---|---|---|---|
| Route Optimization | ? | ? | ? |
| Fleet Management | ? | ? | ? |
| Real-time Tracking | ? | ? | ? |
| Warehouse | ? | ? | ? |
| Order Management | ? | ? | ? |
| POD | ? | ? | ? |
| Billing | ? | ? | ? |

### Phần C: So sánh (5 phút)

| Câu hỏi | ITO | Logistics |
|---|---|---|
| Core Domain #1 là gì? | ? | ? |
| Tại sao nó là Core? | ? | ? |
| Generic dễ nhận nhất? | ? | ? |
| Domain bạn phân vân nhất? | ? | ? |

---

## 🪞 Reflect

1. **"Customer Success" trong ITO: Core hay Supporting?** Ít công ty ITO đầu tư vào Customer Success. Nếu ITO là một trong số ít làm tốt → đó là lợi thế cạnh tranh (Core). Nhưng nếu ITO mới bắt đầu, chưa có data retention → nó có thể là Supporting trước, "lên hạng" Core khi có data. **Bạn nghĩ gì?**

2. **Khi nào domain "lên hạng" Supporting → Core?** Gợi ý: thị trường thay đổi. VD: AI khiến "Knowledge Management" từ Supporting thành Core — công ty nào có knowledge base tốt → AI Agent chính xác hơn → lợi thế.

3. **Nếu team chỉ có 5 dev và 3 Core Domains — chọn build domain nào trước?** Tiêu chí: revenue impact + competitive urgency + data dependency (cần Lesson 3.2 — Priority Matrix).

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Core / Supporting / Generic + Copy Test + Competitive Test
- [ ] **Apply:** Phân loại ≥6 domains cho ITO + ≥6 domains cho Logistics
- [ ] **Analyze:** Giải thích tại sao ≤3 Core — liên hệ budget constraint của CTO Minh (4 dev × 12 tháng)

---

> 🔗 **Tiếp theo:** Bạn biết domain nào là Core. Nhưng 3 Core Domains, build cái nào TRƯỚC? Bài tiếp — *Priority Matrix* — sẽ cho bạn tiêu chí: revenue impact × effort × risk → thứ tự build chính xác.
