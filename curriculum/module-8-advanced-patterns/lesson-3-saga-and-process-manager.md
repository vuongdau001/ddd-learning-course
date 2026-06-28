---
type: lesson
module: 8
lesson: 3
title: "Saga & Process Manager"
duration: "35 phút"
prerequisites: ["module-8/lesson-2"]
---

# Lesson 8.3: Saga & Process Manager

## 🎓 Concept — "Khi quy trình vượt qua 1 Aggregate"

### Vấn đề: Long-running business processes

```
Quy trình Staffing trong ITO:
1. PM tạo Resource Request
2. RM search matching resources
3. RM chọn resource → allocate
4. Resource confirm acceptance
5. PM approve assignment
6. System notify Client

→ 6 bước, 3 aggregates (Request, Resource, Project)
→ Bước 4 có thể mất 2 ngày (chờ resource respond)
→ Bước 5 có thể fail (PM reject)
→ Không thể dùng 1 database transaction!
```

### Saga = Chuỗi steps + compensation

```
Happy path:
  Step 1: CreateRequest         → RequestCreated
  Step 2: SearchMatches         → MatchesFound
  Step 3: AllocateResource      → ResourceAllocated
  Step 4: ConfirmAcceptance     → AcceptanceConfirmed
  Step 5: ApproveAssignment     → AssignmentApproved
  Step 6: NotifyClient          → ClientNotified

Compensation (khi Step 4 fails):
  Step 4 fails: ResourceDeclined
    → Compensate Step 3: DeallocateResource
    → Compensate Step 2: SearchAlternativeMatches
    → Retry from Step 2
```

### 2 loại Saga

**Choreography (event-based):**
```
Mỗi service listen events và tự biết bước tiếp:

RequestCreated → RM Service listens → SearchMatches
MatchesFound → RM Service → AllocateResource  
ResourceAllocated → Resource Service → WaitForConfirmation
ResourceDeclined → RM Service → DeallocateResource (compensation)

✅ Đơn giản, decoupled
❌ Khó follow full flow, khó debug
```

**Orchestration (coordinator):**
```
1 Process Manager điều phối tất cả:

StaffingProcessManager:
  on RequestCreated:     → call SearchMatches
  on MatchesFound:       → call AllocateResource
  on ResourceAllocated:  → call WaitForConfirmation
  on AcceptanceConfirmed:→ call ApproveAssignment
  on ResourceDeclined:   → call DeallocateResource (compensation)

✅ Dễ follow, dễ debug, rõ ràng
❌ Centralized, ProcessManager có thể phức tạp
```

### Ví dụ — ITO CRM: Staffing Saga

```
StaffingProcessManager {
  state: StaffingState = {
    requestId, status, selectedResourceId, attempts: 0
  }

  on ResourceRequested:
    → status = "SEARCHING"
    → send SearchMatchingResources command

  on MatchingResourcesFound:
    → status = "ALLOCATING"
    → send AllocateResource command (top match)

  on ResourceAllocated:
    → status = "AWAITING_CONFIRMATION"
    → send RequestConfirmation command
    → set timeout: 48 hours

  on ResourceAccepted:
    → status = "APPROVED"
    → send NotifyClient command

  on ResourceDeclined:
    → attempts++
    → send DeallocateResource command (compensation)
    → if attempts < 3: send SearchMatchingResources (retry)
    → else: status = "ESCALATED", notify RM manually

  on Timeout:
    → send DeallocateResource command (compensation)
    → status = "TIMED_OUT"
}
```

### Ví dụ — Logistics: Order-to-Delivery Saga

```
DeliveryProcessManager {
  on OrderCreated:
    → AssignVehicle
  on VehicleAssigned:
    → AssignDriver
  on DriverAssigned:
    → PlanRoute
  on RoutePlanned:
    → DispatchTrip
  
  Compensation:
  on DriverUnavailable:
    → ReleaseVehicle (compensation)
    → SearchAlternativeDriver
  
  on DeliveryFailed:
    → RescheduleDelivery
    → NotifyCustomer
}
```

---

## 🏋️ Exercise — Thiết kế Saga

### Phần A: ITO — Opportunity-to-Contract Saga (15 phút)

Thiết kế saga cho: Opportunity won → Contract created → Resources staffed

```
Steps:
1. _______________  → Event: _______________
2. _______________  → Event: _______________
3. _______________  → Event: _______________
4. _______________  → Event: _______________

Compensation (nếu Step 3 fails):
→ _______________
→ _______________
```

Chọn Choreography hay Orchestration? Tại sao?

### Phần B: Logistics — Return/Refund Saga (10 phút)

Thiết kế saga cho: Customer requests return → pickup → refund

```
Steps + compensations: _______________
```

### Phần C: Timeout handling (5 phút)

Trong Staffing Saga, nếu resource không respond trong 48h:
- Option A: Auto-reject, chọn resource khác
- Option B: Escalate to RM
- Option C: Auto-extend 24h rồi reject

Bạn chọn option nào? Tại sao?

---

## 🪞 Reflect

1. **Choreography vs Orchestration — khi nào dùng cái nào?** Gợi ý: Choreography cho đơn giản (2-3 steps). Orchestration cho phức tạp (>3 steps + compensations).

2. **Saga có cần "rollback" như database transaction không?** Gợi ý: Không! Saga dùng compensation — "undo" bằng action mới, không undo action cũ.

3. **Process Manager nên lưu state ở đâu?** Gợi ý: Database riêng, Event Store, hoặc in-memory (tùy requirements).

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Saga và compensation bằng ví dụ
- [ ] Phân biệt Choreography vs Orchestration
- [ ] Thiết kế Opportunity-to-Contract saga
- [ ] Xử lý timeout scenario
- [ ] Trả lời ≥2/3 câu hỏi reflection
