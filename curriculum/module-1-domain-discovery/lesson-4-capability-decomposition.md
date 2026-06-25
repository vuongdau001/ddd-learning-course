---
type: lesson
module: 1
lesson: 4
title: "Capability Decomposition"
duration: "30 phút"
prerequisites: ["module-1/lesson-3"]
---

# Lesson 1.4: Capability Decomposition

## 🎓 Concept — "Một domain không phải là một cục"

### Vấn đề

Bạn đã có Domain Map — VD: "Resource Management". Nhưng nếu ai đó hỏi *"Resource Management gồm những gì?"* và bạn chỉ trả lời *"quản lý resource"* — thì chưa đủ.

**Capability Decomposition** = tách 1 domain thành các **khả năng nghiệp vụ** cụ thể.

### Domain vs Capability

| Cấp | Ví dụ | Mô tả |
|---|---|---|
| **Domain** | Resource Management | Phạm vi lớn |
| **Capability** | Skill Inventory | 1 khả năng cụ thể trong domain |
| **Feature** | "Tìm resource theo skill" | 1 chức năng phần mềm |

```
Domain → Capability → Feature → User Story → Task
(lớn)                                        (nhỏ)
```

### Tại sao cần tách?

1. **Ưu tiên trong domain:** Không cần build cả domain cùng lúc. Chọn 2-3 capabilities đau nhất cho MVP.

2. **Scope rõ ràng:** Khi PM hỏi "Phase 1 gồm gì?" — trả lời bằng capabilities, không phải features.

3. **AI hiểu tốt hơn:** Khi bạn prompt AI: *"Generate API for Skill Inventory capability"* rõ ràng hơn *"Generate API for Resource Management"*.

### Cách tách: Hỏi "domain này CÓ THỂ LÀM GÌ?"

Với mỗi domain, hỏi: **"Cái gì có thể xảy ra trong domain này?"**

Mỗi câu trả lời dạng động từ + danh từ = 1 capability:

| Câu trả lời | Capability |
|---|---|
| "Tìm ai có skill Java Senior" | **Skill Inventory** |
| "Biết tháng sau còn bao nhiêu người rảnh" | **Capacity Planning** |
| "Match người vào dự án" | **Project Staffing** |
| "Theo dõi ai đang bench" | **Bench Management** |
| "Đo billable hours" | **Utilization Tracking** |
| "Dự đoán 6 tháng tới cần gì" | **Workforce Forecast** |

### Ví dụ — ITO: Resource Management

```
Resource Management
├── Skill Inventory          "Ai biết gì? Cấp độ bao nhiêu?"
├── Capacity Planning        "Tháng sau còn mấy người rảnh?"
├── Project Staffing         "Project A cần 3 Java Senior → ai match?"
├── Bench Management         "Ai đang không có dự án? Bao lâu rồi?"
├── Utilization Tracking     "Billable hours / Available hours = ?"
└── Workforce Forecast       "6 tháng nữa thị trường cần skill gì?"
```

**MVP (Phase 1):** Skill Inventory + Project Staffing + Bench Management
**Phase 2:** Utilization Tracking + Capacity Planning
**Phase 3:** Workforce Forecast (cần data tích lũy)

### Ví dụ — Logistics: Fleet Management

```
Fleet Management
├── Vehicle Registry         "Có bao nhiêu xe? Loại gì? Tải trọng?"
├── Driver Assignment        "Tài xế nào lái xe nào?"
├── Maintenance Scheduling   "Xe nào cần bảo trì? Khi nào?"
├── Fuel Management          "Mỗi xe tiêu bao nhiêu nhiên liệu?"
├── Compliance Tracking      "Xe nào hết đăng kiểm? Giấy phép?"
└── Fleet Optimization       "Cần thêm/bớt xe loại nào?"
```

**MVP:** Vehicle Registry + Driver Assignment + Maintenance Scheduling
**Phase 2:** Fuel Management + Compliance Tracking
**Phase 3:** Fleet Optimization (cần AI/data)

### Quy tắc ngón tay cái

- **3-7 capabilities mỗi domain** — Ít hơn 3: domain quá nhỏ (có thể gộp). Nhiều hơn 7: domain quá lớn (có thể tách).
- **Mỗi capability có verb** — "Skill Inventory" = khả năng quản lý skill. Nếu không tìm ra verb → chưa phải capability.
- **Capabilities là independent** — Có thể build Skill Inventory mà không cần Workforce Forecast. Nếu 2 capabilities luôn đi cùng → chúng là 1.

---

## 🏋️ Exercise — Tách Capabilities

### Phần A: Opportunity Management — ITO (10 phút)

Tách domain Opportunity Management thành capabilities:

```
Opportunity Management
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
└── _______________         "_______________"
```

MVP gồm capabilities nào? _______________

<details>
<summary>💡 Gợi ý: Nghĩ về lifecycle của Opportunity</summary>

Từ Lead vào → đánh giá → theo dõi → gửi proposal → thắng/thua → phân tích.
Mỗi bước = 1 capability tiềm năng.
</details>

### Phần B: Route Optimization — Logistics (10 phút)

Tách domain Route Optimization:

```
Route Optimization
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
├── _______________         "_______________"
└── _______________         "_______________"
```

MVP gồm capabilities nào? _______________

### Phần C: Self-check (5 phút)

| Kiểm tra | Phần A | Phần B |
|---|:---:|:---:|
| Có 3-7 capabilities? | ☐ | ☐ |
| Mỗi capability có verb ngầm? | ☐ | ☐ |
| Capabilities independent? | ☐ | ☐ |
| Chọn được MVP (2-3 caps)? | ☐ | ☐ |

---

## 🪞 Reflect

1. **Capability nào trong Resource Management bạn cho là khó build nhất? Tại sao?** Gợi ý: nghĩ về data dependency — capability nào cần data từ capability khác?

2. **Trong Logistics, "Real-time Tracking" là domain riêng hay capability của Transportation Management?** Tiêu chí nào giúp bạn quyết định?

3. **Khi nào thì 1 Capability nên "lên chức" thành Domain riêng?** Gợi ý: nghĩ về quy mô team và tốc độ thay đổi.

---

## ✅ Hoàn thành lesson khi
- [ ] Tách Opportunity Management thành ≥4 capabilities
- [ ] Tách Route Optimization thành ≥4 capabilities
- [ ] Chọn MVP cho mỗi domain
- [ ] Self-check đạt ≥3/4 tiêu chí cho cả 2 phần
- [ ] Trả lời ≥2/3 câu hỏi reflection

---

## 🏁 Hoàn thành Module 1

Bạn đã học:
- **Lesson 1.1:** Phân biệt Domain vs System Component
- **Lesson 1.2:** Value Chain Analysis — đi theo dòng tiền
- **Lesson 1.3:** 4 kỹ thuật Discovery — System Inventory, Pain Point Mining
- **Lesson 1.4:** Capability Decomposition — tách domain thành capabilities

**Deliverable:** `practice/domain-map.md` — Domain Map + Capability cho ≥2 domain

→ Tiếp: **Quiz Module 1** rồi sang **Module 2: Ubiquitous Language**
