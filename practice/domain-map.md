# Domain Map — ITO CRM
## Deliverable: Module 1 ✅

> **Trạng thái:** ✅ Hoàn thành  
> **Người thực hiện:** Frankie (hỗ trợ bởi AI)  
> **Ngày hoàn thành:** 25/06/2026

---

## Bước 1: Value Chain Discovery

> **Kỹ thuật dùng:** Value Chain Interview — "Khi khách trả tiền, tiền đi qua những bước nào?"

```
Tiền vào từ đâu?
→ Partner/Referral từ Hàn Quốc & Nhật Bản + Marketing (website, events)

Từ nguồn đó, qua bao nhiêu bước để "ra tiền"?
→ Bước 1: Lead Generation — Tiếp nhận thông tin khách hàng tiềm năng
→ Bước 2: Qualification — Đánh giá: khách có phù hợp không? Có budget? Có timeline?
→ Bước 3: Meeting & Discovery — Gặp khách, hiểu scope, viết proposal
→ Bước 4: Proposal & Negotiation — Gửi đề xuất, đàm phán giá/scope/timeline
→ Bước 5: Contract Signing — Ký hợp đồng chính thức
→ Bước 6: Project Staffing — Tìm và phân bổ kỹ sư vào dự án
→ Bước 7: Development & Delivery — Triển khai, sprint, milestone
→ Bước 8: UAT & Go Live — Bàn giao sản phẩm
→ Bước 9: Maintenance & Support — Hỗ trợ sau go-live
→ Bước 10: Customer Success — Đảm bảo hài lòng → Upsell/Gia hạn

Bước nào hay bị nghẽn nhất?
→ Bước 6 (Project Staffing) — Thường mất 1-2 tuần để match đúng người,
  nhiều khi mất deal vì không có người phù hợp kịp thời.
→ Bước 2-3 (Qualification) — Sales dùng Excel, không có visibility, 
  không biết opportunity nào đang ở stage nào.

Sau khi ra tiền lần đầu, làm gì để khách quay lại?
→ Delivery tốt → Build trust → Khách giới thiệu thêm scope/team mới
→ Hiện tại KHÔNG có quy trình Customer Success chính thức
→ Phụ thuộc vào quan hệ cá nhân của PM/Sales, không có hệ thống track
```

### 💡 Giải thích Bước 1

**Value Chain** (chuỗi giá trị) là toàn bộ hành trình từ khi bạn **tiếp cận** khách đến khi **nhận được tiền** và **giữ chân khách quay lại**. Trong DDD, mỗi bước lớn trong chuỗi này thường tương ứng với **ít nhất 1 domain**.

Quan sát quan trọng:
- **Bước 6 (Staffing) bị nghẽn nhất** → Resource Management là domain đau nhất → cần ưu tiên
- **Không có Customer Success** → đây là "gap domain" — doanh nghiệp cần nhưng chưa có gì hỗ trợ
- **Excel ở Bước 2-3** → Opportunity Management đang được xử lý bằng công cụ không phù hợp

---

## Bước 2: System Inventory

> **Kỹ thuật dùng:** Liệt kê mọi tool → mỗi tool gợi ý ≥1 domain

| Tool/System | Ai dùng? | Dùng làm gì? | Domain gợi ý |
|---|---|---|---|
| **Excel** (Sales Pipeline) | Sales team, BD | Track leads, pipeline stages, forecast | Lead Mgmt, Opportunity Mgmt |
| **SAP** (ERP) | Finance, Legal | Contract quản lý, xuất hóa đơn, billing | Contract Mgmt, Billing |
| **Jira** | PM, Dev team | Track sprint, task, bug | Delivery Mgmt |
| **Internal Tool** (tự phát triển) | Resource Manager | Xem allocation, skill matrix | Resource Mgmt |
| **Email/Chat** (Slack, Teams) | Tất cả | Trao đổi, quyết định, approval | *(Không có domain riêng — nhưng tri thức bị mất ở đây)* |
| **Google Drive/SharePoint** | BD, Pre-sales | Lưu proposal, SOW template | Proposal Mgmt |
| **HRIS** (nếu có) | HR | Quản lý nhân sự, lương, nghỉ phép | HR (ngoài scope CRM) |

**Câu hỏi kiểm tra:**

```
Tool nào đang phục vụ >1 domain?
→ Excel: vừa track Lead, vừa track Opportunity, vừa forecast → 3 concerns trong 1 file
→ Jira: vừa track task (Delivery), vừa bị một số PM dùng để track resource allocation
  (Resource) → 2 domain trộn lẫn

Domain nào chưa có tool hỗ trợ?
→ Customer Success: KHÔNG có tool nào. Hoàn toàn dựa vào cảm nhận cá nhân.
→ Knowledge Management: Tài liệu nằm rải rác trong Drive/Email, không có cấu trúc.
→ Proposal Management: Template nằm trong Drive nhưng không có workflow quản lý version.
```

### 💡 Giải thích Bước 2

**System Inventory** giúp bạn nhìn thấy 3 điều:

1. **Domain nào đang bị "nhồi" vào tool sai** — Excel không phải CRM, nhưng Sales đang dùng nó như CRM. Đây là dấu hiệu domain quan trọng nhưng bị đầu tư thiếu.

2. **Domain nào đang bị 2 tool cover** — Khi Jira vừa track delivery vừa track resource, nghĩa là 2 domain (Delivery + Resource) đang bị trộn lẫn → vi phạm Bounded Context ngay từ đầu.

3. **Domain nào hoàn toàn invisible** — Customer Success không có tool nào → công ty đang "bay mù" về mức độ hài lòng của khách hàng.

---

## Bước 3: Pain Point Mining

> **Kỹ thuật dùng:** "Tuần vừa rồi, cái gì làm bạn khó chịu nhất?"

| Vai trò | Pain Point | Domain liên quan |
|---|---|---|
| **Sales Manager** | "Không biết pipeline đang ở đâu. Mở Excel thì data cũ, không ai update. Không biết deal nào hot deal nào cold." | **Opportunity Mgmt** |
| **Sales Manager** | "Muốn biết win rate theo industry/technology nhưng Excel không tính được. Không có data để improve." | **Opportunity Mgmt** (analytics) |
| **PM** | "Khi nhận dự án mới, phải email/chat hỏi Resource Manager ai rảnh. Mất 3-5 ngày chỉ để tìm người." | **Resource Mgmt** |
| **PM** | "Khách hỏi 'khi nào team ready?' mà tôi không trả lời được vì phụ thuộc Resource Manager." | **Resource Mgmt ↔ Delivery Mgmt** (cross-domain) |
| **Resource Manager** | "Internal Tool show allocation nhưng không real-time. PM đã release người rồi mà tool chưa update." | **Resource Mgmt** (data freshness) |
| **Resource Manager** | "Không biết trước 2-3 tháng sẽ cần gì. Khi cần thì đã muộn — phải scramble hoặc mất deal." | **Resource Mgmt** (forecast) |
| **CEO/COO** | "Utilization rate bao nhiêu? Ai cũng trả lời khác nhau. HR nói 95%, Finance nói 78%." | **Resource Mgmt + Glossary problem** |
| **Finance** | "SAP có contract data nhưng PM dùng Jira track khác. Reconciliation cuối tháng rất mệt." | **Delivery ↔ Billing** (integration gap) |

### 💡 Giải thích Bước 3

**Pain Point Mining** là kỹ thuật mạnh nhất vì nó cho bạn 2 thông tin cùng lúc:

1. **Domain nào cần ưu tiên** — Domain nào xuất hiện nhiều nhất trong bảng pain point → đó là nơi đang tạo friction lớn nhất cho doanh nghiệp.

   Đếm: Resource Mgmt xuất hiện **4 lần**, Opportunity Mgmt **2 lần** → Đây là 2 Core Domain đau nhất.

2. **Ranh giới domain ở đâu** — Khi pain point nằm ở **giao điểm 2 domain** (PM hỏi Resource Manager → Resource ↔ Delivery), đó là nơi cần thiết kế **integration point** (API, event, shared data) cẩn thận nhất.

3. **Glossary problem** — CEO hỏi "Utilization bao nhiêu?" mà ai cũng trả lời khác → đây là **vấn đề ngôn ngữ**, sẽ giải quyết ở Module 2 (Ubiquitous Language).

---

## Bước 4: Domain Map (tổng hợp từ Bước 1-3)

> Mỗi domain dưới đây được extract từ ít nhất 1 trong 3 kỹ thuật trên.

```
ITO Business
│
├── Lead Management              ← Value Chain bước 1-2, Excel pain
├── Opportunity Management       ← Value Chain bước 2-4, Sales pain x2
├── Proposal Management          ← Value Chain bước 3-4, Google Drive
├── Contract Management          ← Value Chain bước 5, SAP
├── Resource Management          ← Value Chain bước 6, Pain x4 (đau nhất)
├── Delivery Management          ← Value Chain bước 7-8, Jira
├── Customer Success             ← Value Chain bước 9-10, GAP (không có tool)
├── Billing                      ← SAP, Finance pain
└── Knowledge Management         ← GAP: tài liệu rải rác, AI không đọc được
```

### 💡 Giải thích Bước 4

Nhìn lại — 9 domain này **không phải do ai "nghĩ ra"**. Chúng được **phát hiện** từ 3 kỹ thuật:

| Domain | Phát hiện từ kỹ thuật nào? |
|---|---|
| Lead Management | Value Chain (bước 1-2) + System Inventory (Excel) |
| Opportunity Management | Value Chain (bước 2-4) + Pain Point (Sales x2) |
| Proposal Management | Value Chain (bước 3) + System Inventory (Google Drive) |
| Contract Management | Value Chain (bước 5) + System Inventory (SAP) |
| **Resource Management** | Value Chain (bước 6) + **Pain Point (x4)** + System Inventory (Internal Tool) |
| Delivery Management | Value Chain (bước 7-8) + System Inventory (Jira) |
| **Customer Success** | Value Chain (bước 9-10) + **System Inventory (GAP — không có tool)** |
| Billing | System Inventory (SAP) + Pain Point (Finance reconciliation) |
| Knowledge Management | System Inventory (GAP) + Pain Point (CEO: thông tin rải rác) |

**Resource Management** xuất hiện ở **cả 3 kỹ thuật** với pain cao nhất → Đây gần chắc chắn là Core Domain #1.

---

## Bước 5: Capability Decomposition

### Domain: Resource Management
```
Resource Management
├── Skill Inventory          — Ai biết gì? Cấp độ bao nhiêu? Đang update?
│                              [Pain: "không biết ai giỏi React Native"]
│
├── Capacity Planning        — Tháng sau còn bao nhiêu người rảnh?
│                              [Pain: "không biết trước 2-3 tháng"]
│
├── Project Staffing         — Project A cần 3 Senior Java → ai match?
│                              [Pain: "mất 3-5 ngày email qua lại"]
│
├── Bench Management         — Ai đang không có dự án? Bao lâu rồi?
│                              [Pain: "Resource Manager check thủ công"]
│
├── Utilization Tracking     — Billable hours / Total hours = ?
│                              [Pain: "HR, Finance, RM trả lời khác nhau"]
│
└── Workforce Forecast       — 6 tháng nữa, market cần gì? Nên tuyển ai?
                               [Pain: "reactive, không proactive"]
```

### 💡 Giải thích: Tại sao tách thành Capabilities?

**Capability** = "khả năng nghiệp vụ" — là đơn vị nhỏ hơn Domain, giúp bạn:

1. **Ưu tiên trong từng domain** — Không cần build cả 6 capabilities cùng lúc. Có thể bắt đầu từ "Project Staffing" (đau nhất) rồi mở rộng sau.

2. **Scope cho MVP** — Phase 1 của Resource Management có thể chỉ cần: Skill Inventory + Project Staffing + Bench Management. Capacity Planning và Forecast làm sau.

3. **Map vào feature** — Mỗi capability thường = 1-3 features trong system mới. Khi viết requirement, bạn viết theo capability, không viết theo "màn hình".

---

### Domain: Opportunity Management
```
Opportunity Management
├── Lead Qualification       — Lead vào → đánh giá: có tiềm năng không?
│                              Tiêu chí: budget, timeline, tech fit, authority
│                              [Pain: "không có tiêu chí rõ ràng, mỗi Sales đánh giá khác"]
│
├── Pipeline Tracking        — Opportunity đang ở stage nào? Bao lâu rồi?
│                              Stages: Qualification → Meeting → Proposal → Negotiation → Won/Lost
│                              [Pain: "Excel không show pipeline view"]
│
├── Account Management       — Quản lý thông tin khách hàng (company profile, contacts, history)
│                              [Pain: "contact info nằm trong đầu Sales, không có hệ thống"]
│
├── Revenue Forecast         — Tháng sau/quý sau dự kiến bao nhiêu revenue?
│                              = Σ(Opportunity value × Win probability)
│                              [Pain: "CEO hỏi forecast, Sales ước lượng bằng cảm tính"]
│
├── Win/Loss Analysis        — Tại sao thắng? Tại sao thua? Pattern gì?
│                              [Pain: "không ai track lý do win/loss → không cải thiện"]
│
└── Proposal Coordination    — Phối hợp với Pre-sales/Tech Lead viết proposal
                               [Pain: "proposal gửi muộn vì waiting for tech input"]
```

### 💡 Giải thích: Opportunity Management

Đây là domain mà **doanh thu trực tiếp phụ thuộc vào**. Mỗi capability ở đây đều liên quan đến câu hỏi: *"Làm sao để win nhiều hơn, nhanh hơn?"*

Chú ý:
- **Lead Qualification** có thể tách thành domain riêng (Lead Management) nếu lượng leads đủ lớn. Hiện tại gộp vì ITO chủ yếu từ referral, lượng lead không quá lớn.
- **Proposal Coordination** nằm ở ranh giới với Proposal Management. Ở đây nó thuộc Opportunity vì concern chính là "đẩy deal về phía trước", không phải "quản lý nội dung proposal".

---

## Bước 6: Self-check

**1. Bạn tìm ra bao nhiêu domain?**
```
9 domain — Nằm trong khoảng hợp lý (7-12 cho một doanh nghiệp vừa).
Nếu < 5: đang gộp quá nhiều (ví dụ: gộp Resource + Delivery = sai).
Nếu > 12: đang tách quá nhỏ (ví dụ: tách "Login" thành domain riêng = overkill).
```

**2. Domain nào bạn chưa chắc chắn có nên tách riêng?**
```
Lead Management vs Opportunity Management:
- Hiện tại ITO nguồn lead chủ yếu từ referral/partner → lượng ít
- Có thể gộp Lead vào Opportunity Management
- Nếu tương lai có marketing inbound mạnh (content, ads) → tách ra

Proposal Management:
- Hiện tại proposal nằm trong Google Drive, workflow đơn giản
- Có thể gộp vào Opportunity Management như "Proposal Coordination" capability
- Nếu proposal phức tạp (nhiều version, approval flow, template engine) → tách ra

Knowledge Management:
- Chưa rõ scope — có thể chỉ là "tổ chức tài liệu" (generic)
- Hoặc có thể là "AI-readable knowledge base" (core nếu AI-first strategy)
- Cần CEO/CTO quyết định: công ty có muốn đầu tư AI-first không?
```

**3. Có domain nào xuất hiện trong Pain Point nhưng không có trong System Inventory?**
```
Customer Success:
- Pain: PM nói "không biết khách có hài lòng không cho đến khi họ cancel"
- System: KHÔNG có tool nào track satisfaction, renewal signal, escalation
- → Đây là GAP nghiêm trọng nhất: doanh nghiệp cần nhưng hoàn toàn invisible

Knowledge Management:
- Pain: CEO nói "thông tin nằm rải rác, người mới mất 3 tháng mới hiểu hệ thống"
- System: Google Drive/Email nhưng không structured → coi như không có
- → GAP: tri thức đang bị mất mỗi khi có người nghỉ việc
```

---

## ✅ Checklist hoàn thành Module 1

- [x] Value Chain tự vẽ — 10 bước, xác định bottleneck
- [x] System Inventory — 7 tools, tìm ra overlap và gap
- [x] Pain Point của ≥3 vai trò — 8 pain points từ 5 vai trò
- [x] Domain Map có ≥7 domain — 9 domain
- [x] Capability Decomposition cho ≥2 domain — Resource (6 caps) + Opportunity (6 caps)
- [x] Self-check trả lời đủ 3 câu

**→ ✅ Module 1 hoàn thành — Sẵn sàng sang Module 2: Ubiquitous Language**

---

## 📝 Ghi chú cho các Module tiếp theo

### Những "Hot Spots" cần mang theo sang Module 2 (Glossary)
- ⚠️ "Utilization" — HR, Finance, RM đo khác nhau → **phải thống nhất**
- ⚠️ "Resource" — 5 nghĩa khác nhau → **phải chốt 1 definition**
- ⚠️ "Customer" — Sales gọi Account, PM gọi Customer, Finance gọi Billing Entity
- ⚠️ "Allocation" — đo bằng %, Man-Day, hay Giờ? → **phải chọn 1**

### Những insight mang theo sang Module 3 (Strategic Design)
- Resource Mgmt: pain x4, xuất hiện ở cả 3 kỹ thuật → **rất có khả năng là Core #1**
- Customer Success: GAP hoàn toàn → **Core tiềm năng nhưng cần CEO confirm**
- Billing: SAP đang handle OK → **Supporting hoặc Generic, đừng tự build**
