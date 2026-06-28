---
type: overview
module: 7
title: "Tactical DDD"
stage: 2
duration: "~1.5 tuần"
prerequisites: ["module-6"]
---

# Module 7: Tactical DDD — Từ model nghiệp vụ đến code

## Mục tiêu
Sau module này, người học có thể:
- Phân biệt Entity, Value Object, Aggregate — và biết khi nào dùng cái nào
- Thiết kế Aggregate Root với consistency boundary rõ ràng
- Áp dụng Domain Service và Factory pattern đúng chỗ
- Thiết kế Repository interface tách biệt domain và persistence

## Prerequisites
Module 6 hoàn thành — đã có Knowledge Architecture.

## Lessons
| # | Tên | Thời lượng | Nội dung chính |
|:---:|---|---|---|
| 7.1 | [Entities & Value Objects](./lesson-1-entities-and-value-objects.md) | 35 phút | Identity vs Equality, khi nào dùng cái nào |
| 7.2 | [Aggregates & Roots](./lesson-2-aggregates-and-roots.md) | 40 phút | Consistency boundaries, transaction scope |
| 7.3 | [Domain Services & Factories](./lesson-3-domain-services-and-factories.md) | 30 phút | Logic không thuộc Entity nào |
| 7.4 | [Repository Pattern](./lesson-4-repository-pattern.md) | 30 phút | Persistence ignorance, interface design |

## Deliverable
`practice/tactical-model/` — Entity, VO, Aggregate cho Resource Management

## Assessment
[Quiz Module 7](./assessment.md) — 10 câu hỏi
