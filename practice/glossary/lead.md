# Lead

## Definition

Một cá nhân hoặc tổ chức đã **thể hiện sự quan tâm** đến dịch vụ của ITO, nhưng **chưa được đánh giá** (qualify) về tiềm năng trở thành khách hàng.

> Lead là bước **đầu tiên** trong pipeline. Chỉ khi Lead được qualify → chuyển thành Opportunity.

## Bounded Context

**Opportunity Management** (hoặc tách thành Lead Management nếu volume lớn)

## Attributes

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| lead_id | string | ✅ | |
| company_name | string | ✅ | Tên công ty khách hàng |
| contact_name | string | ✅ | Người liên hệ |
| contact_email | string | ✅ | |
| source | enum | ✅ | Referral · Partner · Website · Event · Cold · LinkedIn |
| status | enum | ✅ | New · Contacted · Qualified · Disqualified |
| notes | string | | Ghi chú từ Sales |
| created_at | date | ✅ | Ngày nhận lead |

## Business Rules

1. **Lead mới phải được contact trong 48 giờ** — Lead để nguội > 48h giảm 50% conversion
2. **Qualification dựa trên BANT:** Budget, Authority, Need, Timeline
3. **Lead Qualified → chuyển thành Opportunity** — Tạo Opportunity mới, link về Lead gốc
4. **Lead Disqualified → ghi lý do + schedule follow-up 6 tháng**

## States

```
New → Contacted → Qualified → (chuyển thành Opportunity)
                      ↓
                 Disqualified → (follow-up 6 tháng)
```

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Opportunity** | Lead = chưa evaluate. Opportunity = đã evaluate, đang pursue. |
| **Account** | Account = tổ chức/công ty. Lead = 1 lần quan tâm cụ thể từ tổ chức đó. 1 Account có thể tạo nhiều Leads theo thời gian. |
| **Contact** | Contact = thông tin người liên hệ (tên, email, SĐT). Lead = sự kiện "có người quan tâm" + context (từ đâu, cần gì). |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Khách hàng tiềm năng" | Quá dài, mơ hồ — dùng "Lead" |
| "Prospect" | Trong một số framework, Prospect ≠ Lead. Tránh nhầm lẫn. |

## Related Concepts

- [[opportunity]] — Lead qualified → Opportunity
- [[account]] — Lead thuộc về Account nào?

## Owner

**Sales / Business Development Team**

---

## 💡 Lead vs Opportunity: Ranh giới ở đâu?

```
Referral từ partner: "Công ty ABC đang tìm team Java"
    → LEAD (chưa biết budget, timeline, decision maker)
    
Sales gọi điện, gặp mặt, xác nhận:
    ✅ Budget: $300K/năm
    ✅ Authority: CTO là decision maker, đã gặp
    ✅ Need: Cần 5 Java dev cho platform mới
    ✅ Timeline: Start Q3 2026
    → OPPORTUNITY (đủ 4 tiêu chí BANT)
```

**Ranh giới = BANT qualification.** Trước BANT = Lead. Sau BANT = Opportunity.
