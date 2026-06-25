---
type: lesson
module: 4
lesson: 3
title: "Guided Workshop"
duration: "45 phút"
prerequisites: ["module-4/lesson-2"]
---

# Lesson 4.3: Guided Workshop — Event Storm cho Resource Management

## 🎓 Concept — "Làm thật từ đầu đến cuối"

### Tại sao làm workshop?

Lesson 4.1 và 4.2 dạy từng building block riêng lẻ. Lesson này gộp tất cả thành **1 Event Storm hoàn chỉnh** cho Core Domain: Resource Management.

### Workshop Flow — 5 bước

```
Bước 1: Brain-dump Events (10 phút)
   → Viết mọi event có thể nghĩ ra, không cần thứ tự
   
Bước 2: Timeline (10 phút)
   → Sắp xếp events theo thứ tự thời gian
   
Bước 3: Commands + Actors (10 phút)
   → Thêm: ai gửi command gì để tạo event đó?
   
Bước 4: Policies + Read Models (10 phút)
   → Thêm: quy tắc tự động nào? Xem data gì trước khi quyết định?
   
Bước 5: Hot Spots (5 phút)
   → Đánh dấu chỗ không chắc, tranh cãi, chưa rõ
```

---

## 🏋️ Workshop — Resource Management (làm tất cả)

### Bước 1: Brain-dump Events (10 phút)

Viết mọi event liên quan đến Resource Management. Không cần thứ tự. Càng nhiều càng tốt.

> Gợi ý: nghĩ về lifecycle — resource vào công ty → có skill → được match → allocate → chuyển project → bench → leave → ra đi

```
Event 1: _______________
Event 2: _______________
Event 3: _______________
Event 4: _______________
Event 5: _______________
Event 6: _______________
Event 7: _______________
Event 8: _______________
Event 9: _______________
Event 10: ______________
(thêm nếu cần)
```

<details>
<summary>💡 Gợi ý: Ít nhất nên có các events sau</summary>

- ResourceOnboarded, SkillProfileUpdated, ResourceRequested
- MatchingResourcesFound, ResourceAllocated, AllocationConfirmed
- AllocationRejected, AllocationEnded, ResourceBenched
- BenchAlerted, UtilizationCalculated, ResourceOffboarded
</details>

### Bước 2: Timeline (10 phút)

Sắp xếp events theo thứ tự thời gian. Nhóm theo "swim lane" nếu có parallel flows:

```
Timeline →

[Early lifecycle]
_____ → _____ → _____

[Allocation cycle] (lặp lại nhiều lần)
_____ → _____ → _____ → _____ → _____

[Monitoring] (chạy song song)
_____ → _____

[End lifecycle]
_____ → _____
```

### Bước 3: Commands + Actors (10 phút)

Với mỗi event, thêm Command và Actor:

| Actor | Command | → Event |
|---|---|---|
| HR | OnboardResource | → ResourceOnboarded |
| Resource | UpdateSkillProfile | → SkillProfileUpdated |
| | | → ResourceRequested |
| | | → MatchingResourcesFound |
| | | → ResourceAllocated |
| | | → AllocationConfirmed |
| | | → AllocationEnded |
| | | → ResourceBenched |
| | | → BenchAlerted |
| | | → UtilizationCalculated |

### Bước 4: Policies + Read Models (10 phút)

Thêm Policies (Whenever Event → Command) và Read Models (data xem trước):

| Event | Policy | Read Model cần xem |
|---|---|---|
| ResourceRequested | Auto-Match → FindMatches | Skills DB, Availability Calendar |
| ResourceAllocated | Notify → NotifyAll | — |
| AllocationEnded | Check Bench → EvaluateBench | Current Allocations |
| ResourceBenched | Alert → AlertManager | Bench Duration, Cost |
| UtilizationCalculated | — | Billable Hours Log |

Thêm policies của bạn:

| Event | Policy | Read Model |
|---|---|---|
| | | |
| | | |
| | | |

### Bước 5: Hot Spots (5 phút)

Đánh dấu ≥3 Hot Spots:

| Hot Spot 🔥 | Loại (Conflict/Unknown/TechDebt) | Giữa event nào? |
|---|---|---|
| | | |
| | | |
| | | |

---

## Event Storm hoàn chỉnh — Tham khảo

Sau 5 bước, Event Storm Resource Management nên trông như thế này:

```
HR → OnboardResource → ResourceOnboarded
                             ↓
Resource → UpdateSkillProfile → SkillProfileUpdated
                                      ↓
PM → RequestResource → ResourceRequested
                             ↓
      [Policy: Auto-Match] → FindMatchingResources
                             ↓
                        MatchingResourcesFound
                             ↓
      RM → (xem Available Resources + Skills) → AllocateResource
                             ↓
                        ResourceAllocated
                             ↓
      [Policy: Notify PM + Resource]
                             ↓
      🔥 "Resource có quyền reject?"
                             ↓
      Resource → ConfirmAllocation → AllocationConfirmed
                                         ↓
                        ... (delivery phase) ...
                                         ↓
      PM/RM → EndAllocation → AllocationEnded
                                    ↓
      [Policy: Check Bench] → EvaluateBenchStatus
                                    ↓
                    ResourceBenched (nếu no new allocation)
                                    ↓
      [Policy: Alert if bench > 5 days]
                                    ↓
                        BenchAlerted → RM review

=== Song song ===
      [Scheduled] → CalculateUtilization → UtilizationCalculated
                                                ↓
      [Policy: Alert if > 95%] → AlertBurnoutRisk
```

---

## 🪞 Reflect

1. **Bạn tìm được bao nhiêu events?** Dưới 10 → chưa đào đủ sâu. 10-20 → tốt cho Core Domain. Trên 30 → có thể domain này nên tách.

2. **Policy nào bạn muốn implement đầu tiên?** Tiêu chí: impact cao + complexity thấp = Quick Win.

3. **Hot Spot nào nguy hiểm nhất?** Hot Spot loại Conflict cần resolve TRƯỚC KHI code. Hot Spot loại TechDebt có thể defer.

---

## ✅ Hoàn thành lesson khi
- [ ] Brain-dump ≥10 events cho Resource Management
- [ ] Sắp xếp timeline
- [ ] Thêm Commands + Actors cho mỗi event
- [ ] Thêm ≥4 Policies
- [ ] Đánh dấu ≥3 Hot Spots
- [ ] Lưu output vào `practice/event-storm/resource-management.md`

---

## 🏁 Hoàn thành Module 4

→ Tiếp: **Quiz Module 4** rồi sang **Module 5: Bounded Context**
