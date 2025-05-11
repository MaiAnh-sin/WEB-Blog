---
title: "Distributed System notes 2"
date: "2025-05-11"
updated: "2025-05-11"
categories:
  - "sveltekit"
  - "markdown"
coverImage: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT5uJDmZ9pT8h6BnlkTT5EGBrdJyxtKMYpGwQ&s"
coverWidth: 16
coverHeight: 9
excerpt: Check out how heading links work with this starter in this post.
---
# Tiến trình & Luồng

## 1. Kiểm tra CPU, GPU, RAM và giải thích về hiệu năng của máy tính

* **CPU**: Intel Core i5-11300H (4 nhân, 8 luồng, xung nhịp cơ bản 3.1 GHz, Turbo Boost đến 4.4 GHz)

  * 4 nhân vật lý cho khả năng xử lý đa nhiệm cơ bản, 8 luồng nhờ Intel Hyper-Threading giúp tận dụng luồng ảo để cải thiện hiệu năng khi chạy song song nhiều tác vụ.

* **GPU**: Intel Iris Xe Graphics

  * GPU tích hợp dòng Iris Xe cung cấp hiệu năng đồ họa cơ bản: phù hợp cho xử lý UI, video playback, một số tác vụ tăng tốc phần cứng (WebGL, CSS animations). Không đủ sức mạnh cho game nặng hay tính toán GPU chuyên sâu như đào tạo mô hình học sâu.

* **RAM**: 8 GB DDR4-3200

  * Dung lượng 8 GB đủ cho nhu cầu phát triển web cơ bản (IDE, trình duyệt, server local) nhưng sẽ nhanh chóng đầy khi mở nhiều tab, chạy container hoặc VM. Tốc độ DDR4-3200 hỗ trợ băng thông dịch vụ phần mềm nhanh hơn DDR4 tốc độ thấp.

* **Hệ điều hành**: Windows 10/11

  * Windows có overhead hệ thống cao hơn Linux, nhưng bù lại hỗ trợ driver, phần mềm phong phú. Quản lý tiến trình và luồng tuân theo Win32 API.

**Giải thích hiệu năng tổng thể**:

* Với 4 nhân/8 luồng và RAM 8 GB, máy đáp ứng tốt các tác vụ phát triển web nhẹ: chạy local server, IDE, database nhẹ.
* GPU tích hợp Iris Xe giúp tăng tốc giao diện đồ hoạ, nhưng không hỗ trợ CUDA/OpenCL cho tính toán nặng.
* Nếu chạy nhiều dịch vụ (Docker, container, VM) hoặc build frontend lớn, RAM dễ cạn, ảnh hưởng đến hoán đổi (swap) và làm chậm toàn bộ hệ thống.

## 2. 12 bài toán phổ biến trong lĩnh vực Web sử dụng đa luồng / đa tiến trình

| STT | Bài toán                                   | Cách sử dụng đa luồng / đa tiến trình                                                       |
| --- | ------------------------------------------ | ------------------------------------------------------------------------------------------- |
| 1   | Xử lý HTTP requests                        | Web server như Gunicorn (multi-process), thread pool để xử lý đồng thời nhiều kết nối HTTP. |
| 2   | Xử lý upload/download file                 | Sử dụng thread để không chặn luồng chính, hoặc process riêng xử lý file nặng.               |
| 3   | Thực thi batch job (cron jobs)             | Process riêng chạy định kỳ, có thể spawn nhiều process song song.                           |
| 4   | Thực thi queue task (message broker)       | Worker multi-thread hoặc multi-process (Celery với prefork hoặc threads).                   |
| 5   | Xử lý stream real-time (WebSocket)         | Thread pool hoặc event loop kết hợp thread để handle I/O không đồng bộ.                     |
| 6   | Xử lý ảnh, video tĩnh/vừa tải lên          | Spawn process tách biệt cho convert, resize đảm bảo an toàn bộ nhớ.                         |
| 7   | Gửi email hàng loạt                        | Thread pool để gửi nhiều kết nối SMTP song song.                                            |
| 8   | ETL (Extract–Transform–Load)               | Multi-process để load và transform dữ liệu song song, tận dụng nhiều CPU.                   |
| 9   | Tìm kiếm full-text (Lucene, Elasticsearch) | Multi-threading trong engine để phân mảnh và tìm kiếm song song trên nhiều phân vùng.       |
| 10  | Kiểm thử hiệu năng (load testing)          | Spawn nhiều process hoặc thread để giả lập hàng ngàn user đồng thời.                        |
| 11  | CI/CD pipeline (build, test)               | Parallel job trên nhiều runner (process riêng biệt).                                        |
| 12  | Crawling / Scraping web                    | Thread pool request để tăng tốc, kết hợp process để tách luồng an toàn.                     |

## 3. Khi nào nên dùng thread, process, hoặc cả hai

| Tình huống                                  | Dùng Thread | Dùng Process | Dùng Cả hai | Ví dụ                                  |
| ------------------------------------------- | ----------- | ------------ | ----------- | -------------------------------------- |
| Tác vụ I/O-bound (đọc ghi file, network)    | X           |              |             | Gửi request HTTP, đọc file log         |
| Tác vụ CPU-bound (tính toán nặng)           |             | X            |             | Mã hóa/giải mã video                   |
| Yêu cầu cách ly bộ nhớ (bảo mật, stability) |             | X            |             | Sandbox chạy script người dùng         |
| Kết hợp I/O-bound và CPU-bound              |             |              | X           | Ứng dụng web kết hợp xử lý ảnh upload  |
| Chạy nhiều worker song song, chia tải       |             | X            |             | Celery worker prefork                  |
| Xử lý sự kiện, callback nhẹ (event-driven)  | X           |              |             | Node.js thread pool trong native addon |

## 4. ChatGPT training với tập dữ liệu lớn bằng distributed system

ChatGPT (GPT-3, GPT-4) được huấn luyện trên các cluster máy chủ GPU/TPU phân tán, sử dụng hai kỹ thuật cơ bản:

1. **Data Parallelism**: chia dataset lớn thành nhiều phần, mỗi GPU xử lý một mini-batch dữ liệu riêng, sau đó đồng bộ gradient qua giao thức NCCL hoặc AllReduce.
2. **Model Parallelism**: phân tách mô hình khổng lồ thành nhiều phần (layers, tensor slices), mỗi GPU giữ một phần của mô hình, cho phép huấn luyện các mô hình vượt quá giới hạn bộ nhớ GPU đơn.
3. **Optimizer State Sharding**: lưu trữ trạng thái optimizer (Adam, RMSProp) phân tán giữa các GPU để giảm tải bộ nhớ.
4. **Pipeline Parallelism**: chia chuỗi layers thành các stage, chạy xen kẽ giữa các micro-batch.

Các framework phổ biến hỗ trợ huấn luyện phân tán:

* [DeepSpeed (Microsoft)](https://www.deepspeed.ai/) cho optimizer sharding và zero redundancy.
* [Megatron-LM (NVIDIA)](https://github.com/NVIDIA/Megatron-LM) cho model parallelism.
* [PyTorch Distributed](https://pytorch.org/docs/stable/distributed.html) cung cấp giao diện AllReduce, RPC.

**Nguồn tham khảo**:

* "Megatron-LM: Training Multi-Billion Parameter Language Models Using Model Parallelism" – NVIDIA Megatron [https://github.com/NVIDIA/Megatron-LM](https://github.com/NVIDIA/Megatron-LM)
* "DeepSpeed: Extreme-scale model training for everyone" – Microsoft DeepSpeed [https://www.deepspeed.ai/](https://www.deepspeed.ai/)
