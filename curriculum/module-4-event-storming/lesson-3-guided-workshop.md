---
type: lesson
module: 4
lesson: 3
title: "Guided Workshop"
duration: "45 phút"
prerequisites: ["module-4/lesson-2"]
narrative_phase: "khám phá hành vi"
migration_phase: "Pre-migration: Event Storm Workshop thực tế"
business_invariant: "1 Core Domain cần 15-30 events; 0 Hot Spots = chưa đào đủ sâu; Event Storm output = tài liệu kiến trúc cho dev"
---

# Lesson 4.3: Guided Workshop — Event Storm cho Resource Management

## 📍 Context — Bạn đang ở đây

> Lesson 4.1 dạy Events + Commands. Lesson 4.2 dạy Policies + Hot Spots. Bạn đã áp dụng từng building block riêng lẻ cho Opportunity Lifecycle. Bây giờ, CTO **Minh** muốn một Event Storm **hoàn chỉnh** cho Core Domain quan trọng nhất: **Resource Management** — domain được đánh giá Build + AI-Augment (M3.3), Priority Score cao nhất Phase 2 (M3.2).
>
> Bạn sẽ facilitate 1 buổi Event Storming workshop: brain-dump → timeline → actors → policies → hot spots, cho ra output đủ chi tiết để dev team bắt đầu implement.

## 🔥 Tension — "3 PM, 3 flow khác nhau"

Minh mời 3 PM đến buổi Event Storm cho Resource Management. Mỗi người mô tả "flow request resource" khác nhau:

> **PM Alpha:** *"Tôi email Tuấn danh sách skill cần. Tuấn trả lời trong 1-2 ngày. Nếu không có người → tôi escalate lên CTO."*
>
> **PM Beta:** *"Tôi tạo ticket trên Jira. Tuấn xem khi nào rảnh. Thường 3-5 ngày. Không có SLA."*
>
> **PM Gamma:** *"Tôi gọi điện Tuấn trực tiếp. Nếu Tuấn bận thì tôi hỏi dev team nào available rồi tự pull."*

**Tuấn** (RM) ngồi nghe, mặt đỏ:
> *"Tôi nhận yêu cầu qua 3 kênh: email, Jira, điện thoại. Mỗi PM một flow. Tôi không track nổi. Khi 2 PM request cùng 1 resource → xung đột. Ai biết trước? Ai ưu tiên?"*

**Minh** kết luận:
> *"Đây là tại sao chúng ta cần Event Storm. Không phải để vẽ diagram đẹp — mà để 3 PM và RM cùng thống nhất 1 flow DUY NHẤT."*

> 💭 **Câu hỏi:** 3 PM, 3 quy trình. Resource Matching Engine bạn định build (Build + AI-Augment) sẽ implement flow nào? Làm sao chọn?

## 🎓 Explanation — Workshop Process

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"1 Core Domain phải có MỘT quy trình duy nhất (single source of truth). Event Storm giúp phát hiện và thống nhất khi domain experts có flow khác nhau. Output = event flow diagram + policy list + hot spot register — đủ cho dev implement Sprint 1."*

### Workshop Flow — 5 bước (45 phút)

```
Bước 1: Brain-dump Events (10 phút)
   → Viết MỌI event có thể nghĩ ra — không cần thứ tự, không judge

Bước 2: Timeline (10 phút)
   → Sắp xếp events theo thứ tự thời gian — left to right

Bước 3: Commands + Actors (10 phút)
   → Thêm: AI gửi command gì để tạo event đó?

Bước 4: Policies + Read Models (10 phút)
   → Thêm: quy tắc tự động nào? Xem data gì trước khi quyết định?

Bước 5: Hot Spots (5 phút)
   → Đánh dấu chỗ không chắc, tranh cãi, chưa rõ
```

### Facilitation Tips

| Situation | Facilitator nên | Facilitator KHÔNG nên |
|---|---|---|
| 2 PM tranh cãi về flow | Dán 🔥 Hot Spot, tiếp tục | Dừng lại debate 30 phút |
| Im lặng quá lâu | Hỏi "Trước event này, chuyện gì xảy ra?" | Tự viết event |
| Quá nhiều events (>30) | Nhóm theo swim lanes | Xóa events "thừa" |
| Domain expert dùng sai tên | Mở Glossary, correct ngay | Bỏ qua, sửa sau |

---

## 🏋️ Workshop — Resource Management Event Storm

### Bước 1: Brain-dump Events (10 phút)

Viết MỌI event liên quan đến Resource Management. Không cần thứ tự. Càng nhiều càng tốt.

> 💡 **Gợi ý:** Nghĩ về full lifecycle — resource vào công ty → có skill → được match → allocate → delivery → chuyển project → bench → leave → ra đi. Nhớ: **mỗi kết quả = 1 event** (success + failure).

```
Event 1:  _______________
Event 2:  _______________
Event 3:  _______________
Event 4:  _______________
Event 5:  _______________
Event 6:  _______________
Event 7:  _______________
Event 8:  _______________
Event 9:  _______________
Event 10: _______________
Event 11: _______________
Event 12: _______________
(thêm nếu cần — target ≥12)
```

<details>
<summary>💡 Reference: Ít nhất nên có các events sau</summary>

**Onboarding:** ResourceOnboarded, SkillProfileUpdated
**Matching:** ResourceRequested, MatchingResourcesFound, NoMatchFound
**Allocation:** ResourceAllocated, AllocationConfirmed, AllocationRejected
**Delivery:** AllocationExtended, AllocationEnded
**Bench:** ResourceBenched, BenchAlerted
**Monitoring:** UtilizationCalculated, BurnoutRiskDetected
**Offboarding:** ResourceOffboarded
</details>

### Bước 2: Timeline (10 phút)

Sắp xếp events theo thứ tự thời gian. Nhóm theo swim lane:

```
Timeline →

[Onboarding]
_____ → _____

[Request & Match] (trigger từ Opportunity flow)
_____ → _____ → _____

[Allocation Cycle] (lặp lại nhiều lần trong career 1 resource)
_____ → _____ → _____ → _____ → _____

[Bench Management] (khi giữa 2 allocations)
_____ → _____ → _____

[Monitoring] (chạy song song, scheduled)
_____ → _____

[Offboarding]
_____
```

### Bước 3: Commands + Actors (10 phút)

Với mỗi event, thêm Command + Actor:

| Actor | Command | → Event |
|---|---|---|
| HR | OnboardResource | → ResourceOnboarded |
| Resource | UpdateSkillProfile | → SkillProfileUpdated |
| PM | RequestResource | → ResourceRequested |
| [Policy: Auto-Match] | FindMatchingResources | → MatchingResourcesFound |
| RM (Tuấn) | ? | → ResourceAllocated |
| Resource | ? | → AllocationConfirmed |
| Resource | ? | → AllocationRejected |
| PM / RM | ? | → AllocationEnded |
| [Policy: Check Bench] | ? | → ResourceBenched |
| [Scheduled] | ? | → UtilizationCalculated |
| HR / RM | ? | → ResourceOffboarded |

### Bước 4: Policies + Read Models (10 phút)

| Event | Policy (Whenever → then) | Read Model cần xem trước |
|---|---|---|
| ResourceRequested | Auto-Match → FindMatchingResources | Skills DB, Availability Calendar |
| ResourceAllocated | Notify → NotifyPM + NotifyResource | — |
| AllocationEnded | Check Bench → EvaluateBenchStatus | Current Allocations |
| ResourceBenched(> 5 days) | Alert → AlertManager | Bench Duration, Cost per Day |
| UtilizationCalculated(> 95%) | Alert → AlertBurnoutRisk | Billable Hours Log |

**Thêm policies bạn phát hiện:**

| Event | Policy | Read Model |
|---|---|---|
| ? | ? | ? |
| ? | ? | ? |

### Bước 5: Hot Spots (5 phút)

Đánh dấu ≥3 Hot Spots:

| Hot Spot 🔥 | Loại | Giữa event nào? | Ai cần quyết định? |
|---|---|---|---|
| "Resource có quyền reject?" | Conflict | AllocateResource → Confirm/Reject | CTO + HR |
| "2 PM request cùng 1 resource → ai ưu tiên?" | Conflict | ResourceRequested × 2 | CTO (priority rule) |
| "AI match semantic skills — accuracy threshold bao nhiêu?" | Unknown | MatchingResourcesFound | Tech Lead + RM |
| ? (thêm của bạn) | ? | ? | ? |

---

## 📋 Event Storm hoàn chỉnh — Reference

```
[ONBOARDING]
HR → OnboardResource → ResourceOnboarded
                              ↓
Resource → UpdateSkillProfile → SkillProfileUpdated

[REQUEST & MATCH — trigger khi Opportunity flow cần resource]
PM → RequestResource → ResourceRequested
                             ↓
[Policy: Auto-Match] → FindMatchingResources
                             ↓
MatchingResourcesFound    OR    NoMatchFound
       ↓                              ↓
       ↓                    [Policy: Alert RM to recruit]
       ↓
[AI-Augment: Semantic Skill Matching — "React" ≈ "Next.js" = 85%]
       ↓
RM (Tuấn) → (xem Matching Results + Availability + Cost)
          → AllocateResource
                ↓
ResourceAllocated
       ↓
[Policy: Notify PM + Resource + Update Calendar]
       ↓
🔥 "Resource có quyền reject?"
       ↓
Resource → ConfirmAllocation → AllocationConfirmed
                                    ↓
                  ... (delivery — tracked in Delivery Mgmt domain) ...
                                    ↓
PM/RM → EndAllocation → AllocationEnded
                              ↓
[Policy: Check Bench] → EvaluateBenchStatus
                              ↓
ResourceBenched (nếu no new allocation)
       ↓
🔥 "2 PM request cùng resource — ai ưu tiên?"
       ↓
[Policy: Alert if bench > 5 days] → BenchAlerted
       ↓
RM → (review) → FindNewProject OR StartOffboarding

=== MONITORING (chạy song song, weekly) ===
[Scheduled] → CalculateUtilization → UtilizationCalculated
                                          ↓
[Policy: if > 95%] → AlertBurnoutRisk → Manager review
[Policy: if < 50%] → AlertUnderutilized → RM review

=== OFFBOARDING ===
HR/RM → OffboardResource → ResourceOffboarded
                               ↓
[Policy: Reallocate active projects] → NotifyAffectedPMs
```

### ⚖️ Trade-offs — Workshop Output Format

| | Event Storm Board (visual) | Event Catalog Document | Cả hai |
|---|---|---|---|
| **Được** | Dễ facilitate, stakeholders thấy big picture | Dev đọc được, searchable, version controlled | Đầy đủ nhất |
| **Mất** | Khó maintain khi team grow, not searchable | Mất visual flow, stakeholder khó đọc | Double effort maintain |
| **Recommendation** | Workshop → photograph board → convert thành document. Board = discovery tool. Document = reference tool. |

---

## 🪞 Reflect

1. **Bạn tìm được bao nhiêu events?** <10 → chưa đào đủ sâu (thiếu failure events, edge cases). 10-20 → tốt cho 1 Core Domain. >30 → domain này có thể nên tách thành sub-domains (signal cho Bounded Context ở Module 5!).

2. **Policy nào implement đầu tiên?** Tiêu chí: **Impact cao + Complexity thấp = Quick Win.** Auto-Staffing (Whenever OpportunityWon → CreateStaffingRequest) = Quick Win vì giải quyết ngay sự cố 3-ngày-delay từ Sprint 2.

3. **Hot Spot nào phải resolve TRƯỚC khi code?** Conflict Hot Spots → **phải resolve trước Sprint 1** (không biết ai approve allocation → dev code cái gì?). Unknown Hot Spots → có thể defer nếu không block flow chính. Tech Debt → plan cho Phase 2.

---

## ✅ Completion Checklist
- [ ] **Recall:** Brain-dump ≥12 events cho Resource Management, sắp xếp timeline
- [ ] **Apply:** Thêm Commands + Actors + ≥5 Policies + ≥3 Hot Spots = Event Storm hoàn chỉnh
- [ ] **Analyze:** So sánh output của bạn với Reference — thiếu gì? Thừa gì? — liên hệ với vấn đề "3 PM, 3 flow khác nhau"
- [ ] **Deliverable:** Lưu output vào `practice/event-storm/resource-management.md`

---

## 🏁 Hoàn thành Module 4 — Event Storming

| Lesson | Bạn đã làm | Output |
|---|---|---|
| 4.1 Events + Commands | Phân biệt 5 building blocks, viết Opportunity Lifecycle | Event Storm cơ bản (events, commands, actors) |
| 4.2 Policies + Hot Spots | Thêm automation rules + conflict markers | Event Storm mở rộng (+ policies, hot spots) |
| 4.3 Guided Workshop | Full Event Storm cho Resource Management | Event Storm hoàn chỉnh + Hot Spot Register |

**Deliverable:** `practice/event-storm/resource-management.md`

> 🔗 **Tiếp theo — Module 5: Bounded Context.** Event Storm cho bạn bức tranh hành vi: events nào, actors nào, policies nào. Nhưng có 1 vấn đề: "Resource" trong Resource Mgmt context = con người có skill. "Resource" trong Delivery context = con người assigned vào sprint. **Cùng 1 từ, 2 nghĩa khác nhau.** Module 5 sẽ dạy bạn **Bounded Context** — kỹ thuật vẽ ranh giới rõ ràng giữa các vùng nghĩa, để "Resource" trong mỗi context có model riêng, code riêng, team riêng.
