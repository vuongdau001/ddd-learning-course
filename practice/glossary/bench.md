# Bench

## Definition

Trạng thái của một Resource khi **không có Allocation nào active**, hoặc tổng Allocation **< 20%**, kéo dài liên tục **> 5 ngày làm việc**.

> Bench **không phải** là entity riêng — nó là **computed state** của Resource.

## Bounded Context

**Resource Management**

## Attributes

| Attribute | Type | Mô tả |
|---|---|---|
| resource | Resource | Ai đang bench? |
| bench_since | date | Ngày bắt đầu bench |
| bench_reason | enum | ProjectEnded · NotMatched · ClientCancelled · BetweenProjects |
| expected_end | date | Dự kiến kết thúc bench (nếu biết) |
| days_on_bench | integer | Số ngày bench liên tục (computed) |

## Business Rules

1. **Threshold: Allocation < 20% trong > 5 ngày → Bench** — Auto-flagged
2. **Bench → trigger thông báo Resource Manager** — Ngay khi Resource vào bench
3. **Bench > 10 ngày → escalate** — Đề xuất Training Plan hoặc Internal Project
4. **Bench > 20 ngày → escalate lên Director** — Cần quyết định: tuyển ít hơn? Bán thêm?
5. **Nghỉ phép planned KHÔNG tính bench** — Resource đang On Leave ≠ Bench

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Available** | Available = có thể nhận allocation mới (trạng thái neutral). Bench = đã available **quá lâu** mà chưa được assign (trạng thái cảnh báo). |
| **On Leave** | On Leave = nghỉ phép có kế hoạch. Bench = chưa có dự án. |
| **Idle** | "Idle" nghe tiêu cực/phán xét. "Bench" là thuật ngữ trung lập, industry-standard. |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Rảnh" | Thiếu tôn trọng + mơ hồ |
| "Không có việc" | Mang tính phán xét, thực tế Resource có thể đang training/presales |
| "Idle" | Nghe như machine state, không phù hợp cho người |

## Câu hỏi chưa giải quyết ⚠️

- [x] **Threshold bao nhiêu ngày?** → Đề xuất: 5 ngày (1 tuần làm việc)
- [ ] **Resource đang làm internal project (non-billable) có tính bench không?** VD: đang build internal tool — allocation = 0% billable nhưng 100% occupied
- [ ] **Resource chủ động muốn nghỉ giữa 2 project (1-2 tuần) có tính bench không?**

## Related Concepts

- [[resource]] — Bench là trạng thái của Resource
- [[allocation]] — Allocation < 20% → Bench
- [[utilization]] — Bench kéo dài → Utilization giảm

## Owner

**Resource Management Team**

---

## 💡 Tại sao Bench quan trọng về mặt business?

```
Bench Cost = Số Resource on bench × Average daily cost × Số ngày bench

Ví dụ ITO:
- 5 Senior Dev on bench × $200/ngày × 10 ngày = $10,000 mất trắng

Đây là tiền công ty trả lương nhưng không có revenue.
→ Bench management tốt = tiết kiệm hàng trăm nghìn USD/năm
→ Đây là lý do Resource Management là Core Domain
```
