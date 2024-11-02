# Set-up-a-SOC-alert-system-to-monitor-system-security
# Kiến trúc hệ thống
Web Server (Nginx): Lưu trữ website và tạo log truy cập của người dùng.
Gateway (Nginx): Hoạt động như một proxy, thu thập log từ Web Server và chuyển log tới Kafka.
Message Queue (Kafka): Lưu trữ và chuyển tiếp log tới Graylog, đảm bảo tính liên tục của dữ liệu log.
Graylog Server: Thu thập, xử lý log từ Kafka và lưu trữ vào Elasticsearch hoặc Logstash, cung cấp các tính năng phân tích và cảnh báo.
Elasticsearch hoặc Logstash: Hỗ trợ Graylog lưu trữ và truy vấn log một cách hiệu quả.

1. Cài đặt Nginx Webserver
2. Cài đặt Nginx Gateway (Reverse Proxy Nginx Webserver)
3. Cài đặt Message Queue Kafka
4. Cài đặt Graylog server
5. Chuyển log từ Nginx Gateway vào Kafka
6. Chuyển log từ Kafka đến Graylog
7. Cấu hình Graylog nhận log
8. Thiết lập Rule Giám sát
9. Cấu hình Cảnh báo cho Rule đã Giám sát
