Để cấu hình Nginx làm gateway cho các request đến một ứng dụng hoặc service khác (như một backend server), bạn cần sử dụng tính năng reverse proxy của Nginx. Dưới đây là hướng dẫn chi tiết về cách thực hiện điều này:

## Cấu hình Nginx làm Gateway
- Mở file cấu hình Nginx: Tùy thuộc vào hệ thống của bạn, file cấu hình thường nằm ở `/etc/nginx/nginx.conf` hoặc bạn có thể tạo một file mới trong thư mục `sites-available`.

```bash
sudo nano /etc/nginx/sites-available/gateway.conf
```
- Thêm cấu hình cho Gateway: Giả sử bạn muốn Nginx chuyển tiếp các request đến một ứng dụng backend chạy trên địa chỉ `http://127.0.0.1:5000`. Bạn có thể thêm cấu hình như sau:

```bash
server {
    listen 80;
    server_name your_domain.com;  # Thay đổi bằng tên miền của bạn

    location / {
        proxy_pass http://127.0.0.1:5000;  # Địa chỉ backend của bạn
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Các cấu hình proxy bổ sung (nếu cần)
        proxy_read_timeout 90;
        proxy_redirect http://127.0.0.1:5000/ /;
    }
}
```
- Kích hoạt cấu hình: Nếu bạn tạo một file mới trong `sites-available`, hãy tạo `ymlink` đến `sites-enabled` để kích hoạt nó.

```bash
sudo ln -s /etc/nginx/sites-available/gateway.conf /etc/nginx/sites-enabled/
```
- Kiểm tra cú pháp cấu hình Nginx: Trước khi khởi động lại Nginx, bạn cần kiểm tra xem cấu hình có lỗi hay không.
```bash
sudo nginx -t
```
- Khởi động lại Nginx: Nếu không có lỗi, hãy khởi động lại Nginx để áp dụng các thay đổi.
```bash
sudo systemctl restart nginx
```
- Kiểm tra Gateway: mở trình duyệt và truy cập vào địa chỉ `http://your_domain.com`. Nginx sẽ chuyển tiếp các request đến backend của bạn (trong trường hợp này là `http://127.0.0.1:5000`).
