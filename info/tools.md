# ติดตั้ง Cargo & Rust และ Docker

## 5 ติดตั้ง Cargo & Rust

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

> [!NOTE]
> เลือกข้อ 1 หรือกด Enter เลยก็ได้

### 5.1 ตั้งค่าให้ใช้งานได้

```bash
. "$HOME/.cargo/env"  
```

### 5.2 ตรวจสอบเวอร์ชั่น Cargo & Rust

```bash
rustc --version
```

```bash
cargo --version
```

> [!NOTE]
>  cargo & rustc v 1.90.0 


## 6 ติดตั้ง Docker

### 6.1 ดาวน์โหลดสคริปต์

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
```

### 6.2 รันสคริปติดตั้ง

```bash
sudo sh get-docker.sh
```

### 6.3 ตรวจสอบเวอร์ชั่น

```bash
docker --version
```

### 6.4 ตรวจสอบสถานะ Docker

```bash
sudo systemctl status docker
```


### 6.5 วิธีตั้งค่าให้ใช้คำสั่ง docker โดยไม่ได้ใช้ sudo

โดยเริ่มต้น คำสั่ง Docker จำเป็นต้องใช้สิทธิ์ root หรือใช้ sudo ในการรันคำสั่ง เราจะมาตั้งค่าให้อยู่ในกลุ่มเดียวกันโดยใช้คำสั่งนี้

```bash
sudo usermod -aG docker ${USER}
```

### 6.6 สลับเข้าสู่ user อีกครั้ง

```bash
su - ${USER}
```

### 6.7 ทดสอบคำสั่ง:

```bash
docker ps
```

----

[YAM >>](yam.md)

