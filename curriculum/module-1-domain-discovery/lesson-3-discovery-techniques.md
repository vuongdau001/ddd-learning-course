---
type: lesson
module: 1
lesson: 3
title: "Kỹ thuật khám phá Domain"
duration: "40 phút"
prerequisites: ["module-1/lesson-2"]
narrative_phase: "khám phá"
migration_phase: "Pre-migration: Khảo sát hiện trạng"
business_invariant: "Mọi pain point phải được đo lường (tiền mất, thời gian trễ, % sai)"
---

# Lesson 1.3: Kỹ thuật khám phá Domain — "4 ống kính nhìn tổ chức"

## 📍 Context — Bạn đang ở đây

> Sau khi vẽ Value Chain cho ITO Corp (Lesson 1.2), bạn đã thấy được dòng chảy chính: Lead → Deal → Contract → Staffing → Delivery. Hà muốn Sales Pipeline trước, Tuấn muốn Resource Allocation trước, CFO muốn sửa invoice flow. Nhưng CTO **Minh** hỏi thẳng: *"Bạn nói Staffing là bottleneck. Chứng minh bằng số đi."*
>
> Bạn nhận ra: Value Chain cho bạn **xương sống**, nhưng chưa cho bạn **dữ liệu định lượng** để bảo vệ quyết định. Bạn cần thêm 3 kỹ thuật nữa để "quét" hết tổ chức — và quan trọng hơn, để **đo lường** mỗi pain point bằng con số.

## 🔥 Tension — "Value Chain không thấy hết"

Bạn đem value chain trình bày cho CTO Minh. Minh gật đầu nhưng chỉ ra 3 lỗ hổng:

> **Minh:** *"Anh vẽ được dòng chính, tốt. Nhưng tôi có 3 câu hỏi:*
> 1. *HR department không xuất hiện ở đâu cả — họ không làm gì à? Mỗi tháng họ onboard 20 người, offboard 10 người. Resource data phụ thuộc họ.*
> 2. *Mọi người đang dùng tool gì? Nếu Sales dùng Excel, Finance dùng SAP, PM dùng Jira — thì mỗi tool là 1 source of truth khác nhau. Data sai là vì đây.*
> 3. *Anh nói 'Staffing mất 3-5 ngày'. Nhưng 3 ngày hay 5 ngày? Mất bao nhiêu tiền? Nếu không có số, tôi không ký budget.*"

Minh đúng. Value Chain cho bạn **cái gì**, nhưng:
- **Không thấy** domain ẩn (HR, Knowledge Management)
- **Không thấy** tool landscape (fragmentation)
- **Không đo** được impact bằng tiền

## 🎓 Explanation — 4 kỹ thuật khám phá

### Từ Business đến Technical

**Business Invariant cần bảo vệ:**
> *"Mọi pain point phải được đo lường bằng số: tiền mất, thời gian trễ, % sai lệch. Nếu không đo được, không có cơ sở ưu tiên."*

| Kỹ thuật | Phát hiện gì | Analogy | Ở ITO, bạn hỏi ai? |
|---|---|---|---|
| Value Chain (đã học) | Dòng chảy chính | Sông chính | Hà, Tuấn, CFO |
| Organizational Analysis | Domain theo phòng ban | Bản đồ địa hình | CTO Minh (org chart) |
| System Inventory | Domain theo tool đang dùng | Bản đồ hạ tầng | IT Team + mỗi phòng ban |
| Pain Point Mining | Domain đau nhất + đo lường | Bản đồ nhiệt | Mọi vai trò |

### Kỹ thuật 2: Organizational Analysis

**Nguyên lý:** Cách tổ chức chia phòng ban thường phản ánh cách họ nghĩ về domain.

Bạn xin org chart từ CTO Minh:

```
ITO Corp — 2.000 nhân sự
CEO
├── Sales Department (Hà)     → Opportunity Management
├── Delivery Department        → Delivery Management
├── Resource Department (Tuấn) → Resource Management
├── Finance Department (CFO)   → Billing, Contract
├── HR Department              → HR ← KHÔNG THẤY TRÊN VALUE CHAIN!
└── IT Department              → Infrastructure (Generic)
```

**Phát hiện quan trọng:** HR không nằm trên value chain nhưng là **upstream** của Resource Management — mỗi tháng HR onboard 20 người, data đó phải sync vào Resource pool.

**⚠️ Cẩn thận — Phòng ban ≠ Domain:**
- 1 phòng ban cover 2 domain: Sales vừa quản lý Lead vừa quản lý Account
- 1 domain cần 2 phòng ban: Delivery cần cả PM + Dev
- 1 phòng ban không tương ứng domain: Admin, IT Support

### Kỹ thuật 3: System Inventory

**Nguyên lý:** *"Hôm nay bạn mở bao nhiêu app/tool để làm việc?"*

Bạn đi hỏi từng phòng ban. Kết quả gây sốc — CTO Minh gọi là "tool soup":

| Tool | Ai dùng | Dùng làm gì | Domain gợi ý | Vấn đề |
|---|---|---|---|---|
| **Excel** (12 files khác nhau!) | Sales | Track pipeline | Opportunity Mgmt | 12 nguồn sự thật! |
| **SAP** | Finance | Contract, billing | Contract, Billing | Không sync với Excel |
| **Jira** | PM, Dev | Sprint, task | Delivery Mgmt | Không có resource view |
| **Internal Tool** (cũ, 2015) | RM Tuấn | Allocation, skill | Resource Mgmt | Chạy chậm, data 15% sai |
| **Google Drive** | BD | Proposal templates | Proposal Mgmt | Không link với pipeline |
| **Email/Slack** | Tất cả | Trao đổi, quyết định | *(Knowledge gap)* | Quyết định mất sau 2 tuần |

**3 câu hỏi phát hiện vấn đề:**
1. Tool nào đang phục vụ >1 domain? → Dấu hiệu domain đang bị gộp
2. Domain nào chưa có tool? → Dấu hiệu domain bị bỏ quên
3. Tool nào 2 phòng ban dùng khác nhau? → Dấu hiệu cần tách Bounded Context (sẽ học ở M5)

> **Pattern:** Domain nào dùng "Excel" hoặc "giấy" = domain đang bị underserved = ưu tiên số hóa.

### Kỹ thuật 4: Pain Point Mining

**Nguyên lý:** *"Tuần vừa rồi, cái gì làm bạn khó chịu nhất? Mất bao nhiêu thời gian/tiền?"*

Đây là kỹ thuật mạnh nhất vì cho bạn **số liệu** mà CTO Minh yêu cầu:

| Vai trò | Pain | Domain | **Đo lường** |
|---|---|---|---|
| Hà (Sales) | "Excel không show pipeline, data cũ" | Opportunity | Mất 1 deal 2 tỷ/tháng vì trùng approach |
| PM | "Email tìm resource mất 3-5 ngày" | Resource | **~200 triệu/quý** do delay staffing |
| Tuấn (RM) | "Không biết ai rảnh tháng sau" | Resource | Utilization sai 15% → **~500 triệu/quý** over/under-allocate |
| CEO | "Hỏi utilization, ai trả lời khác nhau" | Resource + Glossary | Quyết định dựa trên data sai |
| CFO | "Reconciliation SAP vs Jira mệt" | Billing ↔ Delivery | **40 giờ/tháng** manual reconciliation |

→ **Đếm frequency:** Resource Mgmt xuất hiện **3 lần**, impact tổng **~700 triệu/quý** = Core Domain tiềm năng #1.

Bây giờ bạn có thể nói với CTO Minh:
> *"Staffing chậm trung bình 4 ngày (data từ 20 deals gần nhất). Impact: 200 triệu/quý do delay penalty. Resource utilization sai 15% gây over-allocate: 500 triệu/quý. Tổng: 700 triệu/quý. Đầu tư 2 tháng build Resource Management trước = ROI dương từ quý 2."*

### Tổng hợp: 4 kỹ thuật → Domain Map hoàn chỉnh

```
Value Chain:           [Lead → Opportunity → Contract → Staffing → Delivery]
+ Org Analysis:        + HR (upstream of Resource), Admin (out of scope), IT (generic)
+ System Inventory:    + Knowledge Gap (email/slack), 12-Excel problem
+ Pain Point Mining:   + Ưu tiên: Resource (~700tr/quý) > Opportunity (2 tỷ/năm) > Billing (40h/tháng)

= Domain Map hoàn chỉnh + biết domain nào quan trọng nhất + có SỐ LIỆU bảo vệ
```

### ⚖️ Trade-offs — Khảo sát sâu vs Khảo sát nhanh

| | Dùng cả 4 kỹ thuật | Chỉ dùng Value Chain |
|---|---|---|
| **Được** | Phát hiện domain ẩn, đo lường impact, thuyết phục C-level bằng số | Nhanh (1-2 ngày), đủ cho hệ thống nhỏ |
| **Mất** | Mất 1-2 tuần, cần access nhiều stakeholders, data có thể bias | Thiếu domain ẩn, không có số liệu, khó bảo vệ quyết định |
| **Khi nào** | Enterprise >50 người, nhiều department, cần budget lớn | Startup, 1-2 team, scope rõ ràng |

---

## 🏋️ Exercise — Áp dụng 4 kỹ thuật cho Logistics

### Phần A: System Inventory (15 phút)

Tưởng tượng bạn là consultant mới vào công ty vận tải 500 nhân sự. Điền bảng:

| Tool/System | Ai dùng? | Dùng làm gì? | Domain gợi ý | Vấn đề? |
|---|---|---|---|---|
| TMS | ? | ? | ? | ? |
| WMS | ? | ? | ? | ? |
| GPS Tracking | ? | ? | ? | ? |
| Excel | ? | ? | ? | ? |
| Giấy/SĐT | ? | ? | ? | ? |

Trả lời:
- Domain nào chưa có tool? _______________
- Tool nào cover >1 domain? _______________
- Domain nào đang dùng Excel/giấy = ưu tiên số hóa? _______________

### Phần B: Pain Point Mining — Có đo lường (15 phút)

Nghĩ từ góc nhìn từng vai trò. **Bạn PHẢI điền cột "Đo lường":**

| Vai trò | "Cái gì khó chịu nhất?" | Domain liên quan | **Đo lường (tiền/thời gian)** |
|---|---|---|---|
| Dispatcher (điều phối) | | | ? triệu/tháng hoặc ? giờ/ngày |
| Tài xế | | | |
| Warehouse Manager | | | |
| Sales/Pricing | | | |
| CFO | | | |

Domain nào xuất hiện nhiều nhất + impact lớn nhất? _______________

### Phần C: Tổng hợp Domain Map cho Logistics (10 phút)

Kết hợp Value Chain (Lesson 1.2) + System Inventory (Phần A) + Pain Point (Phần B):

```
Logistics Business
│
├── _______________  (Core — pain lớn nhất)
├── _______________
├── _______________
├── _______________
├── _______________
├── _______________  (Supporting)
├── _______________  (Generic)
└── _______________  (Domain bị thiếu?)
```

---

## 🪞 Reflect

1. **Kỹ thuật nào mạnh nhất khi thuyết phục C-level?** Gợi ý: CEO/CTO nói bằng tiền. Pain Point Mining với số liệu > Value Chain diagram đẹp.

2. **Nếu chỉ được 2 ngày khảo sát, bạn chọn kỹ thuật nào?** Trade-off: Value Chain (ngày 1) + Pain Point Mining (ngày 2) = đủ xương sống + ưu tiên. Org Analysis và System Inventory có thể bổ sung sau.

3. **Stakeholder nào bạn sẽ interview đầu tiên khi vào ITO Corp?** CTO Minh (tổng quan, org chart) hay Hà (pain point cụ thể)? Tại sao?

---

## ✅ Completion Checklist
- [ ] **Recall:** Giải thích 4 kỹ thuật Discovery, khi nào dùng kỹ thuật nào
- [ ] **Apply:** Hoàn thành System Inventory (≥5 tool) + Pain Point Mining (≥5 vai trò, **CÓ ĐO LƯỜNG**) cho Logistics
- [ ] **Analyze:** Tổng hợp Domain Map cho Logistics, bảo vệ domain nào là Core bằng **số liệu từ Pain Point Mining**

---

> 🔗 **Tiếp theo:** Bạn đã có Domain Map + số liệu impact. CTO Minh hỏi tiếp: *"OK, 8 domains. Budget chỉ đủ build 3 cái trong 6 tháng đầu. Domain nào build trước, domain nào outsource, domain nào mua?"* Bài tiếp — *Capability Decomposition* — sẽ dạy bạn cách phân rã từng domain thành **capabilities** để tìm ra domain nào thực sự cần custom build vs. có thể dùng off-the-shelf.
