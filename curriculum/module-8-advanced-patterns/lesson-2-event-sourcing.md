---
type: lesson
module: 8
lesson: 2
title: "Event Sourcing"
duration: "40 phút"
prerequisites: ["module-8/lesson-1"]
narrative_phase: "advanced patterns"
migration_phase: "Phase 3: Event-driven persistence cho audit + temporal queries"
business_invariant: "Event Sourcing = lưu events (lịch sử), KHÔNG lưu state (snapshot); Rebuild state = replay events; Projections = read models từ events; Snapshot = optimization cho performance"
---

# Lesson 8.2: Event Sourcing — "Lưu chuyện gì đã xảy ra, không lưu trạng thái"

## 📍 Context — Bạn đang ở đây

> Lesson 8.1 CQRS tách Read/Write models. Write side vẫn lưu **STATE** (latest snapshot trong DB). Nhưng có bài toán cần biết **LỊCH SỬ**: "Opportunity-001 chuyển từ Lead → Qualified khi nào? Ai thay đổi value từ 300K → 500K? Lúc nào bị reject rồi mở lại?" State-based = mất lịch sử. Event Sourcing = lưu MỌI SỰ KIỆN → rebuild state bất kỳ thời điểm.

## 🔥 Tension — "State-based mất lịch sử"

Sprint 10. Audit yêu cầu:

> **CFO ITO:** *"Opportunity-001 vừa Won với giá 500M. Tôi cần biết: giá ban đầu bao nhiêu? Ai thay đổi? Tại sao tăng từ 300M lên 500M? Timeline chính xác."*

> **Dev B:** *"Database chỉ lưu row hiện tại: `stage: Won, value: 500M, updated_at: 2026-06-28`. Tôi không biết value trước đó, không biết ai đổi, không biết lúc nào."*

> **CFO:** *"Thế có nghĩa là chúng ta không có audit trail? Compliance yêu cầu full traceability!"*

**Tuấn:**
> *"State-based = chỉ lưu snapshot cuối. Giống camera chỉ chụp 1 ảnh cuối ngày — không biết chuyện gì xảy ra trong ngày. **Event Sourcing** = camera quay liên tục — lưu MỌI SỰ KIỆN. Muốn biết state tại bất kỳ thời điểm → replay events đến thời điểm đó."*

> 💭 **Câu hỏi:** Khi nào cần biết "chuyện gì đã xảy ra" chứ không chỉ "trạng thái hiện tại"? Finance, legal, audit — mọi ngành cần accountability đều cần Event Sourcing.

## 🎓 Explanation — Event Sourcing

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Event Sourcing = lưu immutable event stream (append-only log), KHÔNG lưu mutable state. State = replay(events). Projection = read model từ events. Snapshot = optimization — cache state mỗi N events để avoid full replay."*

### State-based vs Event Sourcing

```
State-based (truyền thống):            Event Sourcing:
┌─────────────────────┐                ┌──────────────────────────────────────────┐
│ Opportunity row:    │                │ Event Store (Opportunity-001):           │
│   stage: "Won"      │                │                                          │
│   value: 500M       │                │ 1. OpportunityCreated  { value: 300M }   │
│   updated_at: 06/28 │                │ 2. Qualified           { by: "PM Loan" } │
│                     │                │ 3. ValueUpdated        { 300M → 450M }   │
│ → MẤT lịch sử      │                │ 4. ProposalSubmitted   { id: "P-001" }   │
│ → Không biết ai đổi │                │ 5. ValueUpdated        { 450M → 500M }   │
│ → Không audit được  │                │ 6. OpportunityWon      { contract: C-001 }│
└─────────────────────┘                │                                          │
                                       │ → FULL lịch sử                           │
                                       │ → Replay → state tại BẤT KỲ thời điểm   │
                                       │ → Immutable (append-only, never delete)   │
                                       └──────────────────────────────────────────┘
```

### Rebuild State từ Events

```typescript
class Opportunity {
  private stage: OpportunityStage
  private value: Money
  private accountId: AccountId

  // Replay events → rebuild state
  static fromEvents(events: DomainEvent[]): Opportunity {
    const opp = new Opportunity()
    for (const event of events) {
      opp.apply(event)
    }
    return opp
  }

  private apply(event: DomainEvent): void {
    switch (event.type) {
      case "OpportunityCreated":
        this.stage = OpportunityStage.Lead
        this.value = Money.from(event.data.value, event.data.currency)
        this.accountId = event.data.accountId
        break
      case "OpportunityQualified":
        this.stage = OpportunityStage.Qualified
        break
      case "ValueUpdated":
        this.value = Money.from(event.data.newValue, event.data.currency)
        break
      case "OpportunityWon":
        this.stage = OpportunityStage.Won
        break
    }
  }

  // State tại thời điểm bất kỳ
  static atPointInTime(events: DomainEvent[], until: Date): Opportunity {
    const filtered = events.filter(e => e.timestamp <= until)
    return Opportunity.fromEvents(filtered)
  }
}
```

### Projections — Read Models từ Events

```
Event Stream          →  Projection Handler  →  Read Model
──────────────────────────────────────────────────────────────
OpportunityCreated    →  ProjectionHandler   →  SalesPipelineView (kanban)
OpportunityQualified  →  ProjectionHandler   →  SalesPipelineView (move card)
ValueUpdated          →  ProjectionHandler   →  RevenueReportView (update total)
OpportunityWon        →  ProjectionHandler   →  WinRateAnalyticsView (recalc)

→ Cùng events, nhiều projections khác nhau
→ Thêm view mới? Replay ALL events → build view từ đầu!
→ CQRS + Event Sourcing = natural combo
```

### Apply cho ITO CRM

**Opportunity Event Stream:**
```
Timeline Opportunity-001:
  Jan 15: OpportunityCreated     { value: 300M, account: "TechCorp" }
  Feb 01: OpportunityQualified   { qualifiedBy: "PM Loan" }
  Feb 15: ValueUpdated           { 300M → 450M, reason: "scope expanded" }
  Mar 01: ProposalSubmitted      { proposalId: "P-001" }
  Mar 10: ValueUpdated           { 450M → 500M, reason: "premium pricing" }
  Mar 20: OpportunityWon         { contractId: "C-001", closedBy: "VP" }

→ CFO query: "State tại Feb 15?" → replay 1-3 → { stage: Qualified, value: 450M }
→ Audit: "Ai đổi value?" → events 3, 5 → PM Loan, VP
```

**Resource Event Stream:**
```
Timeline Resource R001 (Nguyễn Văn A):
  Jan 01: ResourceOnboarded      { name: "NVA", skills: ["Java"] }
  Jan 15: SkillAdded             { skill: "React" }
  Feb 01: ResourceAllocated      { project: "PRJ-X", percentage: 60% }
  Mar 01: ResourceAllocated      { project: "PRJ-Y", percentage: 30% }
  Apr 01: ResourceDeallocated    { project: "PRJ-X" }
  Apr 06: ResourceBenched        { reason: "no active project" }
```

### Apply cho Logistics

**Shipment Event Stream:**
```
Timeline Shipment SH-001:
  08:00: ShipmentCreated         { origin: HCM, dest: Đà Nẵng }
  08:30: ShipmentPickedUp        { driver: DRV-001, vehicle: VH-001 }
  12:00: InTransit               { location: Nha Trang }
  16:00: DeliveryAttempted       { status: "customer absent" }
  16:05: DeliveryFailed          { reason: "no one at address" }
  Next day 09:00: Rescheduled    { newTime: "10:00-12:00" }
  Next day 10:30: Delivered      { POD: "signed by Trần Văn B" }
```

### Snapshot — Performance Optimization

```
Problem: Resource R001 có 10,000 events → replay mỗi lần load = slow

Solution: Snapshot mỗi 100 events
  Event 1-100:   replay → Snapshot-1 (state at event 100)
  Event 101-200: replay from Snapshot-1 → Snapshot-2
  ...
  Load: Snapshot-99 + events 9901-10000 → current state
  → Replay 100 events thay vì 10,000
```

### ⚖️ Trade-offs — Khi nào Event Sourcing?

| Dùng Event Sourcing | KHÔNG dùng |
|---|---|
| Audit trail bắt buộc (finance, legal, compliance) | CRUD đơn giản, không cần lịch sử |
| Temporal queries ("state tại thời điểm X") | Data không có lifecycle |
| Domain naturally event-driven | Team chưa quen, complexity high |
| Cần nhiều projections từ cùng data | 1-2 read models đủ |
| **ITO:** Opportunity pipeline (audit), Resource history | **ITO:** Address management, system settings |

### Challenges

| Challenge | Giải pháp |
|---|---|
| Event schema evolution (v1 → v2) | **Upcasting**: transform event cũ khi read |
| Performance (replay 10K events) | **Snapshot**: cache state mỗi N events |
| Debugging khó hơn state-based | **Event log viewer**: tool hiển thị timeline |
| Team complexity | **Incremental adoption**: ES cho core domain only |

---

## 🏋️ Exercise — Thiết kế Event Sourcing

### Phần A: ITO — Resource Event Stream (15 phút)

```
1. ResourceOnboarded    { name, email, initialSkills }
2. _______________      { ... }
3. _______________      { ... }
4. _______________      { ... }
5. _______________      { ... }
6. _______________      { ... }
```

Viết `rebuildResource(events)`:
```typescript
function rebuildResource(events: DomainEvent[]): Resource {
  // replay logic...
}
```

### Phần B: Logistics — Shipment Event Stream (10 phút)

Liệt kê events + viết rebuild function.

### Phần C: Projection design (10 phút)

Từ Resource events, thiết kế 2 projections:
1. **UtilizationDashboardView** — fields: _______________
2. **BenchReportView** — fields: _______________

---

## 🪞 Reflect

1. **ES + CQRS = combo phổ biến — nhưng có PHẢI dùng chung?** → **Không!** ES có thể dùng không CQRS (1 model, replay events). CQRS có thể dùng không ES (state-based write + separate read model). Combo = mạnh nhất nhưng complex nhất. **Start with CQRS, add ES when audit needed.**

2. **Event schema thay đổi — events cũ xử lý thế nào?** → **Upcasting:** khi read event v1, transform → v2 format. Events trong store KHÔNG bao giờ sửa (immutable). Transformation xảy ra at read time. Giống DB migration nhưng cho events.

3. **"Mọi domain đều nên Event Sourcing" — đúng hay sai?** → **Sai!** ES thêm significant complexity. Chỉ dùng cho Core Domain cần audit/temporal/multi-projection. Generic/Supporting subdomain dùng state-based đủ tốt. **Complexity budget có hạn — spend wisely.**

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích Event Sourcing + sự khác biệt với state-based + snapshot optimization
- [ ] **Apply:** Thiết kế Event Stream cho Resource (≥5 events) + rebuild function
- [ ] **Analyze:** Thiết kế ≥2 projections — giải thích tại sao cùng events tạo views khác nhau

---

> 🔗 **Tiếp theo:** Events cho 1 aggregate OK. Nhưng khi business process **vượt qua 1 aggregate** — "Opportunity Won → tạo Contract → staff Resources" — cần 3 aggregates, nhiều bước, có thể fail giữa chừng. **Saga & Process Manager** sẽ trả lời: cách orchestrate long-running business processes + compensation khi bước nào đó fail.
