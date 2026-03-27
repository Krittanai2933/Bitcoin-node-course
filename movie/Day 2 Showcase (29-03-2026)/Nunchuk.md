# Nunchuk Wallet Desktop

ลอง connect Nunchuk Wallet กับ node ของเรากัน

## 1. click ที่โปรไฟล์ตรงซ้ายล่าง

![nunchuk_desktop1.png](../assets/nunchuk_desktop1.png)

## 2. เลือก Setting

![nunchuk_desktop2.png](../assets/nunchuk_desktop2.png)

## 3. click ที่ Network setting

![nunchuk_desktop3.png](../assets/nunchuk_desktop3.png)

## 4. click ที่ MAINNET SERVER

![nunchuk_desktop4.png](../assets/nunchuk_desktop4.png)

## 5. กด add server

![nunchuk_desktop5.png](../assets/nunchuk_desktop5.png)

## 6. ใส่ hostname ของ raspberrypi และ port ของ electrs

![nunchuk_desktop6.png](../assets/nunchuk_desktop6.png)

## 7. click save

![nunchuk_desktop7.png](../assets/nunchuk_desktop7.png)

## 8. click Save network setting

![nunchuk_desktop8.png](../assets/nunchuk_desktop8.png)

## 9. ถ้า nunchuk wallet สามาถเชื่อมต่อ electrs ของเราได้ตรง Wallets เป็นสีเขียว

![nunchuk_desktop9.png](../assets/nunchuk_desktop9.png)

## 10. หรือจะลองใช้ tor_address ก็ได้

### 10.1 ตั้งการ tor

```bash
sudo nano /etc/tor/torrc
```

```
SocksPort 127.0.0.1:9050
SocksPort 192.168.0.103:9050
```

ตัวอย่าง

```

SocksPort 127.0.0.1:9050
SocksPort 192.168.0.103:9050

# ControlPort & Authentication
...

# Bitcoin RPC
...
```

## 10.2 restart tor.service

```bash
sudo systemctl restart tor.service
```

## 10.3 หา tor address

```bash
sudo cat /var/lib/tor/electrs/hostname
```

![nunchuk_desktop10.png](../assets/nunchuk_desktop10.png)

# Nunchuk mobile

## 1. กด Continue as guest

![nunchuk_mobile1.png](../assets/nunchuk_mobile1.png)

## 2. กด Profile

![nunchuk_mobile2.png](../assets/nunchuk_mobile2.png)

## 3. กด Network settings

![nunchuk_mobile3.png](../assets/nunchuk_mobile3.png)

## 4. กด `->` ตรง Mainnet server

![nunchuk_mobile4.jpeg](../assets/nunchuk_mobile4.jpeg)

## 5. กด Add server

![nunchuk_mobile5.jpeg](../assets/nunchuk_mobile5.jpeg)

## 6. ใส่ hostname ของ node และ เลข port ของ electrs

### 6.1 หา ip ของ node

```bash
hostname -I
```

ตัวอย่าง

```bash
$ hostname -I
192.168.0.103 100.116.235.99 172.16.57.1 172.17.0.1 fd7a:115c:a1e0::a838:eb63
```

อย่างลืม check ว่า mobile ต่อเน็ตเดี๋ยวกับ node

### 6.2 กด Save

![nunchuk_mobile6.jpeg](../assets/nunchuk_mobile6.jpeg)

## 7. จะเห็นว่าตรง Wallets เป็น สีเขียว และ ขึ้นว่า Online

![nunchuk_mobile2.png](../assets/nunchuk_mobile2.png)

