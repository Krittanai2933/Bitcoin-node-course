## Connect to wifi on Ubuntu server

ตอนติดตั้ง Ubuntu Server เราได้ตั้งค่าให้เชื่อมต่อกับ Wi-Fi ของ CDCstaff ไว้เท่านั้น ดังนั้นเมื่อกลับบ้าน เครื่องจะไม่สามารถเชื่อมต่อ Wi-Fi ที่บ้านได้โดยอัตโนมัติ จึงจำเป็นต้องเพิ่มการตั้งค่า Wi-Fi ของบ้านเข้าไปด้วย

```bash
$ sudo nano /etc/netplan/50-cloud-init.yaml
```

you should see something like this:

```yaml
network:
  version: 2
  wifis:
    wlan0:
      optional: true
      dhcp4: true
      access-points:
        "CDCstaff":
          hidden: true
          auth:
            key-management: "psk"
            password: "6927fa0887ce107f274e36e5640a93e9f33e17d247212bff991ddb459c94384a"
```

then add your wifi network like this:

```yaml
network:
  version: 2
  wifis:
    wlan0:
      optional: true
      dhcp4: true
      access-points:
        "CDCstaff":
          hidden: true
          auth:
            key-management: "psk"
            password: "6927fa0887ce107f274e36e5640a93e9f33e17d247212bff991ddb459c94384a"
        "<name-of-your-wifi-home>":
          auth:
            key-management: "psk"
            password: "<name-of-your-wifi-home>"
        "<name-of-your-hotspot>":
          auth:
            key-management: "psk"
            password: "<your-hotspot-password>"
```

- replace `<name-of-your-wifi-home>`, `<your-wifi-home-password>`, `<name-of-your-hotspot>`, and `<your-hotspot-password>` with your actual Wi-Fi names and passwords.
- make sure to keep the indentation(ย่อหน้า) correct (2 spaces per level).

then save(control+s then control+x) the file and run:

```bash
$ sudo netplan apply
```

you can check if you are connected to wifi by running:

```bash
$ iwconfig
wlan0  ESSID:"CDCstaff"
```

then connect to your wifi home or hotspot by running:

```bash
$ sudo iwlist wlan0 scan | grep ESSID
            ESSID:"<name-your-wifi-home>"
            ESSID:"<name-your-hotspot>"
```

then run:

```bash
$ nmcli dev wifi connect "<name-your-hotspot>" password "<your-hotspot-password>"
```

next should be connected to your wifi home or hotspot, you can check by running:

```bash
$ iwconfig
wlan0  ESSID:"<name-your-hotspot>"
```
