# 📘 Hướng Dẫn Sử Dụng — Công Cụ Tính Lương Ngày

> **Ứng dụng:** Session Staff Payroll Tool  
> **Phiên bản:** 1.0 · **Cập nhật:** Tháng 3/2026  
> **Đối tượng:** Admin kho / Nhân viên tính lương

---

## 📋 Tham Khảo Nhanh

| Mục | Chi tiết |
|-----|---------|
| **Truy cập** | Mở file `payroll_tool.html` bằng trình duyệt |
| **Đăng nhập** | Username: `Automation` · Password: `@1m!nd` |
| **Ngôn ngữ** | 🇹🇭 Thái · 🇬🇧 Tiếng Anh · 🇻🇳 Tiếng Việt |
| **Loại nhân viên** | Nhóm BM (BM1–BMM) · Nhóm DW (DW1–DWM) · Thực tập |
| **Nhập dữ liệu** | Nhập thủ công · Dán từ Clipboard · Nhập Excel (.xlsx/.xls) |
| **Kết quả** | Bảng trên màn hình · Xuất CSV · Xuất Excel (.xlsx) · In |

---

## 1. Đăng Nhập

1. Mở Payroll Tool trong trình duyệt web.
2. Nhập thông tin:
   - **Tên đăng nhập:** `Automation`
   - **Mật khẩu:** `@1m!nd`
3. Nhấn nút **Đăng nhập**.

> **Lưu ý:** Phiên làm việc được lưu cho đến khi bạn đóng tab trình duyệt. Không cần đăng nhập lại khi tải lại trang.

### Hướng dẫn lần đầu (Onboarding)

Khi đăng nhập lần đầu, **popup chào mừng** sẽ hiện với 3 bước:

| Bước | Nội dung |
|------|---------|
| 1 — Chào mừng | Tổng quan tính năng công cụ |
| 2 — Cách sử dụng | Tóm tắt quy trình làm việc |
| 3 — Sẵn sàng | Tùy chọn kết quả (CSV, in) |

Nhấn **Tiếp →** để tiếp tục hoặc **Bỏ qua** để đóng. Popup sẽ không hiện lại.

---

## 2. Cài Đặt Ca Làm

Thẻ **⚙️ Cài Đặt Ca Làm** nằm ở đầu trang.

### 2.1 Chọn ngày

- Nhấn vào ô **Ngày** và chọn ngày làm việc.
- Hoặc nhấn nút **Hôm nay** để tự động điền ngày hiện tại.

### 2.2 Chọn ca có sẵn

Chọn từ dropdown **Ca (Gợi ý)**:

| Ca | Khung giờ | Thời lượng |
|----|-----------|-----------|
| Ca thường | 08:30 – 17:30 | 9 giờ |
| Ca muộn | 09:00 – 18:00 | 9 giờ |
| Ca ngắn | 09:00 – 15:00 | 6 giờ |
| Ca ngắn muộn | 10:00 – 16:00 | 6 giờ |
| Ca sáng ngắn | 07:00 – 12:00 | 5 giờ |
| Ca đêm | 17:00 – 02:00 | 9 giờ (qua đêm) |

### 2.3 Ca tùy chỉnh

Bạn có thể điều chỉnh **Giờ bắt đầu** và **Giờ kết thúc** thủ công. Khi chỉnh, preset sẽ chuyển về "Tự chọn."

---

## 3. Thêm Nhân Viên

Có 4 cách nhập dữ liệu nhân viên:

### 3.1 Thêm thủ công

1. Nhấn **＋ Thêm nhân viên**.
2. Thẻ nhân viên xuất hiện — điền từng trường:

| Trường | Mô tả | Bắt buộc |
|--------|-------|----------|
| Họ và tên | Tên đầy đủ nhân viên | ✅ |
| Tên thường gọi | Tên gọi hàng ngày | Không |
| Nhóm / Note | Chọn từ dropdown (BM1, DW1...) | ✅ |
| Giờ vào | Thời gian đến thực tế | ✅ |
| Giờ ra | Thời gian về thực tế | ✅ |
| OT trước (giờ) | Giờ OT trước ca | Không |
| OT sau (giờ) | Giờ OT sau ca | Không |
| Nghỉ phép (giờ) | Số giờ nghỉ trong ca | Không |
| Trừ hàng hỏng (฿) | Trừ tiền hàng hư hỏng | Không |
| Ghi chú | Ghi chú bổ sung | Không |

### 3.2 Dán từ Excel

1. Nhấn **📋 Dán từ Excel** (hoặc nút 📋 trên thanh tiêu đề).
2. Cửa sổ xuất hiện với ô nhập liệu.
3. Trong Excel/Google Sheets, chọn các hàng rồi **Sao chép (Ctrl+C)**.
4. **Dán (Ctrl+V)** vào ô nhập liệu.
5. Nhấn **✅ Nhập dữ liệu**.

**Định dạng hỗ trợ (phân tách bằng Tab):**

```
STT | Họ tên | Giờ vào | Giờ ra | Nhóm | Tên thường gọi
```

> **Mẹo:** Cột STT (số thứ tự) sẽ được bỏ qua tự động.

### 3.3 Nhập file Excel (.xlsx / .xls)

1. Nhấn **📥 Nhập Excel** trong thanh hành động.
2. Hộp thoại chọn file mở ra — chọn file `.xlsx` hoặc `.xls` của bạn.
3. Công cụ tự động đọc sheet đầu tiên và nhập dữ liệu nhân viên.

**Thứ tự cột được hỗ trợ:**

```
STT | Họ tên | Giờ vào | Giờ ra | Nhóm | Tên thường gọi
```

> **Tự động nhận diện:** Công cụ tự động phát hiện và bỏ qua dòng tiêu đề (tìm "ลำดับ", "#", "No", "Seq", "STT"). Giá trị thời gian Excel (serial number) tự động chuyển đổi sang định dạng HH:MM.

### 3.4 Tải dữ liệu mẫu

Nhấn **🔽 Tải dữ liệu mẫu** trên màn hình trống để tải 10 nhân viên mẫu phục vụ kiểm thử.

---

## 4. Sử Dụng Time Chips (Chọn Nhanh)

Khi nhấn vào ô **Giờ vào** hoặc **Giờ ra**, các **chip thời gian màu** xuất hiện phía dưới:

### Chip giờ vào

| Màu | Ý nghĩa |
|-----|---------|
| ⬜ Trắng | ✅ Đúng giờ (đúng giờ bắt đầu ca) |
| 🟡 Vàng | Đi muộn (+5, +10, +15, +20, +30 phút...) |

### Chip giờ ra

| Màu | Ý nghĩa |
|-----|---------|
| 🔴 Đỏ | Về sớm (−60, −30, −15 phút) |
| ⬜ Trắng | ✅ Đúng giờ (đúng giờ kết thúc ca) |
| 🟢 Xanh | Tăng ca (+30, +60, +90, +120 phút) |

Nhấn chip để điền giờ tức thì.

---

## 5. Xem Trước Lương (Live Preview)

Mỗi thẻ nhân viên hiển thị **huy hiệu màu** ở phần đầu:

| Màu | Ý nghĩa |
|-----|---------|
| 🟢 Xanh | Đúng giờ — hiện "Đúng giờ ฿500" |
| 🟡 Vàng | Muộn — hiện "Muộn 15ph → ฿484.38" |
| 🔴 Đỏ | Về sớm — hiện tổng lương giảm |
| ⬜ Xám | Thực tập — hiện "฿100" |

Preview cập nhật **theo thời gian thực** khi bạn thay đổi bất kỳ trường nào.

---

## 6. Tính Lương

### 6.1 Thực hiện tính

- Nhấn **⚡ Tính lương** trong thanh hành động, hoặc
- Nhấn **nút ⚡ nổi** (góc dưới bên phải).

### 6.2 Bảng kết quả

Chuyển sang tab **📊 Kết quả** để xem:

| Cột | Mô tả |
|-----|-------|
| # | Số thứ tự |
| Họ tên | Họ tên đầy đủ + tên thường gọi |
| Nhóm | BM1, DW1... kèm huy hiệu màu |
| ฿ Cơ bản | Lương cơ bản (500/400/350/100) |
| Muộn (p) | Số phút bị trừ muộn |
| Trừ muộn | Số tiền trừ muộn (฿) |
| Về sớm | Số tiền trừ về sớm (฿) |
| OT | Tiền tăng ca (฿) |
| Trừ phép | Trừ nghỉ phép (฿) |
| Trừ hàng | Trừ hàng hỏng (฿) |
| **Tổng ฿** | **Lương thực nhận** |

Dòng tổng kết hiển thị **Tổng BM**, **Tổng DW** và **Tổng cộng**.

---

## 7. Tab Tổng Kết

Tab **📋 Tổng kết** hiển thị:

- **Thẻ thống kê:** Số nhân viên, tổng lương, tổng BM, tổng DW
- **Thẻ cảnh báo:** Số người đi muộn, về sớm, lương âm
- **Bảng theo nhóm:** Lương tổng hợp theo từng nhóm
- **Quy tắc tính lương:** Các quy tắc đang áp dụng

---

## 8. Xuất Dữ Liệu

Có 3 cách xuất kết quả:

### 8.1 Xuất Excel (.xlsx) ⭐ Khuyên dùng

1. Nhấn **📤 Excel** trong tab Kết quả (hoặc nút 📤 trên thanh tiêu đề).
2. File `payroll_YYYY-MM-DD.xlsx` tự động tải về.
3. Mở trực tiếp trong Excel với độ rộng cột phù hợp.

> **Mẹo:** Nên dùng xuất Excel thay vì CSV vì giữ nguyên định dạng số và không bị lỗi ký tự.

### 8.2 Tải CSV

1. Nhấn **⬇ CSV** trong tab Kết quả.
2. File `payroll_YYYY-MM-DD.csv` tự động tải về.
3. Mở bằng Excel hoặc Google Sheets để xử lý tiếp.

### 8.3 In

1. Nhấn **🖨 In** trong tab Kết quả.
2. Hộp thoại in của trình duyệt mở với bố cục tối ưu cho in.

---

## 9. Thao Tác Hàng Loạt

Khi đã có nhân viên, **thanh thao tác hàng loạt** xuất hiện:

| Thao tác | Mô tả |
|----------|-------|
| ✓ Đặt giờ ra tất cả | Đặt giờ ra cho TẤT CẢ nhân viên bằng giờ kết thúc ca |
| ▲ Thu gọn | Thu gọn tất cả thẻ nhân viên chỉ hiện phần đầu |

---

## 10. Đổi Ngôn Ngữ

Nhấn nút ngôn ngữ trên thanh tiêu đề:

| Nút | Ngôn ngữ |
|-----|----------|
| **TH** | Thái 🇹🇭 |
| **EN** | Tiếng Anh 🇬🇧 |
| **VI** | Tiếng Việt 🇻🇳 |

Tất cả nhãn, nút, quy tắc tính và định dạng ngày cập nhật ngay lập tức.

---

## 📌 Quy Tắc Tính Lương

### Lương cơ bản

| Điều kiện | Nhóm BM | Nhóm DW | Thực tập |
|-----------|---------|---------|----------|
| Ca đủ (>6 giờ) | ฿500 | ฿500 | ฿100 (khoán) |
| Ca ngắn (≤6 giờ) | ฿400 | ฿350 | ฿100 (khoán) |

### Giờ chuẩn (Q)

- Ca đủ (>6 giờ): **Q = 8**
- Ca ngắn (≤6 giờ): **Q = 5**

### Quy tắc trừ muộn

| Nhóm | Quy tắc |
|------|---------|
| **BM** | Làm tròn LÊN 15 phút (muộn 1 phút = trừ 15 phút) |
| **DW** | Trừ theo phút thực tế (muộn 22 phút = trừ 22 phút) |
| **Thực tập** | Không trừ |

### Công thức OT

```
OT = (Lương cơ bản ÷ Q) × 1.5 × Số giờ OT
```

### Công thức lương cuối cùng

```
Tổng = Lương cơ bản − Trừ muộn − Trừ về sớm + OT − Trừ phép − Trừ hàng hỏng
```

> ⚠️ Tổng có thể âm nếu các khoản trừ vượt quá lương cơ bản.

---

## ❓ Xử Lý Sự Cố

| Vấn đề | Cách giải quyết |
|--------|----------------|
| Không đăng nhập được | Kiểm tra username/password chính xác (phân biệt hoa/thường) |
| Dán Excel nhưng không có dữ liệu | Đảm bảo dữ liệu phân tách bằng Tab, không phải dấu phẩy |
| Chip thời gian không hiện | Nhấn trực tiếp vào ô nhập giờ |
| Kết quả hiện ฿0 cho mọi người | Kiểm tra đã nhập giờ vào và chọn nhóm |
| File CSV bị lỗi ký tự | Mở với encoding UTF-8 trong Excel |

---

## 🔒 Ghi Chú Bảo Mật

- Phiên đăng nhập lưu trong **sessionStorage** (mất khi đóng tab).
- Trạng thái bỏ qua onboarding lưu trong **localStorage** (giữ qua các phiên).
- **Không có dữ liệu nào được gửi lên server** — mọi thứ chạy cục bộ trên trình duyệt.
- Đóng tab trình duyệt để đăng xuất hoàn toàn.

---

*Kết thúc hướng dẫn · Session Staff Payroll Tool v1.0*
