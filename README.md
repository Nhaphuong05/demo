# EnHouse - Nền tảng Giáo dục & An toàn Số cho Trẻ em (Dự án UNESCO Youth Hackathon 2026)

## 📖 Tóm tắt Dự án
**EnHouse** là một nền tảng web công nghệ giáo dục (EdTech) an toàn, được thiết kế đặc biệt nhằm kết nối thanh thiếu niên yếu thế và trẻ mồ côi (Mentee) với những người trưởng thành từng trải qua hoàn cảnh tương tự (Mentor). 

Mục tiêu cốt lõi của dự án là cung cấp sự **thấu cảm của con người thật** kết hợp với đào tạo **kỹ năng An toàn số (Media and Information Literacy - MIL)**. Trong bối cảnh công nghệ phát triển, EnHouse giúp trẻ em nhận thức và tránh xa cạm bẫy của "Bạn ảo AI" (False AI Friends) cũng như các rủi ro độc hại khác trên không gian mạng.

## 🎨 Định hướng UI/UX
Toàn bộ Frontend hiện tại (được viết bằng HTML/Tailwind CSS/JS thuần) đã được thiết kế và tuân thủ chặt chẽ theo chuẩn phong cách của tổ chức phi chính phủ (NGO) nổi tiếng **Theirworld.org**.
Giao diện tập trung vào sự thân thiện, an toàn, dễ tiếp cận và tạo cảm giác đáng tin cậy.

## 🚀 Các Tính năng Cốt lõi (Core Features) cần Backend hỗ trợ
Frontend hiện đang sử dụng LocalStorage để giả lập dữ liệu. Dưới đây là các tính năng chính cần đội ngũ Backend thiết kế và kết nối API thực tế:

1. **Hệ thống Đăng ký/Đăng nhập đặc thù (Custom Auth):** Ưu tiên **bảo mật tuyệt đối danh tính trẻ em**. Không thu thập thông tin định danh cá nhân (PII) của người học.
2. **Module Khám phá Mentor (Mentor Discovery):** Hiển thị và lọc danh sách Mentor dựa trên các kỹ năng MIL chuyên biệt như:
   - *Deepfake & AI Detection*
   - *Cyberbullying Defense*
   - *Digital Privacy*
3. **Hệ thống Đặt lịch hẹn (Booking System):** Quản lý trạng thái lịch rảnh của Mentor, đặt lịch tư vấn và tự động tạo/liên kết link Google Meet.
4. **Quản lý Hồ sơ Mentor (Profile Management):** Cho phép các Mentor tự động cập nhật thông tin cá nhân (Giới thiệu, Kỹ năng, Khung giờ trống) trực tiếp từ giao diện Web.
5. **Trò chơi Tương tác "Spot the Deepfake" & CMS Quiz:**
   - **Mentee:** Chơi quiz nhận diện rủi ro mạng/AI, backend tự động chấm điểm và lưu kết quả (cơ chế chống gian lận).
   - **Admin/Mentor:** Cung cấp CMS (Hệ thống quản trị nội dung) cho phép thêm, sửa, xoá ngân hàng câu hỏi động. Hỗ trợ upload hình ảnh minh họa cho câu hỏi (sử dụng Firebase Storage).
6. **Dịch thuật Đa ngôn ngữ (Translation Proxy):** Tính năng dịch toàn trang (hiện đang gọi thẳng Gemini API từ Frontend, cần chuyển về Backend để bảo mật API Key).
