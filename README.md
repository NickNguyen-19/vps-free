# vps-test


---

### 📄 `README.md` – Hướng Dẫn Cài Đặt & Chạy Windows Docker Container

# 🚀 Hướng dẫn cài đặt Windows 10 trong Docker với Dockurr

> Cài đặt và chạy nhanh Windows 10 trong Docker bằng image `dockurr/windows`. Hướng dẫn này dành cho Ubuntu/Debian Linux (bao gồm WSL2 & VPS).

---

## 🧰 Yêu cầu hệ thống

- Ubuntu 20.04+ / Debian-based Linux
- Quyền `sudo` hoặc `root`
- Hỗ trợ phần cứng ảo hóa (VT-x / AMD-V)
- KVM phải được kích hoạt (`/dev/kvm`)

---

## 🪜 Các bước cài đặt chi tiết

### 1. Cài đặt thư viện

```bash
sudo apt update
sudo apt install docker.io docker-compose
````

### 2. Cập nhật hệ thống

```bash
sudo apt update
```

### 3. Cài đặt Docker & Docker Compose

```bash
sudo apt install docker.io docker-compose -y
```

> ✅ **Kiểm tra** sau khi cài:

```bash
docker -v
docker compose version
```

---

## 📂 Cấu trúc file docker-compose

Tạo file `windows10.yml` với nội dung:

```yaml
version: "3.8"
services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "10"
      USERNAME: "MASTER"
      PASSWORD: "admin@123"
      RAM_SIZE: "8G"
      CPU_CORES: "4"
      DISK_SIZE: "600G"
      DISK2_SIZE: "200G"
    devices:
      - /dev/kvm
      - /dev/net/tun
    cap_add:
      - NET_ADMIN
    ports:
      - "8006:8006"
      - "3389:3389/tcp"
      - "3389:3389/udp"
    stop_grace_period: 2m
```

---

## ▶️ Chạy Windows trong Docker

```bash
sudo docker-compose -f windows10.yml up -d
```

> 📌 Nếu bạn gặp lỗi **`no configuration file provided`**, hãy chắc chắn file tên đúng (`windows10.yml`) và sử dụng đúng cờ `-f`.

---

## 🐛 Các lỗi thường gặp & cách khắc phục

| Lỗi                                          | Cách sửa                                                             |
| -------------------------------------------- | -------------------------------------------------------------------- |
| `no configuration file provided`             | Thêm `-f windows10.yml` vào lệnh                                     |
| `Cannot access /dev/kvm`                     | Kiểm tra xem KVM đã bật chưa: `ls -la /dev/kvm`                      |
| `permission denied on /dev/kvm`              | Thêm user vào nhóm `kvm`: `sudo usermod -aG kvm $USER && newgrp kvm` |
| `image not found`                            | Kiểm tra kết nối mạng, thử `docker pull dockurr/windows` trước       |
| Không lưu được dữ liệu ổ đĩa sau khi restart | Mount volume hoặc sử dụng `named volumes`                            |

---

## 🌐 Tốc độ mạng trong VPS

Bạn có thể test tốc độ mạng tại:
👉 [https://fast.com](https://fast.com)

---

## 📎 Tham khảo

* [https://github.com/dockur/windows](https://github.com/dockur/windows)
* [https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)
* [https://docs.docker.com/compose/](https://docs.docker.com/compose/)

---

## 🧼 Gỡ cài đặt (nếu cần)

```bash
sudo docker-compose -f windows10.yml down
sudo apt remove docker.io docker-compose
```

---

## ☕ Liên hệ / hỗ trợ

Nếu bạn gặp lỗi không xử lý được, hãy mở issue tại repo hoặc gửi email kỹ thuật.


---

Bạn muốn mình lưu hướng dẫn này thành file `README.md` sẵn trong thư mục hiện tại không?
