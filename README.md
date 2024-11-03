# Thiết lập hệ thống cảnh báo SOC để giám sát an ninh hệ thống
# Kiến trúc hệ thống
- Web Server (Nginx): Lưu trữ website và tạo log truy cập của người dùng.
- Gateway (Nginx): Hoạt động như một proxy, thu thập log từ Web Server và chuyển log tới Kafka.
- Message Queue (Kafka): Lưu trữ và chuyển tiếp log tới Graylog, đảm bảo tính liên tục của dữ liệu log.
- Elasticsearch hoặc Logstash: Hỗ trợ Graylog lưu trữ và truy vấn log một cách hiệu quả.
- Graylog Server: Thu thập, xử lý log từ Kafka và lưu trữ vào Elasticsearch hoặc Logstash, cung cấp các tính năng phân tích và cảnh báo.

![image](https://github.com/user-attachments/assets/374f04f0-be12-4cd3-9ce4-c5707f7f51a6)

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
6. Chuyển log từ Kafka đến Graylog

7. Cấu hình Graylog nhận log

8. Thiết lập Rule Giám sát

9. Cấu hình Cảnh báo cho Rule đã Giám sát
