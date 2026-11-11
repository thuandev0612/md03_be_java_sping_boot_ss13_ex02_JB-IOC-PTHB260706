# Session13 - Bài 1 và 2: Spring Security, SecurityFilterChain và BCrypt

Java 21, Spring Boot, Spring Security và Lombok. Employee có id, fullName, salary; API trả danh sách 3 nhân viên được fix cứng.

## Chạy ứng dụng

1. Mở project bằng IntelliJ, chọn JDK 21 và tải lại Gradle.
2. Chạy `BaitapSession13Application`.
3. Dùng Postman thử các endpoint bên dưới, chọn Authorization → No Auth.

## Bài 1: Cơ chế mặc định đã quan sát

Trước khi thêm SecurityConfig, dependency `spring-boot-starter-security` tự bảo vệ ứng dụng: API chưa xác thực trả 401, trình duyệt nhận HTML được chuyển đến form login. Basic Auth với user và mật khẩu sinh ngẫu nhiên trong Console trả 200 cùng 3 nhân viên. Mật khẩu thay đổi sau mỗi lần chạy.

Project hiện áp dụng cấu hình của Bài 2 bên dưới.

## Bài 2: SecurityFilterChain và PasswordEncoder

Trong Postman chọn **GET**, Authorization → **No Auth**:

```text
GET http://localhost:8080/api/v1/employees
Accept: application/json
```

Kết quả hiện tại là **403 Forbidden**, vì endpoint yêu cầu authenticated và chưa có cơ chế đăng nhập tùy chỉnh.

### API public

```text
GET http://localhost:8080/api/v1/auth/test
```

Không cần tài khoản/mật khẩu, trả **200 OK**:

```json
{"message": "API public truy cập thành công"}
```

Endpoint test cũng nhận **POST**, trả 200 mà không cần CSRF token để quan sát cấu hình tắt CSRF.

### Các Bean đã cấu hình

- `SecurityConfig` dùng `@Configuration`.
- `PasswordEncoder` là `BCryptPasswordEncoder`. BCrypt tạo hash kèm salt ngẫu nhiên; dùng encode để tạo hash và matches để kiểm tra mật khẩu, không so sánh trực tiếp hai hash.
- `SecurityFilterChain` tắt CSRF và form login, permitAll cho `/api/v1/auth/**`, authenticated cho các request còn lại.
- HTTP Basic cũng được tắt trong cấu hình hiện tại; hướng dẫn Basic Auth của Bài 1 là hành vi trước khi thêm SecurityConfig.

Với Bài 2, truy cập `/login` không còn hiển thị form đăng nhập mặc định; request private chưa xác thực trả 403 cho cả JSON và HTML. Các API đăng ký/đăng nhập thực tế có thể được bổ sung dưới `/api/v1/auth/**` ở bài tiếp theo. Bean BCrypt đã sẵn sàng để mã hóa mật khẩu khi cần.

Build/cache và log kiểm tra chứa mật khẩu sinh ngẫu nhiên được đặt ngoài thư mục nộp. Khi kéo thả GitHub, bỏ build/.gradle/.idea nếu IntelliJ tạo thêm.
