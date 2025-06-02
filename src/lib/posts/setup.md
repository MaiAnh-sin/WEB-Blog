---
title: "Hệ thống Tìm kiếm Phân tán với Elasticsearch"
date: "2025-06-06"
categories:
  - "distributed-system"
  - "elasticsearch"
excerpt: "Giới thiệu và hướng dẫn cài đặt hệ thống tìm kiếm phân tán dựa trên Elasticsearch."
---

# Giới thiệu Hệ thống Tìm kiếm Phân tán với Elasticsearch

Hệ thống tìm kiếm phân tán với Elasticsearch được thiết kế để cung cấp khả năng tìm kiếm và phân tích dữ liệu mạnh mẽ, nhanh chóng và hiệu quả. Elasticsearch là một công cụ dựa trên Lucene, cho phép người dùng thực hiện các truy vấn tìm kiếm phức tạp trên một lượng dữ liệu lớn và đa dạng.

Hệ thống này bao gồm nhiều thành phần, bao gồm:

- **Giao diện người dùng (Frontend)**: Nơi người dùng có thể nhập từ khóa và xem kết quả tìm kiếm.
- **API Server**: Đảm nhận chức năng xử lý yêu cầu từ giao diện người dùng và giao tiếp với Elasticsearch để thực hiện các truy vấn.
- **Elasticsearch Cluster**: Nơi lưu trữ và xử lý dữ liệu, hỗ trợ tìm kiếm toàn văn bản và phân tích dữ liệu theo thời gian thực.

Hệ thống được thiết kế với khả năng mở rộng cao, cho phép xử lý nhiều yêu cầu đồng thời mà không làm giảm hiệu suất. Điều này giúp các doanh nghiệp và tổ chức có thể nhanh chóng truy xuất và phân tích dữ liệu, từ đó đưa ra quyết định kịp thời và chính xác.

## 1. Tổng quan về Elasticsearch

### 1.1. Elasticsearch là gì?

Elasticsearch là một công cụ tìm kiếm và phân tích dựa trên Lucene, cho phép bạn thực hiện tìm kiếm văn bản, phân tích dữ liệu, và lưu trữ thông tin theo cách phân tán. Nó được sử dụng rộng rãi trong nhiều lĩnh vực, từ phân tích log đến tìm kiếm sản phẩm trên các trang thương mại điện tử.

### 1.2. Tính năng nổi bật

- **Tìm kiếm toàn văn bản**: Hỗ trợ tìm kiếm mạnh mẽ với nhiều phương pháp khác nhau.
- **Phân tán**: Có khả năng mở rộng để xử lý lượng dữ liệu lớn.
- **Tính linh hoạt**: Dễ dàng tích hợp với các công nghệ khác như Kibana và Logstash.

### 1.3. Lợi ích của việc sử dụng Elasticsearch

- **Tốc độ tìm kiếm nhanh**: Elasticsearch sử dụng chỉ mục để tăng tốc độ tìm kiếm.
- **Khả năng mở rộng**: Dễ dàng mở rộng để xử lý lượng dữ liệu lớn bằng cách thêm node.
- **Phân tích thời gian thực**: Hỗ trợ phân tích dữ liệu ngay khi dữ liệu được ghi vào.

## 2. Kiến trúc Hệ thống

### 2.1. Các thành phần chính

- **Frontend (React/Next.js)**: Giao diện người dùng để nhập từ khóa tìm kiếm và hiển thị kết quả.
- **Search API Server (Node.js)**: Chịu trách nhiệm nhận và xử lý các yêu cầu từ frontend, gửi truy vấn đến Elasticsearch, và trả kết quả về cho người dùng.
- **Elasticsearch Cluster**: Nơi lưu trữ và xử lý dữ liệu tìm kiếm, cho phép thực hiện các truy vấn phức tạp.
- **Log & Monitor**: Hệ thống giám sát đơn giản được code dựa trên các API giám sát được cung cấp sẵn bởi Elasticsearch

### 2.2. Sơ đồ Kiến trúc

[Frontend] <--> [Search API Server] <--> [Elasticsearch Cluster]
|
+--> [Log & Monitor]

## 3. Hướng dẫn Cài đặt

### 3.1. Yêu cầu Môi Trường

Trước khi bắt đầu cài đặt, hãy đảm bảo rằng bạn đã cài đặt các công cụ cần thiết sau:

- **Docker**: Đây là phần mềm cần thiết để chạy các container. Bạn có thể tải Docker từ [đường dẫn này].
- **Docker Compose**: Công cụ này giúp bạn quản lý nhiều container Docker cùng lúc. Đảm bảo rằng bạn đã cài đặt Docker Compose. Tham khảo hướng dẫn cài đặt tại [đây].
- **Windows Subsystem for Linux (WSL):** tính năng cho phép người dùng chạy hệ điều hành Linux ngay trên Windows (Nếu cài đặt trên môi trường Windows) 

### 3.2. Các bước Triển khai

1. **Clone Repository từ GitHub**:
   Mở terminal và sử dụng lệnh sau để sao chép mã nguồn về máy của bạn:
   ```bash
   git clone https://github.com/namnguyenit/Distributed-search-system-based-on-Elasticsearch.git
   cd Distributed-search-system-based-on-Elasticsearch
   ```

2. **Cấu hình Biến Môi Trường**:
   Tạo và chỉnh sửa file `.env` trong thư mục gốc của dự án để cấu hình kết nối đến Elasticsearch:

   ```env
   ELASTICSEARCH_URL=http://elasticsearch:9200
   SEARCH_API_PORT=3000
   ```

3. **Khởi động Hệ thống bằng Docker Compose**:
   Sử dụng lệnh sau để xây dựng và khởi động toàn bộ hệ thống:

   ```bash
   docker-compose up --build
   ```

4. **Truy cập Ứng dụng**:
   Mở trình duyệt và truy cập địa chỉ `http://localhost:3000` để sử dụng hệ thống tìm kiếm.

### 3.3. Giám sát và Log

Truy cập trang web với tài khoản admin:
- **Username:** `admin`
- **Password:** `admin`

### 3.4. Kiểm tra Cài đặt

- Kiểm tra giao diện tìm kiếm và đảm bảo Elasticsearch hoạt động đúng bằng cách thực hiện truy vấn.

## 4. Tính năng Chính của Hệ thống

### 4.1. Tìm kiếm toàn văn bản

- Hỗ trợ tìm kiếm nâng cao, đa tiêu chí, từ khóa, cụm từ và biểu thức chính quy.

### 4.2. Phân tích và Giám sát

- Hệ thống giám sát cơ bản được làm dựa trên các API theo dõi của Elasticsearch

### 4.3. Tính năng mở rộng

- Multi-tenancy, mở rộng cluster bằng cách thêm node.

## 5. Cấu hình và Tùy chỉnh

### 5.1. Cấu hình File .env

- Tùy chỉnh thông số kết nối, port, cấu hình Elasticsearch.

### 5.2. Tùy chỉnh Elasticsearch

- Sharding/Replication
- Custom Analyzer

## 6. Giám sát và Bảo trì

### 6.1. Thiết lập Kibana

- Cấu hình và sử dụng dashboard tùy chỉnh.

### 6.2. Quản lý Log

- **Filebeat**: Thu thập log
- **Logstash**: Xử lý và gửi log về Elasticsearch

## 7. Ứng dụng Thực tế

### 7.1. Các lĩnh vực sử dụng

- E-commerce, phân tích dữ liệu, hệ thống log.

### 7.2. Các trường hợp sử dụng

- Tìm kiếm sản phẩm, phân tích log truy cập website.

## 8. Hướng dẫn Giải quyết Sự cố

### 8.1. Sự cố thường gặp

- Elasticsearch không chạy: kiểm tra Docker và file `.env`.

### 8.2. Hỗ trợ và Tài nguyên

- Tài liệu chính thức Elasticsearch
- Diễn đàn cộng đồng: Stack Overflow

---