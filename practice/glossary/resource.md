# Resource

## Definition

Một nhân sự thuộc biên chế công ty ITO, có kỹ năng chuyên môn xác định, và **có thể được phân bổ (allocate) vào dự án** để tạo ra giá trị billable cho khách hàng.

## Bounded Context

**Resource Management**

## Attributes

| Attribute | Type | Bắt buộc | Mô tả |
|---|---|:---:|---|
| employee_id | string | ✅ | Mã nhân viên nội bộ (unique) |
| full_name | string | ✅ | Họ và tên |
| email | string | ✅ | Email công ty |
| skills | Skill[] | ✅ | Danh sách kỹ năng + cấp độ (VD: Java/Senior, React/Mid) |
| seniority | enum | ✅ | Junior · Middle · Senior · Lead · Principal |
| role | enum | ✅ | Developer · QA · BA · PM · Designer · DevOps |
| current_allocations | Allocation[] | | Dự án đang tham gia + % allocation |
| availability_from | date | | Ngày có thể nhận allocation mới |
| bench_status | boolean | | Đang bench hay không? (computed) |

## Business Rules

1. **Tổng allocation ≤ 100%** — Một Resource không thể được phân bổ quá 100% cùng lúc
2. **Allocation < 20% trong > 2 tuần → Bench Status** — Tự động chuyển sang Bench
3. **Resource phải có ≥1 Skill** — Không thể tồn tại Resource không có skill nào
4. **Mỗi Resource chỉ thuộc 1 phòng ban tại 1 thời điểm**

## States

```
Onboarding → Available → Allocated → Available (cycle)
                ↓                        ↓
              Bench ← ─ ─ ─ ─ ─ ─ ─ ─ ─ ┘
                ↓
           On Leave (tạm thời)
                ↓
             Offboarded (kết thúc)
```

## Phân biệt với

| Hay bị nhầm với | Khác ở đâu |
|---|---|
| **Employee** | Employee = khái niệm HR (lương, HĐ, bảo hiểm, nghỉ phép). Resource = khái niệm Delivery (ai làm gì, cho dự án nào). Một Employee có thể không phải Resource (VD: kế toán, admin). |
| **Headcount** | Headcount = con số tổng (VD: "công ty có 200 headcount"). Resource = từng cá nhân cụ thể có skill/seniority. |
| **User** | User = tài khoản đăng nhập hệ thống (Authentication context). Resource = con người có skill (Resource Management context). |
| **Team Member** | Team Member là vai trò tạm thời trong 1 project. Resource là identity cố định xuyên suốt nhiều project. |

## Đồng nghĩa nguy hiểm ⚠️

| ❌ Không dùng | Tại sao |
|---|---|
| "Người" | Quá mơ hồ — người gì? Khách hàng? Nhân sự? |
| "Dev" | Chỉ Developer, bỏ sót QA, PM, BA, Designer |
| "Staff" | Dùng trong HR context, gây nhầm với Employee |
| "Member" | Mơ hồ — member của cái gì? Team? Công ty? |
| "Assignee" | Thuật ngữ Jira, gắn với task, không phải project-level |

## Câu hỏi chưa giải quyết ⚠️

- [ ] **Freelancer/Vendor có phải Resource không?** Nếu họ được allocate vào project giống nhân viên → có nên track chung? Hay tạo concept riêng (`ExternalResource`)?
- [ ] **Intern có phải Resource không?** Intern có allocation nhưng không billable — có tính Utilization?
- [ ] **Resource nghỉ phép dài hạn (>1 tháng) có bị tính Bench không?** Hay cần state riêng?

## Related Concepts

- [[allocation]] — Resource được phân bổ vào Project qua Allocation
- [[skill]] — Mỗi Resource có danh sách Skill + cấp độ
- [[bench]] — Trạng thái khi Resource không có Allocation
- [[utilization]] — KPI đo mức sử dụng thời gian Resource
- [[staffing-request]] — Yêu cầu cần Resource cho Project

## Owner

**Resource Management Team** — Resource Manager là người có quyền approve/update definition này.

---

## 💡 Tại sao term này xung đột nặng nhất?

"Resource" là từ **xuất hiện ở mọi phòng ban** nhưng mỗi nơi hiểu khác:

```
HR:              "Resource" = headcount, employee record
Finance:         "Resource" = cost item, labor expense  
PM:              "Resource" = người trong team tôi, assignee
Sales:           "Resource" = capability tôi bán cho khách
Resource Mgr:    "Resource" = kỹ sư có thể allocate

→ Khi CEO hỏi "resource utilization bao nhiêu?"
→ 5 phòng ban trả lời 5 con số khác nhau
→ Vì họ đang nói về 5 khái niệm khác nhau dùng chung 1 từ
```

**Giải pháp DDD:** Trong **Resource Management context**, "Resource" CHỈ có nghĩa như definition ở trên. Trong context khác (HR, Finance), dùng thuật ngữ riêng của context đó (Employee, Labor Cost).
