## SmartCardWebsite (Flashcard / Quizlet Clone)

Ứng dụng web học tập bằng **Flashcard** theo hướng **Spaced Repetition** (ôn tập ngắt quãng). Hỗ trợ quản lý **Deck/Flashcard**, học tập, làm **Quiz**, làm **Exam**, và khu vực **Admin**.

> Backend: Spring Boot (MVC) + Thymeleaf. Database: Microsoft SQL Server.

### Công nghệ sử dụng

- **Java 21**, **Spring Boot 3.5.x**
- **Spring MVC + Thymeleaf** (render HTML server-side)
- **Spring Data JPA (Hibernate)**
- **Microsoft SQL Server** (runtime) + **H2** (test)
- **Maven Wrapper** (`mvnw`, `mvnw.cmd`)
- Upload/IO: **Commons IO**, xử lý file: **Apache POI** (Excel/Office), **PDFBox** (PDF)
- HTTP client: **OkHttp**, JSON: **org.json**, Jackson
- Email: **Spring Boot Starter Mail**
- Test UI: **Selenium**

## Chức năng của dự án

- **Deck (Bộ thẻ)**: tạo/sửa/xóa deck, xem danh sách.
- **Flashcard**: thêm/sửa/xóa thẻ trong deck, hỗ trợ ảnh (upload từ máy hoặc lấy từ URL).
- **Study / Học**: học theo deck.
- **Spaced Repetition**: ôn tập ngắt quãng.
- **Quiz**: làm quiz theo deck, xem kết quả.
- **Exam**: tạo đề, làm bài theo mã, xem lịch sử và kết quả.
- **Tài khoản**: đăng ký/đăng nhập/đăng xuất.
- **Admin**: dashboard, quản lý user/deck/flashcard/exam, import PDF.

## Chức năng nổi bật

- **Spaced Repetition** giúp tối ưu lịch ôn tập.
- **Exam theo mã** (chia sẻ mã để người khác vào làm bài).
- **Import nội dung từ PDF** cho khu vực Admin.
- **Upload ảnh** cho flashcard.

## Sơ đồ cây thư mục (tóm tắt)

```text
SmartCard_Website/
├─ README.md
└─ flashcard/
	└─ flashcard/                       # Spring Boot project (Maven)
		├─ pom.xml
		├─ mvnw, mvnw.cmd
		├─ src/
		│  ├─ main/
		│  │  ├─ java/com/quizletclone/...
		│  │  └─ resources/
		│  │
		│  │     ├─ application.example.properties # file mẫu để copy
		│  │     ├─ static/                        # css/js
		│  │     └─ templates/                     # Thymeleaf pages
		│  └─ test/                                # test + selenium
		├─ uploads/                                # nơi lưu file upload (local)
		└─ target/                                 # output build (auto)
```

## Cách chạy dự án và truy cập website

### 1) Yêu cầu môi trường

- Java **JDK 21**
- Microsoft SQL Server (khuyến nghị SQL Server Express) + SSMS

> Dự án có Maven Wrapper nên không cần cài Maven riêng.

### 2) Cấu hình database trên SQL Server

Tạo database:

```sql
CREATE DATABASE Smartcard_DB;
```

Tạo SQL Login và cấp quyền (ví dụ minh hoạ):

```sql
CREATE LOGIN test WITH PASSWORD = 'YourStrongPassword!123';
USE Smartcard_DB;
CREATE USER test FOR LOGIN test;
ALTER ROLE db_owner ADD MEMBER test;
```

### 3) Tạo file application.properties để kết nối SQL Server

Trong project Spring Boot, copy file mẫu:

- Từ: `flashcard/flashcard/src/main/resources/application.example.properties`
- Thành: `flashcard/flashcard/src/main/resources/application.properties`

Sau đó chỉnh các dòng quan trọng:

- `spring.datasource.url` (host/instance/port/database)
- `spring.datasource.username`
- `spring.datasource.password`

**Ví dụ URL cho SQL Server Express local**:

```properties
spring.datasource.url=jdbc:sqlserver://localhost\\SQLEXPRESS:1433;databaseName=Smartcard_DB;encrypt=true;trustServerCertificate=true
```

> Gợi ý: `encrypt=true;trustServerCertificate=true` giúp tránh lỗi chứng chỉ SSL khi chạy local.

### 4) Chạy ứng dụng

Mở terminal tại thư mục `flashcard/flashcard` và chạy:

```bash
./mvnw spring-boot:run
```

Trên Windows PowerShell có thể dùng:

```powershell
./mvnw.cmd spring-boot:run
```

### 5) Truy cập website

- Mặc định chạy ở: `http://localhost:8087`
- Nếu đổi port thì chỉnh `server.port` trong `application.properties`.

Một số URL thường dùng:

- Trang chủ: `http://localhost:8087/`
- Danh sách deck: `http://localhost:8087/decks`
- Đăng nhập: `http://localhost:8087/login`
- Đăng ký: `http://localhost:8087/signup`
- Spaced repetition: `http://localhost:8087/spacedrepetition` (tùy routing của dự án)
- Admin: `http://localhost:8087/admin` (cần quyền phù hợp)

Gợi ý luồng sử dụng nhanh:

1. Tạo tài khoản tại `/signup` và đăng nhập tại `/login`.
2. Vào `/decks` để tạo deck.
3. Thêm flashcard trong deck, sau đó học/quiz/ôn tập.

## Cấu hình secrets (khuyến nghị)

Không nên commit mật khẩu DB / API key / email password. Dự án đã hỗ trợ đọc từ biến môi trường (ví dụ):

- `DB_USERNAME`, `DB_PASSWORD`
- `OPENROUTER_API_KEY`
- `MAIL_USERNAME`, `MAIL_PASSWORD`

Ví dụ set tạm trong PowerShell rồi chạy:

```powershell
$env:DB_USERNAME="test"
$env:DB_PASSWORD="YourStrongPassword!123"
$env:OPENROUTER_API_KEY="Bearer <your-key>"
$env:MAIL_USERNAME="your@gmail.com"
$env:MAIL_PASSWORD="<app-password>"
./mvnw.cmd spring-boot:run
```

## Test (tuỳ chọn)

```bash
./mvnw test
```

# \- \*\*Selenium\*\* (test UI)

#

# \## Ghi chú

#

# \- Nếu chạy mà báo lỗi kết nối DB, hãy kiểm tra lại `spring.datasource.url` (tên instance/port) và quyền user trên `Smartcard\_DB`.

# \- Nếu port `8087` bị trùng, đổi `server.port` sang port khác và truy cập theo port mới.
