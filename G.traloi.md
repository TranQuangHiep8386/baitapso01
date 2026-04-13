# baitapso01
baitao13/4
Chào Hiệp, những câu hỏi này rất thực tế và đánh động đúng vào bản chất của việc triển khai hệ thống chuyên nghiệp. Dưới đây là giải đáp chi tiết cho từng vấn đề của bạn:

1. Tại sao dùng Nginx làm Reverse Proxy mà không trỏ thẳng Tunnel vào Node-RED?
Tính bảo mật & Tập trung: Nginx đóng vai trò là "người gác cổng". Bạn có thể cấu hình SSL, chặn IP xấu, giới hạn tốc độ truy cập tại một nơi duy nhất thay vì cấu hình lẻ tẻ trên từng service.

Điều hướng thông minh (Routing): Nginx giúp bạn chạy nhiều dịch vụ trên cùng một tên miền. Ví dụ: / vào trang Web, /api vào Node-RED, /dashboard vào một app khác. Tunnel chỉ cần trỏ vào Nginx, còn lại Nginx sẽ lo việc "chia bài".

Ẩn giấu hạ tầng: Người dùng bên ngoài chỉ thấy Nginx, họ không hề biết phía sau có những service gì, chạy cổng bao nhiêu, giúp giảm nguy cơ bị tấn công trực diện.

2. Sự khác biệt giữa Mount file và Mount thư mục trong Docker
Mount file: Chỉ gắn kết một file duy nhất (ví dụ nginx.conf).

Đặc điểm: Nếu bạn xóa file đó trên máy ảo, container có thể bị lỗi hoặc vẫn giữ bản cache cũ.

Mount thư mục: Gắn kết toàn bộ một folder (ví dụ ./nodered:/data).

Đặc điểm: Mọi file mới sinh ra bên trong folder đó (log, db, config) đều được đồng bộ hai chiều. Đây là cách tốt nhất để lưu trữ dữ liệu bền vững (Persistent Data) cho các ứng dụng như Node-RED.

3. Thay đổi index.html trên Ubuntu có thay đổi trên Web ngay không?
Có, thay đổi ngay lập tức.

Tại sao: Vì bạn đã dùng tính năng Bind Mount. File index.html trong container thực chất "dùng chung" một vùng nhớ trên ổ cứng với file trên máy Ubuntu. Khi bạn lưu file trên Ubuntu, Nginx trong container sẽ đọc đúng file đã sửa đó để trả về cho người dùng.

4. Ý nghĩa của restart: always và unless-stopped
always: Container sẽ luôn tự khởi động lại nếu bị lỗi (crash) hoặc khi bạn khởi động lại máy ảo Ubuntu.

unless-stopped: Tương tự always, nhưng có một ngoại lệ: Nếu chính tay bạn gõ lệnh docker stop, nó sẽ không tự bật lại khi máy ảo khởi động. Điều này giúp tránh việc container "tự sống dậy" khi bạn đang muốn bảo trì hệ thống.

5. Dùng chung 1 Network và cách cấu hình
Lợi ích: Các container có thể gọi nhau bằng Tên Service (như http://node-red:1880) thay vì dùng IP (vốn thường xuyên thay đổi). Nó cũng tăng tính bảo mật vì mạng này là mạng nội bộ, tách biệt hoàn toàn với bên ngoài.

Cách sửa file docker-compose.yml dùng chung network:

YAML
services:
  node-red:
    networks:
      - iot-network
  nginx:
    networks:
      - iot-network
  tunnel:
    networks:
      - iot-network

networks:
  iot-network:
    driver: bridge
6. Sử dụng file .env và bảo mật mã nguồn
Cách làm: Tạo file .env cùng cấp với file compose:

Đoạn mã
CF_TOKEN=your_secret_token_here
Trong docker-compose.yml, bạn gọi bằng: token: ${CF_TOKEN}.

Tại sao quan trọng: Token là chìa khóa mở cửa vào hệ thống của bạn. Nếu bạn đẩy (push) Token lên GitHub, bất kỳ ai cũng có thể chiếm quyền điều khiển Tunnel của bạn. Thêm vào .gitignore giúp bạn chia sẻ được mã nguồn (code) nhưng giữ lại "chìa khóa" (token) an toàn dưới máy cục bộ.

7. Tại sao nên thêm hậu tố :ro khi mount cấu hình Nginx?
:ro (Read-Only): Nghĩa là container chỉ có quyền đọc, không có quyền ghi vào file cấu hình.

Lý do: Đây là quy tắc bảo mật "đặc quyền tối thiểu". Ngăn chặn trường hợp container Nginx bị hacker chiếm quyền và sửa ngược lại file cấu hình trên máy Ubuntu của bạn để chèn mã độc.

8. Dùng Cloudflare Tunnel có cần mở cổng (port) nữa không?
Không cần thiết. * Lý do: Tunnel tạo một kết nối "đi ra" (outbound) từ máy ảo đến Cloudflare. Dữ liệu sẽ đi qua đường hầm này. Bạn có thể đóng hết các cổng 80, 443, 1880 trên Firewall (UFW) của Ubuntu, hệ thống vẫn chạy bình thường. Điều này cực kỳ an toàn vì máy chủ của bạn hoàn toàn "tàng hình" trước các đợt scan cổng từ Internet.
