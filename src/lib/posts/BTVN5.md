---
title: "Distributed System notes 2"
date: "2025-05-28"
updated: "2025-05-28"
categories:
  - "sveltekit"
  - "markdown"
coverImage: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT5uJDmZ9pT8h6BnlkTT5EGBrdJyxtKMYpGwQ&s"
coverWidth: 16
coverHeight: 9
excerpt: Check out how heading links work with this starter in this post.
---
# So sánh giao thức mạng và tìm hiểu OpenMPI trong bài toán tính số nguyên tố

## 1. So sánh các giao thức HTTP, TCP/IP, UDP, REST, GraphQL, SOAP, AJAX, RPC, gRPC

| Giao thức | Mục đích sử dụng | Liên quan/Khác biệt |
|-------------|-------------------------|-----------------------------|
| **HTTP**    | Truyền tải siêu văn bản giữa client-server | Dựa trên TCP, là giao thức ứng dụng |
| **TCP/IP**  | Giao thức truyền tin đảm bảo trên mạng | Nền tảng cho HTTP, REST, SOAP |
| **UDP**     | Truyền tin nhanh, không đảm bảo | Nhẹ, dùng cho game, stream |
| **REST**    | API truy vấn dữ liệu qua HTTP | Tuân thủ giao thức HTTP, dễ hiểu |
| **GraphQL** | API truy vấn linh hoạt do client định nghĩa | Thay thế REST trong nhiều trường hợp |
| **SOAP**    | Giao thức API dựa trên XML, ràng buộc | Cố định, bền hơn REST |
| **AJAX**    | Kỹ thuật client-side gọi HTTP async | Dùng với REST, GraphQL... |
| **RPC**     | Remote Procedure Call giống gọi hàm trên mạng | Có thể dùng với TCP |
| **gRPC**    | Phiên bản RPC tối ưu, sử dụng Protobuf | Nhanh, nhỏ, có stream, dùng HTTP/2 |

### Tổng quan mối quan hệ:
- HTTP là giao thức để truyền REST, GraphQL, SOAP, AJAX.
- TCP/IP và UDP là nền tảng truyền tin của HTTP, RPC, gRPC.
- REST, GraphQL, SOAP đều là kiểu API giao tiếp client-server.
- RPC và gRPC dùng cho giao tiếp nhanh giữa các dịch vụ.

---

## 2. Tìm hiểu về OpenMPI và ứng dụng vào bài toán tính 10 triệu số nguyên tố

### Giới thiệu OpenMPI
OpenMPI (Open Message Passing Interface) là thư viện chuẩn MPI dùng cho lập trình song song trên các hệ thống phân tán. Hỗ trợ truyền thông giữa nhiều process trên nhiều node.

### Các hàm MPI cơ bản:
- `MPI_Init`: Khởi tạo môi trường MPI
- `MPI_Comm_rank`: Lấy ID (rank) của process
- `MPI_Comm_size`: Lấy số lượng process
- `MPI_Send`, `MPI_Recv`: Gửi/nhận dữ liệu
- `MPI_Bcast`, `MPI_Reduce`, `MPI_Gather`: Các hàm giao tiếp nhóm
- `MPI_Finalize`: Kết thúc chương trình MPI

---

## 3. Giải pháp bài toán tính 10 triệu số nguyên tố

### Chiến lược phân tán:
1. **Phân phối công việc**: Chia dải số dự kiến (tính bằng Sieve of Eratosthenes) theo số core/process.
2. **Chạy song song**: Mỗi process tính số nguyên tố trong khoảng đã chia.
3. **Thu thập kết quả**: Dùng `MPI_Gather` hoặc `MPI_Reduce` tổng hợp danh sách số nguyên tố.
4. **Linh hoạt core**: Tính tổng số core (MPI_Comm_size) và chia khoảng tự động.

### Mục tiêu:
- Tính đúng: Đảm bảo số nguyên tố được tính đầy đủ, không trùng.
- Hiệu suất: Chia đều công việc, tối ưu truyền thông.
- Linh hoạt: Chạy được với bất kỳ số core nào (>=1).


