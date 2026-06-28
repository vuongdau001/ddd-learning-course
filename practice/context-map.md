---
type: practice
module: 5
title: "Context Map"
case_study: "ITO CRM"
---

# Context Map — Bài làm Module 5

> Hướng dẫn: Sau Module 5, bạn sẽ vẽ Context Map cho hệ thống với các Bounded Contexts và integration patterns.

---

## Phần 1: Liệt kê Bounded Contexts

| # | Bounded Context | Thuộc Domain | Phân loại | Team owner |
|:---:|---|---|---|---|
| 1 | | | Core / Supporting / Generic | |
| 2 | | | | |
| 3 | | | | |
| 4 | | | | |
| 5 | | | | |

> Gợi ý: Không nhất thiết 1 Domain = 1 Bounded Context. Một Core Domain có thể tách thành 2+ contexts.

---

## Phần 2: Integration Matrix

Đánh dấu quan hệ giữa các contexts (U = Upstream, D = Downstream):

| | Context 1 | Context 2 | Context 3 | Context 4 | Context 5 |
|---|:---:|:---:|:---:|:---:|:---:|
| **Context 1** | — | | | | |
| **Context 2** | | — | | | |
| **Context 3** | | | — | | |
| **Context 4** | | | | — | |
| **Context 5** | | | | | — |

---

## Phần 3: Integration Patterns

| Upstream | Downstream | Pattern | Lý do chọn pattern |
|---|---|---|---|
| _Ví dụ: Sales_ | _Resource Mgmt_ | **Customer-Supplier** | _Sales quyết định khi nào cần resource_ |
| | | | |
| | | | |
| | | | |
| | | | |

**7 patterns tham khảo:**
1. **Shared Kernel** — Shared code, cả 2 cùng sửa
2. **Customer-Supplier** — Upstream deliver cho downstream, downstream có tiếng nói
3. **Conformist** — Downstream tuân theo upstream model
4. **Anti-Corruption Layer (ACL)** — Downstream bảo vệ model riêng
5. **Open Host Service (OHS)** — Upstream cung cấp public API
6. **Published Language** — Shared format (JSON schema, Protobuf)
7. **Separate Ways** — Không integrate, mỗi bên tự làm

---

## Phần 4: Context Map Diagram

Vẽ diagram (dùng text-based hoặc mermaid):

```
┌──────────────┐         ┌──────────────┐
│              │  U/D     │              │
│  Context A   │─────────▶│  Context B   │
│              │ Pattern  │              │
└──────────────┘         └──────────────┘
        │
        │ U/D
        ▼
┌──────────────┐
│              │
│  Context C   │
│              │
└──────────────┘
```

---

## Phần 5: Shared Kernel (nếu có)

| Shared Element | Dùng bởi Contexts | Loại (VO, Entity, Enum) |
|---|---|---|
| _Ví dụ: Money_ | _Finance, Sales, Delivery_ | _Value Object_ |
| | | |
| | | |

---

> Ghi chú: Đây là template. Tham khảo Lesson 5.1-5.3 để hiểu cách chọn pattern phù hợp.
