# Account

## Definition

Một tổ chức (công ty, tập đoàn) **đã hoặc đang** có quan hệ kinh doanh với ITO — bao gồm cả khách hàng tiềm năng đang được theo đuổi và khách hàng hiện hữu.

## Bounded Context

**Sales / CRM** — Nhưng concept này xuất hiện ở NHIỀU context với tên và nghĩa KHÁC NHAU.

## ⚠️ Term này có nghĩa KHÁC nhau ở mỗi context

| Bounded Context | Tên gọi | Nghĩa | Quan tâm gì |
|---|---|---|---|
| **Sales/CRM** | **Account** | Tổ chức đang được Sales theo đuổi | Contacts, pipeline, revenue potential |
| **Contract** | **Client** | Đơn vị ký hợp đồng (legal entity) | HĐ, điều khoản pháp lý, SLA |
| **Delivery** | **Customer** | Team/người mà PM làm việc hàng ngày | Technical contacts, requirements |
| **Finance** | **Billing Entity** | Đơn vị nhận hóa đơn | Mã thuế, địa chỉ invoice, payment terms |

> **Đây là ví dụ điển hình cho Bounded Context:** Cùng "khách hàng" nhưng 4 context có 4 model khác nhau — và **đó là đúng**, không phải lỗi.

## Attributes (trong Sales/CRM context)

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| account_id | string | ✅ | |
| company_name | string | ✅ | Tên công ty |
| industry | enum | ✅ | Fintech · E-commerce · Healthcare · Manufacturing · ... |
| country | enum | ✅ | Korea · Japan · Singapore · Vietnam · ... |
| company_size | enum | | Startup · SME · Enterprise |
| primary_contact | Contact | ✅ | Người liên hệ chính |
| relationship_status | enum | ✅ | Prospect · Active · Dormant · Churned |
| total_revenue | decimal | | Tổng revenue lịch sử từ Account này |
| account_owner | Resource | ✅ | Sales phụ trách |

## Business Rules

1. **Mỗi Account phải có ≥1 Contact** — Không thể tồn tại Account "vô chủ"
2. **1 Account có thể có nhiều Opportunities cùng lúc** — VD: 1 opportunity cho team Java, 1 cho QA
3. **Account Churned > 12 tháng → có thể archive** — Giữ data nhưng ẩn khỏi active list
4. **Merge Account phải được Sales Manager approve** — Tránh mất lịch sử

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Contact** | Account = tổ chức. Contact = người cụ thể trong tổ chức. 1 Account có nhiều Contacts. |
| **Lead** | Lead = 1 sự kiện quan tâm. Account = tổ chức tồn tại lâu dài. 1 Account → nhiều Leads theo thời gian. |
| **Opportunity** | Account = đối tượng (ai?). Opportunity = hành động (cơ hội bán gì?). |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng (trong CRM context) | Tại sao |
|---|---|
| "Khách hàng" | Quá chung — Account? Client? Customer? Billing Entity? |
| "Customer" | Dành cho Delivery context (PM dùng) |
| "Client" | Dành cho Contract context (Legal dùng) |
| "Company" | Quá generic — không phân biệt được prospect vs active |

## Related Concepts

- [[lead]] — Leads thuộc về Account
- [[opportunity]] — Opportunities thuộc về Account
- [[contract]] — Contracts ký với Account (nhưng gọi là "Client")
- [[project]] — Projects deliver cho Account (nhưng gọi là "Customer")

## Owner

**Sales / Business Development Team**

---

## 💡 Bài học DDD: Cùng 1 thực thể, 4 mô hình

Đây là ví dụ giáo khoa cho **Bounded Context**:

```
Thực tế: Công ty "ABC Corp" — 1 entity duy nhất trong thế giới thực

Sales/CRM context:        Account "ABC Corp"
                           → pipeline, contacts, revenue potential
                           
Contract context:          Client "ABC Corp Co., Ltd."
                           → legal name, mã thuế, SLA
                           
Delivery context:          Customer "ABC Corp - Platform Team"
                           → technical contacts, sprint cadence
                           
Finance context:           Billing Entity "ABC Corp Co., Ltd."
                           → invoice address, payment terms, tax ID
```

→ Trong mỗi context, cùng "ABC Corp" nhưng model khác nhau: attributes khác, rules khác, tên khác.  
→ Đây **không phải duplicate data** — đây là **đúng thiết kế DDD**.  
→ Kết nối giữa các context thông qua `account_id` / `client_code` mapping.
