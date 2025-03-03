# 1. OpenCloud
openCloud là một nền tảng mã nguồn mở giúp quản lý và triển khai hệ thống lưu trữ đám mây theo mô hình object storage. Nó hỗ trợ giao diện S3 API để tương thích với các dịch vụ lưu trữ phổ biến như AWS S3, MiniO.

# 2. MiniO
MiniO là một object storage server mã nguồn mở tương thích với Amazon S3. Nó được sử dụng để lưu trữ dữ liệu dưới dạng objects.

## 2.1. Vai trò của MiniO trong openCloud
- **Lưu trữ dữ liệu:** MiniO hoạt động như một backend lưu trữ các tệp tin, ảnh, video, dữ liệu người dùng trên openCloud.

- **Hỗ trợ giao thức S3:** MiniO tương thích với API của Amazon S3, giúp các ứng dụng có thể dễ dàng đọc/ghi dữ liệu qua các thư viện hỗ trợ S3.

- **Linh hoạt và hiệu suất cao:** MiniO có thể triển khai trên các máy chủ vật lý hoặc cloud, hỗ trợ phân tán dữ liệu.

## 2.2. Cách hoạt động của MiniO trong OpenCloud
1. Khi người dùng tải lên một file qua openCloud, file này được gửi tới MiniO.
2. MiniO sẽ lưu file vào **bucket** (giống như thư mục) và tạo một object.
3. Khi cần truy cập lại, openCloud sẽ gửi yêu cầu tới MiniO qua giao thức S3 API để lấy file.

## 2.3. Ví dụ về cách lưu file
- Người dùng tải ảnh **photo.jpg** lên openCloud.

- OpenCloud gửi file này tới MiniO và lưu vào bucket **user-data**.

- Khi truy cập lại ảnh, openCloud sẽ gọi API **GET /user-data/photo.jpg** từ MiniO.

# 3. Decomposeds3
Decomposeds3 là một module mở rộng giúp quản lý dữ liệu trong MiniO theo cách phân tán (decomposed). Nó hỗ trợ:
- **Chia nhỏ dữ liệu thành nhiều phần (sharding)** để cải thiện hiệu suất.

- **Cân bằng tải (load balancing)** khi có nhiều node lưu trữ dữ liệu.

- **Bảo mật và quản lý dữ liệu** thông qua cơ chế metadata.

## 3.1. Vai trò của Decomposeds3 trong OpenCloud
- **Tối ưu hóa lưu trữ:** Khi dữ liệu quá lớn, Decomposeds3 giúp chia nhỏ và phân phối dữ liệu.

- **Giảm tải cho MiniO:** Thay vì lưu trữ toàn bộ file trong một MiniO duy nhất, Decomposeds3 giúp phân tán file qua nhiều server ( MiniO được cài trên mỗi server) hoặc phân tán dữ liệu cho 1 MiniO duy nhất.

- **Cải thiện tốc độ truy xuất dữ liệu:** Khi cần lấy dữ liệu, Decomposeds3 có thể lấy nhiều phần song song thay vì đọc một file lớn duy nhất.

## 3.2. Cách hoạt động của Decomposeds3 trong OpenCloud
1. Khi người dùng tải lên một file, Decomposeds3 chia file thành nhiều phần nhỏ **(shards)**.
2. Các **shards** này được lưu trữ trên MiniO theo cách phân tán.
3. Khi tải file xuống, Decomposeds3 tập hợp các **shards** và gửi lại cho người dùng.

## 3.3. Ví dụ về cách lưu file với Decomposeds3
- Người dùng tải một file video.mp4 (1GB).

- Decomposeds3 chia thành 4 shards (250MB mỗi shard).

- Mỗi shard được lưu trên một MiniO khác nhau để tăng tốc độ truy xuất.

- Khi cần tải file, decomposeds3 sẽ gom các shards lại và gửi về cho người dùng.

