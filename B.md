B. Cài đặt Ubuntu + Docker
Bước 1: Đăng nhập hệ thống
Nhập username: admin1 → nhấn Enter
Nhập mật khẩu: abc13579 → nhấn Enter
Bước 2: Lấy địa chỉ IP
Sau khi đăng nhập thành công, nhập lệnh: ip -4 addr
<img width="1036" height="160" alt="image" src="https://github.com/user-attachments/assets/91ea58e4-a883-4d7c-af98-54b523912d34" />

Cấu hình Docker không cần sudo
Chạy lệnh sau:exit

Sau đó đăng nhập lại bằng SSH:

ssh administrator@192.168.129.128
Lúc này cấu hình mới có hiệu lực

Mở tường lửa (UFW)
Cho phép các cổng theo yêu cầu:
<img width="1483" height="809" alt="image" src="https://github.com/user-attachments/assets/2829c5fe-9120-4796-8fbe-3e01215c6de0" />
