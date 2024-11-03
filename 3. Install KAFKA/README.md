# Để cài đặt Apache Kafka trên một hệ thống Linux, dưới đây là hướng dẫn chi tiết từ việc cài đặt Zookeeper (bắt buộc để chạy Kafka) đến việc cấu hình và khởi động Kafka. 
- [1. Cài đặt Java](#1-cài-đặt-java)
- [2. Kiểm tra cài đặt Java](#2-kiểm-tra-cài-đặt-java)
- [3. Tải Kafka](#3-tải-kafka)
- [4. Cài đặt Zookeeper](#4-cài-đặt-zookeeper)
- [5. Khởi động Kafka](#5-khởi-động-kafka)
- [6. Tạo một Topic](#6-tạo-một-topic)
- [7. Gửi và Nhận Tin Nhắn](#7-gửi-và-nhận-tin-nhắn)
- [8. Dừng Zookeeper và Kafka](#8-dừng-zookeeper-và-kafka)


## 1: Cài đặt Java
- Apache Kafka yêu cầu Java 8 hoặc cao hơn. Có thể cài đặt OpenJDK bằng cách chạy các lệnh sau:
```bash
sudo apt update
sudo apt install openjdk-11-jdk
```
## 2: Kiểm tra cài đặt Java
- Sau khi cài đặt xong, hãy kiểm tra phiên bản Java:
```bash
java -version
```
## 3: Tải Kafka
- Tải Kafka: Truy cập trang Apache Kafka Download để tìm phiên bản mới nhất. Có thể sử dụng lệnh wget để tải Kafka. Ví dụ:
```bash
wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz
```
- Giải nén file tải về:
```bash
tar -xzf kafka_2.13-3.4.0.tgz
```
Di chuyển thư mục Kafka:
```bash
sudo mv kafka_2.13-3.4.0 /usr/local/kafka
```
## 4: Cài đặt Zookeeper
- Apache Kafka yêu cầu Zookeeper để quản lý cluster. Kafka đi kèm với một phiên bản Zookeeper tích hợp sẵn.
- Khởi động Zookeeper:
```bash
cd /usr/local/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
```
## 5: Khởi động Kafka
- Mở một terminal mới và khởi động Kafka broker:
```bash
cd /usr/local/kafka
bin/kafka-server-start.sh config/server.properties
```
## 6: Tạo một Topic
- Để kiểm tra Kafka, có thể tạo một topic mới:
```bash
bin/kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```
## 7: Gửi và Nhận Tin Nhắn

Gửi tin nhắn đến topic.
- Mở một terminal mới và chạy lệnh sau để gửi tin nhắn:
```bash
bin/kafka-console-producer.sh --topic test-topic --bootstrap-server localhost:9092
```
- C thể gõ một vài tin nhắn và nhấn Enter.
Nhận tin nhắn từ topic.
- Mở một terminal mới và chạy lệnh sau để nhận tin nhắn:
```bash
bin/kafka-console-consumer.sh --topic test-topic --from-beginning --bootstrap-server localhost:9092
```
### 8: Dừng Zookeeper và Kafka
- Khi muốn dừng Zookeeper và Kafka, có thể nhấn Ctrl+C trong terminal nơi đã khởi động chúng.
