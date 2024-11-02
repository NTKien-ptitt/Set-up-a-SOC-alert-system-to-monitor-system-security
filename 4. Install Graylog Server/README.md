Graylog là một hệ thống tổng hợp và quản lý log mã nguồn mở, được sử dụng để phân tích một lượng lớn dữ liệu. Nó lưu trữ và phân tích log được thu thập từ máy chủ và gửi thông báo. Nó sử dụng Elasticsearch để lập chỉ mục dữ liệu log với MongoDB để lưu trữ thông tin meta.

Bài hướng dẫn này Graylog sẽ được tiến hành cài đặt trên Ubuntu 20.04 LTS

Cập nhật hệ thống
Để đảm bảo rằng tất cả các phụ thuộc của Graylog đều được cập nhật, hãy thực thi lệnh bên dưới:

bash (root)
apt-get update && apt-get upgrade
_
Trong quá trình cập nhật hệ thống chúng ta chờ từ 5 phút đến 15 phút.

Bước 1: Cài đặt Java OpenJDK
Quá trình cài đặt Graylog chúng ta cần tiến hành kiểm tra xem Java OpenJDK đã được cài đặt chừa vì đây là là thứ cần thiết để Graylog có thể hoạt động.

Để kiểm tra xem Java đã được cài đặt hay chưa, hãy chạy lệnh:

bash (root)
java -version
_
Nếu chưa cài đặt javajdk chúng ta tiến hành cài đặt javajdk và các gói phụ thuộc bằng lệnh sau:

bash (root)
apt -y install bash-completion apt-transport-https uuid-runtime pwgen openjdk-11-jre-headless
_
Kiểm tra phiên bản javajdk đã cài đặt:

bash (root)
java -version
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.20.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.20.04, mixed mode, sharing)
_
Bước 2: Cài đặt Elasticsearch
Nhập Elasticsearch PGP key bằng cách thực thi lệnh sau:

bash (root)
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
_
Tiến thành thêm Elasticsearch từ repository.

bash (root)
echo "deb https://artifacts.elastic.co/packages/oss-6.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-6.x.list
_
Cập nhật và tiến hành cài đặt Elasticsearch:

bash (root)
apt update
apt -y install elasticsearch-oss
_
Tiến hành chỉnh sửa file cấu hình của Elasticsearch /etc/elasticsearch/elasticsearch.yml thêm 2 dòng sau vào cuối file:

/etc/elasticsearch/elasticsearch.yml
. . .
cluster.name: graylog
action.auto_create_index: false
Thực hiện khởi động lại dịch vụ daemon và Elasticsearch:

bash (root)
systemctl daemon-reload && systemctl restart elasticsearch && systemctl enable elasticsearch
_
Bước 3: Cài đặt MongoDB
Thực thi lệnh sau để tiến hành cài đặt MongoDB:

bash (root)
apt install mongodb-server -y
_
Tiến hành khởi động dịch vụ MongoDB và cho MongoDB khởi động cùng với hệ thống:

bash (root)
systemctl start mongodb && systemctl enable mongodb
_
Bước 3: Cài đặt Graylog
Thực hiện thêm Graylog repository:

bash (root)
wget https://packages.graylog2.org/repo/packages/graylog-4.1-repository_latest.deb
_
Tiến hành cài đặt Graylog server package:

bash (root)
dpkg -i graylog-4.1-repository_latest.deb
_
Tiến hành cài đặt Graylog:

bash (root)
apt -y install graylog-server
_
Sau khi cài đặt hoàn tất chúng ta tiến hành tạo một chuỗi 96 ký tự ngẫu nhiên cho Graylog và lưu một bản backup để sử dụng trong tệp cấu hình máy chủ Graylog:

bash (root)
pwgen -N 1 -s 96
_
Chọn một mật khẩu cho tài khoản quản trị của chúng ta và tạo một băm64 ký tự.

bash (root)
echo -n mypassword | sha256sum
_
Sau đó chúng ta tiến hành cấu hình Graylog như sau:

Sử dụng trình soạn thảo để tiến hành chỉnh sửa file /etc/graylog/server/server.conf. Cụ thể:

Chỉnh sửa password_secret với chuỗi 96 ký tự ngẫu nhiên mà chúng ta đã tạo trước đó.

Chỉnh sửa root_password_sha2 với hàm băm 64 ký tự của mật khẩu admin của chúng ta.

Chỉnh sửa http_bind_address thành 0.0.0.0:9000

/etc/graylog/server/server.conf
. . .
password_secret = N4qIY1eLCYHN6oyykEgqbd1lutEZ7AWK0VPLj0u912tDsqNC8MuB0pJLTTWcm9GnOcNMDsYqokTtW6XbNAjgr
root_password_sha2 = 1cbd96d2e1677744e728f8714ce10a73583febf369556787120136
http_bind_address = 0.0.0.0:9000
. . .
Thực hiện khởi động lại dịch vụ daemon và Graylog:

systemctl daemon-reload
systemctl restart graylog-server
Truy cập giao diện Web Graylog
Chúng ta tiến hành truy cập giao diện Web Graylog bằng cách tiến hành mở trình duyệt của chúng ta và truy cập link http://IP:9000. Kết quả sẽ như sau:

Tiến hành đăng nhập vào Graylog, user là admin, password là password lúc nảy chúng ta thực hiện đặt
