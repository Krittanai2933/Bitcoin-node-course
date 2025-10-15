# Using your own node

เพื่อให้เราเข้าใจการทำงานของโหนดบิตคอยน์มากขึ้น ในส่วนนี้เราจะมาทำการลองใช้โหนดของเราผ่าน `bitcoin-cli` เพื่อให้เราได้เข้าใจการที่โปรแกรมอื่น ๆ เข้ามาดึงข้อมูลต่าง ๆ จากโหนดเรา พวกเขาทำได้อย่างไร

Bitcoin CLI เป็นเครื่องมือ command line ของ Bitcoin Core ที่จะใช้เชื่อมต่อและสื่อสารกับ bitcoind (Bitcoin daemon) ผ่าน JSON-RPC โดยคำสั่งจะอยู่ในรูปแบบ
```
bitcoin-cli [options] <command> [params]
```
---
1. Setup + Blockchain Basics
```
#เริ่มจากการเปิดใช้งานโหนด
bitcoind -daemon

#จากนั้นลองเช็คสถานะของบล๊อคเชนในโหนดของเรา
bitcoin-cli getblockchaininfo

#ลองเช็คว่าเราสามารถทำอะไรกับโหนดเราได้บ้าง
bitcoin-cli help

#สั่งหยุดการทำงานของโหนด
bitcoin-cli stop
```
2. Setup regtest + wallet basic
```
#เปิดใช้งานโหนดในโหมด regtest
bitcoind -regtest -daemon

#สร้าง wallet
bitcoin-cli -regtest createwallet "rsNodeCourse"

#เช็คยอดคงเหลือ
bitcoin-cli -regtest getbalance

#สร้าง address
bitcoin-cli -regtest getnewaddress

#ขุดบิตคอยน์ไปยัง address
bitcoin-cli -regtest generatetoaddress 101 $address

#เช็คสถานะของบล๊อคเชน
bitcoin-cli -regtest getblockchaininfo

```
3. Transaction Workflow
