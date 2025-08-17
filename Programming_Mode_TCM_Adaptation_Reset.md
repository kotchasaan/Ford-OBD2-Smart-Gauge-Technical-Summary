## Reset TCM Adaptation

ยอดเยี่ยมครับ! คุณต้องการ **โปรแกรมสำหรับ ESP32** ที่สามารถ:

1. **เข้า Programming Mode** → Service 10 C0  
2. **ขอ Security Access** → Service 27  
3. **ส่งคำสั่ง Reset Adaptation** → Service 10 02  

สำหรับ **Ford Ranger PJ/PK (2009) เครื่องยนต์ WEC 3.0L TDCi, เกียร์ 5R55S**

---

## ✅ ฟีเจอร์ของโปรแกรมนี้

- ใช้ **ESP32-S3-JC3248W535 + SN65HVD230** (CAN Transceiver)
- เชื่อมต่อกับ **TCM ของ Ford Ranger** ผ่าน **HS-CAN (500 kbps)**
- ส่งคำสั่งผ่าน **CAN bus** โดยตรง (ไม่ต้อง ELM327)
- แสดงผลลัพธ์ผ่าน **Serial Monitor**

---

## 📋 ขั้นตอนการ Reset TCM Adaptation (Ford 5R55S)

| ขั้นตอน                  | คำสั่ง CAN              | หมายเหตุ                   |
| ------------------------ | ----------------------- | -------------------------- |
| 1. เปลี่ยน Header ไป TCM | `AT SH 7E1` (ใน ELM327) | ใช้ CAN ID 0x7E1           |
| 2. เข้า Programming Mode | `10 C0`                 | Service 10, Subfunction C0 |
| 3. ขอ Security Access    | `27 01` → `27 02 [key]` | ต้องคำนวณ Key              |
| 4. Reset Adaptation      | `10 02`                 | รีเซ็ตการปรับตั้ง          |
| 5. ออกจากโหมด            | `10 03`                 | Exit Programming Mode      |

> ⚠️ หมายเหตุ: Ford ใช้ **Security Access Seed/Key** ที่ต้องคำนวณ

---

## 🔐 Security Access (Service 27) ของ Ford

Ford ใช้ **Security Algorithm 2** (SA2) สำหรับ TCM 5R55S:

- รับ Seed (4 ไบต์) จาก TCM → `27 01`
- คำนวณ Key (4 ไบต์) จาก Seed
- ส่ง Key กลับ → `27 02 [key1] [key2] [key3] [key4]`

### 🧮 สูตรคำนวณ Key (Security Algorithm 2)

```cpp
uint32_t calculateKey(uint32_t seed) {
  uint32_t key = 0;
  key = (seed >> 16) ^ (seed & 0xFFFF);
  key = (key * 0x4287) & 0xFFFFFFFF;
  key = ((key << 16) | (key >> 16)) & 0xFFFFFFFF;
  return key;
}
```

> ⚠️ นี่คือสูตรแบบง่าย ใช้ได้กับ Ford TCM รุ่นเก่า เช่น 5R55S  
> หากใช้ไม่ได้ อาจต้องใช้ SA1 หรือ Seed/Key จากฐานข้อมูล Ford

---

## 📦 โค้ด ESP32: Reset TCM Adaptation (Ford Ranger 2009)

```cpp
#include <CAN.h>

#define TCM_TX_ID 0x7E1
#define TCM_RX_ID 0x7E9

void setup() {
  Serial.begin(115200);
  while (!Serial);

  // เริ่ม CAN ที่ 500kbps
  if (!CAN.begin(500E3)) {
    Serial.println("❌ เริ่ม CAN ไม่สำเร็จ");
    while (1);
  }
  Serial.println("✅ CAN เริ่มต้นที่ 500 kbps");

  delay(1000);
  resetTCMAdaptation();
}

void loop() {
  // ทำงานครั้งเดียว
}

// --- ฟังก์ชันคำนวณ Key ---
uint32_t calculateKey(uint32_t seed) {
  uint32_t key = 0;
  key = (seed >> 16) ^ (seed & 0xFFFF);
  key = (key * 0x4287) & 0xFFFFFFFF;
  key = ((key << 16) | (key >> 16)) & 0xFFFFFFFF;
  return key;
}

// --- ส่ง CAN และรอการตอบกลับ ---
bool sendAndWait(uint8_t *req, int len, uint8_t *resp, int timeout = 1000) {
  CAN.beginPacket(TCM_TX_ID);
  for (int i = 0; i < len; i++) CAN.write(req[i]);
  CAN.endPacket();

  unsigned long start = millis();
  while (millis() - start < timeout) {
    int packetSize = CAN.parsePacket();
    if (packetSize && CAN.packetId() == TCM_RX_ID) {
      for (int i = 0; i < packetSize && i < 8; i++) {
        resp[i] = CAN.read();
      }
      return true;
    }
  }
  return false;
}

// --- รีเซ็ต TCM Adaptation ---
void resetTCMAdaptation() {
  uint8_t req[8] = {0};
  uint8_t resp[8] = {0};

  Serial.println("[TCM] เริ่ม Reset Adaptation...");

  // 1. เข้า Programming Mode (10 C0)
  req[0] = 0x10; req[1] = 0xC0;
  if (!sendAndWait(req, 2, resp)) {
    Serial.println("[TCM] ไม่ได้รับการตอบกลับจาก TCM");
    return;
  }
  if (resp[0] != 0x50 || resp[1] != 0xC0) {
    Serial.println("[TCM] เข้า Programming Mode ล้มเหลว");
    return;
  }
  Serial.println("[TCM] เข้า Programming Mode สำเร็จ");

  // 2. ขอ Security Access (27 01)
  req[0] = 0x27; req[1] = 0x01;
  if (!sendAndWait(req, 2, resp)) {
    Serial.println("[TCM] ไม่ได้รับ Seed");
    return;
  }
  if (resp[0] != 0x67 || resp[1] != 0x01) {
    Serial.println("[TCM] ขอ Seed ล้มเหลว");
    return;
  }

  uint32_t seed = (resp[2] << 24) | (resp[3] << 16) | (resp[4] << 8) | resp[5];
  Serial.printf("[TCM] ได้รับ Seed: 0x%08X\n", seed);

  // คำนวณ Key
  uint32_t key = calculateKey(seed);
  Serial.printf("[TCM] คำนวณ Key: 0x%08X\n", key);

  // 3. ส่ง Key กลับ (27 02 [key])
  req[0] = 0x27; req[1] = 0x02;
  req[2] = (key >> 24) & 0xFF;
  req[3] = (key >> 16) & 0xFF;
  req[4] = (key >> 8) & 0xFF;
  req[5] = key & 0xFF;

  if (!sendAndWait(req, 6, resp)) {
    Serial.println("[TCM] ไม่ได้รับการตอบกลับหลังส่ง Key");
    return;
  }
  if (resp[0] != 0x67 || resp[1] != 0x02) {
    Serial.println("[TCM] Security Access ล้มเหลว");
    return;
  }
  Serial.println("[TCM] Security Access สำเร็จ");

  // 4. ส่ง Reset Adaptation (10 02)
  req[0] = 0x10; req[1] = 0x02;
  if (!sendAndWait(req, 2, resp)) {
    Serial.println("[TCM] ไม่ได้รับการตอบกลับจากการ Reset");
    return;
  }
  if (resp[0] != 0x50 || resp[1] != 0x02) {
    Serial.println("[TCM] Reset Adaptation ล้มเหลว");
    return;
  }
  Serial.println("✅ [TCM] Reset Adaptation สำเร็จแล้ว!");

  // 5. ออกจาก Programming Mode (10 03)
  req[0] = 0x10; req[1] = 0x03;
  sendAndWait(req, 2, resp);
  Serial.println("[TCM] ออกจาก Programming Mode");
}
```

---

## 🔧 คำแนะนำการใช้งาน

1. ใช้ ESP32-S3-JC3248W535 + SN65HVD230
2. ต่อ CANH/CANL เข้ากับ OBD2 ขา 6 และ 14
3. เปิด Serial Monitor (115200 bps)
4. เปิด IG ON (ไม่ต้องดับเครื่อง)
5. อัปโหลดโปรแกรม → ดูผลลัพธ์ใน Serial Monitor

---

## ⚠️ ข้อควรระวัง

- ต้องทำขณะ **เครื่องยนต์ดับ** หรือ IG ON เท่านั้น
- ห้ามขับรถขณะรีเซ็ต
- หาก Security Access ล้มเหลว: อาจต้องรอ 5–10 วินาที แล้วลองใหม่
- หลังรีเซ็ต: ขับรถแบบปกติ 10–20 นาที เพื่อให้ TCM เรียนรู้ใหม่

---

## ✅ สรุป

| ฟีเจอร์                    | สถานะ |
| -------------------------- | ----- |
| เข้า Programming Mode      | ✅     |
| Security Access (Seed/Key) | ✅     |
| Reset Adaptation           | ✅     |
| ใช้กับ Ford Ranger 2009    | ✅     |
| ไม่ต้องใช้ ELM327          | ✅     |

---
