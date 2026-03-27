# 4. Bitcoin Core for Raspberry Pi

<img src="assets/bitcoin-core.svg" alt="Bitcoin Core" width="600">

## 4.1 ดาวน์โหลดและตรวจสอบ Bitcoin Core

### 4.1.1 ดาวน์โหลด Bitcoin core ลงเครื่อง

```bash
wget https://bitcoincore.org/bin/bitcoin-core-30.2/bitcoin-30.2-aarch64-linux-gnu.tar.gz
```

> [github](https://github.com/bitcoin/bitcoin/)

### 4.1.2 ดาวน์โหลด signatures ล่าสุด

```bash
wget https://bitcoincore.org/bin/bitcoin-core-30.2/SHA256SUMS
```

```bash
wget https://bitcoincore.org/bin/bitcoin-core-30.2/SHA256SUMS.asc
```



### 4.1.3 นำเข้าคีย์ของผู้พัฒนาและตรวจสอบลายเซ็น

นำเข้าคีย์ของผู้พัฒนา

```bash
curl -s "https://api.github.com/repositories/355107265/contents/builder-keys" | grep download_url | grep -oE "https://[a-zA-Z0-9./-]+" | while read url; do curl -s "$url" | gpg --import; done
```
output
```
gpg: key 17565732E08E5E41: 29 signatures not checked due to missing keys
gpg: /home/admin/.gnupg/trustdb.gpg: trustdb created
gpg: key 17565732E08E5E41: public key "Andrew Chow <andrew@achow101.com>" imported
gpg: Total number processed: 1
gpg:               imported: 1
gpg: no ultimately trusted keys found
[...]
```

### 4.1.4 ตรวจสอบ Signature

```bash
gpg --verify SHA256SUMS.asc SHA256SUMS
```
output
```
gpg: Good signature from...
Primary key fingerprint:...
```

### 4.1.5 ตรวจสอบซอฟต์แวร์ว่าถูกต้องไหม

```bash
sha256sum --ignore-missing --check SHA256SUMS
```
output
```
bitcoin-30.0-aarch64-linux-gnu.tar.gz: OK
```



## 4.2 ติดตั้ง Bitcoin Core

### 4.2.1 แตกไฟล์ Bitcoin core

```bash
tar -xzvf bitcoin-30.2-aarch64-linux-gnu.tar.gz
```

### 4.2.2 ติดตั้ง Bitcoin core

```bash
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-30.2/bin/bitcoin-cli bitcoin-30.2/bin/bitcoind
```

### 4.2.3 ตรวจสอบเวอร์ชั่น

```bash
bitcoind --version
```

### 4.2.4 ทดสอบ Bitcoin core โดยสั่งให้ทำงาน

```bash
bitcoind -daemon
```

### 4.2.5 ตรวจสอบไฟล์ log 

```bash
tail -f ~/.bitcoin/debug.log
```

### 4.2.6 ตรวจสอบการซิงค์ของ Bitcoin core

```bash
bitcoin-cli getblockchaininfo
```

### 4.2.7 ตรวจสอบการเชื่อมต่อ Peers

```bash
bitcoin-cli getconnectioncount
```

### 4.2.8 ตรวจสอบ Bitcoin core เขื่อมต่อกับ network ไหนบ้าง

```bash
bitcoin-cli -netinfo
```

### 4.2.9 สั่ง Bitcoin core หยุดทำงาน

```bash
bitcoin-cli stop
```

### 4.2.10 ลบไฟล์ติดตั้งที่ไม่ใช้แล้ว

```bash
sudo rm -r bitcoin-30.2 bitcoin-30.2-aarch64-linux-gnu.tar.gz SHA256SUMS SHA256SUMS.asc
```

## 4.3 ตั้งค่า bitcoin.conf

ตัวไฟล์คอนฟิกจะตั้งค่าไว้ให้ใช้ Tor อย่างเดียวเพื่อเชื่อมต่อ peer อาจทำให้ซิงค์ช้าลง

### 4.3.1 สร้างไฟล์ bitcoin.conf

```bash
nano ~/.bitcoin/bitcoin.conf
```

ตัวอย่างไฟล์ bitcoin.conf

```
# Bitcoin Core
daemon=1
txindex=1
blockfilterindex=1
coinstatsindex=1

[main]
# RPC
server=1
rpcport=8332
rpcbind=0.0.0.0
rpcallowip=127.0.0.1
rpcallowip=10.0.0.0/8
rpcallowip=172.0.0.0/8
rpcallowip=192.0.0.0/8
rpcuser=bitcoin
rpcpassword=bitcoin

zmqpubrawblock=tcp://0.0.0.0:28332
zmqpubrawtx=tcp://0.0.0.0:28333
zmqpubhashblock=tcp://0.0.0.0:28334
whitelist=127.0.0.1

# Network
listen=1
onlynet=onion
onion=127.0.0.1:9050
proxy=127.0.0.1:9050
bind=127.0.0.1

i2p=1
onlynet=i2p
i2pacceptincoming=1
i2psam=127.0.0.1:7656

addnode=etehks5xyh32nyjldpyeckk3nwpanivqhrzhsoracwqjxtk5apgq.b32.i2p:0

# Performance
#dbcache=2048

[regtest]
rpcport=18443
rpcbind=127.0.0.1
tisten=1
server=1
onlynet=ipv4
rpcallowip=127.0.0.1
```

> [!NOTE]
> คุณสามารถตั้งค่า bitcoin.conf ด้วยตัวเองได้ที่
> https://jlopp.github.io/bitcoin-core-config-generator




## 4.4 สร้าง Systemd service

การสร้าง Systemd Service เพื่อให้ระบบสามารถเรียกใช้ bitcoin daemon โดยอัตโนมัติในพื้นหลังได้หลังปิด-เปิดเครื่อง

```bash
sudo nano /etc/systemd/system/bitcoind.service
```

configuration

```
[Unit]
Description=Bitcoin daemon
After=network.target

[Service]
ExecStart=/usr/local/bin/bitcoind -daemon \
                                  -pid=/run/bitcoind/bitcoind.pid \
                                  -conf=/home/username/.bitcoin/bitcoin.conf \
                                  -datadir=/home/username/.bitcoin 

ExecStop=/usr/local/bin/bitcoin-cli stop

# Make sure the config directory is readable by the service user
PermissionsStartOnly=true

# Process management
####################
Type=forking
Restart=on-failure
TimeoutStartSec=infinity
TimeoutStopSec=600


# Directory creation and permissions
####################################

User=uesermane
Group=uesermane

# /run/bitcoind
RuntimeDirectory=bitcoind
RuntimeDirectoryMode=0710

# /etc/bitcoin
ConfigurationDirectory=bitcoin
ConfigurationDirectoryMode=0710

# /var/lib/bitcoind
StateDirectory=bitcoind
StateDirectoryMode=0710

# Hardening measures
####################

# Provide a private /tmp and /var/tmp.
PrivateTmp=true

# Mount /usr, /boot/ and /etc read-only for the process.
ProtectSystem=full

# Disallow the process and all of its children to gain
# new privileges through execve().
NoNewPrivileges=true

# Use a new /dev namespace only populated with API pseudo devices
# such as /dev/null, /dev/zero and /dev/random.
PrivateDevices=true

# Deny the creation of writable and executable memory mappings.
MemoryDenyWriteExecute=true

[Install]
WantedBy=multi-user.target
```

> [!NOTE]
> อย่าลืมเปลี่ยน USERNAME ให้ตรงกับ user ของคุณ
> แหล่งอ้างอิง https://raw.githubusercontent.com/bitcoin/bitcoin/663f6cd9ddadeec30b27ec12f0f5ed49f3146cc9/contrib/init/bitcoind.service

## 4.5 เปิดใช้งาน Bitcoind

```bash
sudo systemctl enable bitcoind
```

```bash
sudo systemctl start bitcoind
```

ตรวจสอบว่า Bitcoind ทำงานไหม

```bash
sudo systemctl status bitcoind
```
output
```
$ sudo systemctl status bitcoind
● bitcoind.service - Bitcoin daemon
     Loaded: loaded (/etc/systemd/system/bitcoind.service; enabled; preset: enabled)
     Active: active (running) since Sat 2025-04-19 14:10:16 UTC; 23min ago
    Process: 812 ExecStart=/usr/local/bin/bitcoind -daemon -conf=/home/node/.bitcoin/bitcoin.conf -datadir=/home/node/.bitcoin (code=exited, >
   Main PID: 876 (bitcoind)
      Tasks: 26 (limit: 4552)
     Memory: 3.2G (peak: 3.2G swap: 252.0K swap peak: 252.0K)
        CPU: 12min 55.480s
     CGroup: /system.slice/bitcoind.service
             └─876 /usr/local/bin/bitcoind -daemon -conf=/home/node/.bitcoin/bitcoin.conf -datadir=/home/node/.bitcoin

Apr 19 14:10:14 node systemd[1]: Starting bitcoind.service - Bitcoin daemon...
Apr 19 14:10:16 node bitcoind[812]: Bitcoin Core starting
Apr 19 14:10:16 node systemd[1]: Started bitcoind.service - Bitcoin daemon.
```


### ทุกครั้งเมื่อมีการแก้ไขไฟล์ bitcoin.conf ควร restart bitcoind.service ทุกครั้ง

ใช้คำสั่ง restart

```bash
sudo systemctl restart bitcoind
```

หลังจาก restart เสร็จควรตรวจสอบสถานะทุกครั้ง

```bash
sudo systemctl status bitcoind
```

## หากต้องการ Upgrade VERSION

วิธีอัปเกรดจะคล้ายกับการติดตั้งในข้างต้น เราแค่เปลี่ยน "VERSION=x.xx" ตามที่เราต้องการได้เลย
ตรวจสอบ release ล่าสุดได้ที่ [GitHub](https://github.com/bitcoin/bitcoin/releases) ของ Bitcoin core 

> [!NOTE]
> เมื่อมีการอัปเกรดอาจมีการเปลี่ยนโครงสร้างของ Bitcoin Core โปรดอ่านรายละเอียดใน Notes please ทุกครั้งเมื่อเราต้องอัปเกรด

ตั้งค่า version environment ที่ต้องการ

```sh
VERSION=x.x
```

ดาวน์โหลด Bitcoin core เวอร์ชั่นใหม่

```bash
wget https://bitcoincore.org/bin/bitcoin-core-$VERSION/bitcoin-$VERSION-aarch64-linux-gnu.tar.gz
```

> [!NOTE]
> ตรวจสอบลายเซ็นของผู้พัฒนาตามขั้นตอนข้างต้น

แตกไฟล์ Bitcoin core

```bash
tar -xzvf bitcoin-$VERSION-aarch64-linux-gnu.tar.gz
```

ติดตั้ง Bitcoin core

```bash
sudo install -m 0755 -o root -g root -t /usr/local/bin bitcoin-$VERSION/bin/bitcoin-cli bitcoin-$VERSION/bin/bitcoind
```

ตรวจสอบเวอร์ชั่นใหม่

```bash
bitcoind-cli --version
```

สั่ง restart Bitcoin core เพื่อใช้เวอร์ชั่นใหม่

```bash
sudo systemctl restart bitcoind
```

----

[Back to info >>](README.md)

