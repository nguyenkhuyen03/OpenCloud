# 1. OpenCloud
OpenCloud là một nền tảng mã nguồn mở giúp quản lý và triển khai hệ thống lưu trữ đám mây theo mô hình object storage. Nó hỗ trợ giao diện S3 API để tương thích với các dịch vụ lưu trữ phổ biến như AWS S3, MiniO.

# 2. Collabora
- Collabora Online là một bộ ứng dụng văn phòng trực tuyến mã nguồn mở dựa trên LibreOffice. Nó cho phép người dùng xem, chỉnh sửa tài liệu Word, Excel, PowerPoint, và nhiều loại file khác ngay trên trình duyệt mà không cần tải xuống.

## 2.1. Vai trò của Collabora trong OpenCloud
Collabora được sử dụng cùng với OpenCloud, hoặc các hệ thống lưu trữ đám mây khác để cung cấp khả năng chỉnh sửa tài liệu online cho người dùng mà không cần phải tải về rồi mới chỉnh sửa.

## 2.2. Quá trình hoạt động của Collabora với OpenCloud
1. Người dùng mở một tài liệu (ví dụ: test.docx) trên OpenCloud.
2. OpenCloud gửi yêu cầu đến máy chủ Collabora (thường chạy trên container riêng).
3. Collabora tải tài liệu từ OpenCloud thông qua giao thức WOPI (Web Application Open Platform Interface).
4. Collabora hiển thị tài liệu trên trình duyệt của người dùng với giao diện tương tự như LibreOffice Online.
5. Người dùng chỉnh sửa tài liệu và các thay đổi được gửi ngược lại OpenCloud để lưu.

## 2.3. Ví dụ
1. Ta mở contract.docx trên OpenCloud.
2. OpenCloud gửi yêu cầu đến Collabora để hiển thị file.
3. Collabora tải file này từ OpenCloud và hiển thị giao diện chỉnh sửa trên trình duyệt.
4. Bạn chỉnh sửa file và nhấn "Save".
5. Collabora gửi bản cập nhật ngược lại OpenCloud, file contract.docx được lưu trên server.

# 3. MiniO
MiniO là một object storage server mã nguồn mở tương thích với Amazon S3. Nó được sử dụng để lưu trữ dữ liệu dưới dạng objects.

## 3.1. Vai trò của MiniO trong OpenCloud
- **Lưu trữ dữ liệu:** MiniO hoạt động như một backend lưu trữ các tệp tin, ảnh, video, dữ liệu người dùng trên openCloud.

- **Hỗ trợ giao thức S3:** MiniO tương thích với API của Amazon S3, giúp các ứng dụng có thể dễ dàng đọc/ghi dữ liệu qua các thư viện hỗ trợ S3.

- **Linh hoạt và hiệu suất cao:** MiniO có thể triển khai trên các máy chủ vật lý hoặc cloud, hỗ trợ phân tán dữ liệu.

## 3.2. Cách hoạt động của MiniO trong OpenCloud
1. Khi người dùng tải lên một file qua openCloud, file này được gửi tới MiniO.
2. MiniO sẽ lưu file vào **bucket** (giống như thư mục) và tạo một object.
3. Khi cần truy cập lại, openCloud sẽ gửi yêu cầu tới MiniO qua giao thức S3 API để lấy file.

## 3.3. Ví dụ về cách lưu file
- Người dùng tải ảnh **photo.jpg** lên openCloud.

- OpenCloud gửi file này tới MiniO và lưu vào bucket **user-data**.

- Khi truy cập lại ảnh, openCloud sẽ gọi API **GET /user-data/photo.jpg** từ MiniO.

# 4. Decomposeds3
Decomposeds3 là một module mở rộng giúp quản lý dữ liệu trong MiniO theo cách phân tán (decomposed). Nó hỗ trợ:
- **Chia nhỏ dữ liệu thành nhiều phần (sharding)** để cải thiện hiệu suất.

- **Cân bằng tải (load balancing)** khi có nhiều node lưu trữ dữ liệu.

- **Bảo mật và quản lý dữ liệu** thông qua cơ chế metadata.

## 4.1. Vai trò của Decomposeds3 trong OpenCloud
- **Tối ưu hóa lưu trữ:** Khi dữ liệu quá lớn, Decomposeds3 giúp chia nhỏ và phân phối dữ liệu.

- **Giảm tải cho MiniO:** Thay vì lưu trữ toàn bộ file trong một MiniO duy nhất, Decomposeds3 giúp phân tán file qua nhiều server ( MiniO được cài trên mỗi server) hoặc phân tán dữ liệu cho 1 MiniO duy nhất.

- **Cải thiện tốc độ truy xuất dữ liệu:** Khi cần lấy dữ liệu, Decomposeds3 có thể lấy nhiều phần song song thay vì đọc một file lớn duy nhất.

## 4.2. Cách hoạt động của Decomposeds3 trong OpenCloud
1. Khi người dùng tải lên một file, Decomposeds3 chia file thành nhiều phần nhỏ **(shards)**.
2. Các **shards** này được lưu trữ trên MiniO theo cách phân tán.
3. Khi tải file xuống, Decomposeds3 tập hợp các **shards** và gửi lại cho người dùng.

## 4.3. Ví dụ về cách lưu file với Decomposeds3
- Người dùng tải một file video.mp4 (1GB).

- Decomposeds3 chia thành 4 shards (250MB mỗi shard).

- Mỗi shard được lưu trên một MiniO khác nhau để tăng tốc độ truy xuất.

- Khi cần tải file, decomposeds3 sẽ gom các shards lại và gửi về cho người dùng.

