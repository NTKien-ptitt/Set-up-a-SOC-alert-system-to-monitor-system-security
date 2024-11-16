# Thiết lập hệ thống cảnh báo SOC để giám sát an ninh hệ thống
# Kiến trúc hệ thống
- Web Server (Nginx): Lưu trữ website và tạo log truy cập của người dùng.
- Gateway (Nginx): Hoạt động như một proxy, thu thập log từ Web Server và chuyển log tới Kafka.
- Message Queue (Kafka): Lưu trữ và chuyển tiếp log tới Graylog, đảm bảo tính liên tục của dữ liệu log.
- Elasticsearch hoặc Logstash: Hỗ trợ Graylog lưu trữ và truy vấn log một cách hiệu quả.
- Graylog Server: Thu thập, xử lý log từ Kafka và lưu trữ vào Elasticsearch hoặc Logstash, cung cấp các tính năng phân tích và cảnh báo.

![image](https://github.com/user-attachments/assets/374f04f0-be12-4cd3-9ce4-c5707f7f51a6)
# Hướng dẫn cài đặt và cấu hình

1. [Cài đặt Nginx Webserver](#cai-dat-nginx-webserver)
2. [Cài đặt Nginx Gateway (Reverse Proxy Nginx Webserver)](#cai-dat-nginx-gateway)
3. [Cài đặt Message Queue Kafka](#cai-dat-message-queue-kafka)
4. [Cài đặt Graylog server](#cai-dat-graylog-server)
5. [Chuyển log từ Nginx Gateway vào Kafka](#chuyen-log-tu-nginx-gateway-vao-kafka)
- 5.1 [Tổng quan giải pháp](#51-tong-quan-giai-phap)
- 5.2 [Các bước thực hiện](#52-cac-buoc-thuc-hien)
- 5.3 [Cài đặt và cấu hình Filebeat](#53-cai-dat-va-cau-hinh-filebeat)
- 5.4 [Khởi động services](#54-khoi-dong-services)
- 5.5 [Kiểm tra log trong Kafka](#55-kiem-tra-log-trong-kafka)
- 5.6 [Monitoring và Troubleshooting](#56-monitoring-va-troubleshooting)
6. [Chuyển log từ Kafka đến Graylog](#chuyen-log-tu-kafka-den-graylog)
7. [Cấu hình Graylog nhận log](#cau-hinh-graylog-nhan-log)
8. [Thiết lập Rule Giám sát](#thiet-lap-rule-giam-sat)
9. [Cấu hình Gửi Mail Cảnh Báo đến Người Giám Sát](#cau-hinh-gui-mail-canh-bao-den-nguoi-giam-sat)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## 1. Cài đặt Nginx Webserver
- [Hướng dẫn cài đặt Nginx Webserver](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/1.%20Nginx%20Webserver/README.md)

## 2. Cài đặt Nginx Gateway (Reverse Proxy Nginx Webserver)
- [Hướng dẫn cài đặt Nginx Gateway](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/2.%20Nginx%20Gateway/README.md)

## 3. Cài đặt Message Queue Kafka
- [Hướng dẫn cài đặt Kafka](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/3.%20Install%20KAFKA/README.md)

## 4. Cài đặt Graylog server
- [Hướng dẫn cài đặt Graylog Server](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/4.%20Install%20Graylog%20Server/README.md)

## 5. Chuyển log từ Nginx Gateway vào Kafka
### 5.1 Tổng quan giải pháp
- Sử dụng `Filebeat` để đọc log từ `Nginx access.log` và `error.log`.
- Đẩy log vào Kafka để xử lý và phân tích sau này
- Cấu hình Nginx để ghi log theo định dạng JSON
### 5.2 Các bước thực hiện
- Cấu hình Nginx log format
- Chỉnh sửa file nginx.conf:
```bash
http {
    log_format json_combined escape=json '{ '
        '"time_local": "$time_local", '
        '"remote_addr": "$remote_addr", '
        '"remote_user": "$remote_user", '
        '"request": "$request", '
        '"status": "$status", '
        '"body_bytes_sent": "$body_bytes_sent", '
        '"request_time": "$request_time", '
        '"http_referrer": "$http_referer", '
        '"http_user_agent": "$http_user_agent" }';

    access_log /var/log/nginx/access.log json_combined;
}
```

### 5.3 Cài đặt và cấu hình Filebeat
- filebeat.yml
```bash filebeat.inputs:
- type: log
  enabled: true
  paths:
    - /var/log/nginx/access.log
  json.keys_under_root: true
  json.add_error_key: true
  
- type: log
  enabled: true
  paths:
- /var/log/nginx/error.log

output.kafka:
  hosts: ["kafka1:9092", "kafka2:9092"]
  topic: "nginx-logs" 
  partition.round_robin:
    reachable_only: false
  required_acks: 1
  compression: gzip
  max_message_bytes: 1000000
```

### 5.4 Khởi động services
-  Restart Nginx để áp dụng cấu hình mới
```bash
sudo systemctl restart nginx
```
- Khởi động Filebeat
```bash
sudo systemctl start filebeat
```

## 5.5 Kiểm tra log trong Kafka
- Consume message từ topic nginx-logs:
```bash
kafka-console-consumer.sh --bootstrap-server localhost:9092 \    --topic nginx-logs --from-beginning
```

## 5.6. Monitoring và Troubleshooting
- Kiểm tra trạng thái Filebeat
- Xem status
```bash
sudo systemctl status filebeat
```

-  Xem log
```bash
sudo tail -f /var/log/filebeat/filebeat
```
- Kiểm tra metrics của Kafka
- Xem thông tin topic
```bash
kafka-topics.sh --describe --topic nginx-logs --bootstrap-server localhost:9092
```
- Xem log của consumer groups
```bash
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group filebeat
```
## 6. Chuyển log từ Kafka đến Graylog
Tạo một topic trong Kafka để gửi log
```bash
bin/kafka-topics.sh --create --topic nginx-logs --bootstrap-server localhost:9092
```

## 7. Cấu hình Graylog nhận log
Bước 1: Tạo Input Kafka trên Graylog
- Đầu tiên, truy cập vào giao diện quản lý của Graylog.
- Vào `System` > `Inputs` và chọn mục `Kafka`, sau đó nhấn `Launch new input`.
- Chọn `Node` nơi bạn muốn `Input` hoạt động.
Cấu hình các trường:
- `Topic`: Nhập tên topic Kafka mà Graylog sẽ lắng nghe.
- `Bootstrap servers`: Điền địa chỉ máy chủ Kafka (ví dụ: `localhost:9092`).
- Các tùy chọn bổ sung như `Consumer Group`, `Threads`, và `Key Deserializer` có thể chỉnh sửa theo nhu cầu.
- Nhấn `Save` để khởi chạy `Input`.

Bước 2: Cấu hình Kafka Producer
- Cấu hình `Kafka Producer`, đặt địa chỉ `Kafka Bootstrap servers` để đảm bảo kết nối đúng.
- `Producer` phải được thiết lập để gửi log vào topic Kafka đã cấu hình trong Graylog.
- Nếu dùng ứng dụng để gửi log, kiểm tra mã nguồn để đảm bảo log được gửi đúng topic.

## 8. Thiết lập Rule Giám sát
Truy cập giao diện Graylog:
- Đăng nhập vào Graylog và điều hướng đến `Alerts & Events` trong thanh `menu`.
Tạo `Event Definition`:
- Nhấp vào `Create Event Definition`.
- Đặt tên cho `Rule`, ví dụ: “Cảnh báo truy cập trang Admin/Config”.
- Thêm mô tả ngắn gọn về mục đích giám sát.
Thiết lập `Filter & Aggregation`.
- Trong phần Filter, sử dụng một truy vấn để giám sát các URL cụ thể. Ví dụ:
```bash
nginx.access.url:/admin OR nginx.access.url:/config
```
- `Time Range`: Đặt khoảng thời gian phù hợp (ví dụ: 1 phút) để Graylog kiểm tra các sự kiện.
Cấu hình `Trigger & Threshhold`:
- Đặt điều kiện kích hoạt sự kiện. Ví dụ, nếu có hơn 1 lần truy cập vào các URL nhạy cảm trong 1 phút, kích hoạt cảnh báo.
- Cấu hình mức độ nghiêm trọng, như “High” nếu cần cảnh báo khẩn cấp.
Thiết lập Hành động Cảnh báo (Notification):
- Thêm một hành động khi `Rule` được kích hoạt, như:
- Gửi Email: Nhập địa chỉ email của bạn hoặc nhóm quản trị.
- Webhook: Tích hợp với các hệ thống khác, như Slack, nếu cần.
Kiểm tra kỹ các thiết lập thông báo để đảm bảo nhận cảnh báo đúng cách.
Lưu và Kích hoạt `Rule`:
- Nhấn `Save` để lưu `Rule`.
- Bật trạng thái `Rule` để Graylog bắt đầu giám sát.
Kiểm tra Hoạt động
- Để chắc chắn Rule hoạt động, thử truy cập vào /admin hoặc /config từ trình duyệt và kiểm tra xem Graylog có phát hiện và gửi cảnh báo hay không.

## 9. Cấu hình Gửi Mail Cảnh Báo đến Người Giám Sát
Tạo Notification
Truy cập vào phần `Notifications`:
- Đăng nhập vào Graylog và điều hướng đến `Alerts & Events` > `Notifications`.
Tạo `Notification` mới:
- Nhấp vào `Create Notification`.
Điền thông tin:
- `Name`: Đặt tên cho `Notification`, ví dụ: “Cảnh báo Truy cập Trang Nhạy cảm”.
- `Type`: Chọn `Email Alert`.
Cấu hình chi tiết Email:
```bash
Subject: Cảnh báo Truy cập Trang Nhạy cảm
Body: 
Cảnh báo: Đã phát hiện truy cập vào trang nhạy cảm.
Thời gian: ${event.timestamp}
URL: ${event.fields.url}
```
Recipients:
- Nhập địa chỉ email của người giám sát (vd: admin@example.com).
Lưu `Notification`:
- Nhấn `Save` để lưu cấu hình.
Gắn `Notification` vào `Rule` Giám sát
Truy cập vào Event Definitions:

- Quay lại `Alerts & Events` > `Event Definitions`.
Chọn Rule giám sát:

- Tìm `Rule` mà bạn đã tạo trước đó (ví dụ: “Cảnh báo truy cập trang Admin/Config”).
Gắn `Notification`:

- Nhấn vào `Rule` đó, sau đó nhấn `Add Notification`.
- Chọn `Notification` mà bạn đã tạo và nhấn Save.
3. Kiểm tra `Notification`
Thực hiện kiểm tra:
- Thử truy cập vào một trong các trang nhạy cảm (như /admin hoặc /config) từ trình duyệt.
Xác minh Email:
- Kiểm tra email của người giám sát để xem có nhận được cảnh báo hay không.
