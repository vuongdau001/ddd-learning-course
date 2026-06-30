---
type: lesson
module: 3
lesson: 2
title: "Priority Matrix"
duration: "25 phút"
prerequisites: ["module-3/lesson-1"]
narrative_phase: "chiến lược"
migration_phase: "Pre-migration: Roadmap"
business_invariant: "Phase 1 = Quick Win (high score + low complexity); không build Core Domain phức tạp nhất trước"
---

# Lesson 3.2: Priority Matrix — "Biết Core rồi, nhưng build gì trước?"

## 📍 Context — Bạn đang ở đây

> Lesson 3.1 phân loại xong: Resource Management, Opportunity Management, Customer Success đều là 🔴 Core. CTO **Minh** nghe xong gật đầu: *"OK, 3 cái Core. Nhưng team 5 dev, 12 tháng. KHÔNG THỂ build 3 cái cùng lúc. Cái nào trước?"*
>
> Bạn cần framework chuyển từ **"biết cái gì quan trọng"** sang **"build theo thứ tự nào"**.

## 🔥 Tension — "Ai cũng muốn làm trước"

Sprint Planning cho Phase 1. Hà và Tuấn tranh cãi:

> **Hà** (VP Sales): *"Opportunity Management trước. Sales đang track deal trên Excel — mỗi tháng miss 2-3 deal vì quên follow-up. Revenue impact: ~500 triệu/quý."*
>
> **Tuấn** (RM): *"Resource Matching trước. Mỗi deal Hà ký, tôi mất 5 ngày tìm người. Staffing chậm = delivery chậm = khách cancel. Revenue impact: ~700 triệu/quý."*
>
> **Head of CS:** *"Customer Success trước. Churn rate 25%/năm — mất khách cũ đắt gấp 5× tìm khách mới. Revenue impact: ~1.2 tỷ/năm."*

**Minh** nghe 3 con số, tất cả đều lớn:
> *"500 triệu, 700 triệu, 1.2 tỷ — nhưng Resource Matching phức tạp nhất, mất 6 tháng. Customer Success cần data khách hàng mà chúng ta chưa có. Opportunity Management đơn giản hơn, data có sẵn trong Excel. Tôi cần bảng so sánh — không chỉ revenue, mà cả complexity và data readiness."*

> 💭 **Câu hỏi:** Cả 3 đều quan trọng. Nhưng build theo thứ tự nào để **tạo giá trị sớm nhất** mà không sacrifice long-term?

## 🎓 Explanation — Priority Matrix

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Phase 1 = Quick Win: domain score CAO + complexity THẤP. Không bao giờ build Core Domain phức tạp nhất trước — vì fail Phase 1 = mất niềm tin stakeholder = không có Phase 2."*

### 4 Tiêu chí đánh giá

| Tiêu chí | Đo gì | Scale | Data từ đâu |
|---|---|---|---|
| **Business Impact** | Revenue/profit tăng bao nhiêu nếu cải thiện? | 1-5 | CFO, Sales data |
| **Pain Level** | Stakeholders đang đau bao nhiêu? | 1-5 | Pain Point Mining (Module 1.3) |
| **Complexity** | Phức tạp cỡ nào để build? | 1-5 | Tech Lead estimate |
| **Data Readiness** | Data hiện tại đủ để bắt đầu? | 1-5 | System Inventory |

### Công thức ưu tiên

```
Priority Score = (Business Impact + Pain Level + Data Readiness) - Complexity
```

**Tại sao trừ Complexity?** Vì domain phức tạp cần nhiều thời gian → nếu business impact bằng nhau, **chọn domain đơn giản trước** → ship nhanh → tạo momentum → stakeholder tin tưởng → Phase 2 được approve.

### ITO Priority Matrix

| Domain | Impact | Pain | Complexity | Data Ready | Score | Phase |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Opportunity Mgmt | 4 | 4 | 3 | 4 | **9** | 🥇 Phase 1 |
| Resource Mgmt | 5 | 5 | 4 | 3 | **9** | 🥇 Phase 1 |
| Customer Success | 4 | 3 | 3 | 2 | **6** | 🥉 Phase 3 |
| Delivery Mgmt | 3 | 2 | 3 | 4 | **6** | 🥈 Phase 2 |
| Contract Mgmt | 2 | 2 | 2 | 3 | **5** | — SaaS |
| Billing | 2 | 1 | 2 | 4 | **5** | — SAP |

**Đọc kết quả:**
- **Opportunity Mgmt score 9** — Impact 4 + Pain 4 + Data Ready 4 (Excel data có sẵn) − Complexity 3 = 9 → **Quick Win**
- **Resource Mgmt score 9** — Impact + Pain cao nhất nhưng Complexity cũng cao → build MVP trước, full sau
- **Customer Success score 6** — Pain 3 (chưa đau lắm), Data Ready 2 (chưa có data retention) → **Phase 3 — Build When Ready**

### Logistics Priority Matrix (so sánh)

| Domain | Impact | Pain | Complexity | Data Ready | Score | Phase |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Real-time Tracking | 4 | 4 | 3 | 4 | **9** | 🥇 Phase 1 |
| Fleet Mgmt | 3 | 3 | 2 | 4 | **8** | 🥇 Phase 1 |
| Route Optimization | 5 | 5 | 5 | 3 | **8** | 🥈 Phase 2 |
| POD | 3 | 4 | 2 | 2 | **7** | 🥉 Phase 3 |
| Warehouse | 3 | 2 | 3 | 3 | **5** | — WMS |

**Insight:** Real-time Tracking score = Route Optimization, nhưng **Tracking ship nhanh hơn** (complexity 3 vs 5). Build Tracking trước → khách thấy value → buy-in cho Route Optimization (phức tạp hơn, cần AI/ML).

### Migration Roadmap

Từ Priority Matrix → vẽ roadmap 3 phases:

```
ITO Corp:
Phase 1 (Q3): Opportunity Mgmt MVP + Resource Mgmt MVP
              → Quick Win: Sales track deal online, RM match cơ bản
Phase 2 (Q4): Resource Mgmt full + Delivery Mgmt
              → Strategic Investment: matching algorithm, project tracking
Phase 3 (Q1+): Customer Success + Knowledge Mgmt
              → Build When Ready: cần data tích lũy từ Phase 1-2
```

**Quy tắc Phase:**
| Phase | Loại | Tiêu chí |
|---|---|---|
| **Phase 1** | Quick Win | Score cao + Complexity ≤ 3 + Data Ready ≥ 3 |
| **Phase 2** | Strategic Investment | Score cao + Complexity > 3 (cần time) |
| **Phase 3** | Build When Ready | Score trung bình HOẶC Data Ready ≤ 2 |

### ⚖️ Trade-offs — Quick Win vs Strategic Investment

| | Build Quick Win trước | Build Core phức tạp nhất trước |
|---|---|---|
| **Được** | Ship trong 2-3 tháng, stakeholder thấy value, momentum | Giải quyết pain point lớn nhất ngay |
| **Mất** | Core phức tạp bị delay → risk nếu đối thủ nhanh hơn | 6+ tháng không ship → stakeholder mất kiên nhẫn → project bị cancel |
| **Khi nào** | Team mới, cần prove value, stakeholder chưa commit dài hạn | Stakeholder đã full commit, budget secure 12+ tháng |

---

## 🏋️ Exercise — Xây Priority Matrix

### Phần A: ITO — Tự đánh giá (10 phút)

Cho điểm từng domain. Nếu không chắc → ghi "?" và lý do:

| Domain | Impact (1-5) | Pain (1-5) | Complexity (1-5) | Data Ready (1-5) | Score | Lý do |
|---|:---:|:---:|:---:|:---:|:---:|---|
| Resource Mgmt | ? | ? | ? | ? | ? | ? |
| Opportunity Mgmt | ? | ? | ? | ? | ? | ? |
| Customer Success | ? | ? | ? | ? | ? | ? |
| Delivery Mgmt | ? | ? | ? | ? | ? | ? |
| Contract Mgmt | ? | ? | ? | ? | ? | ? |
| Knowledge Mgmt | ? | ? | ? | ? | ? | ? |

**Top 2 ưu tiên:** _______________
**Phase 1 gồm:** _______________

### Phần B: Logistics — Tự đánh giá (10 phút)

| Domain | Impact (1-5) | Pain (1-5) | Complexity (1-5) | Data Ready (1-5) | Score | Lý do |
|---|:---:|:---:|:---:|:---:|:---:|---|
| Route Optimization | ? | ? | ? | ? | ? | ? |
| Real-time Tracking | ? | ? | ? | ? | ? | ? |
| Fleet Mgmt | ? | ? | ? | ? | ? | ? |
| POD | ? | ? | ? | ? | ? | ? |
| Warehouse | ? | ? | ? | ? | ? | ? |
| Order Mgmt | ? | ? | ? | ? | ? | ? |

**Top 2 ưu tiên:** _______________

### Phần C: Vẽ Migration Roadmap (5 phút)

```
Phase 1 (Q3): _____________ + _____________
Phase 2 (Q4): _____________ + _____________
Phase 3 (Q1): _____________ + _____________
```

Giải thích: tại sao Phase 1 trước Phase 2? _______________

---

## 🪞 Reflect

1. **Score bằng nhau thì chọn thế nào?** ITO: Resource Mgmt = Opportunity Mgmt = 9. Tiêu chí phụ: **Data Ready cao hơn → ship nhanh hơn → chọn trước**. Hoặc: **dependency** — Resource cần Opportunity data (deal nào → cần resource gì) → build Opportunity trước.

2. **Stakeholders muốn "làm hết Phase 1".** Push-back: *"Budget = 5 dev × 4 tháng = 20 man-months. Opportunity MVP = 8 man-months, Resource MVP = 10 man-months. Tổng = 18. Thêm Customer Success MVP = 12 man-months. Tổng = 30 > 20. Không đủ."* → **Priority Matrix = bằng chứng bằng số.**

3. **"Quick Win" có rủi ro gì?** Nếu team chỉ build Quick Win mà né Strategic Investment → 1 năm sau, hệ thống chỉ có CRUD features, không có competitive advantage. **Quick Win = tạo momentum, không phải chiến lược dài hạn.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Liệt kê 4 tiêu chí + công thức Priority Score
- [ ] **Apply:** Xây Priority Matrix ≥5 domains cho ITO + ≥5 domains cho Logistics
- [ ] **Analyze:** Vẽ Migration Roadmap 3 phases, giải thích logic phân pha — liên hệ với constraint "5 dev × 12 tháng"

---

> 🔗 **Tiếp theo:** Bạn biết build theo thứ tự nào. Nhưng cho mỗi domain, quyết định tiếp: **Build from scratch? Mua SaaS? Dùng AI generate?** Bài tiếp — *Build vs Buy vs AI* — sẽ cho bạn decision framework cho câu hỏi cuối cùng trước khi code.
