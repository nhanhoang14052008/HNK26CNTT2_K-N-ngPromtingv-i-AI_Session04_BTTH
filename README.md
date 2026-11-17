# BÀI TẬP THỰC HÀNH TỔNG HỢP – SESSION 04
## KỸ THUẬT PROMPTING NÂNG CAO (LOGIC & CHUỖI)

## 1. Bối cảnh và mục tiêu

**Bài toán:** Xây dựng hệ thống AI tự động tiếp nhận và xử lý đơn hàng cho cửa hàng.

**Mục tiêu:**
- Tự động trích xuất thông tin khách hàng và sản phẩm.
- Kiểm tra dữ liệu thiếu, sai hoặc bất thường.
- Tính tổng tiền đơn hàng chính xác.
- Xuất báo cáo theo định dạng JSON.
- Khôi phục quy trình khi AI bị ngắt phản hồi.

## 2. Thiết kế giải pháp (Input/Output)

**Input:**
- Tên khách hàng, số điện thoại, địa chỉ.
- Danh sách sản phẩm và số lượng.
- Bảng giá sản phẩm.
- Nội dung tin nhắn đặt hàng.

**Output:**
- Thông tin khách hàng.
- Danh sách sản phẩm, số lượng, đơn giá.
- Thành tiền từng sản phẩm và tổng tiền.
- Trạng thái đơn hàng.
- Danh sách lỗi cần xử lý.

## 3. Thiết kế chuỗi Prompt Chaining

Quy trình gồm 5 bước:

1. **Trích xuất dữ liệu:** Đọc tin nhắn và lấy thông tin đơn hàng.
2. **Kiểm tra dữ liệu:** Phát hiện thông tin trống, sai định dạng hoặc không hợp lệ.
3. **Phân tích logic (CoT):** Tính tiền từng sản phẩm và kiểm tra phép tính.
4. **Tổng hợp:** Kết hợp dữ liệu thành báo cáo đơn hàng.
5. **Kiểm tra đầu ra:** Xác thực JSON, trạng thái và tổng tiền trước khi hoàn tất.

## 4. Prompt hệ thống

Bạn là AI chuyên xử lý đơn hàng cho cửa hàng. Hãy thực hiện đúng quy trình 5 bước, đảm bảo chính xác, tiết kiệm Token và không tự bịa thông tin. Chỉ xuất JSON hợp lệ theo cấu trúc yêu cầu.

**Quy tắc:**
- Dữ liệu thiếu: gán null và ghi nhận lỗi.
- Số lượng phải là số nguyên dương.
- Sản phẩm không có trong bảng giá: không tự đoán giá.
- Dữ liệu mâu thuẫn: chuyển trạng thái CHO_XAC_NHAN.
- Không để một lỗi làm dừng toàn bộ quy trình.
- Chỉ tính tổng tiền khi tất cả sản phẩm đều có dữ liệu giá hợp lệ.

## 5. Prompt thực thi từng bước

**Prompt 1 – Trích xuất:**

Đọc dữ liệu khách hàng và trích xuất tên, số điện thoại, địa chỉ, sản phẩm, số lượng. Trường không có thông tin trả về null. Không tự suy đoán.

**Prompt 2 – Kiểm tra:**

Kiểm tra các trường bắt buộc, định dạng số điện thoại, số lượng và bảng giá. Liệt kê lỗi nếu có, không tự sửa dữ liệu.

**Prompt 3 – Tính toán CoT:**

Tính thành tiền bằng số lượng nhân đơn giá. Tính tổng tiền và kiểm tra lại phép tính. Chỉ xuất kết quả và giải thích ngắn gọn, không hiển thị suy luận nội bộ.

**Prompt 4 – Tổng hợp:**

Tổng hợp thông tin đã kiểm tra thành báo cáo đơn hàng. Giữ nguyên dữ liệu hợp lệ và liệt kê lỗi chưa xử lý.

**Prompt 5 – Kiểm tra đầu ra:**

Kiểm tra cú pháp JSON, trường bắt buộc, phép tính và trạng thái. Nếu có lỗi, không xác nhận đơn hàng.

## 6. Xử lý đứt gãy và tối ưu Token

- Sử dụng Context Anchoring để duy trì mục tiêu xử lý đơn hàng.
- Sử dụng Rolling Summary để tóm tắt dữ liệu và tiến độ khi hội thoại dài.
- Khi AI bị ngắt do Max Output Tokens, lưu bước đang thực hiện và kết quả đã hoàn thành.
- Sử dụng Continue Prompting để tiếp tục từ bước bị ngắt, không làm lại từ đầu.
- Chỉ truyền dữ liệu cần thiết giữa các bước để tiết kiệm Token.
- Kiểm tra từng bước để tránh lỗi lan truyền.

## 7. Kết quả chạy thử

**Dữ liệu đầu vào:**

- Khách hàng: Nguyễn Văn A.
- Số điện thoại: 098
