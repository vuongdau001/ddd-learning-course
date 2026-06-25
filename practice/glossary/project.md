# Project

## Definition

Một cam kết delivery có thời hạn, được tạo **sau khi Opportunity Won** và Contract ký, trong đó ITO phân bổ Resources để thực hiện scope đã thỏa thuận với khách hàng.

> Project chỉ tồn tại **SAU KHI** có Contract. Trước đó, nó là Opportunity.

## Bounded Context

**Delivery Management**

## Attributes

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| project_id | string | ✅ | |
| name | string | ✅ | VD: "ABC Corp - AI Testing Platform Phase 1" |
| account | Account | ✅ | Khách hàng (trong Delivery context → gọi là "Customer") |
| contract | Contract | ✅ | HĐ tương ứng |
| pm | Resource | ✅ | Project Manager phụ trách |
| team | Resource[] | ✅ | Danh sách team members + allocation |
| start_date | date | ✅ | |
| end_date | date | ✅ | |
| status | enum | ✅ | Setup · Active · OnHold · Completed · Cancelled |
| delivery_model | enum | ✅ | ODC · FixedPrice · TimeAndMaterial |
| technology_stack | string[] | | |

## Business Rules

1. **Project chỉ được tạo khi có Contract signed** — Không delivery khi chưa có HĐ
2. **Project phải có ≥1 Resource allocated trước khi Active** — Không chạy project không có người
3. **Project OnHold > 30 ngày → review: continue hoặc cancel** — Tránh project zombie
4. **Thay đổi team > 2 người cùng lúc → phải có Risk Assessment** — Impact analysis

## States

```
Setup → Active → Completed
          ↓           
        OnHold → Active (resume)
          ↓
       Cancelled
```

## ⚠️ "Project" nghĩa khác nhau ở mỗi context

| Bounded Context | Gọi là | Ý nghĩa | Quan tâm gì |
|---|---|---|---|
| **CRM/Sales** | Opportunity | Cơ hội bán hàng, chưa chắc có | Value, win probability |
| **Delivery** | **Project** | Cam kết delivery đang chạy | Timeline, sprint, quality |
| **Resource** | Engagement | Khoảng thời gian resource tham gia | Allocation %, start/end |
| **Finance** | Work Order | Đơn vị tính chi phí + billing | Cost center, billing rate |

> Trong **Delivery context**, luôn gọi là "Project". Đừng gọi là "engagement" hay "work order" — đó là thuật ngữ của context khác.

## Phân biệt với

| Hay bị nhầm | Khác ở đâu |
|---|---|
| **Opportunity** | Opportunity = cơ hội bán (chưa chắc). Project = cam kết delivery (đã chắc). Transition: Opportunity Won → Project Created. |
| **Contract** | Contract = tài liệu pháp lý. Project = hoạt động thực hiện. 1 Contract → 1+ Projects (nếu có phases). |
| **Sprint** | Sprint = iteration (2 tuần). Project = toàn bộ engagement (3-18 tháng). |
| **Task** | Task = đơn vị công việc nhỏ. Project = tập hợp tất cả tasks + team + timeline. |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Dự án" + "Opportunity" lẫn nhau | Sales nói "dự án này" khi còn đang negotiate → PM hiểu đã ký → chaos |
| "Engagement" (ngoài Resource context) | Gây nhầm với Delivery. Trong Resource context OK, ngoài context → dùng "Project" |

## Câu hỏi chưa giải quyết ⚠️

- [ ] **1 Contract → nhiều Projects?** VD: Contract 12 tháng chia 3 phases = 3 Projects hay 1 Project?
- [ ] **Maintenance sau Go-Live là Project mới hay extension?** Delivery model khác (T&M vs Fixed) nhưng cùng khách
- [ ] **Internal project (xây tool nội bộ) có phải Project không?** Nếu có → billable = false. Nếu không → cần concept riêng.

## Related Concepts

- [[opportunity]] — Tạo từ Opportunity Won
- [[contract]] — Project phải có Contract
- [[allocation]] — Resource được allocate vào Project
- [[resource]] — Team members của Project

## Owner

**Delivery Management Team** (PM)

---

## 💡 Project là "điểm gặp" của 4 Bounded Contexts

```
           Sales               Resource             Finance
        (Opportunity)        (Engagement)         (Work Order)
              ↓                    ↓                    ↓
              └───────── PROJECT ──────────┘            ↑
                     (Delivery)                        │
                         │                             │
                         └─────────────────────────────┘
```

Mỗi context nhìn "Project" từ góc khác:
- Sales: "Chúng ta thắng deal chưa?"
- Delivery: "Dự án đang on-track không?"
- Resource: "Ai đang làm, % bao nhiêu?"
- Finance: "Revenue / cost bao nhiêu?"

→ Đây là lý do **Context Map** quan trọng — nó chỉ ra cách 4 góc nhìn này kết nối với nhau.
