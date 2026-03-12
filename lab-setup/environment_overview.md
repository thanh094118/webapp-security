# BÀI THỰC HÀNH AN NINH MẠNG: THIẾT LẬP HẠ TẦNG ẢO HÓA TRÊN VIRTUALBOX

## 📖 Giới thiệu chung (Introduction)

Tài liệu này cung cấp hướng dẫn từng bước để thiết lập một môi trường thực hành an ninh mạng hoàn chỉnh và an toàn trên nền tảng ảo hóa **VirtualBox**. Thông qua việc xây dựng hạ tầng này, học viên có thể triển khai các kịch bản tấn công - phòng thủ mạng thực tế giữa các máy ảo (VM) mà không làm ảnh hưởng đến máy tính vật lý (Host).

### 💻 Môi trường Thực hành
* **Hệ điều hành Host:** Windows hoặc Linux.
* **Hệ điều hành Guest (Máy ảo):** Xubuntu 22.04 LTS Desktop.
* **Công cụ Ảo hóa:** Oracle VM VirtualBox (Phiên bản 7.2.4).
* **Công cụ bảo mật tích hợp (trên máy Attacker):** Nmap, Hping3, BurpSuite, Wireshark.

### 🎯 Yêu cầu Chuẩn bị
* Máy tính cá nhân có khả năng chạy ảo hóa.
* Hiểu biết cơ bản về cách thức hoạt động của VirtualBox ([Tham khảo tại trang chủ VirtualBox](https://www.virtualbox.org)).
* Các file ổ cứng ảo (`.vdi`) của máy Attacker và Victim đã được tải về và giải nén.

---

## ⚙️ Phần 1: Cài đặt và Cấu hình VirtualBox

*(Bỏ qua phần này nếu máy bạn đã cài đặt sẵn VirtualBox).*

1.  **Tải phần mềm:** Truy cập trang chủ để tải VirtualBox phiên bản 7.2.4 và gói mở rộng **VirtualBox Extension Pack**.
2.  **Cài đặt:** Chạy file `.exe` và tuân thủ các bước Next mặc định. 
    * *Lưu ý:* Việc cài đặt sẽ làm gián đoạn kết nối mạng trong giây lát (cảnh báo *Network Disconnection*) và yêu cầu cài thêm Python Core/Win32API. Hãy chọn **Yes** để đồng ý.
3.  **Cài đặt Extension Pack:** Mở VirtualBox > Đi tới **File** > **Tools** > **Extensions** > Thêm file `.vbox-extpack` đã tải > Kéo đọc giấy phép và chọn **I Agree**.

---

## 🌐 Phần 2: Thiết lập Hạ tầng Mạng Ảo

Để các máy ảo có thể giao tiếp với nhau và với máy vật lý, chúng ta cần cấu hình mạng cho VirtualBox. 



**Bảng tóm tắt các chế độ mạng trong VirtualBox:**

| Chế độ (Mode) | VM → Host | VM ← Host | VM1 ↔ VM2 | VM → Internet | VM ← Internet |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Host-only** | + | + | + | – | – |
| **Internal** | – | – | + | – | – |
| **Bridged** | + | + | + | + | + |
| **NAT** | + | Port forward | – | + | Port forward |
| **NATservice** | + | Port forward | + | + | Port forward |

### Cấu hình dải mạng Host-Only (Mạng giao tiếp cục bộ)
Tạo mạng Host-Only để máy Attacker và Victim nhìn thấy nhau và kết nối được với Host:
1.  Vào **File** > **Tools** > **Network** > tab **Host-Only Networks**.
2.  Chọn **Create** để tạo dải mạng mới (ví dụ: *VirtualBox Host-Only Ethernet Adapter #2*).
3.  Cấu hình Adapter (Manual):
    * **IPv4 Address:** `192.168.117.1`
    * **IPv4 Network Mask:** `255.255.255.0`
4.  Cấu hình DHCP Server:
    * **Server Address:** `192.168.117.2`
    * **Lower Bound:** `192.168.117.100` | **Upper Bound:** `192.168.117.200`
5.  *Kiểm tra:* Mở CMD trên Windows, gõ lệnh `ping 192.168.117.1` để xác nhận thành công.

### Cấu hình dải mạng NAT (Mạng kết nối Internet)
Tạo mạng NAT nếu cần máy ảo truy cập Internet để tải công cụ:
1.  Chuyển sang tab **NAT Networks** > **Create**.
2.  Dải mạng mặc định tạo ra sẽ là `10.0.2.0/24`. Đảm bảo đã tích chọn **Enable DHCP**.

---

## 🖥️ Phần 3: Triển khai Máy Ảo lên Hạ tầng

Kịch bản triển khai bao gồm 2 máy ảo:
* **Máy Attacker:** IP tĩnh `192.168.117.10/24`
* **Máy Server (Nạn nhân):** IP tĩnh `192.168.117.13/24`



### 3.1 Nạp ổ cứng ảo (Tạo VM)
1.  Trên VirtualBox, chọn **New**.
2.  Điền thông tin cơ bản: Tên (`vm-server`), OS (Linux), Version (Ubuntu 64-bit).
3.  Phân bổ tài nguyên: **RAM 2048 MB**, **CPU 1 core**.
4.  Tại mục ổ cứng, chọn **Use an Existing Virtual Hard Disk File** > Chọn nút Add và duyệt đến file `vm-server.vdi`.

> **Khắc phục lỗi UUID (Nếu có):**
> Nếu gặp lỗi trùng UUID khi thêm file `.vdi`, hãy mở CMD (quyền Admin) và gõ:
> ```cmd
> cd %programfiles%\Oracle\VirtualBox
> VBoxManage internalcommands sethduuid "đường_dẫn_tới_file.vdi"
> ```

### 3.2 Gắn Network Adapter
1.  Chuột phải vào VM vừa tạo > **Settings** > **Network**.
2.  Tại Adapter 1: Chọn **Attached to: Host-only Adapter**.
3.  Name: Chọn đúng tên card mạng `VirtualBox Host-Only Ethernet Adapter #2` đã tạo ở Phần 2.

### 3.3 Khởi động và Cấu hình IP tĩnh
1.  Khởi động VM và đăng nhập (Tài khoản: `bkcs` / Mật khẩu: `bkcs`).
2.  Chỉnh IP tĩnh qua giao diện GUI của Xubuntu (Edit Connections > IPv4 Settings):
    * **Method:** Manual
    * **Address:** `192.168.117.13` (Cho máy nạn nhân)
    * **Netmask:** `24`
    * **Gateway:** `192.168.117.1`
3.  Lưu lại, mở Terminal gõ lệnh `reboot`.
4.  Sau khi máy khởi động lại, kiểm tra IP bằng lệnh `$ ip a` hoặc `$ ifconfig`.

### 3.4 Kiểm tra kết nối
Từ máy vật lý hoặc máy Attacker, mở Terminal/CMD và ping tới máy nạn nhân:
```bash
ping 192.168.117.13
ssh bkcs@192.168.117.13