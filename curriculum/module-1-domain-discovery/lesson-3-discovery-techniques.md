---
type: lesson
module: 1
lesson: 3
title: "Kỹ thuật khám phá Domain"
duration: "40 phút"
prerequisites: ["module-1/lesson-2"]
---

# Lesson 1.3: Kỹ thuật khám phá Domain

## 🎓 Concept — "4 ống kính nhìn tổ chức"

### Tại sao cần nhiều kỹ thuật?

Value Chain (Lesson 1.2) cho bạn **xương sống** — dòng chảy chính. Nhưng tổ chức không chỉ có dòng chính. Có những domain **ẩn** mà value chain không thấy.

```
Value Chain thấy:       Lead → Opportunity → Contract → Delivery
Value Chain KHÔNG thấy: Knowledge Management, Customer Success, HR
```

Cần thêm 3 kỹ thuật nữa để "quét" hết:

| Kỹ thuật | Phát hiện gì | Analogy |
|---|---|---|
| Value Chain | Dòng chảy chính | Sông chính |
| Organizational Analysis | Domain theo phòng ban | Bản đồ địa hình |
| System Inventory | Domain theo tool đang dùng | Bản đồ hạ tầng |
| Pain Point Mining | Domain đau nhất, domain bị thiếu | Bản đồ nhiệt |

### Kỹ thuật 2: Organizational Analysis

**Nguyên lý:** Cách tổ chức chia phòng ban thường phản ánh cách họ nghĩ về domain.

```
Nhìn sơ đồ tổ chức:
CEO
├── Sales Department        → Opportunity Management
├── Delivery Department     → Delivery Management
├── Resource Department     → Resource Management
├── Finance Department      → Billing, Contract
├── HR Department           → HR (ngoài scope?)
└── IT Department           → Infrastructure (Generic)
```

**Cẩn thận:** Không phải 1 phòng ban = 1 domain. Có khi:
- 1 phòng ban cover 2 domain (Sales vừa quản lý Lead vừa quản lý Account)
- 1 domain cần 2 phòng ban (Delivery cần cả PM + Dev)
- 1 phòng ban không tương ứng domain nào (Admin, IT Support)

### Kỹ thuật 3: System Inventory

**Nguyên lý:** Liệt kê mọi tool/hệ thống → mỗi tool gợi ý ≥1 domain.

**Câu hỏi:** "Hôm nay bạn mở bao nhiêu app/tool để làm việc?"

#### Ví dụ — ITO

| Tool | Ai dùng | Dùng làm gì | Domain gợi ý |
|---|---|---|---|
| Excel | Sales | Track pipeline | Opportunity Mgmt |
| SAP | Finance | Contract, billing | Contract, Billing |
| Jira | PM, Dev | Sprint, task | Delivery Mgmt |
| Internal Tool | RM | Allocation, skill | Resource Mgmt |
| Google Drive | BD | Proposal templates | Proposal Mgmt |
| Email/Slack | Tất cả | Trao đổi, quyết định | *(Knowledge gap)* |

**3 câu hỏi mở rộng:**
1. Tool nào đang phục vụ >1 domain? → Dấu hiệu domain đang bị gộp
2. Domain nào chưa có tool? → Dấu hiệu domain bị bỏ quên
3. Tool nào 2 phòng ban dùng khác nhau? → Dấu hiệu Bounded Context

#### Ví dụ — Logistics

| Tool | Ai dùng | Dùng làm gì | Domain gợi ý |
|---|---|---|---|
| TMS (Transport Mgmt System) | Dispatcher | Phân tuyến, phân xe | Route, Fleet |
| WMS (Warehouse Mgmt System) | Warehouse | Nhập/xuất kho | Warehouse |
| GPS Tracking | Operations | Theo dõi xe | Tracking |
| Excel | Accounting | Invoice, payment | Billing |
| Giấy/SĐT | Tài xế | Xác nhận giao hàng | POD *(gap — chưa số hóa)* |

→ **Pattern:** Domain nào dùng "Excel" hoặc "giấy" = domain đang bị underserved = cơ hội số hóa.

### Kỹ thuật 4: Pain Point Mining

**Nguyên lý:** Hỏi mỗi vai trò: *"Tuần vừa rồi, cái gì làm bạn khó chịu nhất?"*

**Tại sao mạnh nhất?**
- Value Chain cho bạn domain
- Pain Point cho bạn **ưu tiên** — domain nào đau nhất cần fix trước

#### Ví dụ — ITO

| Vai trò | Pain | Domain liên quan |
|---|---|---|
| Sales | "Excel không show pipeline, data cũ" | Opportunity Mgmt |
| PM | "Mất 3-5 ngày email tìm resource" | Resource Mgmt |
| Resource Mgr | "Không biết trước ai sẽ rảnh tháng sau" | Resource Mgmt |
| CEO | "Hỏi utilization, ai cũng trả lời khác nhau" | Resource Mgmt + Glossary |
| Finance | "Reconciliation SAP vs Jira rất mệt" | Billing ↔ Delivery |

→ Đếm: Resource Mgmt xuất hiện **3 lần** = domain đau nhất = Core Domain tiềm năng #1.

#### Ví dụ — Logistics

| Vai trò | Pain | Domain liên quan |
|---|---|---|
| Dispatcher | "Phân tuyến thủ công, mất 2 giờ/ngày" | Route Optimization |
| Tài xế | "Khách không ký POD kịp, phải quay lại" | POD |
| Warehouse | "Không biết xe đến lúc nào để chuẩn bị hàng" | Tracking ↔ Warehouse |
| Sales | "Không báo giá nhanh được vì phải check capacity" | Pricing ↔ Fleet |
| CFO | "Chi phí nhiên liệu tăng mà không biết tối ưu ở đâu" | Fleet + Route |

→ Route Optimization = Core Domain tiềm năng cho Logistics.

### Tổng hợp: 4 kỹ thuật → Domain Map

```
Value Chain:           [Lead → Opportunity → Contract → Delivery]
+ Org Analysis:        + HR, Admin, IT (ngoài scope)
+ System Inventory:    + Knowledge Gap (email/slack)
+ Pain Point Mining:   + Ưu tiên: Resource > Opportunity > Delivery

= Domain Map hoàn chỉnh + biết domain nào quan trọng nhất
```

---

## 🏋️ Exercise — System Inventory + Pain Point cho Logistics

### Phần A: System Inventory (15 phút)
Tưởng tượng bạn là consultant mới vào công ty vận tải. Điền bảng:

| Tool/System | Ai dùng? | Dùng làm gì? | Domain gợi ý |
|---|---|---|---|
| | | | |
| | | | |
| | | | |
| | | | |
| | | | |

Trả lời:
- Domain nào chưa có tool? _______________
- Tool nào cover >1 domain? _______________

### Phần B: Pain Point Mining (15 phút)
Nghĩ từ góc nhìn từng vai trò trong công ty logistics:

| Vai trò | "Cái gì khó chịu nhất?" | Domain liên quan |
|---|---|---|
| Dispatcher (điều phối) | | |
| Tài xế | | |
| Warehouse Manager | | |
| Sales/Pricing | | |
| CFO | | |

Domain nào xuất hiện nhiều nhất? _______________

### Phần C: Tổng hợp Domain Map cho Logistics (10 phút)

Từ Value Chain (Lesson 1.2) + System Inventory (Phần A) + Pain Point (Phần B):

```
Logistics Business
│
├── _______________
├── _______________
├── _______________
├── _______________
├── _______________
├── _______________
├── _______________
└── _______________
```

---

## 🪞 Reflect

1. **Kỹ thuật nào bạn thấy hiệu quả nhất? Tại sao?** So sánh: Value Chain cho "cái gì", Pain Point cho "cái gì đau", System Inventory cho "cái gì đang thiếu".

2. **Nếu chỉ được dùng 1 kỹ thuật, bạn chọn kỹ thuật nào?** Tại sao?

3. **Stakeholder nào bạn sẽ interview đầu tiên khi vào công ty mới?** CEO? PM? End user? Tại sao người đó?

---

## ✅ Hoàn thành lesson khi
- [ ] Giải thích được 4 kỹ thuật Discovery và khi nào dùng
- [ ] Hoàn thành System Inventory cho Logistics (≥5 tools)
- [ ] Hoàn thành Pain Point cho Logistics (≥5 vai trò)
- [ ] Tổng hợp Domain Map cho Logistics (≥6 domain)
- [ ] Trả lời ≥2/3 câu hỏi reflection
