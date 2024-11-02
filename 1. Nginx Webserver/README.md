# Cài đặt Nginx Web Server
- cập nhật chỉ mục gói máy chủ của bạn. Sau đó, bạn có thể sử dụng apt install để cài đặt Nginx:
```bash
sudo apt update
sudo apt install nginx
systemctl start nginx
```
- Kiểm tra cấu hình UFW nào khả dụng, hãy chạy:
```bash
sudo ufw app list
```
![image](https://github.com/user-attachments/assets/87546131-26bc-4c29-b765-1cb951fba4d8)

- Xem trạng thái ufw bằng cách gõ:
```bash
sudo ufw allow 'Nginx HTTP'
sudo ufw status
```
![image](https://github.com/user-attachments/assets/7eebbe47-b217-43bc-8acd-264a108dbcf5)

- Với quy tắc tường lửa mới được thêm vào,kiểm tra xem máy chủ có hoạt động hay không bằng cách truy cập vào tên miền hoặc địa chỉ IP công cộng của máy chủ trong trình duyệt web.
- Nhập địa chỉ mà bạn nhận được trong trình duyệt web của mình và nó sẽ đưa bạn đến trang đích mặc định của Nginx:
```bash
http://server_domain_or_IP
```
![image](https://github.com/user-attachments/assets/8d313d28-5ee4-4669-908a-c2dd877a2f44)

Nếu thấy trang này, điều đó có nghĩa là bạn đã cài đặt Nginx thành công và kích hoạt lưu lượng HTTP cho máy chủ web.

## Cài đặt MySQL
- Cài đặt hệ thống cơ sở dữ liệu để có thể lưu trữ và quản lý dữ liệu website. MySQL là một hệ thống quản lý cơ sở dữ liệu phổ biến được sử dụng trong môi trường PHP.
- Sử dụng apt để tải và cài đặt phần mềm này:
```bash
sudo apt install mysql-server
```
- Khi được nhắc, hãy xác nhận cài đặt bằng cách nhập `Y`, rồi `ENTER`.
- Khi quá trình cài đặt kết thúc, bạn nên chạy tập lệnh bảo mật được cài đặt sẵn với MySQL. Tập lệnh này sẽ xóa một số cài đặt mặc định không an toàn và khóa quyền truy cập vào hệ thống cơ sở dữ liệu của bạn. Bắt đầu tập lệnh tương tác bằng cách chạy:
```bash
sudo mysql_secure_installation
```
- Thao tác này sẽ hỏi bạn có muốn thiết lập cấu hình VALIDATE PASSWORD PLUGIN không.
(Lưu ý: Kích hoạt tính năng này là một cuộc gọi phán xét. Nếu được bật, mật khẩu không phù hợp với tiêu chí đã chỉ định sẽ bị MySQL từ chối kèm theo lỗi. Việc tắt tính năng xác thực là an toàn, nhưng bạn phải luôn sử dụng mật khẩu mạnh, duy nhất cho thông tin đăng nhập cơ sở dữ liệu.)
- Trả lời Y nếu có hoặc bất kỳ điều gì khác để tiếp tục mà không cần bật.
![image](https://github.com/user-attachments/assets/c3f76dc7-9f2a-4a18-8942-4749e6bd0b73)

- Nếu bạn trả lời “yes”, bạn sẽ được yêu cầu chọn mức độ xác thực mật khẩu. Hãy nhớ rằng nếu bạn nhập 2 cho mức mạnh nhất, bạn sẽ gặp lỗi khi cố gắng đặt bất kỳ mật khẩu nào không chứa số, chữ hoa và chữ thường, ký tự đặc biệt hoặc dựa trên các từ thông dụng trong từ điển.
![image](https://github.com/user-attachments/assets/eda3b8da-bed7-4d7d-acc7-059d50e51ec9)

- Bất kể bạn có chọn thiết lập VALIDATE PASSWORD PLUGIN hay không, máy chủ của bạn tiếp theo sẽ yêu cầu bạn chọn và xác nhận mật khẩu cho MySQL root user. Điều này không được nhầm lẫn với system root. Database root user là người dùng quản trị có đầy đủ đặc quyền đối với hệ thống cơ sở dữ liệu. Mặc dù phương thức xác thực mặc định cho MySQL root user không sử dụng mật khẩu, ngay cả khi mật khẩu được đặt, bạn nên xác định một mật khẩu mạnh tại đây như một biện pháp an toàn bổ sung. Chúng ta sẽ nói về điều này trong giây lát.

- Nếu bạn đã bật xác thực mật khẩu, bạn sẽ được hiển thị độ mạnh của mật khẩu cho mật khẩu gốc mà bạn vừa nhập và máy chủ của bạn sẽ hỏi bạn có muốn tiếp tục với mật khẩu đó không. Nếu bạn hài lòng với mật khẩu hiện tại của mình, hãy nhập Y cho “yes” tại dấu nhắc:

![image](https://github.com/user-attachments/assets/a827af85-a10d-4419-a6c8-fd475012182e)

- Đối với các câu hỏi còn lại, nhấn Y và nhấn phím ENTER tại mỗi dấu nhắc. Thao tác này sẽ xóa một số người dùng ẩn danh và cơ sở dữ liệu thử nghiệm, vô hiệu hóa đăng nhập gốc từ xa và tải các quy tắc mới này để MySQL ngay lập tức tôn trọng những thay đổi bạn đã thực hiện.

Khi bạn hoàn tất, hãy kiểm tra xem bạn có thể đăng nhập vào bảng điều khiển MySQL hay không bằng cách nhập:
```bash
sudo mysql
```
- Điều này sẽ kết nối với máy chủ MySQL với tư cách là administrative database user root, điều này được suy ra bằng cách sử dụng sudo khi chạy lệnh này. Bạn sẽ thấy đầu ra như thế này:

![image](https://github.com/user-attachments/assets/ea4cadd7-8693-4721-bcd9-e2af22a3461d)

## Cài đặt PHP
- Bạn đã cài đặt Nginx để phục vụ nội dung của mình và cài đặt MySQL để lưu trữ và quản lý dữ liệu của bạn. Cài đặt PHP để xử lý mã và tạo nội dung động cho máy chủ web.

- Để cài đặt các gói `php-fpm` và `php-mysql`, hãy chạy:
```bash
sudo apt install php-fpm php-mysql
```
- Khi được nhắc, hãy nhập `Y` và `ENTER` để xác nhận cài đặt.


## Thiết lập cấu hình Nginx để sử dụng Bộ xử lý PHP
- Khi sử dụng máy chủ web Nginx, chúng ta có thể tạo các khối máy chủ (tương tự như máy chủ ảo trong Apache) để đóng gói các chi tiết cấu hình và lưu trữ nhiều tên miền trên một máy chủ. Trong hướng dẫn này, chúng tôi sẽ sử dụng your_domain làm tên miền mẫu.

Tạo thư mục web gốc cho your_domain như sau:
```bash
sudo mkdir /var/www/your_domain
```
- Tiếp theo, chỉ định quyền sở hữu thư mục với biến môi trường `$USER`, sẽ tham chiếu người dùng hệ thống hiện tại của bạn:
```bash
sudo chown -R $USER:$USER /var/www/your_domain
```
- Sau đó, mở một tệp cấu hình mới trong thư mục có sẵn các trang web của Nginx bằng trình chỉnh sửa dòng lệnh ưa thích của bạn. Ở đây, chúng tôi sẽ sử dụng nano:
```bash
sudo nano /etc/nginx/sites-available/your_domain
```
- Thao tác này sẽ tạo một tệp trống mới. Dán vào cấu hình cơ bản sau:
```bash
/etc/nginx/sites-available/your_domain
server {
    listen 80;
    server_name your_domain www.your_domain;
    root /var/www/your_domain;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
- Đây là những gì mỗi chỉ thị và khối vị trí này làm:
- Khi bạn chỉnh sửa xong, hãy lưu và đóng tệp. Nếu bạn đang sử dụng nano, bạn có thể làm như vậy bằng cách nhập CTRL+X rồi y và ENTER để xác nhận.

- Kích hoạt cấu hình của bạn bằng cách liên kết đến tệp cấu hình từ thư mục `sites-enabled` của Nginx:
```bash
sudo ln -s /etc/nginx/sites-available/your_domain /etc/nginx/sites-enabled/
```
- Sau đó, hủy liên kết tệp cấu hình mặc định khỏi thư mục `/sites-enabled/`:
```bash
sudo unlink /etc/nginx/sites-enabled/default
sudo nginx -t
```
Nếu có bất kỳ lỗi nào được báo cáo, hãy quay lại tệp cấu hình của bạn để xem lại nội dung trước khi tiếp tục.
```bash
sudo systemctl reload nginx
```
Trang web mới của bạn hiện đang hoạt động nhưng gốc web `/var/www/your_domain` vẫn trống. 


## Kiểm tra PHP với Nginx
- Kiểm tra nó để xác thực rằng Nginx có thể chuyển giao chính xác các tệp .php cho bộ xử lý PHP của bạn.

-  Mở một tệp mới có tên info.php trong thư mục gốc của tài liệu trong trình soạn thảo văn bản của bạn:
```bash
nano /var/www/your_domain/info.php
```
- Nhập hoặc dán các dòng sau vào tệp mới. Đây là mã PHP hợp lệ sẽ trả về thông tin về máy chủ của bạn:
```bash
/var/www/your_domain/info.php
<?php
phpinfo();
?>
```
- Khi bạn hoàn tất, hãy lưu và đóng tệp bằng cách nhập `CTRL+X` rồi `y` và `ENTER` để xác nhận.

Bây giờ bạn có thể truy cập trang này trong trình duyệt web của mình bằng cách truy cập tên miền hoặc địa chỉ IP công khai mà bạn đã thiết lập trong tệp cấu hình Nginx của mình, theo sau là `/info.php`:
```bash
http://server_domain_or_IP/info.php
```
![image](https://github.com/user-attachments/assets/1d8a4ddd-8c50-4e39-a9fd-b57a97148dc9)
