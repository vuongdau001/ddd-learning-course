---
type: overview
module: 8
title: "Advanced Patterns"
stage: 2
duration: "~1.5 tuần"
prerequisites: ["module-7"]
---

# Module 8: Advanced Patterns — CQRS, Event Sourcing, Saga

## Mục tiêu
Sau module này, người học có thể:
- Áp dụng CQRS để tách read/write models
- Hiểu Event Sourcing và khi nào nên dùng
- Thiết kế Saga/Process Manager cho long-running processes

## Prerequisites
Module 7 hoàn thành — đã biết Entity, VO, Aggregate, Repository.

## Lessons
| # | Tên | Thời lượng | Nội dung chính |
|:---:|---|---|---|
| 8.1 | [CQRS](./lesson-1-cqrs.md) | 35 phút | Command/Query separation, read models |
| 8.2 | [Event Sourcing](./lesson-2-event-sourcing.md) | 40 phút | Event store, projection, temporal queries |
| 8.3 | [Saga & Process Manager](./lesson-3-saga-and-process-manager.md) | 35 phút | Long-running processes, compensation |

## Deliverable
`practice/cqrs-model/` — CQRS design cho 1 Core Domain

## Assessment
[Quiz Module 8](./assessment.md) — 8 câu hỏi
