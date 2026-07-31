# Yêu cầu Kỹ thuật Backend Chi tiết (Backend Requirements & System Design)

Chúng ta sẽ ưu tiên sử dụng hệ sinh thái **Firebase (Firestore/Firebase Auth)** kết hợp với **Node.js (Cloud Functions hoặc Express)**.

---

## 🛡️ 1. Nguyên tắc Safeguarding by Design (Bảo vệ trẻ em)

Đây là yêu cầu **BẮT BUỘC** và mang tính sống còn của dự án:
- **Tuyệt đối KHÔNG lưu trữ PII (Personally Identifiable Information):** Không yêu cầu, không thu thập và không lưu trữ Email, Số điện thoại, hay Tên thật của Mentee. 
- **Mục đích:** Chúng ta cần thiết kế kiến trúc phòng thủ chủ động nhằm chống lại "Kẻ đánh cắp dữ liệu" (The Thief). Trong trường hợp xấu nhất nếu Database bị rò rỉ, kẻ tấn công cũng không thể định danh được bất kỳ đứa trẻ nào ngoài đời thực.

---

## 🔐 2. Logic Xác thực (Custom Auth logic trên Firestore)

Vì tính chất bảo mật đặc thù của Mentee và sự chuyên nghiệp của Mentor, hệ thống Auth được chia thành 2 luồng rõ rệt (phân biệt bằng trường `role`):

- **Đối với Mentee (Trẻ em/Người học):** 
  - Đăng nhập/Đăng ký thông qua cơ chế **Custom Auth**.
  - Định danh duy nhất bằng **Nickname**.
  - Mật khẩu là một **Mã PIN đã được băm (Hashed PIN)** (ví dụ: dùng bcrypt trước khi lưu). Không dùng cơ chế quên mật khẩu qua Email (có thể thay thế bằng security questions nếu cần, hiện tại cứ giữ ở mức PIN).
  
- **Đối với Mentor (Người trưởng thành):**
  - Yêu cầu xác thực danh tính rõ ràng.
  - Sử dụng **Email/Password Auth** hoặc **Google OAuth 2.0** do Firebase hỗ trợ mặc định.

---

## 🗄️ 3. Lược đồ Cơ sở dữ liệu (Database Schema)

Chúng ta sẽ sử dụng NoSQL (Firestore). Dưới đây là cấu trúc Collections cơ bản:

| Collection / Bảng | Trường (Fields) | Kiểu dữ liệu | Mô tả |
| :--- | :--- | :--- | :--- |
| **Users** | `id` | String | UID do Firebase cấp hoặc tự sinh |
| | `nickname_or_email` | String | Nickname (Mentee) hoặc Email (Mentor) |
| | `hashed_pin` | String | Mã PIN (chỉ dùng cho Mentee) |
| | `role` | String | `"mentee"` hoặc `"mentor"` |
| | `created_at` | Timestamp | Thời gian tạo tài khoản |
| **Mentors_Profile** | `id` | String | Profile ID |
| | `user_id` | String | Tham chiếu tới `Users.id` |
| | `bio` | String | Giới thiệu bản thân |
| | `skills_array` | Array[String] | VD: `["Cyberbullying", "Deepfake"]` |
| | `availability` | Array[Object] | Khung giờ rảnh rỗi |
| **Sessions_Booking**| `id` | String | Booking ID |
| | `mentee_id` | String | Tham chiếu tới `Users.id` (Mentee) |
| | `mentor_id` | String | Tham chiếu tới `Users.id` (Mentor) |
| | `status` | String | `"pending"`, `"confirmed"`, `"completed"` |
| | `time_slot` | Timestamp | Khung giờ hẹn |
| | `meet_link` | String | URL Google Meet cho buổi tư vấn |
| **Quiz_Scores** | `id` | String | Record ID |
| | `user_id` | String | Tham chiếu tới `Users.id` |
| | `score` | Number | Điểm số đạt được |
| | `played_at` | Timestamp | Thời điểm hoàn thành trò chơi |

---

## 🌐 4. Danh sách API Endpoints / Cloud Functions cần thiết

Backend cần triển khai các dịch vụ dưới dạng RESTful API hoặc Firebase Cloud Functions:

### 4.1. Dịch vụ Auth (Custom Mentee Auth)
- `POST /api/auth/check-nickname`: Kiểm tra xem Nickname Mentee muốn đăng ký đã tồn tại hay chưa (Chống trùng lặp).
- `POST /api/auth/register-mentee`: Nhận Nickname và PIN (từ client), băm PIN và lưu vào bảng `Users` với role là `mentee`. Trả về JWT Token hoặc Firebase Custom Token.
- `POST /api/auth/login-mentee`: So khớp Nickname và Hashed PIN để cấp quyền truy cập.

### 4.2. Dịch vụ Đặt lịch & Google Meet (Booking)
- `POST /api/booking/create`: 
  - Ghi nhận yêu cầu đặt lịch vào collection `Sessions_Booking`.
  - **Tích hợp Webhook/Function:** Tự động giao tiếp với Google Calendar API (bằng Service Account) để tạo một event và sinh ra URL Google Meet. Hoặc cách đơn giản hơn là hệ thống cấp phát một URL tĩnh từ một pool có sẵn.
  - Lưu URL này vào trường `meet_link` và trả về cho Frontend hiển thị ở phòng chờ (Secure Waiting Room).

### 4.3. Dịch vụ AI Proxy (Bảo mật API Key)
- `POST /api/translate`: 
  - **Vấn đề:** Hiện tại Frontend (JS) đang để lộ thẳng Gemini API Key trong source code khi gọi tính năng dịch.
  - **Giải pháp:** Frontend chỉ cần gửi raw text hoặc JSON object chứa text tới endpoint này. Backend sẽ lấy Gemini API Key từ biến môi trường (`.env`), gọi Google Generative AI API lấy bản dịch, rồi trả kết quả về cho Frontend. Điều này giúp ẩn hoàn toàn API Key ở phía Server.
