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

เราทำความรู้จักกับคลาสที่ไว้ตรวจสอบธุรกรรมไปแล้ว ต่อไปเรามาดูคลาสที่ใช้ตรวจสอบ block กับต่อดีกว่ากับคลาสที่ชื่อว่า
```
enum class BlockValidationResult { ... };
```
หน้าที่ของคลาส BlockValidationResult มีเพื่อใช้ระบุว่าทำไมบล๊อก (Block) ถึงไม่ผ่านการตรวจสอบ เพื่อให้โหนดรู้ว่าควร “แค่ปฏิเสธ” หรือ “ลงโทษ (ban)” โหนดที่ส่งมาหรือไม่ โดยมี ทั้งหมด 8 กรณี ดังนี้
| ค่าคงที่                | ความหมาย                                        |
| ----------------------- | ----------------------------------------------- |
| `BLOCK_CONSENSUS`       | ผิดกฎฉันทามติ (เช่น ลายเซ็น, ขนาด, PoW)         |
| `BLOCK_INVALID_HEADER`  | header ผิด (เช่น hash < target ไม่ผ่าน)         |
| `BLOCK_MUTATED`         | ข้อมูลบล็อกไม่ตรงกับที่ PoW ระบุ                |
| `BLOCK_MISSING_PREV`    | ไม่มีบล็อกก่อนหน้า (chain ขาด)                  |
| `BLOCK_INVALID_PREV`    | บล็อกก่อนหน้านั้นไม่ถูกต้อง                     |
| `BLOCK_TIME_FUTURE`     | timestamp เดินหน้าเกิน 2 ชั่วโมงจากเวลาปัจจุบัน |
| `BLOCK_CHECKPOINT`      | ไม่ตรงกับ checkpoint ที่กำหนดไว้                |
| `BLOCK_HEADER_LOW_WORK` | header อยู่บน chain ที่มี work น้อยเกินไป       |

และทั้งสองคลาสนี้จะถูกรวมกันเพื่อแสดงผลในคลาส `ValidationState<Result>` ซึ่งเป็น template class ที่ใช้ร่วมกันได้ทั้งกับ Transaction และ Block ใช้เก็บข้อมูลสถานะของการตรวจสอบ เช่น
- ผ่านหรือไม่ (M_VALID, M_INVALID, M_ERROR)
- เหตุผลที่ถูกปฏิเสธ (reject_reason)
- ข้อความดีบัก (debug_message)
- ประเภทของผล (Result)
โดยมี method สำคัญ ๆ ดังนี้

| metthod                                     | หน้าที่                                                          |
| ----------------------------------------- | ---------------------------------------------------------------- |
| `Invalid(result, reason, debug)`          | ตั้งสถานะเป็น invalid และบันทึกเหตุผล                            |
| `Error(reason)`                           | เกิด error ภายใน (runtime error)                                 |
| `IsValid()` / `IsInvalid()` / `IsError()` | ตรวจสถานะปัจจุบัน                                                |
| `ToString()`                              | แปลงเป็นข้อความสรุป เช่น `"TX_CONSENSUS, bad-txns-inputs-spent"` |

สุดท้ายของไฟล์นี้จะเป็น function ที่เกี่ยวข้องกับการคำนวนน้ำหนักของ block ที่มีการใช้ witness และ การตรวจว่าบล๊อกน้ัน ๆ มี witness หรือไม่
```
133 static inline int32_t GetTransactionWeight(const CTransaction& tx)
{
    return ::GetSerializeSize(TX_NO_WITNESS(tx)) * (WITNESS_SCALE_FACTOR - 1)
         + ::GetSerializeSize(TX_WITH_WITNESS(tx));
}
```
ฟังก์ชั่นนี้ใช้ในการคำนวนน้ำหนักของธุรกรรม โดยจะนำธุรกรรมในส่วนที่ไม่ใช่ witness คูณด้วย 3 และบวกด้วยน้ำหนักในส่วนของ witness โดยนอกจากจะใช้เพื่อเช็คน้ำหนักของธุรกรรมแล้วยังใช้เช็คในเรื่องของค่าธรรมเนียมได้อีกด้วย และนอกจากนี้แล้วยังมี function ที่คล้าย ๆ กันอีกจำนวนหนึ่ง คือ

- GetTransactionWeight(tx)
- GetBlockWeight(block)
- GetTransactionInputWeight(txin)

```
148 inline int GetWitnessCommitmentIndex(const CBlock& block)
```
ฟังก์ชันนี้ ใช้หา “ตำแหน่งของ output” ใน coinbase ที่มี witness commitment ตาม BIP141 โดยจะเช็กว่ามี OP_RETURN ตามด้วย signature 0x24 aa21a9ed หรือไม่และแบ่งออกเป็น 2 กรณี ดังนี้
- ถ้าเจอ → ให้คืนค่าตำแหน่ง index ของ output นั้น
- ถ้าไม่เจอ → ให้คืนค่า -1 (คือ NO_WITNESS_COMMITMENT)

เนื่องจากไฟล์นี้ค่อนข้างยาว ผมมีสรุปมาให้หวังว่าจะช่วยให้เข้าใจมากขึ้นนะครับ
| หมวด                          | หน้าที่                                         | ตัวอย่าง                                                    |
| ----------------------------- | ----------------------------------------------- | ----------------------------------------------------------- |
| **Validation Result Enums**   | แบ่งประเภทความผิดพลาดของ tx/block               | `TX_CONSENSUS`, `BLOCK_MUTATED`                             |
| **ValidationState Class**     | เก็บสถานะผลตรวจสอบ                              | ใช้โดยฟังก์ชัน `CheckTransaction()` และ `ProcessNewBlock()` |
| **Weight Calculation**        | คำนวณน้ำหนักจริงของบล็อกตาม BIP141              | จำกัดบล็อก 4,000,000 weight units                           |
| **Witness Commitment Helper** | หา output witness commitment                    | ใช้ตรวจสอบ SegWit coinbase                                  |
| **Integration**               | ใช้โดยไฟล์ `validation.cpp` และ `tx_verify.cpp` | ตอบว่า “valid หรือ invalid และเพราะอะไร”                    |

  
นอกจากนี้ใน consensus ยังมีอีกหลายไฟล์ไม่ว่าจะเป็น `markle.cpp`, `markle.h` ที่เป็นวิธีในการคำนวณ merkle root, `tx_check.cpp`, `tx_check.h`, `tx_verify.cpp`,`tx_verify.h` ที่ใช้ check เกี่ยวกับ transaction เช่นส่วนไหนขนาดเท่าไหร่ มีการใช้ input ที่มีอยู่จริงมั้ย
`params.h` ที่คอยเก็บพารามิเตอร์ที่อาจต่างกันระหว่างเครือข่าย ไม่ว่าจะเป็น mainnet, testnet, regtest

## relay policy
อย่างที่ได้กล่าวไว้ในช่วงต้นของเอกสารนี้แล้วว่า relay policy ใช้ในการกำหนดนโยบายภายในโหนดของเราเท่านั้น น่าที่หลัก ๆ คือการเลือกว่าจะให้โหนดของเราจะรับธุรกรรมไหนบ้างเข้ามาในเมมพูลของเรา 
> ไม่รับเข้าเมมพูลเท่านั้น หากธุรกรรมนั้นไปอยู่ในบล๊อกและบล๊อกนั้นถูกตรวจสอบแล้วว่าถูกต้องตามกฏภายใน consensus ธุรกรรมเหล่านั้นก็ถูกบรรจุลงบล๊อคแล้วเข้ามาภายในโหนดของคุณอยู่ดี

โดยไฟล์ต่าง ๆ ในเรื่องพวกนี้นั้นสามารถอ่านได้ที่ githubของบิตคอยน์เช่นกัน `bitcoin/src/policy` โดยแยกเป็นประเภทดังนี้
- ธุรกรรมที่สร้างหรือใช้จ่าย UTXO ขนาดเล็ก (dust): `sephemeral_policy.h`, `ephemeral_policy.cpp`
- เกี่ยวกับค่าธรรมเนียมในการใช้งานระบบ: `feerate.h`, `feerate.cpp`, `fees.h`, `fees.cpp`, `fee_args.h`, `fee_args.cpp`, `rbf.h`, `rbf.cpp`
- อื่น ๆ: `settings.h`, `settings.cpp`, `packages.h`, `packages.cpp`, `truc_policy.h`, `truc_policy.cpp`
- ตัวกำหนดค่า policy หลัก ๆ: `policy.h`, `policy.cpp`

โดยในการเรียนครั้งนี้เนื่องจากเวลาที่เรามีค่อนข้างจำกัด ฉะนั้นผมจะขอพูดถึงแค่ไฟล์ `policy.h` เป็นหลัก (แต่ไม่ต้องห่วงนะครับรับรองได้ว่าเนื้อหาครอบคลุมทุกส่วนแน่นอน) ซึ่งในไฟล์นี้เนี่ยแบ่งส่วนสำคัญออกเป็น 7 ส่วนหลัก ๆ 
1. Block/Transaction Limits
   ```
    static constexpr unsigned int DEFAULT_BLOCK_MAX_WEIGHT{MAX_BLOCK_WEIGHT};
    static constexpr unsigned int DEFAULT_BLOCK_RESERVED_WEIGHT{8000};
    static constexpr int32_t MAX_STANDARD_TX_WEIGHT{400000};
    static constexpr unsigned int MIN_STANDARD_TX_NONWITNESS_SIZE{65};
   ```
   ในส่วนนี้เป็นการกำหนดถึงค่าต่าง ๆ เริ่มจากขนาดของบล๊อก `DEFAULT_BLOCK_MAX_WEIGHT` ให้เท่ากับขนาดของ `MAX_BLOCK_WEIGHT` ที่ถูกกำหนดไว้ใน consensus เป็น 4,000,000 Byte `DEFAULT_BLOCK_RESERVED_WEIGHT` หรือก็คือส่วนของ block header ให้มีขนาด 8,000 Byte `MAX_STANDARD_TX_WEIGHT` หรือก็คือขนาดสูงสุดที่เป็นไปได้ใน 1 ธุรกรรม ซึ่งเดิมจะถูกตั้งค่าไว้เป็น 400,000 B หรือ 1/10 ของขนาดบล๊อก ส่วนตัวสุดท้ายจะเป็น `MIN_STANDARD_TX_NONWITNESS_SIZE` หรือก็คือขนาดต่ำสุดของธุรกรรมที่ไม่ใช่ประเภท segwit (เพื่อป้องกันไม่ให้มีการประกาศธุรกรรมที่ไม่สมบูรณ์)

2. Fee Policy & Dust Rules
   ```
    static constexpr unsigned int DUST_RELAY_TX_FEE{3000};
    static constexpr unsigned int DEFAULT_MIN_RELAY_TX_FEE{100};
    static constexpr unsigned int DEFAULT_BLOCK_MIN_TX_FEE{1};
   ```
   ```
    CAmount GetDustThreshold(const CTxOut& txout, const CFeeRate& dustRelayFee);
    bool IsDust(const CTxOut& txout, const CFeeRate& dustRelayFee);
    std::vector<uint32_t> GetDust(const CTransaction& tx, CFeeRate dust_relay_rate);

   ```
   ในส่วนนี้จะเป็นนโยบายที่เกี่ยวกับค่าธรรมเนียมในการทำธุรกรรม และการกำหนดว่าธุรกรรมแบบไหนที่จะถูกมองว่าเป็น dust โดยเริ่มจาก `DUST_RELAY_TX_FEE` ซึ่งมีค่าเริ่มต้นเป็น 3,000 ตัวนี้เป็น “อัตรา feerate” (sat/kVB) ที่ใช้คำนวณ threshold ใน `GetDustThreshold` เช่น ถ้าต้องใช้ 148 bytes เพื่อใช้จ่าย → 148 * 3 sat = 444 sat ดังนั้น output ที่น้อยกว่า 444 sat จะถือเป็น “dust” ตัวต่อมาคือ `DEFAULT_MIN_RELAY_TX_FEE` หรือก็คือค่าธรรมเนียมต่ำสุดที่ยอมให้ relay (sat/kvB) ซึ่งมีค่าเริ่มต้นเป็น 100 หรือ 0.1 sat/vb ค่าคงตัวอีกตัวหนึ่งคือ `DEFAULT_BLOCK_MIN_TX_FEE` ค่าธรรมเนียมต่ำสุดที่นักขุดจะใส่ในบล็อก ซึ่งมีค่าเริ่มต้นมาเป็น 1 ส่วนฟังก์ชันที่เหลือใช้ตรวจว่า output ไหน “ต่ำเกินไป” (dust) และควรถูกกรองออกจาก mempool

3. Standard Script Verification Flags
    ```
    static constexpr script_verify_flags MANDATORY_SCRIPT_VERIFY_FLAGS{...};
    static constexpr script_verify_flags STANDARD_SCRIPT_VERIFY_FLAGS{MANDATORY_SCRIPT_VERIFY_FLAGS | ...};
    ```
    ในส่วนนี้แบ่งออกเป็นสองส่วนตามตารางนี้

| ประเภท                     | ใช้ที่ไหน                            | เป้าหมาย                                                      |
| -------------------------- | ------------------------------------ | ------------------------------------------------------------- |
| **MANDATORY SCRIPT FLAGS** | ใช้ตอน *ตรวจสอบ block*               | ต้องเหมือนกันทุกโหนด                                          |
| **Standard Script Flags**  | ใช้ตอน *ตรวจสอบ tx ก่อนเข้า mempool* | ป้องกัน spam, เพิ่มความเข้ากันได้, ป้องกันการใช้ฟีเจอร์แปลก ๆ |

แปลว่าหากธุรกรรมใดไม่ผ่าน flag เหล่านี้ จะ invalid ทันทีในทุกโหนด และยังเป็น “พื้นฐาน” ของ standardness ด้วย

| Flag                                | รายละเอียด                               |
| ----------------------------------- | -------------------------------------------- |
| `SCRIPT_VERIFY_P2SH`                | เปิดใช้การตรวจสอบ Pay-to-Script-Hash (BIP16) |
| `SCRIPT_VERIFY_DERSIG`              | บังคับใช้รูปแบบลายเซ็น DER (BIP66)           |
| `SCRIPT_VERIFY_NULLDUMMY`           | ต้องมี byte 0 ใน dummy element (BIP147)      |
| `SCRIPT_VERIFY_CHECKLOCKTIMEVERIFY` | เปิดใช้ OP_CHECKLOCKTIMEVERIFY (BIP65)       |
| `SCRIPT_VERIFY_CHECKSEQUENCEVERIFY` | เปิดใช้ OP_CHECKSEQUENCEVERIFY (BIP112)      |
| `SCRIPT_VERIFY_WITNESS`             | เปิดใช้ SegWit (BIP141)                      |
| `SCRIPT_VERIFY_TAPROOT`             | เปิดใช้ Taproot (BIP341)                     |

และ Standard Script Flags เปรียบเสมือน “กฎเพิ่มพิเศษ” สำหรับธุรกรรมใน mempool ไม่ทำให้ invalid ตาม consensus  เพียงแต่จะไม่ถูก relay ต่อเท่านั้น โดยมีรายละเอียดตามตารางต่อไปนี้

| Flag                                                  | ความหมาย                                                  | เหตุผล                                                   |
| ----------------------------------------------------- | --------------------------------------------------------- | -------------------------------------------------------- |
| `SCRIPT_VERIFY_STRICTENC`                             | ต้องเข้ารหัส pubkey และ signature ให้ถูกต้องตามรูปแบบ     | ป้องกันลายเซ็นแปลก ๆ ที่อาจก่อช่องโหว่                   |
| `SCRIPT_VERIFY_MINIMALDATA`                           | ต้องใช้จำนวน byte น้อยที่สุดในการ push ข้อมูล             | ป้องกัน script ที่ “ยุ่งเหยิง” และกินพื้นที่โดยไม่จำเป็น |
| `SCRIPT_VERIFY_DISCOURAGE_UPGRADABLE_NOPS`            | ห้ามใช้ OP_NOP ที่อาจถูกใช้ในอนาคต                        | ป้องกันการรัน opcodes ที่อาจเปลี่ยนความหมายภายหลัง       |
| `SCRIPT_VERIFY_CLEANSTACK`                            | หลังประมวลผล script ต้องเหลือค่าบน stack แค่ 1 ค่า        | เพื่อให้ script มีผลชัดเจน (true/false เดียว)            |
| `SCRIPT_VERIFY_MINIMALIF`                             | ค่าที่ใช้กับ OP_IF ต้องเป็น 0 หรือ 1 เท่านั้น             | ทำให้ script มีรูปแบบที่คาดเดาได้                        |
| `SCRIPT_VERIFY_NULLFAIL`                              | การตรวจสอบ signature ที่ล้มเหลว ต้องให้ลายเซ็นเป็นค่าว่าง | ลดโอกาสใช้ข้อมูลผิดพลาดในการทำ side-channel attack       |
| `SCRIPT_VERIFY_LOW_S`                                 | Signature ต้องอยู่ใน canonical form (low-S)               | ลดความเสี่ยงจาก transaction malleability                 |
| `SCRIPT_VERIFY_DISCOURAGE_UPGRADABLE_WITNESS_PROGRAM` | ป้องกัน witness program ที่ใช้ version ยังไม่รองรับ       | ป้องกันการ relay ของฟีเจอร์อนาคต                         |
| `SCRIPT_VERIFY_WITNESS_PUBKEYTYPE`                    | จำกัดให้ pubkey ใน witness เป็นประเภทที่รองรับเท่านั้น    | รักษาความสม่ำเสมอของ SegWit                              |
| `SCRIPT_VERIFY_CONST_SCRIPTCODE`                      | ห้ามแก้ไข script code ขณะประมวลผล                         | เพิ่มความปลอดภัยของ Taproot                              |
| `SCRIPT_VERIFY_DISCOURAGE_UPGRADABLE_TAPROOT_VERSION` | ป้องกันการใช้ Taproot version ที่ยังไม่รองรับ             | ป้องกันการ relay ของ tx ที่อาจไม่ถูกต้องในอนาคต          |
| `SCRIPT_VERIFY_DISCOURAGE_OP_SUCCESS`                 | ปิดการใช้งาน OP_SUCCESS จนกว่าจะถูกกำหนดอย่างเป็นทางการ   | ป้องกันการใช้ฟังก์ชันอนาคตผิดวัตถุประสงค์                |
| `SCRIPT_VERIFY_DISCOURAGE_UPGRADABLE_PUBKEYTYPE`      | จำกัดชนิดของ pubkey ที่อนุญาต                             | ป้องกัน pubkey แปลกที่อาจไม่เข้ากับ Taproot              |

เหตุผลที่ต้องตรวจ `เข้มกว่า` consensus เพราะ Bitcoin Core ตั้งใจให้ ธุรกรรมที่อยู่ใน mempool ต้องปลอดภัย และเข้ากันได้ในอนาคต แม้บางอย่างจะ ยังไม่ผิดตาม consensus ก็ตาม เช่น ธุรกรรมที่ใช้ OP_SUCCESS ในตอนนี้ยังไม่มีความหมาย ใน consensusแต่ในอนาคต อาจถูกนิยามใหม่ ทำให้เกิดปัญหาความเข้ากันได้ ดังนั้น Bitcoin Core จึงเลือก `ไม่ relay` ธุรกรรมแบบนี้

4. การตรวจสอบธุรกรรมมาตรฐาน
```
bool IsStandardTx(const CTransaction& tx, const std::optional<unsigned>& max_datacarrier_bytes, bool permit_bare_multisig, const CFeeRate& dust_relay_fee, std::string& reason);
bool AreInputsStandard(const CTransaction& tx, const CCoinsViewCache& mapInputs);
bool IsWitnessStandard(const CTransaction& tx, const CCoinsViewCache& mapInputs);
```
ฟังก์ชันเหล่านี้ใช้ในการตรวจสอบว่าแต่ละส่วนภายในธุรกรรมนั้นเป็นไปตาม standard หรือไม่ 
- `IsStandardTx`: ตรวจสอบว่า outputs ใช้ scriptPubKey รูปแบบมาตรฐานหรือไม่ เช่น p2pkh, p2tr, p2sh ไหม ถ้าไม่ใช่ธุรกรรมเหล่านั้นจะไม่ถูกนำเข้า mempool และส่งต่ออกไป
- `AreInputsStandard`: ตรวจสอบว่า scriptSig ที่ใช้ปลดล็อก inputs นั้นซับซ้อนเกินไปหรือไม่ เช่น ใช้ P2SH ที่มี sigops เกิน 15 จะถูกนับว่าเป็น non-standard และไม่ relay ธุรกรรมต่อ
- `IsWitnessStandard`: ตรวจสอบว่า witness data (SegWit/Taproot) ถูกต้องตามมาตรฐานหรือไม่
    - จำกัดขนาด witness script ≤ 3600 bytes
    - จำกัดจำนวน stack items ≤ 100
    - จำกัดขนาดต่อ item ≤ 80 bytes
    - ปฏิเสธ annexes ใน Tapscript (เพราะยังไม่ใช้ใน consensus)    
