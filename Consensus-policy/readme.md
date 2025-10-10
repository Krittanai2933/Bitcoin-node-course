# Consensus & Relay policy

เครือข่ายบิตคอยน์ดำเนินการภายใต้ชุดกฎเกณฑ์ที่ทำหน้าที่กำกับพฤติกรรมของโหนดและนักขุด เพื่อรักษาความปลอดภัย ความน่าเชื่อถือ และความเป็นเอกภาพของระบบ โดยกฎเหล่านี้สามารถแบ่งออกเป็นสองระดับสำคัญ ได้แก่ Consensus Rules และ Relay Policy แม้ว่าทั้งสองจะเกี่ยวข้องกับการทำงานของโหนดในเครือข่าย แต่มีจุดมุ่งหมายและผลลัพธ์ที่แตกต่างกันอย่างชัดเจน

Consensus rules คือกฎพื้นฐานซึ่งทำหน้าที่เป็นกติกากลางของเครือข่ายที่ทุกโหนดและนักขุดต้องปฏิบัติตามอย่างเคร่งครัด กฎเหล่านี้กำหนดมาตรฐานที่ใช้ในการตรวจสอบและยืนยันความถูกต้องของธุรกรรมและบล็อก ตัวอย่างที่ชัดเจนได้แก่ การตรวจสอบความถูกต้องของลายเซ็นธุรกรรม การใช้ UTXO ที่มีอยู่จริง และการจำกัดขนาดของบล็อก หากโหนดใดฝ่าฝืนหรือไม่ปฏิบัติตาม consensus rules ผลที่เกิดขึ้นคือโหนดนั้นจะไม่สามารถซิงโครไนซ์หรือเข้าร่วมกับเครือข่ายหลักได้ และจะถูกแยกออกไปกลายเป็นเครือข่ายใหม่หรือที่เรียกว่า fork โดยอัตโนมัติ

ในทางตรงกันข้าม relay policy ไม่ได้มีสถานะเป็นกติกากลางของเครือข่าย แต่เป็นนโยบายภายในที่โหนดแต่ละเครื่องสามารถกำหนดได้เอง เพื่อใช้ตัดสินใจว่าจะรับ เก็บ หรือส่งต่อธุรกรรมในรูปแบบใด ตัวอย่างของ relay policy ได้แก่ การกำหนดค่าธรรมเนียมขั้นต่ำที่ธุรกรรมต้องมี การกำหนดขนาดสูงสุดของ mempool หรือการพิจารณาว่าจะอนุญาตธุรกรรมที่อยู่ในรูปแบบ non-standard หรือไม่ การเปลี่ยนแปลง relay policy จึงไม่มีผลต่อความต่อเนื่องหรือความถูกต้องของเครือข่ายโดยรวม แต่จะมีผลเฉพาะต่อการจัดการธุรกรรมที่ผ่านโหนดนั้นเท่านั้น

กล่าวโดยสรุป consensus rules เป็นกลไกหลักที่รับประกันความสอดคล้องและความปลอดภัยของเครือข่ายบิตคอยน์ หากโหนดไม่ปฏิบัติตามก็จะถูกตัดออกจากเครือข่าย ในขณะที่ relay policy เป็นเพียงนโยบายย่อยที่แต่ละโหนดสามารถปรับเปลี่ยนได้ตามความเหมาะสมโดยไม่ทำให้เกิดการแยกเครือข่าย ทั้งสองระดับจึงมีบทบาทที่แตกต่างกัน แต่ล้วนมีความสำคัญในการทำให้เครือข่ายบิตคอยน์ดำเนินการได้อย่างมีประสิทธิภาพและยืดหยุ่น

## Consensus
อย่างที่ได้กล่าวไปข้างต้นแล้วว่า consensus rules ทั้งหมดจะเป็นข้อกำหนดมาตรฐานที่ใช้ในการตรวจสอบและยืนยันความถูกต้องของธุรกรรมและบล็อก ทีนี้เราลองมาดูกันดีกว่าว่ามันมีอะไรบ้าง (ทุกคนสามารถเช็คกฎได้ใน [Githubของบิตคอยน์](https://github.com/bitcoin/bitcoin/tree/29.x/src/consensus))

1. amount.h

งั้นเรามาเริ่มกันที่ `bitcoin/src/consensus/amount.h` ในไฟล์นี้จะเป็นกฎที่พวกเราคุ้นเคยกันอยู่แล้วว่าบิตคอยน์มีได้ไม่เกิน `21,000,000 เหรียญ` โดย code ส่วนที่อธิบายจุดนั้นไว้ผมได้ยกมาไว้ให้ด้านล่างนี้นะครับ
```
15 static constexpr CAmount COIN = 100000000;
26 static constexpr CAmount MAX_MONEY = 21000000 * COIN;
27 inline bool MoneyRange(const CAmount& nValue) { return (nValue >= 0 && nValue <= MAX_MONEY); }

```
> เลขด้านหน้าจะเป็นบรรทัดนะครับกลัวว่าถ้าเอามาทั้งหมดจะยาวเกินไป

อย่างที่หลายท่านอาจจะทราบกันอยู่แล้วว่าหน่วยในโปรโตคอลของบิตคอยน์คือ COIN (satoshi ที่เรารู้จักนั้นแหละ) ถ้าดูจากในบรรทัดที่ 26 MAX_MONEY = 21000000 * COIN หมายความว่า ค่า MAX_MONEY เนี่ย มีค่าได้ไม่เกิน 21,000,000 * COIN (ซึ่ง COIN ถูกกำหนดไว้ในบรรทัดที่ 15 ว่าเท่ากับ 100,000,000) แปลว่าในระบบของบิตคอยน์นั้นมีได้ไม่เกิน 2,100,000,000,000,000 COIN(satohsi) และในบรรทัด 27 ใช้ในการยืนยันว่าจำนวนบิตคอยน์ในระบบตอนนี้เนี่ยมีเหรียญอยู่ในช่วง 0 - 2,100,000,000,000,000 รึเปล่า

กฎตรงส่วนนี้เองที่ทำให้ผู้ใช้งานทุกคนมั่นใจได้ว่าบิตคอยน์มีไม่เกิน 21,000,000 BTC แน่นอน

2. consensus.h
สามารถหาไฟล์นี้ได้ที่ `bitcoin/src/consensus/consensus.h` ในไฟล์นี้จะมีชุดกฎที่เกี่ยวกับขนาดบล๊อก, จำนวนลายเซ็นในบล๊อก, ตัวคูณ witness, การใช้เงินจาก coinbase transaction, ค่าขั้นต่ำของธุรกรรม, timelock, ค่าบน flag และ sequene, และ ข้อจำกัดของเวลาระหว่างช่วง difficulty adjustment โดย code ต่อไปนี้คือจุดที่อธิบายเรื่องพวกนี้ไว้
```
13 static const unsigned int MAX_BLOCK_SERIALIZED_SIZE = 4000000;
15 static const unsigned int MAX_BLOCK_WEIGHT = 4000000;
17 static const int64_t MAX_BLOCK_SIGOPS_COST = 80000;
19 static const int COINBASE_MATURITY = 100;
21 static const int WITNESS_SCALE_FACTOR = 4;
23 static const size_t MIN_TRANSACTION_WEIGHT = WITNESS_SCALE_FACTOR * 60; 
24 static const size_t MIN_SERIALIZABLE_TRANSACTION_WEIGHT = WITNESS_SCALE_FACTOR * 10;
28 static constexpr unsigned int LOCKTIME_VERIFY_SEQUENCE = (1 << 0);
35 static constexpr int64_t MAX_TIMEWARP = 600;
```
ในไฟล์นี้มีการกำหนดค่าอยู่ทั้งหมด 9 อย่าง: 
- MAX_BLOCK_SERIALIZED_SIZE: จำกัดขนาดสูงสุดของข้อมูลบล็อกเมื่อ serialize แล้ว (4 MB) ใช้เป็น buffer limit ในหน่วย byte
- MAX_BLOCK_WEIGHT: เป็น “น้ำหนักบล็อก” ตามนิยามใน BIP141 (SegWit) ใช้คำนวณโดยนำ witness data มาคิดลดสัดส่วน (weight = base_size * 3 + total_size) ดังนั้นบล็อกที่ใหญ่สุดจะมี weight ไม่เกิน 4,000,000 หน่วย
- MAX_BLOCK_SIGOPS_COST: จำกัดจำนวน signature verification operations ต่อ block → เพื่อป้องกันการโจมตีแบบ CPU exhaustion (ผู้โจมตีใส่ธุรกรรมที่ต้องตรวจลายเซ็นจำนวนมากให้โหนดคำนวณช้า)
- COINBASE_MATURITY: จำนวนบล๊อกที่ต้องรอก่อนจะสามารถนำเงินจาก coinbase transaction มาใช้ได้
- WITNESS_SCALE_FACTOR: ค่าคงที่ที่ใช้ในการคำนวณน้ำหนักบล็อก (block weight) และน้ำหนักธุรกรรม (transaction weight) กำหนดว่าส่วนข้อมูลที่ไม่ใช่ witness จะถูกคูณด้วย 4
- MIN_TRANSACTION_WEIGHT, MIN_SERIALIZABLE_TRANSACTION_WEIGHT: กำหนดขนาดเล็กสุดของธุรกรรมที่ถูกต้อง (เพื่อกัน invalid tx ที่มีโครงสร้างไม่ครบ)
- LOCKTIME_VERIFY_SEQUENCE: ธง (flag) ที่ระบุให้ระบบตีความ nSequence เป็น relative lock-time → ใช้ในฟีเจอร์เช่น CheckSequenceVerify (CSV)
- MAX_TIMEWARP: ข้อจำกัดของเวลาระหว่างช่วง difficulty adjustment  ตาม BIP94, timestamp ของบล็อกในรอบปรับความยาก (2016 blocks) สามารถย้อนหลังได้มากที่สุด 600 วินาที (10 นาที) จากบล็อกสุดท้ายของรอบก่อนหน้า

3. validation.h
คุณสามารถหาไฟล์นี้ได้ที่ `bitcoin\src\consensus\validation.h` โดยไฟล์นี้ทำหน้าที่นิยามโครงสร้างและฟังก์ชันที่ใช้ระบุสถานะของการตรวจสอบบล็อกและธุรกรรม
ใช้ได้ทั้งในระดับ:
- Transaction validation — ตรวจสอบว่า TX ถูกตามกฎ consensus และ policy หรือไม่
- Block validation — ตรวจสอบว่า block ถูกตามกฎ consensus เช่น PoW, timestamp, หรือ SegWit หรือไม่
โดย code ต่อไปนี้คือจุดที่อธิบายเรื่องพวกนี้ไว้
```
15 static constexpr int NO_WITNESS_COMMITMENT{-1};
18 static constexpr size_t MINIMUM_WITNESS_COMMITMENT{38};
```
โค้ดในส่วนนี้อธิบายค่าคงที่เกี่ยวกับ Witness Commitment แบบสั้น ๆ คือ coinbase transaction ของบล็อกที่รองรับ SegWit จะต้องมี “witness commitment” หรือก็คือค่า hash ของ witness root (อ่านเรื่องนี้เพิ่มเติมได้ใน BIP141) โดยมีค่าคงที่กำหนดไว้ 2 ตัว คือ NO_WITNESS_COMMITMENT ที่ = -1 ซึ่งแปลว่าไม่มี witness commitment และ MINIMUM_WITNESS_COMMITMENT คือขนาดขั้นต่ำของ witness commitment (ป้องกันการกำหนดค่าผิดโครงสร้าง)
```
enum class TxValidationResult { ... };
```
ในส่วนของคลาส TxValidationResult มีเพื่อใช้ระบุว่าทำไมธุรกรรม (transaction) ถึงไม่ผ่านการตรวจสอบ เพื่อให้โหนดรู้ว่าควร “แค่ปฏิเสธ” หรือ “ลงโทษ (ban)” โหนดที่ส่งมาหรือไม่ โดยมี ทั้งหมด 12 กรณี ดังนี้
| ค่าคงที่                 | ความหมาย                                                            |
| ------------------------ | ------------------------------------------------------------------- |
| `TX_CONSENSUS`           | ผิดตาม **กฎฉันทามติ** (เช่น ใช้ UTXO ซ้ำ, ลายเซ็นไม่ถูกต้อง)        |
| `TX_INPUTS_NOT_STANDARD` | อินพุตไม่เป็นไปตาม **policy** (ไม่ใช่ consensus แต่ node ปฏิเสธเอง) |
| `TX_NOT_STANDARD`        | ไม่เป็นธุรกรรมที่ node ยอมรับไว้ใน mempool                          |
| `TX_MISSING_INPUTS`      | ขาดอินพุตที่จำเป็น (อาจยังไม่ได้รับ UTXO)                           |
| `TX_PREMATURE_SPEND`     | พยายามใช้ coinbase ก่อนครบ 100 blocks หรือผิด locktime/sequence     |
| `TX_WITNESS_MUTATED`     | witness ถูกดัดแปลงผิดรูปแบบ (ผิดตาม BIP141)                         |
| `TX_WITNESS_STRIPPED`    | ขาดข้อมูล witness ทั้งที่ควรมี                                      |
| `TX_CONFLICT`            | ธุรกรรมขัดแย้งกับที่อยู่ใน chain หรือ mempool แล้ว                  |
| `TX_MEMPOOL_POLICY`      | ละเมิดข้อจำกัด mempool (เช่น ขนาดใหญ่เกิน หรือค่าธรรมเนียมต่ำเกิน)  |
| `TX_NO_MEMPOOL`          | โหนดนี้ไม่มี mempool (เช่น โหมด prune หรือ block-only)              |
| `TX_RECONSIDERABLE`      | ล้มเหลวจาก policy แต่สามารถพิจารณาใหม่ได้ในแพ็กเกจใหญ่              |
| `TX_UNKNOWN`             | ไม่สามารถตรวจสอบเพราะ package ล้มเหลว                               |

นอกจากนี้ใน consensus ยังมีอีกหลายไฟล์ไม่ว่าจะเป็น `markle.cpp`, `markle.h` ที่เป็นวิธีในการคำนวณ merkle root, `tx_check.cpp`, `tx_check.h`, `tx_verify.cpp`,`tx_verify.h` ที่ใช้ check เกี่ยวกับ transaction เช่นส่วนไหนขนาดเท่าไหร่ มีการใช้ input ที่มีอยู่จริงมั้ย
`params.h` ที่คอยเก็บพารามิเตอร์ที่อาจต่างกันระหว่างเครือข่าย ไม่ว่าจะเป็น mainnet, testnet, regtest
## relay policy
