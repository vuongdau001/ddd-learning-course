---
type: lesson
module: 8
lesson: 3
title: "Saga & Process Manager"
duration: "35 phút"
prerequisites: ["module-8/lesson-2"]
narrative_phase: "advanced patterns"
migration_phase: "Phase 3: Orchestrate cross-aggregate workflows"
business_invariant: "Saga = chuỗi steps cross-aggregate + compensation khi fail; Choreography = decoupled (events), Orchestration = centralized (Process Manager); Compensation ≠ rollback — undo bằng action MỚI"
---

# Lesson 8.3: Saga & Process Manager — "Khi quy trình vượt qua 1 Aggregate"

## 📍 Context — Bạn đang ở đây

> Module 8.1-8.2 cho bạn CQRS (tách Read/Write) + Event Sourcing (lưu events). Nhưng events từ 1 aggregate chỉ giải quyết 1 phần. Khi business process cần **nhiều aggregates**, nhiều bước, có thể fail giữa chừng — cần pattern mới: **Saga**. VD: "Opportunity Won → tạo Contract → staff Resources" = 3 aggregates, 6 bước, bước 4 có thể mất 2 ngày (chờ resource respond).

## 🔥 Tension — "Staffing process bị kẹt giữa chừng"

Sprint 11. Staffing workflow:

> **Hà:** *"Opportunity PRJ-ALPHA vừa Won. Tôi cần staff 3 resources. Workflow: tạo request → match resources → allocate → resource confirm → PM approve → notify client. 6 bước, 3 aggregates."*

> **Dev A:** *"Tôi wrap tất cả trong 1 database transaction?"*

> **Tuấn:** *"Bước 4 (resource confirm) có thể mất 2 ngày. Transaction lock 2 ngày? Không thể. Và nếu resource decline ở bước 4 — bạn rollback allocation ở bước 3 thế nào? Bạn không thể 'undo' một email đã gửi."*

**Minh:**
> *"Business process dài hạn KHÔNG thể dùng 1 transaction. Cần **Saga** — chuỗi steps, mỗi step = 1 transaction nhỏ. Nếu step N fail → chạy **compensation** (hành động ngược) cho step N-1, N-2... Compensation KHÔNG PHẢI rollback — nó là action MỚI: 'DeallocateResource' compensate cho 'AllocateResource'."*

> 💭 **Câu hỏi:** Long-running process = many steps, many aggregates, may fail anywhere. Transaction không giải quyết được. Saga = steps + compensation. Nhưng ai orchestrate? Events tự điều phối (Choreography) hay central coordinator (Process Manager)?

## 🎓 Explanation — Saga & Process Manager

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Saga = chuỗi steps cross-aggregate, mỗi step = 1 local transaction. Nếu step fail → compensation (action ngược) cho các steps trước. Choreography = event-driven, decoupled. Orchestration = Process Manager centralized. Compensation ≠ DB rollback — compensation = business action mới."*

### Happy Path vs Compensation

```
Staffing Saga — Happy Path:
  Step 1: CreateResourceRequest   → RequestCreated
  Step 2: SearchMatchingResources → MatchesFound
  Step 3: AllocateResource        → ResourceAllocated
  Step 4: RequestConfirmation     → AcceptanceConfirmed     (chờ 2 ngày)
  Step 5: ApproveAssignment       → AssignmentApproved
  Step 6: NotifyClient            → ClientNotified
  ✅ DONE

Staffing Saga — Step 4 Fail:
  Step 1: CreateResourceRequest   → RequestCreated          ✅
  Step 2: SearchMatchingResources → MatchesFound            ✅
  Step 3: AllocateResource        → ResourceAllocated       ✅
  Step 4: RequestConfirmation     → ResourceDeclined        ❌ FAIL
    → Compensation Step 3: DeallocateResource               ↩️
    → Retry Step 2: SearchAlternativeResources
    → Retry from Step 3...

Key: Compensation = action MỚI (DeallocateResource), KHÔNG phải DB rollback
```

### 2 Loại Saga

**Choreography (Event-driven):**
```
Mỗi service listen events, tự biết bước tiếp:

RequestCreated     → [RM Service listens]    → SearchMatches
MatchesFound       → [RM Service]            → AllocateResource
ResourceAllocated  → [Resource Service]      → WaitForConfirmation
ResourceDeclined   → [RM Service]            → DeallocateResource (compensation)
AcceptanceConfirmed→ [PM Service]            → ApproveAssignment

✅ Decoupled — services không biết nhau
✅ Đơn giản cho 2-3 steps
❌ Khó follow full flow (event chain hidden)
❌ Khó debug — "process đang ở step nào?"
❌ Compensation logic phân tán
```

**Orchestration (Process Manager):**
```
1 Process Manager điều phối tất cả:

StaffingProcessManager:
  on RequestCreated:       → send SearchMatchingResources cmd
  on MatchesFound:         → send AllocateResource cmd (top match)
  on ResourceAllocated:    → send RequestConfirmation cmd
                           → set timeout: 48 hours
  on AcceptanceConfirmed:  → send ApproveAssignment cmd
  on ResourceDeclined:     → send DeallocateResource cmd (compensation)
                           → if attempts < 3: retry search
                           → else: send EscalateToRM cmd

✅ Full flow visible — 1 file, 1 state machine
✅ Dễ debug — check PM state
✅ Compensation centralized
❌ Centralized = single point of complexity
```

### ITO Staffing Saga — Complete

```typescript
class StaffingProcessManager {
  private state: StaffingState

  constructor() {
    this.state = {
      requestId: null,
      status: "IDLE",
      selectedResourceId: null,
      attempts: 0,
      maxAttempts: 3
    }
  }

  on(event: ResourceRequested): void {
    this.state.requestId = event.requestId
    this.state.status = "SEARCHING"
    this.send(new SearchMatchingResources(event.requirements))
  }

  on(event: MatchingResourcesFound): void {
    if (event.matches.length === 0) {
      this.state.status = "NO_MATCHES"
      this.send(new EscalateToRM(this.state.requestId))
      return
    }
    this.state.status = "ALLOCATING"
    this.state.selectedResourceId = event.matches[0].resourceId
    this.send(new AllocateResource(
      this.state.selectedResourceId, event.projectId, event.percentage
    ))
  }

  on(event: ResourceAllocated): void {
    this.state.status = "AWAITING_CONFIRMATION"
    this.send(new RequestConfirmation(this.state.selectedResourceId))
    this.setTimeout(48, "HOURS")  // Business rule: 48h to respond
  }

  on(event: ResourceAccepted): void {
    this.state.status = "APPROVED"
    this.send(new NotifyClient(this.state.requestId))
    this.state.status = "COMPLETED"
  }

  on(event: ResourceDeclined): void {
    this.state.attempts++
    // COMPENSATION: undo allocation
    this.send(new DeallocateResource(this.state.selectedResourceId))

    if (this.state.attempts < this.state.maxAttempts) {
      this.state.status = "SEARCHING"
      this.send(new SearchMatchingResources(/*exclude declined*/))
    } else {
      this.state.status = "ESCALATED"
      this.send(new EscalateToRM(this.state.requestId, "3 declines"))
    }
  }

  on(event: Timeout): void {
    // COMPENSATION: resource didn't respond in 48h
    this.send(new DeallocateResource(this.state.selectedResourceId))
    this.state.status = "TIMED_OUT"
    this.send(new EscalateToRM(this.state.requestId, "timeout"))
  }
}
```

### Logistics — Order-to-Delivery Saga

```typescript
class DeliveryProcessManager {
  on(event: OrderCreated): void {
    this.send(new FindAvailableVehicle(event.requirements))
  }

  on(event: VehicleAssigned): void {
    this.send(new AssignDriver(event.vehicleId, event.requiredLicense))
  }

  on(event: DriverAssigned): void {
    this.send(new PlanRoute(event.stops, event.vehicleId))
  }

  on(event: RoutePlanned): void {
    this.send(new DispatchTrip(event.tripId))
  }

  // Compensation
  on(event: DriverUnavailable): void {
    this.send(new ReleaseVehicle(event.vehicleId))  // compensate VehicleAssigned
    this.send(new FindAlternativeDriver(event.requiredLicense))
  }

  on(event: DeliveryFailed): void {
    this.send(new RescheduleDelivery(event.shipmentId))
    this.send(new NotifyCustomer(event.customerId, "delivery rescheduled"))
  }
}
```

### ⚖️ Trade-offs — Choreography vs Orchestration

| | Choreography | Orchestration |
|---|---|---|
| **Khi dùng** | 2-3 steps, đơn giản | >3 steps + compensation + timeout |
| **Coupling** | Low (events only) | Medium (PM knows all steps) |
| **Visibility** | Low (flow hidden in events) | High (PM = state machine) |
| **Debug** | Khó (trace across services) | Dễ (check PM state) |
| **ITO dùng** | Inter-context notifications | Staffing saga, Opportunity-to-Contract |

---

## 🏋️ Exercise — Thiết kế Saga

### Phần A: ITO — Opportunity-to-Contract Saga (15 phút)

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

Customer requests return → schedule pickup → inspect item → process refund

```
Steps + compensations: _______________
```

### Phần C: Timeout handling (5 phút)

Staffing Saga: resource không respond trong 48h. Chọn và giải thích:
- **A:** Auto-reject, chọn resource khác
- **B:** Escalate to RM
- **C:** Auto-extend 24h rồi reject

---

## 🪞 Reflect

1. **Choreography vs Orchestration — guideline?** → **≤3 steps, no compensation:** Choreography OK. **>3 steps, compensation, timeout, retry:** Orchestration (Process Manager). ITO Staffing = 6 steps + timeout + compensation → **Orchestration**.

2. **Saga "rollback" = DB rollback?** → **KHÔNG!** DB rollback undo data changes. Saga compensation = **business action mới** — "DeallocateResource" là action có nghĩa business (update state, notify). Email đã gửi KHÔNG thể rollback — chỉ có thể gửi email "cancelled".

3. **Process Manager state lưu ở đâu?** → Database (durable, survive restart), Event Store (natural fit với ES), hoặc in-memory (simple nhưng lost on restart). **ITO recommendation:** DB — vì staffing process mất days, cần durable state.

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Saga + compensation + Choreography vs Orchestration
- [ ] **Apply:** Thiết kế Opportunity-to-Contract saga + Return/Refund saga
- [ ] **Analyze:** Chọn timeout strategy + giải thích compensation ≠ DB rollback

---

> 🔗 **Tiếp theo:** Module 7-8 hoàn tất Tactical DDD + Advanced Patterns. Bạn biết model (Entity/VO/Aggregate), persist (Repository/ES), communicate (Events/CQRS), và orchestrate (Saga). Module 9 — *Enterprise Integration* — sẽ mở rộng ra ngoài: DDD trong Enterprise Architecture, Knowledge Graph liên kết mọi artifacts, và Organizational Knowledge Management.
