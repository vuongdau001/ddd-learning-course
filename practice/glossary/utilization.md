# Utilization

## Definition

Tỷ lệ phần trăm thời gian **billable thực tế** của Resource so với **tổng thời gian làm việc khả dụng**, tính trong một khoảng thời gian xác định (tuần/tháng/quý).

**Công thức:**
```
Utilization (%) = (Billable Hours / Available Hours) × 100
```

Trong đó:
- **Billable Hours** = Giờ làm việc được tính phí cho khách hàng
- **Available Hours** = Tổng giờ làm việc trừ nghỉ phép, nghỉ lễ

## Bounded Context

**Resource Management** (KPI chính)

## ⚠️ Đây là thuật ngữ gây xung đột LỚN NHẤT trong công ty

Khi CEO hỏi *"Utilization bao nhiêu?"*, 3 phòng trả lời 3 con số:

| Phòng ban | Cách đo | Kết quả | Vấn đề |
|---|---|---|---|
| **HR** | Headcount có việc / Tổng headcount | **95%** | Đo "có việc hay không" — intern có việc cũng tính 100% |
| **Finance** | Revenue thực tế / Revenue capacity | **78%** | Đo revenue, không đo effort — project fixed-price bị lệch |
| **Resource Mgr** | Billable hours / Available hours | **82%** | Đo effort thực tế — chính xác nhất cho resource planning |

> **Quyết định:** Trong context Resource Management, Utilization **chỉ** được đo bằng công thức Billable Hours / Available Hours. Các cách đo khác thuộc context khác (HR Engagement, Financial Performance).

## Attributes

| Attribute | Type | Mô tả |
|---|---|---|
| resource | Resource | Đo cho ai? |
| period | date range | Khoảng thời gian (tuần/tháng/quý) |
| billable_hours | decimal | Giờ billable thực tế |
| available_hours | decimal | Giờ khả dụng (trừ nghỉ phép, lễ) |
| utilization_rate | percentage | Kết quả tính toán |
| non_billable_hours | decimal | Giờ internal (training, presales, admin) |

## Business Rules

1. **Target: Utilization ≥ 75%** — Dưới 75% → cần review allocation plan
2. **Utilization > 95% → cảnh báo** — Quá cao = risk burnout, không có buffer cho urgent
3. **Internal project (training, presales) KHÔNG tính billable** — Nhưng vẫn track trong `non_billable_hours`
4. **Nghỉ phép trừ khỏi Available Hours** — Nghỉ 5 ngày/tháng → Available = 17 ngày, không phải 22

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Allocation** | Allocation = kế hoạch (planned %). Utilization = thực tế (actual %). VD: Allocation 100% nhưng Utilization chỉ 60% vì project bị delay. |
| **Productivity** | Productivity = output/effort (bao nhiêu feature/sprint). Utilization = time usage (bao nhiêu giờ billable). Resource có thể Utilization cao nhưng Productivity thấp. |
| **HR Engagement Rate** | HR đo "có việc hay không" (binary). Utilization đo "billable bao nhiêu %" (continuous). |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Hiệu suất" | Quá mơ hồ — hiệu suất gì? Resource? Revenue? Team? |
| "Occupancy" | Thuật ngữ call center/hotel, không phù hợp IT outsourcing |
| "Capacity" | Capacity = khả năng tối đa. Utilization = mức sử dụng thực tế |

## Câu hỏi chưa giải quyết ⚠️

- [ ] **Presales effort có tính billable không?** Nếu Sales Manager dùng Senior Dev 2 ngày để demo cho khách → billable hay non-billable?
- [ ] **Training do khách yêu cầu có billable không?** VD: khách yêu cầu training team của họ → billable. Nhưng internal training → non-billable.
- [ ] **Resource part-time (50%) tính Available Hours thế nào?** 50% × 22 ngày = 11 ngày? Hay theo giờ thực tế?

## Related Concepts

- [[resource]] — Đo Utilization cho từng Resource
- [[allocation]] — So sánh planned (Allocation) vs actual (Utilization)
- [[bench]] — Utilization ≈ 0% trong thời gian dài = Bench

## Owner

**Resource Management Team** (định nghĩa) + **Finance** (báo cáo revenue-based riêng)

---

## 💡 Bài học DDD từ term này

Utilization là **ví dụ hoàn hảo** cho việc tại sao cần Ubiquitous Language:

```
TRƯỚC Glossary:
CEO: "Utilization bao nhiêu?"
HR: "95%"   Finance: "78%"   RM: "82%"
CEO: "????"

SAU Glossary:
CEO: "Utilization bao nhiêu?"
Mọi người: "82% — billable hours / available hours"
CEO: "OK. HR Engagement Rate thì sao?"
HR: "95%"
CEO: "Revenue Performance?"
Finance: "78%"
```

→ Không phải ai sai — mà là **3 khái niệm khác nhau đang dùng chung 1 từ**. Glossary giúp **tách** chúng ra, mỗi concept có tên riêng.
