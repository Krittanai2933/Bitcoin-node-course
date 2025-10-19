
# เมื่อคุณนำโหนดกลับไปต่อเองที่บ้านแล้วต้องทำอะไรบ้าง

### 1. ต่อสายแลนเข้ากับ Rasbery pi

### 2. ต่อไฟเลี้ยงให้กับ Rasbery pi

### 3. เมื่อ Rasbery pi ทำงานแล้วให้ Shell เข้าเข้าไปตรวจสอบสถานะ service ต่าง ๆ

วิธี shall เข้าดครื่อง Rasbery pi อยู่ 2 วิธีหลัก ๆ

1. คลิกขวาที่หน้าจอและดลือก open to Terminal จากนั้นให้ลองใช้คำสั่ง ping ก่อนว่าเจอ Hostname ที่เราตั้งไว้ไหม

คำสั่ง ping
```bash
ping Hostname
```
คำสั่ง shall ผ่าน terminal ตรงๆ
```bash
ssh username@hostname or ip
```

2. เปิดโปรแกรม MobaXterm และก็เลือก sessions ที่บันทึกไว้

> สำหรับคนที่ใช้ mac เปิดเข้า Terminus

### 4. ตรวจสอบ Bitcoin Core ยังทำงานปกติ

* ตรวจสอบว่า Bitcoind ทำงานไหม

```bash
sudo systemctl status bitcoind
```
Expected output:
```
satoshi@node:~$ sudo systemctl status bitcoind
● bitcoind.service - Bitcoin daemon
     Loaded: loaded (/etc/systemd/system/bitcoind.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-10-15 07:26:12 +07; 11h ago
    Process: 844 ExecStart=/usr/local/bin/bitcoind -daemon -pid=/run/bitcoind/bitcoind.pid -conf=/home/satoshi/.bitcoin/bitcoin.conf -datadir=/home/satoshi/.bitcoin (code=exited, status=0/SUCCESS)
   Main PID: 950 (bitcoind)
      Tasks: 30 (limit: 9067)
     Memory: 6.5G (peak: 7.0G)
        CPU: 8min 15.214s
     CGroup: /system.slice/bitcoind.service
             └─950 /usr/local/bin/bitcoind -daemon -pid=/run/bitcoind/bitcoind.pid -conf=/home/satoshi/.bitcoin/bitcoin.conf -datadir=/home/satoshi/.bitcoin

```


* ตรวจสอบไฟล์ log ว่าเจอปัญหาอะไรหรือป่าว

```bash
tail -f ~/.bitcoin/debug.log
```
Expected output:
```
satoshi@node:~$ tail -f ~/.bitcoin/debug.log
2025-10-15T11:32:04Z Saw new header hash=00000000000000000000593b0dd771c399796e6a57f92734d2f3f31a13eeda64 height=919190
2025-10-15T11:32:04Z Saw new cmpctblock header hash=00000000000000000000593b0dd771c399796e6a57f92734d2f3f31a13eeda64 peer=8
2025-10-15T11:32:05Z UpdateTip: new best=00000000000000000000593b0dd771c399796e6a57f92734d2f3f31a13eeda64 height=919190 version=0x28280000 log2_work=95.880893 tx=1256769605 date='2025-10-15T11:31:49Z' progress=1.000000 cache=124.0MiB(941905txo)
2025-10-15T11:32:08Z New block-relay-only v1 peer connected: version: 70016, blocks=919190, peer=390
2025-10-15T11:37:44Z Flushed fee estimates to fee_estimates.dat.
2025-10-15T11:38:17Z New block-relay-only v2 peer connected: version: 70016, blocks=919190, peer=393
2025-10-15T11:40:53Z Saw new header hash=00000000000000000000302d5c39224b5e6d78fc9446cc7628ffe16611210496 height=919191
2025-10-15T11:40:57Z UpdateTip: new best=00000000000000000000302d5c39224b5e6d78fc9446cc7628ffe16611210496 height=919191 version=0x2adaa000 log2_work=95.880906 tx=1256773402 date='2025-10-15T11:40:37Z' progress=1.000000 cache=124.5MiB(947710txo)
2025-10-15T11:41:16Z New block-relay-only v2 peer connected: version: 70016, blocks=919191, peer=396
2025-10-15T11:44:56Z New block-relay-only v1 peer connected: version: 70016, blocks=919191, peer=400
```


* ตรวจสอบการซิงค์ของ Bitcoin core

```bash
bitcoin-cli getblockchaininfo
```
Expected output:
```
satoshi@node:~$ bitcoin-cli getblockchaininfo
{
  "chain": "main",
  "blocks": 919191,
  "headers": 919191,
  "bestblockhash": "00000000000000000000302d5c39224b5e6d78fc9446cc7628ffe16611210496",
  "bits": "1701ddb4",
  "target": "00000000000000000001ddb40000000000000000000000000000000000000000",
  "difficulty": 150839487445890.5,
  "time": 1760528437,
  "mediantime": 1760526352,
  "verificationprogress": 0.9999998161670474,
  "initialblockdownload": false,
  "chainwork": "0000000000000000000000000000000000000000ebb749f5384ff4e16cfba2d8",
  "size_on_disk": 789262913183,
  "pruned": false,
  "warnings": [
  ]
}
```


* ตรวจสอบการเชื่อมต่อ Peers ว่ามีอยู่เท่าไหร่

```bash
bitcoin-cli getconnectioncount
```
Expected output:
```
satoshi@node:~$ bitcoin-cli getconnectioncount
14
```


* ตรวจสอบ Bitcoin core เขื่อมต่อกับ network ไหนบ้าง

```bash
bitcoin-cli -netinfo
```
Expected output:
```
satoshi@node:~$ bitcoin-cli -netinfo
Bitcoin Core client v29.0.0 - server 70016/Satoshi:29.0.0/

        onion     npr   total   block
in          0       4       4
out        11       0      11       3
total      11       4      15

Local addresses
Address.onion     port   8333    score      4
```

### 5. ตรวจสอบ Electrum server ยังทำงานปกติ


* ตรวจสอบว่า Electrs ทำงานไหม

```bash
sudo systemctl status electrs
```
Expected output:
```
satoshi@node:~$ sudo systemctl status electrs
● electrs.service - electrs
     Loaded: loaded (/etc/systemd/system/electrs.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-10-15 07:26:12 +07; 11h ago
   Main PID: 979 (electrs)
      Tasks: 20 (limit: 9067)
     Memory: 246.8M (peak: 632.1M)
        CPU: 45.892s
     CGroup: /system.slice/electrs.service
             └─979 /home/satoshi/electrs/target/release/electrs
```

### 6. ตรวจสอบ BTC-RPC-Explorer ยังทำงานปกติ


* ตรวจสอบว่า BTC-RPC-Explorer ทำงานไหม

```bash
sudo systemctl status btcrpcexplorer
```
Expected output:
```
satoshi@node:~$ sudo systemctl status btcrpcexplorer
● btcrpcexplorer.service - BTC RPC Explorer
     Loaded: loaded (/etc/systemd/system/btcrpcexplorer.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-10-15 07:26:12 +07; 11h ago
   Main PID: 986 (npm start)
      Tasks: 23 (limit: 9067)
     Memory: 104.7M (peak: 225.7M)
        CPU: 6.156s
     CGroup: /system.slice/btcrpcexplorer.service
             ├─ 986 "npm start"
             ├─1068 sh -c "node ./bin/www"
             └─1069 node ./bin/www

```

* เข้าหน้าเว็บไซค์ของ BTC-RPC-Explorer ที่เราติดตั้งโดยพิมพ์ใน Browser ที่คุณใช้  

```bas
http://ip:3002
```
เช่น
```bash
http://node.local:3002
```
หรือ
```bash
http://192.168.1.xx:3002
```

> กรณีที่ตั้ง password ไว้อย่าลืมใส่ให้ถูกต้องถึงจะเข้าได้


### 6. ทดลองเชื่อมต่อโหนดเข้ากับ Wallet อย่างเช่น Sparrow

* เชื่อมต่อด้วย RPC จาก Bitcoin core

Open sparrow → File → Preferences.. → server → bitcoin core

```
Bitcoin Core RPC
----
URL: ip or address.onion  8332
Authentication: User/Pass
User/Pass: User  Pass

Test Connection
----
Connected to Cormorant 2.0.0 on protocol version 1.4
Batched RPC enabled.
Server Banner: Cormorant 2.0.0
/Satoshi:29.0.0/

```

* เชื่อมต่อด้วย Electrum server

Open sparrow → File → Preferences.. → server → Private Electrum

```
Private Electrum Server
----
URL: ip or address.onion  50001

Test Connection
----
Connected to electrs/0.10.10 on protocol version 1.4
Batched RPC enabled.
Server Banner: Welcome to electrs 0.10.10 (Electrum Rust Server)!

```

> หากอย่าปรับเปลี่ยน config สามารถกลับไปอ่านเอกสารประกอบการสอนได้ หรือหากเจอปัญหาสามารถพูดคุยได้ที่ Discord