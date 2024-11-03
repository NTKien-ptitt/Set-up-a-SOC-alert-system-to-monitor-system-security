# Cài đặt Graylog trên môi trường Linux (root)

 [1. Cài đặt Java OpenJDK](#1-cài-đặt-java-openjdk)
- [2. Cài đặt Elasticsearch](#2-cài-đặt-elasticsearch)
- [3. Cài đặt MongoDB](#3-cài-đặt-mongodb)
- [4. Cài đặt Graylog](#4-cài-đặt-graylog)

- Cập nhật hệ thống
```bash
apt-get update && apt-get upgrade
```

## 1: Cài đặt Java OpenJDK
- Quá trình cài đặt Graylog chúng ta cần tiến hành kiểm tra xem Java OpenJDK đã được cài đặt chừa vì đây là là thứ cần thiết để Graylog có thể hoạt động.
Để kiểm tra xem Java đã được cài đặt hay chưa, hãy chạy lệnh:
```bash
java -version
```
- Nếu chưa cài đặt javajdk chúng ta tiến hành cài đặt javajdk và các gói phụ thuộc bằng lệnh sau:
```bash
apt -y install bash-completion apt-transport-https uuid-runtime pwgen openjdk-11-jre-headless
```
- Kiểm tra phiên bản javajdk đã cài đặt:
```bash
java -version
```

## 2: Cài đặt Elasticsearch
- Nhập Elasticsearch PGP key bằng cách thực thi lệnh sau:
```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
- Tiến thành thêm Elasticsearch từ repository.
```bash
echo "deb https://artifacts.elastic.co/packages/oss-6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
```
- Cập nhật và tiến hành cài đặt Elasticsearch:
```bash
apt update
apt -y install elasticsearch-oss
```
- Tiến hành chỉnh sửa file cấu hình của Elasticsearch /etc/elasticsearch/elasticsearch.yml thêm 2 dòng sau vào cuối file:
`/etc/elasticsearch/elasticsearch.yml`.
```bash
. . .
cluster.name: graylog
action.auto_create_index: false
```
- Thực hiện khởi động lại dịch vụ daemon và Elasticsearch:
```bash
systemctl daemon-reload && systemctl restart elasticsearch && systemctl enable elasticsearch
```

## 3: Cài đặt MongoDB
- Thực thi lệnh sau để tiến hành cài đặt MongoDB:
```bash
apt install mongodb-server -y
```
- Tiến hành khởi động dịch vụ MongoDB và cho MongoDB khởi động cùng với hệ thống:
```bash
systemctl start mongodb && systemctl enable mongodb
```

## 4: Cài đặt Graylog
- Thực hiện thêm Graylog repository:
```bash
wget https://packages.graylog2.org/repo/packages/graylog-4.1-repository_latest.deb
```
- Tiến hành cài đặt Graylog server package:
```bash
dpkg -i graylog-4.1-repository_latest.deb
```
- Tiến hành cài đặt Graylog:
```bash
apt -y install graylog-server
```
- Sau khi cài đặt hoàn tất chúng ta tiến hành tạo một chuỗi 96 ký tự ngẫu nhiên cho Graylog và lưu một bản backup để sử dụng trong tệp cấu hình máy chủ Graylog:
```bash
pwgen -N 1 -s 96
```
- Chọn một mật khẩu cho tài khoản quản trị của chúng ta và tạo một băm64 ký tự.
```bash
echo -n mypassword | sha256sum
```
- Sau đó chúng ta tiến hành cấu hình Graylog như sau:
- Sử dụng trình soạn thảo để tiến hành chỉnh sửa file `/etc/graylog/server/server.conf`. Cụ thể:
- Chỉnh sửa `password_secret` với chuỗi 96 ký tự ngẫu nhiên mà chúng ta đã tạo trước đó.
- Chỉnh sửa `root_password_sha2` với hàm băm 64 ký tự của mật khẩu admin của chúng ta.
- Chỉnh sửa `http_bind_address` thành `0.0.0.0:9000`, file `/etc/graylog/server/server.conf`
```bash
. . .
password_secret = N4qIY1eLCYHN6oyykEgqbd1lutEZ7AWK0VPLj0u912tDsqNC8MuB0pJLTTWcm9GnOcNMDsYqokTtW6XbNAjgr
root_password_sha2 = 1cbd96d2e1677744e728f8714ce10a73583febf369556787120136
http_bind_address = 0.0.0.0:9000
. . .
```
- Thực hiện khởi động lại dịch vụ daemon và Graylog:
```bash
systemctl daemon-reload
systemctl restart graylog-server
```
- Truy cập giao diện Web Graylog
- Chúng ta tiến hành truy cập giao diện Web Graylog bằng cách tiến hành mở trình duyệt của chúng ta và truy cập link `http://<IP_your_domain>:9000`.
- Tiến hành đăng nhập vào Graylog, `user` là `admin`, `password` là `password` lúc nảy chúng ta thực hiện đặt
