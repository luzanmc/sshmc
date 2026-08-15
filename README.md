🖥️ Windows VM → Termius → Minecraft Server

Hướng dẫn setup Windows VM để điều khiển bằng Termius Android và chạy Minecraft Server.

«Phù hợp với Windows Server/Windows VM có quyền Administrator.»

---

📋 1. Cài OpenSSH Server

Mở PowerShell bằng quyền Administrator:

Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.1.0.0

Nếu lệnh trên không hoạt động, thử:

Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.1.0.0

---

🚀 2. Bật SSH

Start-Service sshd

Cho SSH tự khởi động cùng Windows:

Set-Service -Name sshd -StartupType Automatic

Kiểm tra:

Get-Service sshd

Kết quả cần có:

Status   Name
Running  sshd

---

🔥 3. Mở Firewall port 22

New-NetFirewallRule -Name "OpenSSH-Server-In-TCP" -DisplayName "OpenSSH Server (TCP 22)" -Direction Inbound -Protocol TCP -LocalPort 22 -Action Allow

Kiểm tra:

Get-NetFirewallRule -Name "OpenSSH-Server-In-TCP"

---

🌐 4. Lấy IP Windows VM

Chạy:

ipconfig

Nếu kết nối từ Android qua Internet

Sử dụng Public IP của VM.

Không dùng IP nội bộ như:

192.168.x.x
10.x.x.x
172.16.x.x

---

📱 5. Kết nối bằng Termius Android

Mở Termius:

Hosts
→ +
→ New Host

Điền:

Alias: Windows VM
Hostname: PUBLIC_IP_VM
Port: 22
Username: WINDOWS_USERNAME
Password: WINDOWS_PASSWORD

Ví dụ:

Hostname: 34.xxx.xxx.xxx
Port: 22
Username: Administrator
Password: ********

Sau đó:

Save → Connect

Nếu Termius hỏi xác nhận fingerprint SSH thì chọn Accept nếu bạn đang kết nối đúng VM của mình.

---

☁️ 6. Nếu dùng Google Cloud VM

Nếu Windows VM chạy trên Google Cloud, phải mở port "22" trong Google Cloud Firewall.

Cần cho phép:

Protocol: TCP
Port: 22

Ngoài Windows Firewall, Google Cloud Firewall cũng phải cho phép kết nối.

---

☕ 7. Cài Java 21

Sau khi SSH vào Windows VM, kiểm tra:

java -version

Nếu đã có Java 21 thì bỏ qua bước này.

Kiểm tra:

java -version

Cần thấy phiên bản dạng:

21.x.x

---

⛏️ 8. Tạo thư mục Minecraft Server

mkdir C:\Minecraft
cd C:\Minecraft

---

📦 9. Tải Paper

Paper là server Minecraft tối ưu dành cho Java Edition.

Trang tải chính thức:

https://papermc.io/downloads/paper

Tải file ".jar" phù hợp với phiên bản Minecraft.

Đổi tên file thành:

server.jar

Đặt vào:

C:\Minecraft\server.jar

---

▶️ 10. Chạy server lần đầu

cd C:\Minecraft
java -Xms1G -Xmx4G -jar server.jar nogui

Server sẽ tạo:

eula.txt

---

📜 11. Đồng ý EULA

Mở:

notepad C:\Minecraft\eula.txt

Đổi:

eula=false

thành:

eula=true

Lưu file.

---

🚀 12. Chạy Minecraft Server

cd C:\Minecraft
java -Xms1G -Xmx4G -jar server.jar nogui

Nếu thấy:

Done (...)! For help, type "help"

server đã chạy thành công.

---

🔥 13. Mở port Minecraft

Mở PowerShell Administrator:

New-NetFirewallRule -Name "Minecraft-25565" -DisplayName "Minecraft Server" -Direction Inbound -Protocol TCP -LocalPort 25565 -Action Allow

Nếu dùng Minecraft Bedrock/Geyser UDP thì có thể cần thêm:

New-NetFirewallRule -Name "Minecraft-25565-UDP" -DisplayName "Minecraft Server UDP" -Direction Inbound -Protocol UDP -LocalPort 25565 -Action Allow

---

☁️ 14. Nếu dùng Google Cloud

Ngoài Windows Firewall, cần mở:

TCP 25565

trong Google Cloud VPC Firewall Rules.

Nếu dùng Geyser/Bedrock:

UDP 25565

---

🎮 15. Kết nối Minecraft

Java Edition:

PUBLIC_IP:25565

Ví dụ:

34.xxx.xxx.xxx:25565

Nếu server sử dụng port mặc định "25565", thường chỉ cần nhập:

34.xxx.xxx.xxx

---

💾 16. Chạy server bằng file BAT

Tạo:

C:\Minecraft\start.bat

Nội dung:

@echo off
cd /d C:\Minecraft
java -Xms1G -Xmx4G -jar server.jar nogui
pause

Sau này chỉ cần mở:

start.bat

để chạy server.

---

⚙️ 17. Chỉnh RAM

Ví dụ VPS có 4GB RAM:

java -Xms1G -Xmx3G -jar server.jar nogui

VPS có 8GB RAM:

java -Xms2G -Xmx6G -jar server.jar nogui

Không nên cấp toàn bộ RAM VPS cho Minecraft vì Windows và các chương trình khác cũng cần RAM.

---

🛑 18. Tắt server

Trong console Minecraft:

stop

Không nên đóng cửa sổ hoặc kill Java đột ngột vì có thể gây mất dữ liệu.

---

🔧 19. Các lệnh kiểm tra SSH

Kiểm tra SSH:

Get-Service sshd

Khởi động SSH:

Start-Service sshd

Restart SSH:

Restart-Service sshd

Dừng SSH:

Stop-Service sshd

Cho SSH tự chạy khi Windows khởi động:

Set-Service -Name sshd -StartupType Automatic

---

🔍 20. Kiểm tra port SSH

Get-NetTCPConnection -LocalPort 22

Nếu SSH đang lắng nghe port 22 thì sẽ thấy kết nối/listener tương ứng.

---

🔍 21. Kiểm tra port Minecraft

Get-NetTCPConnection -LocalPort 25565

Nếu server đang chạy và lắng nghe port thì sẽ có kết quả.

---

📌 Tóm tắt

Windows VM
    │
    ├── OpenSSH Server
    │       └── Port 22
    │
    ├── Java 21
    │
    └── Paper Minecraft
            └── Port 25565
                    │
                    ↓
             Minecraft Client

🔑 Các port quan trọng

Dịch vụ| Port| Protocol
SSH / Termius| 22| TCP
Minecraft Java| 25565| TCP
Geyser Bedrock| 25565| UDP

---

⚠️ Lưu ý bảo mật

Không đăng công khai:

- Password Windows
- SSH private key
- API key
- Token
- Service Account JSON
- Database password

Public GitHub chỉ nên chứa hướng dẫn và lệnh setup, không chứa thông tin đăng nhập VPS.
