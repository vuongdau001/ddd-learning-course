# Glossary Index — ITO CRM
## Từ điển nghiệp vụ chính thức

> **Nguyên tắc:** Mỗi khái niệm = 1 file riêng biệt  
> **Quy trình:** Tạo → Review với Stakeholders → Approve → Merge  
> **Trạng thái:** 🟡 Đang xây dựng — 8/15 terms hoàn thành

---

## 📋 Danh sách Terms

### ✅ Đã hoàn thành (8 terms)

| # | Term | File | Domain | Mức xung đột |
|:---:|---|---|---|:---:|
| 1 | Resource | [resource.md](./resource.md) | Resource Management | 🔴 Rất cao |
| 2 | Allocation | [allocation.md](./allocation.md) | Resource Management | 🔴 Rất cao |
| 3 | Utilization | [utilization.md](./utilization.md) | Resource Management | 🔴 Rất cao |
| 4 | Bench | [bench.md](./bench.md) | Resource Management | 🟠 Cao |
| 5 | Opportunity | [opportunity.md](./opportunity.md) | Opportunity Management | 🟠 Cao |
| 6 | Lead | [lead.md](./lead.md) | Opportunity Management | 🟡 Trung bình |
| 7 | Account | [account.md](./account.md) | Sales / CRM | 🔴 Rất cao |
| 8 | Project | [project.md](./project.md) | Delivery Management | 🟠 Cao |

### 🔲 Cần làm tiếp (7 terms)

| Term | File | Domain | Ghi chú |
|---|---|---|---|
| Skill | [skill.md](./skill.md) | Resource Management | Liên quan Skill Matrix |
| Proposal | [proposal.md](./proposal.md) | Proposal Management | |
| Contract | [contract.md](./contract.md) | Contract Management | SAP integration |
| Staffing Request | [staffing-request.md](./staffing-request.md) | Resource Management | |
| Milestone | [milestone.md](./milestone.md) | Delivery Management | |
| Win Probability | [win-probability.md](./win-probability.md) | Opportunity Management | AI feature |
| Customer Health | [customer-health.md](./customer-health.md) | Customer Success | Domain mới |

---

## 🔍 Tại sao 8 term này được chọn trước?

Từ Module 1 (Pain Point Mining + System Inventory), 8 term này có **tần suất xung đột cao nhất** giữa các phòng ban:

```
Resource    → 5 người, 5 nghĩa
Utilization → 3 phòng, 3 con số khác nhau  
Account     → Sales/PM/Finance gọi khác nhau
Allocation  → chưa thống nhất đơn vị đo
```

**Quy tắc:** Giải quyết từ đau nhất trước. Term nào gây hiểu lầm nhiều nhất → viết Glossary trước.

---

## 📝 Template (copy khi tạo term mới)

```markdown
# [Term Name]

## Definition
[Một câu rõ ràng, không mơ hồ]

## Bounded Context
[Domain/Context nào?]

## Attributes
| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|

## Business Rules
- [Rule 1]

## States (nếu có)
[State diagram]

## Phân biệt với
- **[Term A]:** [Khác gì]

## Đồng nghĩa nguy hiểm ⚠️
- ❌ "[Từ sai]" — [Tại sao]

## Câu hỏi chưa giải quyết ⚠️
- [ ] [Cần stakeholders xác nhận]

## Related Concepts
- [[term-a]]

## Owner
[Team chịu trách nhiệm]
```
