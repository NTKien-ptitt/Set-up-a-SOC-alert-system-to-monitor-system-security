Để cài đặt Apache Kafka trên một hệ thống Linux, dưới đây là hướng dẫn chi tiết từ việc cài đặt Zookeeper (bắt buộc để chạy Kafka) đến việc cấu hình và khởi động Kafka. Hướng dẫn này giả định bạn đang sử dụng Ubuntu.

Bước 1: Cài đặt Java
Apache Kafka yêu cầu Java 8 hoặc cao hơn. Bạn có thể cài đặt OpenJDK bằng cách chạy các lệnh sau:

bash
Sao chép mã
sudo apt update
sudo apt install openjdk-11-jdk
Bước 2: Kiểm tra cài đặt Java
Sau khi cài đặt xong, hãy kiểm tra phiên bản Java:

bash
Sao chép mã
java -version
Bước 3: Tải Kafka
Tải Kafka: Truy cập trang Apache Kafka Download để tìm phiên bản mới nhất. Bạn có thể sử dụng lệnh wget để tải Kafka. Ví dụ:

bash
Sao chép mã
wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz
Giải nén file tải về:

bash
Sao chép mã
tar -xzf kafka_2.13-3.4.0.tgz
Di chuyển thư mục Kafka:

bash
Sao chép mã
sudo mv kafka_2.13-3.4.0 /usr/local/kafka
Bước 4: Cài đặt Zookeeper
Apache Kafka yêu cầu Zookeeper để quản lý cluster. Kafka đi kèm với một phiên bản Zookeeper tích hợp sẵn.

Khởi động Zookeeper:

bash
Sao chép mã
cd /usr/local/kafka
bin/zookeeper-server-start.sh config/zookeeper.properties
Bước 5: Khởi động Kafka
Mở một terminal mới và khởi động Kafka broker:

bash
Sao chép mã
cd /usr/local/kafka
bin/kafka-server-start.sh config/server.properties
Bước 6: Tạo một Topic
Để kiểm tra Kafka, bạn có thể tạo một topic mới:

bash
Sao chép mã
bin/kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
Bước 7: Gửi và Nhận Tin Nhắn
Gửi tin nhắn đến topic:

Mở một terminal mới và chạy lệnh sau để gửi tin nhắn:

bash
Sao chép mã
bin/kafka-console-producer.sh --topic test-topic --bootstrap-server localhost:9092
Bạn có thể gõ một vài tin nhắn và nhấn Enter.

Nhận tin nhắn từ topic:

Mở một terminal mới và chạy lệnh sau để nhận tin nhắn:

bash
Sao chép mã
bin/kafka-console-consumer.sh --topic test-topic --from-beginning --bootstrap-server localhost:9092
Bước 8: Dừng Zookeeper và Kafka
Khi bạn muốn dừng Zookeeper và Kafka, bạn có thể nhấn Ctrl+C trong terminal nơi bạn đã khởi động chúng.
