# UTXO Snapshot

## ตัวเลือกในการซิงค์บล็อกให้พร้อมใช้เร็วขึ้นโดยใช้ UTXO Snapshot

หลังจากการซิงค์ blockheaders ถึง block ปัจจุบันแล้วเราจะนำเข้า UTXO snapshot

ตรวจสอบ blockheaders

```bash
bitcoin-cli getblockchaininfo
```

```
$ bitcoin-cli getblockchaininfo
{
  "chain": "main",
  "blocks": 70185,
  "headers": 918316,  << ##เช็คจุดนี้
  "bestblockhash": "000000000149a911463409fada467aa6de45d6ec01ad5a89329777dd23f8fe09",
  "bits": "1c0168fd",
  "target": "000000000168fd00000000000000000000000000000000000000000000000000",
  "difficulty": 181.5432893640505,
  "time": 1280059453,
  "mediantime": 1280057059,
  "verificationprogress": 7.374078779552194e-05,
  "initialblockdownload": true,
  "chainwork": "000000000000000000000000000000000000000000000000000afd9a91ac8bc2",
  "size_on_disk": 28795004,
  "pruned": false,
  "warnings": [
  ]
}
```

สั่งหยุดการทำงาน bitcoind

```bash
sudo systemctl stop bitcoind
```

สั่งให้ bitcoind ทำงานโดยไม่เชื่อมต่อ peer

```bash
bitcoind -daemon -maxconnections=0
```

เพิ่ม utxo-snapshot เข้าไป

```bash
bitcoin-cli -rpcclienttimeout=0 loadtxoutset /path/to/utxo-snapshot-height-840000.dat
```

หลังนำเข้าเสร็จแล้วสั่งหยุด bitcoind

```bash
bitcoin-cli stop
```

สั่งให้ bitcoind ทำงานเป็น service อีกครั้ง

```bash
sudo systemctl restart bitcoind
```

> [!NOTE]
> Download a UTXO Snapshot file https://lopp.net/download/utxo-snapshot-height-840000.dat

