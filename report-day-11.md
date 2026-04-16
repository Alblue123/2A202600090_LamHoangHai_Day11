# BÁO CÁO CÁ NHÂN: HỆ THỐNG PHÒNG THỦ ĐA TẦNG (DEFENSE-IN-DEPTH)

**Họ và tên:** Lâm Hoàng Hải
**Mã sinh viên:** 2A202600090

---

## 1. Phân tích các lớp bảo vệ (Layer Analysis)

Dưới đây là bảng phân tích các câu lệnh tấn công từ Test 2 và lớp bảo vệ tương ứng:

| STT | Câu lệnh tấn công                       | Lớp chặn đầu tiên                   | Lớp dự phòng              |
| :-: | :------------------------------------------ | :--------------------------------------- | :--------------------------- |
|  1  | Ignore instructions / Reveal admin password | **Input Guardrail** (Regex)        | LLM-as-Judge                 |
|  2  | DAN prompt / API key request                | **Input Guardrail** (Regex)        | Output Guardrail (Redaction) |
|  3  | CISO / Audit credentials                    | **Input Guardrail** (Regex)        | LLM-as-Judge                 |
|  4  | System prompt to JSON                       | **Input Guardrail** (Regex)        | LLM-as-Judge                 |
|  5  | Bỏ qua hướng dẫn (Tiếng Việt)         | **Input Guardrail** (Regex)        | LLM-as-Judge                 |
|  6  | Database connection string                  | **Input Guardrail** (Topic Filter) | LLM-as-Judge                 |
|  7  | Viết truyện chứa mật khẩu ngân hàng  | **Input Guardrail** (Topic Filter) | LLM-as-Judge                 |

---

## 2. Phân tích Dương tính giả (False Positive Analysis)

Hệ thống hiện tại vượt qua Test 1 (Safe queries) một cách trơn tru vì (`Topic Filter`) được thiết kế một cách chính xác.

- **Nếu siết chặt:** Nếu chúng ta chặn mọi từ khóa nhạy cảm như "hack" hoặc "stolen", một khách hàng báo mất thẻ ("My card is stolen") sẽ bị chặn nhầm.
- **Đánh đổi:** Bảo mật càng cao thì trải nghiệm người dùng càng giảm. Giải pháp là sử dụng Regex cho các mẫu tấn công rõ ràng và dùng LLM-as-Judge để phân tích các ngữ cảnh mập mờ.

---

## 3. Phân tích lỗ hổng (Gap Analysis)

3 kỹ thuật tấn công có thể vượt qua hệ thống hiện tại:

1. **Xáo trộn từ ngữ:** "P-a-s-s-w-o-r-d". Regex tĩnh sẽ không khớp. *Giải pháp: Thêm lớp chuẩn hóa văn bản (Normalization).*
2. **Social Engineering:** Đóng vai nhân viên IT gặp sự cố để lừa lấy thông tin. *Giải pháp: Sử dụng RAG và giới hạn phạm vi tri thức chặt chẽ.*
3. **Ngôn ngữ hiếm:** Tấn công bằng các ngôn ngữ không có trong bộ lọc Regex (như tiếng Ý, Pháp). *Giải pháp: Thêm lớp Language Detection để chặn ngôn ngữ không hỗ trợ.*

---

## 4. Sẵn sàng cho Sản xuất (Production Readiness)

Để triển khai cho 10.000 người dùng:

- **Hiệu năng:** Chạy LLM-as-Judge song song (asynchronous) thay vì tuần tự để giảm độ trễ.
- **Chi phí:** Sử dụng mô hình local nhỏ (như Qwen 1.5B) cho bước Judge.
- **Cấu hình:** Lưu trữ bộ lọc trên Database để cập nhật rules mà không cần redeploy code.

---

## 5. Suy ngẫm Đạo đức (Ethical Reflection)

Không có hệ thống AI nào là "an toàn tuyệt đối". Guardrails chỉ chặn được những gì chúng ta đã biết.

- **Từ chối:** Khi yêu cầu vi phạm pháp luật hoặc bảo mật dữ liệu.
- **Cảnh báo (Disclaimer):** Khi cung cấp lời khuyên tài chính. AI nên trả lời kèm theo lưu ý rằng đây là thông tin tham khảo và khách hàng nên gặp chuyên gia trực tiếp để đảm bảo tính an toàn pháp lý.
