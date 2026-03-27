# 1. ตั้งค่า Firewall for Bitcoin Node

## 1.1 อัปเดตระบบและติดตั้งเครื่องมือพื้นฐาน

```bash
sudo apt update && sudo apt upgrade -y
```

```bash
sudo apt install wget curl gnupg tar ufw -y
```

## 1.2 ตั้งค่า Firewall
เปิด Port เท่าที่จำเป็นต้องใช้

```bash
sudo ufw allow 22/tcp comment 'ssh'
sudo ufw allow 9050/tcp comment 'Tor SOCKS'
sudo ufw allow 9051/tcp comment 'Tor Control'
sudo ufw allow 8333/tcp comment 'Bitcoin core peer'
sudo ufw allow 8332/tcp comment 'Bitcoin core RPC'
sudo ufw allow 8888/tcp comment 'Mempool'
sudo ufw allow 50001/tcp comment 'Electrs TCP'
sudo ufw allow 50002/tcp comment 'Electrs SSL'
sudo ufw allow 7070/tcp comment 'i2pd WebConsole'
sudo ufw allow 7656/tcp comment 'i2pd SAM'
```


เปิด Firewall
```bash
sudo ufw enable
```
ตรวจสอบ Port Firewall
```bash
sudo ufw status
```

### วิธีจัดการ Firewall
หากต้องการเปิด Port ใช้คำสั่งนี้
```bash
sudo ufw allow xx comment 'xx'
```

ตรวจสอบ Port Firewall ว่าเราเปิดใช้อะไรบ้าง
```bash
sudo ufw status
```

หากต้องการการลบ Port ที่ไม่ต้องการใช้ควรเริ่มตามนี้
ดูหมายเลข Port ก่อน
```bash
sudo ufw status numbered
```

แล้วลบตามหมายเลข เช่น:
```bash
sudo ufw delete 3
```


----
[Tor >>](tor.md)