# Set-up-a-SOC-alert-system-to-monitor-system-security
# Kiến trúc hệ thống
Web Server (Nginx): Lưu trữ website và tạo log truy cập của người dùng.
Gateway (Nginx): Hoạt động như một proxy, thu thập log từ Web Server và chuyển log tới Kafka.
Message Queue (Kafka): Lưu trữ và chuyển tiếp log tới Graylog, đảm bảo tính liên tục của dữ liệu log.
Graylog Server: Thu thập, xử lý log từ Kafka và lưu trữ vào Elasticsearch hoặc Logstash, cung cấp các tính năng phân tích và cảnh báo.
Elasticsearch hoặc Logstash: Hỗ trợ Graylog lưu trữ và truy vấn log một cách hiệu quả.

1. Cài đặt Nginx Webserver
[Hướng dẫn cài đặt Nginx Webserver](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/1.%20Nginx%20Webserver/README.md)
2. Cài đặt Nginx Gateway (Reverse Proxy Nginx Webserver)
[Hướng dẫn cài đặt Nginx Gateway](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/2.%20Nginx%20Gateway/README.md)
3. Cài đặt Message Queue Kafka
[Hướng dẫn cài đặt Kafka](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/3.%20Install%20KAFKA/README.md)
4. Cài đặt Graylog server
[Hướng dẫn cài đặt Graylog Server](https://github.com/NTKien-ptitt/Set-up-a-SOC-alert-system-to-monitor-system-security/blob/main/4.%20Install%20Graylog%20Server/README.md)
5. Chuyển log từ Nginx Gateway vào Kafka
6. Chuyển log từ Kafka đến Graylog
7. Cấu hình Graylog nhận log
8. Thiết lập Rule Giám sát
9. Cấu hình Cảnh báo cho Rule đã Giám sát
