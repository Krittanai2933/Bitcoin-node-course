# 2. Tor

<img src="assets/tor.png" alt="Tor" width="200">

## ติดตั้งและตั้งค่า Tor สำหรับ Bitcoin RPC

### 2.1 ติดตั้ง tor

```bash
sudo apt install tor -y
```

### 2.2 แก้ไข Tor configuration

```bash
sudo nano /etc/tor/torrc
```

### 2.3 เพิ่มค่า Tor configuration

```
# ControlPort & Authentication
ControlPort 9051
CookieAuthentication 1
CookieAuthFileGroupReadable 1

# Bitcoin RPC
HiddenServiceDir /var/lib/tor/bitcoinrpc
HiddenServiceVersion 3
HiddenServicePort 8332 127.0.0.1:8332
HiddenServiceEnableIntroDoSDefense 1

# Electrs
HiddenServiceDir /var/lib/tor/electrs
HiddenServiceVersion 3
HiddenServicePort 50001 127.0.0.1:50001
HiddenServiceEnableIntroDoSDefense 1

# Mempool
HiddenServiceDir /var/lib/tor/mempool
HiddenServiceVersion 3
HiddenServicePort 8888 127.0.0.1:8888
HiddenServiceEnableIntroDoSDefense 1
```

### 2.4 สร้าง Directory สำหรับ Hidden Service

```bash
sudo mkdir -p /var/lib/tor/bitcoinrpc
sudo mkdir -p /var/lib/tor/electrs
sudo mkdir -p /var/lib/tor/mempool
```

### 2.5 เปลี่ยน Ownership และ Permissions ของ Directory

```bash
sudo chown -R debian-tor:debian-tor /var/lib/tor/bitcoinrpc
sudo chown -R debian-tor:debian-tor /var/lib/tor/electrs
sudo chown -R debian-tor:debian-tor /var/lib/tor/mempool
```

```bash
sudo chmod 700 /var/lib/tor/bitcoinrpc
sudo chmod 700 /var/lib/tor/electrs
sudo chmod 700 /var/lib/tor/mempool
```

### 2.6 เพิ่ม User ให้กับ Group debian-tor

```bash
sudo usermod -a -G debian-tor ${USER}
```

### 2.7 restart tor

```bash
sudo systemctl restart tor
```


----
[<< Firewall](firewall.md)  ||  [I2P >>](i2p.md)