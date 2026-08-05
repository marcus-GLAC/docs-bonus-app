# Hướng Dẫn Nhập Field Quan Trọng — Ticket JSM, Invoice, PO

> **Tài liệu con của:** [Hướng Dẫn Sử Dụng Hệ Thống Quản Lý Thưởng](index.html) — xem bản tóm tắt tại Mục 4.11 trong tài liệu chính.
> **Đối tượng:** Người tạo ticket Jira/JSM, Sales/CS và Kế toán/Purchasing nhập liệu trên CRM (Vtiger).
> **Ngôn ngữ:** Tiếng Việt

---

## Mục đích

Số tiền thưởng và trạng thái **"Đủ điều kiện" (Eligible)** phụ thuộc trực tiếp vào cách điền field trên **3 nơi**: ticket Jira/Jira Service Management, Invoice trên CRM, và PO trên CRM. Tài liệu này liệt kê **từng field quan trọng** ở cả 3 nơi — điền đúng ngay từ đầu để tránh thưởng bị tính sai hoặc bị giữ lại (Not Eligible).



## Ai điền field nào

| Vai trò | Điền ở đâu |
|---------|-----------|
| **PIC** (PM/Dev/BA) | Mục A — field trên ticket Jira/JSM |
| **PIC** (trên CRM Vtiger) | Mục B — field trên Invoice, PO |
| **Kế toán / Purchasing** (trên CRM Vtiger) | Cashflow khi trả tiền NCC |
| **Manager** | Không điền field — theo dõi nhãn "Đủ điều kiện" và bấm **Reload** sau khi A/B/C đã cập nhật |

---

## A. Field trên ticket Jira / Jira Service Management

| # | Field | Bắt buộc | Giá trị đúng | Nếu điền sai/thiếu |
|---|-------|:---:|---|---|
| A1 | **Project** (Dự án) | ✅ | Đúng project key đã được COO cấu hình trong quy tắc thưởng (vd `SYS`, `PROJ`) | Không khớp project trong quy tắc → **không quy tắc nào match**, ticket không tạo thưởng |
| A2 | **Loại Issue** (Issue Type) | ✅ | Đúng loại đã cấu hình trong quy tắc (`Task`, `Bug`, `Development`, `Service Request`...) | Sai loại → không match quy tắc, ticket không tạo thưởng |
| A3 | **Trường vai trò** (thường là Assignee, hoặc 1 custom field do COO chỉ định — xem Mục 4.5 trong `user-manual-vi.md`) | ✅ | Điền đúng người sẽ nhận thưởng, đúng vào field mà quy tắc đang đọc — hỏi COO/Manager field nào nếu không chắc | Để trống hoặc điền nhầm người → không tạo được thưởng, hoặc tạo nhầm cho người khác |
| A4 | **Invoice No** (custom field "Invoice" trên ticket, `customfield_10462`) | ✅ (rule `% Lợi nhuận` và `Base Reward`) | Mã Invoice khớp **chính xác 100%** với "Mã hóa đơn" đã tạo trên CRM (Mục B1). Nhiều invoice trên 1 ticket: cách nhau bằng khoảng trắng, dấu phẩy, chấm phẩy hoặc xuống dòng — **tối đa 5 mã** | Sai chính tả → hệ thống không tìm ra Invoice, ticket **không có thưởng nào được tạo**. Quá 5 mã → hệ thống báo lỗi ngay, không tính, yêu cầu tách bớt sang ticket khác |
| A5 | **Trạng thái ticket** (workflow status) | ✅ | Chuyển đúng sang trạng thái được cấu hình làm "trạng thái kích hoạt" trong quy tắc thưởng (thường là `Done`) | Chưa chuyển đúng trạng thái → thưởng **chưa được tạo** (không phải lỗi — chỉ là chưa tới lúc trigger) |

> Field A1–A3, A5 quyết định ticket có **khớp đúng quy tắc thưởng** hay không (khoản thưởng có được tạo cho đúng người, đúng mức hay không). Field A4 (Invoice No) là cầu nối để hệ thống lấy đúng dữ liệu tài chính từ CRM — sai field này thì dù A1–A3, A5 đúng hết, ticket vẫn không ra thưởng.

## B. Field trên Invoice (CRM Vtiger)

| # | Field | Bắt buộc | Giá trị đúng | Nếu điền sai/thiếu |
|---|-------|:---:|---|---|
| B1 | **Mã hóa đơn** (Invoice No.) | ✅ | Trường do CRM **tự sinh**, không tự gõ được — sau khi Invoice được tạo, copy chính xác mã này sang trường Invoice trên ticket (Mục A4) | Copy sai/thiếu 1 ký tự sang A4 → ticket không tìm ra Invoice, không có thưởng nào được tạo |
| B2 | **Trạng thái hóa đơn** (Invoice Status) | ✅ | Phải chuyển sang **`Paid`** hoặc **`Done`** thì thưởng mới được tính | Còn `Draft`/`Pending`... → toàn bộ thưởng bị giữ lại (Not Eligible), lý do hiển thị đúng `Invoice status: {trạng thái hiện tại}` |
| B3 | **Danh sách PO liên quan** (Purchase Order) | ✅ | - Ticket có chi phí: điền đúng (các) mã PO thật, nhiều PO phân cách bằng dấu phẩy.<br>- Ticket **không** có chi phí PO: điền hẳn chữ **`N/A`** (không để trống) | Để **trống** (khác "N/A") → cost = 0 nhưng gắn cảnh báo "Missing PO" và có thể bị giữ lại chờ dữ liệu PO — luôn gõ rõ `N/A` khi cố ý không có PO |
| B4 | **Tổng tiền hóa đơn** (Grand Total) | ✅ | Số tiền cuối cùng sau thuế/chiết khấu — đây là số dùng làm **Doanh thu** để tính lợi nhuận | Điền sai/thiếu chiết khấu, thuế → Doanh thu và lợi nhuận tính sai ngay từ gốc |
| B5 | **Loại tiền hóa đơn** (Currency) | ✅ | Đúng loại tiền thực tế của hóa đơn (VND/USD/SGD) | Sai loại tiền → Doanh thu quy đổi ra VND sai |
| B6 | **Chiết khấu (Discount)** — nhập ở dòng hàng (Item), **không** dùng Overall Discount | ✅ (nếu invoice có nhiều dịch vụ) | Điền discount ngay trên **từng dòng hàng (Item)** đang lên hóa đơn | Điền vào **Overall Discount** (chiết khấu tổng ở cuối hóa đơn) → hệ thống **không biết chiết khấu đó thuộc dịch vụ nào**, doanh thu của đúng dịch vụ được thưởng sẽ bị tính sai |

> **Vì sao phải điền discount ở Item, không phải Overall Discount:** hệ thống tính doanh thu **theo từng dòng hàng** (chỉ cộng doanh thu của dịch vụ được thưởng, bỏ qua dịch vụ khác trên cùng invoice) bằng công thức `(List Price − Discount − Purchase Cost) × Quantity`, đọc thẳng field `discount_amount` của từng Item. Overall Discount là 1 số tổng ở cấp toàn hóa đơn, không gắn với Item nào — hệ thống không có cách nào tự "chia" số đó ngược lại cho đúng dòng hàng, nên sẽ bị bỏ qua khi tính doanh thu theo dịch vụ.

## C. Field trên PO (CRM Vtiger)

| # | Field | Bắt buộc | Giá trị đúng | Nếu điền sai/thiếu |
|---|-------|:---:|---|---|
| C1 | **Mã PO** (Purchase Order No.) | ✅ | Trường do CRM **tự sinh**, không tự gõ được — sau khi PO được tạo, copy chính xác mã này vào trường "Danh sách PO liên quan" trên Invoice (Mục B3) | Copy sai/thiếu ở Invoice → hệ thống báo `PO {mã} không tìm thấy trong CRM`, giữ lại toàn bộ thưởng |
| C2 | **PO Status** | ✅ | Cập nhật đúng tiến độ thật: `Created` → `In review` → `Delivered`/`Paid` (hoặc `Cancelled` nếu hủy) | Xem bảng chi tiết ngay dưới — đây là field ảnh hưởng lớn nhất tới Eligible |
| C3 | **Dòng hàng** (Line items: Product/Service, List Price, Discount, Quantity) | ✅ | Đúng dịch vụ khớp với ticket đang thưởng, đúng đơn giá (List Price − Discount) × Quantity | Sai dòng hàng/đơn giá → chi phí phân bổ sai tỷ trọng khi PO có nhiều dịch vụ |
| C4 | **Cashflow4You liên kết** — Loại giao dịch (`cashflow4you_paytype`) | Khuyến nghị (khi đã trả tiền) | Chọn **`Outgoing`** (không chọn `Incoming` — đó là tiền khách trả vào) | Chọn nhầm hoặc bỏ trống → hệ thống không nhận diện được đây là khoản trả NCC |
| C5 | **Cashflow4You liên kết** — Liên kết tới (`relationid`) | Khuyến nghị | Gắn đúng **PO đang trả tiền** (không phải Invoice) | Gắn sai PO → chi phí bị cộng nhầm hoặc không ghi nhận cho đúng PO |
| C6 | **Cashflow4You liên kết** — Số tiền thực trả + Loại tiền thực nhận (`cf_3154` / `cf_3152`) | Khuyến nghị | Điền đúng số tiền đã chuyển cho NCC và đúng loại tiền (USD/SGD/VND) | Sai loại tiền → chi phí quy đổi VND sai hoàn toàn (đã từng xảy ra sự cố thực tế — xem `PO_CASHFLOW_COST_FLOW_CONFIRMED.md` Mục 1.1) |

**Chi tiết C2 — PO Status quyết định thưởng có bị giữ lại hay không:**

| PO Status | Ảnh hưởng đến thưởng |
|---|---|
| **Delivered** hoặc **Paid** | ✅ Đủ tin cậy — chi phí được tính, thưởng có thể tính/duyệt bình thường |
| **Created** hoặc **In review** | ⛔ **Toàn bộ thưởng của Invoice bị giữ lại** (chưa cho duyệt), lý do hiển thị đúng `PO {mã} đang ở trạng thái {trạng thái}` |
| **Cancelled** | ✅ Không cần làm gì thêm — hệ thống tự hiểu chi phí PO này = 0 và **không** giữ thưởng lại |

> **Lưu ý:** chỉ cần **1 PO** trong số nhiều PO của cùng Invoice còn ở `Created`/`In review` là **toàn bộ** thưởng của Invoice đó bị giữ lại, dù các PO khác đã xong — phải cập nhật đủ hết PO liên quan, không chỉ 1 cái.
>
> Nếu PO đã Delivered/Paid nhưng **chưa có** dòng Cashflow nào (C4–C6) → hệ thống tự động dùng tạm giá niêm yết trên PO (không bị giữ lại thưởng, nhưng số tiền có thể chưa sát thực tế). Ghi nhận Cashflow càng sớm, số thưởng hiển thị càng chính xác.

---

## Kiểm tra "Đủ điều kiện" (Eligible) sau khi các field trên đã điền

Vào **Thưởng Cá Nhân** / **Thưởng Nhóm**, mở chi tiết khoản thưởng — xem nhãn **"Đủ điều kiện tính thưởng"**:

- 🟢 **Đủ điều kiện (Eligible)** — mọi field ở Mục A/B/C đã sẵn sàng, số tiền hiển thị có thể duyệt bình thường.
- 🔴 **Chưa đủ điều kiện (Not Eligible)** — kèm dòng **"Lý do: ..."** nêu chính xác field nào đang chặn.

**Bảng tra lý do "Chưa đủ điều kiện" → field cần sửa:**

| Lý do hiển thị | Field cần sửa | Cách xử lý |
|---|---|---|
| `Invoice status: {trạng thái}` (khác Paid/Done) | B2 | Kế toán cập nhật trạng thái Invoice khi đã thu tiền/hoàn tất, rồi bấm **Reload** |
| `PO {mã} đang ở trạng thái Created` / `In review` | C2 | Cập nhật PO Status sang `Delivered`/`Paid` khi thực tế đã giao/thanh toán, rồi bấm **Reload** |
| `PO {mã} không tìm thấy trong CRM` | B3 hoặc C1 | Sửa lại đúng mã PO trên Invoice, rồi bấm **Reload** |
| `No purchase orders found..., waiting for valid PO data` | B3 | Điền lại `N/A` nếu cố ý không có PO, hoặc điền đúng mã PO thật (đang bị để trống) |
| `Purchase orders failed to fetch from CRM` | C1 | Lỗi tạm thời hoặc PO đã bị xóa — thử **Reload** lại sau vài phút, báo Admin nếu vẫn lỗi |
| Ticket không có thưởng nào xuất hiện | A4 | Kiểm tra lại đúng mã Invoice trên ticket, sửa rồi kích hoạt lại (xem Mục 4.9 Kích Hoạt Thưởng Thủ Công trong `user-manual-vi.md`) |

> **Cách hệ thống kiểm tra lại:** hệ thống **không tự động quét lại** khi CRM thay đổi. Sau khi đã sửa field trên CRM, vào **Thưởng Nhóm** và bấm nút **"Reload"** trên đúng ticket đó để hệ thống tính lại theo dữ liệu mới nhất.

## Checklist nhanh trước khi gửi COO duyệt

- [ ] A1–A3, A5: Project/Issue Type/Vai trò/Trạng thái ticket đúng theo quy tắc thưởng
- [ ] A4 = B1: mã Invoice trên ticket khớp 100% với mã hóa đơn trên CRM
- [ ] B2 = `Paid` hoặc `Done`
- [ ] B3 điền đúng mã PO thật, hoặc `N/A` nếu cố ý không có
- [ ] B6: chiết khấu điền ở từng dòng hàng (Item), **không** dùng Overall Discount
- [ ] C2: mọi PO liên quan = `Delivered`/`Paid` (hoặc chủ đích để `Cancelled`)
- [ ] C4–C6: đã ghi Cashflow `Outgoing` đúng PO, đúng loại tiền — nếu PO đã có thanh toán thực tế
- [ ] Nhãn hiển thị **"Đủ điều kiện"** trước khi gửi yêu cầu duyệt cho COO
