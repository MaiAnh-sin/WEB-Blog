---
title: "Distributed System notes 2"
date: "2025-05-25"
updated: "2025-05-25"
categories:
  - "sveltekit"
  - "markdown"
coverImage: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcT5uJDmZ9pT8h6BnlkTT5EGBrdJyxtKMYpGwQ&s"
coverWidth: 16
coverHeight: 9
excerpt: Check out how heading links work with this starter in this post.
---
## Bài Tập 1: Diễn Giải Quy Trình Duyệt Trang Web www.motvidu.com

### 1. Tổng Quan Quy Trình Truy Cập Trang Web
Khi người dùng nhập vào địa chỉ www.motvidu.com trong trình duyệt, trình duyệt sẽ bắt đầu quy trình truy cập gồm các bước:

1. **Kiểm tra bộ nhớ đệm DNS của trình duyệt:** Trình duyệt kiểm tra xem định danh IP tương ứng với www.motvidu.com đã được lưu trong cache hay chưa.

2. **Tra cứu DNS (DNS Lookup):** Nếu không có trong cache, trình duyệt gửi yêu cầu tra cứu DNS tới recursive DNS resolver (thường do ISP cấp).

3. **Resolving DNS:** Resolver tra cứu theo trình tự:
    - Root DNS server
    - Top-level domain (TLD) server (.com)
    - Authoritative DNS server (chứa bản ghi cho motvidu.com)

4. **Trả về Địa Chỉ IP:** Resolver nhận được IP và trả về trình duyệt, đồng thời cache kết quả để sử dụng lần sau (DNS caching).

5. **Kết nối đến Web Server:** Trình duyệt sử dụng IP vừa nhận để thiết lập kết nối TCP (thường qua HTTP/HTTPS) tới server và gửi HTTP request.

6. **Trả về Nội Dung Trang Web:** Web server xử lý và gửi về trang HTML, CSS, JS,... cho trình duyệt.

### 2. Caching DNS
DNS caching là việc lưu tạm kết quả tra cứu DNS trong:
- Trình duyệt (Browser Cache)
- Hệ điều hành (OS Cache)
- DNS Resolver (ISP Cache)

Giúp giảm độ trễ và tải cho hệ thống DNS toàn cầu.

### 3. Nguồn Tài Liệu
- [How DNS works - Cloudflare](https://www.cloudflare.com/learning/dns/what-is-dns/)
- [DNS lookup explained - MDN Web Docs](https://developer.mozilla.org/en-US/docs/Glossary/DNS_lookup)
- [DNS caching - Wikipedia](https://en.wikipedia.org/wiki/Domain_Name_System#Caching_and_time-to-live)

---

## Bài Tập 2: Thuật Toán Chord

### 1. Giới Thiệu Chord
Chord là một giao thức (protocol) dùng trong hệ thống phân tán peer-to-peer để định vị nơi lưu trữ dữ liệu. Các node và keys được ánh xạ theo một vòng hash (thường là modulo $2^m$).

### 2. Thuật Toán Chord (Python)
```python
import hashlib

def hash_key(key, m=5):
    sha1 = hashlib.sha1(key.encode()).hexdigest()
    return int(sha1, 16) % (2**m)

class Node:
    def __init__(self, id):
        self.id = id
        self.successor = self

    def find_successor(self, key_id):
        if self.id < key_id <= self.successor.id:
            return self.successor
        else:
            return self.successor.find_successor(key_id)

    def join(self, other_node):
        if other_node:
            self.successor = other_node.find_successor(self.id)

    def __repr__(self):
        return f"Node({self.id})"
```

### 3. Test Case
```python
m = 5  # Định nghĩa vòng hash 2^5 = 32

# Tạo các node
node1 = Node(hash_key("node1"))
node2 = Node(hash_key("node2"))
node3 = Node(hash_key("node3"))

# Tham gia vào vòng
node1.join(None)
node2.join(node1)
node3.join(node2)

# Test key
key = "myfile.txt"
key_id = hash_key(key)
responsible_node = node1.find_successor(key_id)

print(f"Key '{key}' có id {key_id} được lưu tại: {responsible_node}")
```

### 4. Kết Quả Mô Phỏng
Ví dụ:
```
node1 id = 9
node2 id = 15
node3 id = 25
key 'myfile.txt' id = 17
=> Node chịu trách nhiệm: Node(25)
```

### 5. Nguồn Tài Liệu
- Stoica et al. "Chord: A Scalable Peer-to-peer Lookup Protocol for Internet Applications"
- https://pdos.csail.mit.edu/papers/chord:sigcomm01/chord_sigcomm.pdf
- https://en.wikipedia.org/wiki/Chord_(peer-to-peer)
