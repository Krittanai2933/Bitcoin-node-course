# 3. ติดตั้ง i2pd สำหรับ Bitcoin Node

<img src="assets/i2p-Network-Layer.png" alt="i2p" width="200">

## 3.1 ติดตั้ง dependencies

```bash
sudo apt update
```

```bash
sudo apt install -y curl gnupg lsb-release
```

## 3.2 เพิ่ม i2pd repository

```bash
wget -q -O - https://repo.i2pd.xyz/.help/add_repo | sudo bash
```

## 3.3 อัปเดต package list

```bash
sudo apt update
```

## 3.4 ติดตั้ง i2pd

```bash
sudo apt install -y i2pd
```

## 3.5 ตั้งค่า i2pd

สำรองไฟล์ config เดิม

```bash
sudo cp /etc/i2pd/i2pd.conf /etc/i2pd/i2pd.conf.bak
```

## 3.6 แก้ config

```bash
sudo nano /etc/i2pd/i2pd.conf
```

ใส่เนื้อหานี้:
```
log = file
loglevel = warn

ipv4 = true
ipv6 = true

[sam]
enabled = true
address = 127.0.0.1
port = 7656

[http]
enabled = true
address = 127.0.0.1
port = 7070
```

## 3.7 ตั้ง permission

```bash
sudo chown -R root:i2pd /etc/i2pd
```

```bash
sudo chmod -R 0750 /etc/i2pd
```

## 3.8 เปิดใช้งาน i2pd

```bash
sudo systemctl enable i2pd
```

```bash
sudo systemctl start i2pd
```

## 3.9 เช็คสถานะ

```bash
systemctl status i2pd
```

หรือ:

```bash
journalctl -u i2pd -n 50 --no-pager
```

---- 

[<< Tor](tor.md)  ||  [Bitcoin Core >>](bitcoin_node.md)
