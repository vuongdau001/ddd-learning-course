---
type: lesson
module: 8
lesson: 2
title: "Event Sourcing"
duration: "40 phút"
prerequisites: ["module-8/lesson-1"]
---

# Lesson 8.2: Event Sourcing — Lưu "chuyện gì đã xảy ra"

## 🎓 Concept — "Đừng lưu trạng thái, hãy lưu lịch sử"

### Vấn đề: State-based storage mất thông tin

```
Truyền thống (State-based):
  Opportunity row:
    stage: "Won"
    value: 500,000
    updated_at: 2026-06-28

  → Không biết:
    - Lúc nào chuyển từ Lead → Qualify?
    - Ai thay đổi value từ 300K → 500K?
    - Opportunity bị reject rồi mở lại chưa?
```

### Event Sourcing = Lưu mọi sự kiện

```
Event Store cho Opportunity-001:

1. OpportunityCreated        { value: 300K, account: "ABC Corp" }      2026-01-15
2. OpportunityQualified      { qualifiedBy: "PM Loan" }                2026-02-01
3. ValueUpdated              { oldValue: 300K, newValue: 450K }        2026-02-15
4. ProposalSubmitted         { proposalId: "P-001" }                   2026-03-01
5. ValueUpdated              { oldValue: 450K, newValue: 500K }        2026-03-10
6. OpportunityWon            { contractId: "C-001", closedBy: "VP" }   2026-03-20

→ Biết CHÍNH XÁC mọi thứ đã xảy ra
→ Có thể "replay" từ đầu → rebuild state bất kỳ thời điểm nào
```

### Rebuild State từ Events

```typescript
function rebuildOpportunity(events: DomainEvent[]): Opportunity {
  let state = Opportunity.initial();
  
  for (const event of events) {
    switch (event.type) {
      case "OpportunityCreated":
        state.value = event.data.value;
        state.stage = "Lead";
        break;
      case "OpportunityQualified":
        state.stage = "Qualified";
        break;
      case "ValueUpdated":
        state.value = event.data.newValue;
        break;
      case "OpportunityWon":
        state.stage = "Won";
        state.contractId = event.data.contractId;
        break;
    }
  }
  return state;
}
```

### Projection — Read models từ Events

```
Event Stream → Projection → Read Model

Events:                          Projections:
OpportunityCreated          →   OpportunityListView (table)
OpportunityQualified        →   SalesPipelineView (kanban)
ValueUpdated                →   RevenueReportView (chart)
OpportunityWon              →   WinRateAnalyticsView (dashboard)

→ Cùng events, nhiều views khác nhau
→ Thêm view mới? Replay events → build view mới!
```

### Ví dụ — ITO CRM

**Opportunity Event Stream:**
```
OpportunityCreated → Qualified → ProposalSubmitted → Won
                                                    → Lost (alternative)
```

**Resource Event Stream:**
```
ResourceOnboarded → SkillAdded → Allocated(60%, ProjA) 
→ Allocated(30%, ProjB) → Deallocated(ProjA) → Benched
```

### Ví dụ — Logistics

**Shipment Event Stream:**
```
ShipmentCreated → PickedUp → InTransit → DeliveryAttempted 
→ DeliveryFailed(reason) → Rescheduled → Delivered(POD)
```

### Khi nào dùng Event Sourcing?

| Phù hợp | Không phù hợp |
|---|---|
| Cần audit trail (finance, legal) | CRUD đơn giản |
| Cần temporal queries ("state tại thời điểm X") | Data không có lifecycle |
| Domain naturally event-driven | Team chưa quen |
| Cần nhiều projections từ cùng data | Ít read models |

### Challenges

```
1. Event schema evolution: event v1 → v2 → cần migration?
2. Performance: replay 1 triệu events mỗi lần load?
   → Snapshot: lưu state mỗi N events
3. Complexity: team cần hiểu cả Event Sourcing + CQRS
4. Debugging: khó debug hơn state-based
```

---

## 🏋️ Exercise — Thiết kế Event Sourcing

### Phần A: ITO — Resource Event Stream (15 phút)

Liệt kê tất cả events trong lifecycle của Resource:

```
1. ResourceOnboarded { ... }
2. _______________   { ... }
3. _______________   { ... }
4. _______________   { ... }
5. _______________   { ... }
6. _______________   { ... }
```

Viết pseudo-code `rebuildResource(events)`:

```typescript
function rebuildResource(events: Event[]): Resource {
  // viết logic replay...
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

1. **Event Sourcing + CQRS là combo phổ biến — nhưng có PHẢI dùng chung không?** Gợi ý: Không! ES có thể dùng không CQRS (chỉ 1 model). CQRS có thể dùng không ES (state-based write + separate read).

2. **Nếu event schema thay đổi (thêm field), events cũ xử lý thế nào?** Gợi ý: Upcasting — transform event v1 → v2 khi read.

3. **"Mọi domain đều nên Event Sourcing" — đúng hay sai? Tại sao?**

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích Event Sourcing bằng ví dụ
- [ ] Thiết kế Event Stream cho Resource (≥5 events)
- [ ] Viết pseudo-code rebuild state
- [ ] Thiết kế ≥2 projections
- [ ] Trả lời ≥2/3 câu hỏi reflection
