# BÁO CÁO BÀI TẬP: THIẾT KẾ GIẢI PHÁP PROMPTING TỰ ĐỘNG HÓA

## Bối cảnh & Vấn đề thực tế (Scenario)
**Cửa hàng điện máy TechStore** cần tự động hóa quy trình tổng hợp và phân tích phản hồi (feedback) của khách hàng trên các kênh mạng xã hội. Nhân viên CSKH đang phải đọc thủ công từng tin nhắn và nhập dữ liệu vào file Excel, gây mất thời gian và dễ sai sót. 
Hệ thống cần một giải pháp AI nhận diện nội dung phản hồi, trích xuất các thông tin quan trọng và xuất ra dưới định dạng JSON (để lưu vào Database) và Bảng (để nhân viên dễ đọc), đồng thời phải biết tự động xử lý các trường hợp khách hàng không cung cấp đủ thông tin.

## Bước 1: Thiết kế giải pháp (I/O)

**1. Input (Đầu vào):** 
Một đoạn văn bản (text) chứa nội dung phản hồi, đánh giá hoặc khiếu nại của khách hàng.

**2. Output (Đầu ra mong muốn):**
AI phải trả về kết quả gồm 2 phần:
*   **Phần 1 - Bảng tóm tắt (Markdown Table):** Gồm các cột `Tên Sản Phẩm`, `Thái Độ Khách Hàng`, `Mức Độ Ưu Tiên`.
*   **Phần 2 - Dữ liệu thô (JSON):** Chứa các Key sau:
    *   `ma_don_hang`: Mã số đơn hàng (nếu có).
    *   `ten_san_pham`: Tên sản phẩm được nhắc đến.
    *   `thai_do`: Phân loại (Tích cực / Tiêu cực / Trung lập).
    *   `van_de_gap_phai`: Trích xuất ngắn gọn vấn đề khách gặp phải.
    *   `muc_do_uu_tien`: Đánh giá mức độ cần xử lý gấp (Cao / Trung bình / Thấp).

**3. Ràng buộc xử lý lỗi (Error Handling):**
*   Nếu không tìm thấy thông tin cụ thể trong câu, AI bắt buộc phải điền `"Không xác định"`.

## Bước 2: Viết Prompt (Theo chuẩn CREATE & Few-shot)

```text
[Context]
Bạn là một Chuyên viên Phân tích Dữ liệu Khách hàng (Customer Data Analyst AI) làm việc tại hệ thống cửa hàng điện máy TechStore. Nhiệm vụ của bạn là đọc các phản hồi của khách hàng, trích xuất thông tin quan trọng và chuẩn hóa chúng.

[Request]
Hãy đọc đoạn [PHẢN HỒI CỦA KHÁCH HÀNG] được cung cấp ở cuối prompt này, sau đó phân tích và xuất dữ liệu ra thành 2 định dạng: 1 Bảng tóm tắt (Markdown) và 1 chuỗi JSON thuần túy.

[Explanation & Examples - Few-shot]
(Như đã trình bày trong bài làm trước)

[Action & Constraints]
- Bạn CHỈ ĐƯỢC trả về 1 Bảng Markdown và 1 Block code JSON. Không giải thích gì thêm, không chào hỏi.
- Xử lý lỗi (Crucial): Nếu bất kỳ trường dữ liệu nào bị thiếu, bạn BẮT BUỘC phải điền giá trị là "Không xác định".

[Tone]
Chuyên nghiệp, chính xác tuyệt đối, tuân thủ nghiêm ngặt định dạng máy tính.
```
