---
type: lesson
module: 3
lesson: 2
title: "Priority Matrix"
duration: "25 phút"
prerequisites: ["module-3/lesson-1"]
---

# Lesson 3.2: Priority Matrix

## 🎓 Concept — "Biết Core rồi, nhưng làm gì trước?"

### Vấn đề

Bạn đã xác định: Resource Management, Opportunity Management, Customer Success đều là Core. Nhưng team chỉ có 5 người. **Build cái nào trước?**

Cần thêm 1 bước: **Priority Matrix** — đánh giá theo nhiều chiều để chọn thứ tự.

### 4 tiêu chí đánh giá

| Tiêu chí | Đo gì | Scale |
|---|---|---|
| **Business Impact** | Nếu cải thiện domain này, revenue/profit tăng bao nhiêu? | 1 (thấp) → 5 (rất cao) |
| **Pain Level** | Stakeholders đang đau bao nhiêu? (từ Pain Point Mining) | 1 (ít đau) → 5 (rất đau) |
| **Complexity** | Phức tạp cỡ nào để build? | 1 (đơn giản) → 5 (rất phức tạp) |
| **Data Readiness** | Data hiện tại đủ để bắt đầu chưa? | 1 (không có gì) → 5 (data sẵn sàng) |

**Công thức ưu tiên:**
```
Priority Score = (Business Impact + Pain Level + Data Readiness) - Complexity
```

Tại sao trừ Complexity? Vì domain phức tạp cần nhiều thời gian hơn → nếu business impact bằng nhau, chọn domain đơn giản hơn trước.

### Ví dụ — ITO Priority Matrix

| Domain | Impact | Pain | Complexity | Data Ready | Score | Thứ tự |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Resource Mgmt | 5 | 5 | 4 | 3 | **9** | 🥇 |
| Opportunity Mgmt | 4 | 4 | 3 | 4 | **9** | 🥇 |
| Customer Success | 4 | 3 | 3 | 2 | **6** | 🥉 |
| Delivery Mgmt | 3 | 2 | 3 | 4 | **6** | 🥉 |
| Contract Mgmt | 2 | 2 | 2 | 3 | **5** | — |
| Billing | 2 | 1 | 2 | 4 | **5** | — |

**Đọc kết quả:**
- Resource Mgmt và Opportunity Mgmt cùng score 9 → build song song hoặc phase 1-2
- Customer Success score 6 nhưng Data Ready chỉ 2 → cần thu thập data trước → phase 2-3
- Billing score thấp + đã có SAP → dùng SAP, không build

### Ví dụ — Logistics Priority Matrix

| Domain | Impact | Pain | Complexity | Data Ready | Score | Thứ tự |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Route Optimization | 5 | 5 | 5 | 3 | **8** | 🥇 |
| Real-time Tracking | 4 | 4 | 3 | 4 | **9** | 🥇 |
| Fleet Mgmt | 3 | 3 | 2 | 4 | **8** | 🥈 |
| POD | 3 | 4 | 2 | 2 | **7** | 🥉 |
| Warehouse | 3 | 2 | 3 | 3 | **5** | — |
| Order Mgmt | 2 | 2 | 2 | 4 | **6** | — |

**Insight:** Real-time Tracking score cao nhất vì complexity thấp (GPS hardware + Google Maps API) nhưng impact cao. Build trước Route Optimization dù Route là Core "quan trọng hơn" — vì Tracking ship nhanh hơn và tạo momentum.

### Migration Roadmap

Từ Priority Matrix → vẽ roadmap:

```
ITO:
Phase 1 (Q3): Resource Mgmt MVP + Opportunity Mgmt MVP
Phase 2 (Q4): Resource Mgmt full + Delivery Mgmt
Phase 3 (Q1): Customer Success + Knowledge Mgmt

Logistics:
Phase 1 (Q3): Real-time Tracking + Fleet Mgmt
Phase 2 (Q4): Route Optimization + POD
Phase 3 (Q1): Warehouse integration + Advanced analytics
```

**Quy tắc:**
- Phase 1 = domain có score cao + complexity thấp → **Quick Win**
- Phase 2 = domain có score cao + complexity cao → **Strategic Investment**
- Phase 3 = domain score trung bình hoặc data chưa ready → **Build When Ready**

---

## 🏋️ Exercise — Xây Priority Matrix

### Phần A: ITO — Tự đánh giá (10 phút)

Cho điểm từng domain. Nếu bạn không chắc → ghi "?" và lý do.

| Domain | Impact (1-5) | Pain (1-5) | Complexity (1-5) | Data Ready (1-5) | Score | Lý do |
|---|:---:|:---:|:---:|:---:|:---:|---|
| Resource Mgmt | | | | | | |
| Opportunity Mgmt | | | | | | |
| Customer Success | | | | | | |
| Delivery Mgmt | | | | | | |
| Contract Mgmt | | | | | | |
| Knowledge Mgmt | | | | | | |

Top 2 ưu tiên: _______________

### Phần B: Logistics — Tự đánh giá (10 phút)

| Domain | Impact (1-5) | Pain (1-5) | Complexity (1-5) | Data Ready (1-5) | Score | Lý do |
|---|:---:|:---:|:---:|:---:|:---:|---|
| Route Optimization | | | | | | |
| Real-time Tracking | | | | | | |
| Fleet Mgmt | | | | | | |
| POD | | | | | | |
| Warehouse | | | | | | |
| Order Mgmt | | | | | | |

Top 2 ưu tiên: _______________

---

## 🪞 Reflect

1. **Score bằng nhau thì chọn thế nào?** VD: 2 domain cùng score 9. Dùng tiêu chí phụ nào?

2. **Stakeholders thường muốn "làm hết Phase 1". Bạn push-back thế nào?** Gợi ý: dùng Priority Matrix làm bằng chứng.

3. **"Quick Win" có rủi ro gì?** Nếu team chỉ build Quick Win mà né Strategic Investment → điều gì xảy ra dài hạn?

---

## ✅ Hoàn thành lesson khi
- [ ] Xây Priority Matrix cho ITO (≥5 domains)
- [ ] Xây Priority Matrix cho Logistics (≥5 domains)
- [ ] Vẽ Migration Roadmap 3 phases
- [ ] Trả lời ≥2/3 câu hỏi reflection
