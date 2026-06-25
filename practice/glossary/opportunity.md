# Opportunity

## Definition

Một cơ hội bán hàng **đã được đánh giá (qualified)** và đang được theo đuổi tích cực, có khả năng dẫn đến việc ký Contract và tạo Project.

> Opportunity chỉ tồn tại SAU KHI Lead đã được Qualify. Trước đó, nó là Lead.

## Bounded Context

**Opportunity Management** (CRM)

## Attributes

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| opportunity_id | string | ✅ | ID duy nhất |
| title | string | ✅ | Tên cơ hội (VD: "ABC Corp - AI Testing Platform") |
| account | Account | ✅ | Khách hàng tiềm năng |
| stage | enum | ✅ | Qualification · Meeting · Proposal · Negotiation · Won · Lost · Stalled |
| estimated_value | decimal | ✅ | Giá trị ước tính (USD) |
| close_date | date | ✅ | Ngày dự kiến close |
| win_probability | percentage | | Xác suất thắng (0–100%) |
| owner | Resource | ✅ | Sales/BD phụ trách |
| source | enum | ✅ | Referral · Partner · Inbound · Event · Cold |
| technology_stack | string[] | | Tech yêu cầu (Java, React, AI/ML...) |
| team_size | integer | | Số resource dự kiến cần |
| loss_reason | string | | Lý do thua (nếu Lost) |

## Business Rules

1. **Opportunity chỉ tạo từ Lead đã Qualified** — Không tạo Opportunity "từ không khí"
2. **Stage Negotiation → phải có Proposal đã gửi** — Không thể negotiate mà chưa gửi đề xuất
3. **Opportunity Won → trigger chuỗi sự kiện:**
   - Tạo Project (Delivery context)
   - Thông báo Resource Manager (Staffing Request)
   - Gửi Welcome Email cho khách
4. **Opportunity không hoạt động > 30 ngày → tự động chuyển Stalled** — Cần Sales review lại
5. **Estimated value thay đổi > 30% → phải có approval** — Tránh inflate/deflate pipeline

## States

```
               ┌─ Meeting ─┐
               │            │
Qualification ─┤            ├─ Proposal ─── Negotiation ─── Won
               │            │                    │
               └────────────┘                    ├─── Lost
                                                 │
                                                 └─── Stalled
                                                        │
                                                    (review)
                                                        │
                                                   Won / Lost
```

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Lead** | Lead = thông tin thô, chưa evaluate. Opportunity = đã evaluate, có potential. Chuyển đổi: Lead → (qualify) → Opportunity. |
| **Project** | Project chỉ tồn tại **SAU KHI** Opportunity Won. Opportunity = cơ hội bán. Project = cam kết delivery. |
| **Contract** | Contract = tài liệu pháp lý. Opportunity = cơ hội kinh doanh. Opportunity Won → tạo Contract. |
| **Proposal** | Proposal = tài liệu gửi cho khách. Opportunity = quá trình theo đuổi tổng thể. 1 Opportunity có thể có nhiều Proposal revisions. |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Deal" | Tiếng lóng Sales — không nên dùng trong hệ thống. "Deal closed" → "Opportunity Won". |
| "Dự án" | Gây nhầm với Project. Dự án = đã triển khai. Opportunity = chưa chắc có. |
| "Khách hàng" | Khách hàng = Account. Opportunity = cơ hội bán cho Account đó. 1 Account có thể có nhiều Opportunities. |

## Câu hỏi chưa giải quyết ⚠️

- [ ] **Win Probability tính tự động hay manual?** Nếu AI-first → tính bằng model (dựa trên historical data). Nếu manual → Sales tự estimate.
- [ ] **Stalled bao lâu thì tự chuyển Lost?** 30 ngày? 60 ngày? 90 ngày?
- [ ] **1 Account có thể có bao nhiêu Opportunity active cùng lúc?** Giới hạn hay không?

## Related Concepts

- [[lead]] — Input: Lead → Qualified → Opportunity
- [[account]] — Mỗi Opportunity thuộc 1 Account
- [[proposal]] — Gửi Proposal trong quá trình pursue Opportunity
- [[contract]] — Output: Opportunity Won → Contract
- [[project]] — Output: Contract → Project

## Owner

**Sales / Business Development Team**

---

## 💡 Tại sao term này gắn với Revenue?

```
Revenue Forecast = Σ (Opportunity Value × Win Probability)

VD Pipeline ITO tháng 7:
Opportunity A: $200K × 80% = $160K
Opportunity B: $150K × 40% = $60K  
Opportunity C: $300K × 20% = $60K
                        Forecast = $280K

→ Nếu "Opportunity" không rõ ràng, forecast = rác
→ Nếu win_probability không consistent, forecast = rác
→ Glossary trực tiếp ảnh hưởng đến độ chính xác dự báo doanh thu
```
