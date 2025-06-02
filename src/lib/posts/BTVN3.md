---
title: "Distributed System notes 2"
date: "2025-05-21"
updated: "2025-05-21"
categories:
  - "sveltekit"
  - "markdown"
coverImage: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT5uJDmZ9pT8h6BnlkTT5EGBrdJyxtKMYpGwQ&s"
coverWidth: 16
coverHeight: 9
excerpt: Check out how heading links work with this starter in this post.
---

# Báo cáo Bài tập về Dịch vụ Truyền thông điệp và RPC

---

## Bài tập 1: Tìm hiểu cơ chế, chức năng và cài đặt dịch vụ truyền thông điệp RabbitMQ

### 1. Giới thiệu chung

RabbitMQ là một hệ thống Message Broker mã nguồn mở, cho phép các ứng dụng trao đổi thông điệp qua mô hình hàng đợi (Message Queue). RabbitMQ hỗ trợ nhiều giao thức, kiến trúc mở và được sử dụng phổ biến trong các hệ thống phân tán để đảm bảo tính bất đồng bộ và tin cậy trong truyền dữ liệu.

### 2. Kiến trúc và cơ chế hoạt động

- **Producer** gửi message vào **Exchange**.
- **Exchange** định tuyến message đến các **Queue** dựa vào loại Exchange và routing key.
- **Consumer** lấy message từ Queue để xử lý.

Các loại Exchange phổ biến:
- **Direct Exchange**: định tuyến theo routing key chính xác.
- **Fanout Exchange**: phát tán đến tất cả các queue liên kết.
- **Topic Exchange**: định tuyến theo pattern routing key.
- **Headers Exchange**: định tuyến dựa trên header message.

### 3. Chức năng chính

- Truyền thông điệp bất đồng bộ, giảm tải hệ thống.
- Đảm bảo message không bị mất nhờ durability và acknowledgment.
- Hỗ trợ cân bằng tải giữa nhiều consumer.
- Tính mở rộng cao, hỗ trợ đa nền tảng.

### 4. Hướng dẫn cài đặt RabbitMQ

#### Bước 1: Cài Erlang

RabbitMQ chạy dựa trên Erlang. Tải và cài Erlang phù hợp tại:  
https://www.erlang.org/downloads

#### Bước 2: Cài RabbitMQ Server

Tải và cài RabbitMQ tại:  
https://www.rabbitmq.com/download.html

Khởi động RabbitMQ server và kiểm tra trạng thái bằng lệnh:  
```bash
rabbitmqctl status
```

Có thể kích hoạt giao diện quản lý web plugin:  
```bash
rabbitmq-plugins enable rabbitmq_management
```

Truy cập web quản lý tại: http://localhost:15672

---

## Bài tập 2: Code hệ thống đơn giản sử dụng RabbitMQ

### 1. Mô tả hệ thống

- Producer gửi message "Hello RabbitMQ!" vào queue tên "hello".
- Consumer nhận message và in ra màn hình.

### 2. Mã nguồn

#### Producer (send.py)
```python
import pika

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello RabbitMQ!')

print(" [x] Sent 'Hello RabbitMQ!'")
connection.close()
```

#### Consumer (receive.py)
```python
import pika

def callback(ch, method, properties, body):
    print(f" [x] Received {body.decode()}")

connection = pika.BlockingConnection(pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_consume(queue='hello',
                      on_message_callback=callback,
                      auto_ack=True)

print(' [*] Waiting for messages. To exit press CTRL+C')
channel.start_consuming()
```

### 3. Hướng dẫn chạy thử

- Mở terminal chạy:  
  ```bash
  python receive.py
  ```
- Mở terminal khác chạy:  
  ```bash
  python send.py
  ```
- Quan sát thông báo message được nhận và in ra.

---

## Bài tập 3: Tìm hiểu và demo thư viện RPC sử dụng JSON

### 1. Giới thiệu về RPC

RPC (Remote Procedure Call) cho phép client gọi hàm trên server từ xa như gọi hàm nội bộ. Đây là mô hình giao tiếp phổ biến trong hệ thống phân tán.

### 2. Các thư viện RPC hỗ trợ JSON

- `jsonrpcserver` và `jsonrpclib` dùng JSON-RPC.
- `grpc` của Google hỗ trợ Protobuf và JSON.
- `rpyc` cho Python.

### 3. Demo JSON-RPC sử dụng `jsonrpcserver`

#### Server (jsonrpc_server.py)
```python
from jsonrpcserver import method, serve

@method
def add(x: int, y: int) -> int:
    return x + y

if __name__ == '__main__':
    serve()
```

#### Client (jsonrpc_client.py)
```python
import requests
import json

url = "http://localhost:5000/"

payload = json.dumps({
    "jsonrpc": "2.0",
    "method": "add",
    "params": [5, 3],
    "id": 1,
})

headers = {'content-type': 'application/json'}

response = requests.post(url, data=payload, headers=headers).json()
print("Result:", response['result'])  # Kết quả: 8
```

### 4. Hướng dẫn chạy

- Cài thư viện:
```bash
pip install jsonrpcserver requests
```
- Chạy server:
```bash
python jsonrpc_server.py
```
- Chạy client:
```bash
python jsonrpc_client.py
```
- Kết quả trả về là tổng 5 + 3 = 8.
