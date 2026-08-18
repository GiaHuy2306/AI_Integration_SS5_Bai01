## Phương án lựa chọn: Phương án B

### Phân tích phương án chọn (Phương án B)
- Phương án B là thiết kế tối ưu nhất cho cơ chế Function Calling trong Spring AI vì các lý do kỹ thuật sau:

- Phân định ranh giới nghiệp vụ rõ ràng: Mô tả chi tiết giúp LLM hiểu chính xác mục đích, phạm vi của từng tool và thứ tự thực thi (chỉ gọi calculateTotalPrice sau khi đã biết roomType và số ngày).

- Định hướng định dạng dữ liệu đầu vào (Formatting Guidance): Nêu rõ định dạng tham số bắt buộc như yyyy-MM-dd cho ngày tháng và điều kiện ràng buộc numberOfDays > 0. Điều này giúp LLM trích xuất (extract) và định dạng tham số chính xác từ câu lệnh của người dùng trước khi đóng gói JSON payload.

- Tối ưu Prompt Context cho LLM: Trong Spring AI, annotation @Description hoặc @Tool(description=...) được chuyển đổi trực tiếp thành trường description trong Schema gửi đến LLM (như OpenAI Tool Specs). Mô tả đầy đủ ngữ cảnh giúp LLM giảm thiểu hiện tượng Hallucination và không bị nhầm lẫn giữa hai hàm.

### Phân tích các phương án loại trừ
1. Rủi ro của Phương án A (Mô tả tối giản)

- Thiếu thông tin định dạng: Mô tả ngắn ("Check phòng trống khách sạn") không cung cấp thông tin về định dạng đầu vào (như định dạng ngày tháng), dẫn đến việc LLM dễ trích xuất sai kiểu dữ liệu hoặc truyền thiếu tham số.

- Nguy cơ nhầm lẫn hàm (Function Misrouting): Giữa "Check phòng trống" và "Tính toán giá tiền" có khoảng mù ngữ cảnh (ambiguity). LLM có thể gọi nhầm calculateTotalPrice ngay cả khi người dùng chỉ muốn hỏi phòng còn trống hay không, hoặc ngược lại.

2. Rủi ro của Phương án C (Mô tả kỹ thuật nội bộ)

- Sai lệch phạm vi ngữ cảnh (Irrelevant Context): Chi tiết kỹ thuật về BookingService, MySQL DB, JPA, hay bảng room_status là thông tin triển khai nội bộ (Implementation Details). LLM không cần biết cấu trúc database để quyết định có nên gọi tool hay không.

- Lãng phí Token & Gây nhiễu LLM: Việc đưa các từ khóa mã nguồn/hệ thống vào mô tả làm tăng độ dài prompt vô ích (token count) và làm nhiễu khả năng suy luận nghiệp vụ của LLM, làm giảm độ chính xác khi trích xuất thông tin người dùng.