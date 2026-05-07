# CS423_Project_Weight-based-Smart-Inventory-System

> ระบบจัดการสินค้าคงคลังโดยใช้น้ำหนัก | ESP32 + HX711 Load Cell + Web Dashboard  
> Project CS423 — Internet of Things and Applications

---

## ภาพรวมระบบ

WeightTrack เป็น IoT Platform สำหรับติดตามสต็อกสินค้าแบบ Real-time โดยใช้เซ็นเซอร์น้ำหนัก HX711 ร่วมกับ ESP32 ส่งข้อมูลมาแสดงผลบน Web Dashboard พร้อมระบบแจ้งเตือนเมื่อสินค้าใกล้หมด

### Features

- **Login System** — ระบบ Authentication ก่อนเข้าใช้งาน
- **Real-time Dashboard** — แสดงน้ำหนักและจำนวนสินค้าอัปเดตทุก 1.5 วินาที
- **Bar Chart** — กราฟแสดงสต็อกสินค้าทุกรายการแบบ Real-time
- **กราฟย้อนหลัง** — บันทึกและแสดงกราฟน้ำหนักย้อนหลัง 60 วินาที
- **Alert System** — แจ้งเตือนเมื่อสต็อกต่ำกว่าเกณฑ์ที่กำหนด
- **Device Control** — Tare, Calibrate, Pause/Resume การอ่านค่า
- **Hardware Status** — แสดงสถานะ ESP32, HX711, Network
- **Notification Log** — บันทึกการแจ้งเตือนทั้งหมด
- **User Management** — เพิ่ม/ลบผู้ใช้ และเปลี่ยน Role

---

## Platform Stack

| Layer | Component |
|-------|-----------|
| Device | ESP32-WROOM-32, HX711 Load Cell |
| Communication | Wi-Fi, WebSocket, HTTP/REST, Serial/UART |
| Backend | Node.js (simulated), REST API, MQTT Broker |
| Database | Firebase RTDB (simulated) |
| Frontend | HTML, CSS, JavaScript (Vanilla) |
| AI Agent | Claude AI — Code generation, Debugging |

---

## โครงสร้างไฟล์

```
weighttrack/
├── inventory_platform.html   # Web Dashboard (ไฟล์เดียว standalone)
└── README.md                 # เอกสารนี้
```

---

## การติดตั้งและใช้งาน

### ความต้องการของระบบ

- Browser สมัยใหม่ (Chrome, Firefox, Edge)
- [VS Code](https://code.visualstudio.com/) + Extension **Live Server**
- Arduino IDE (สำหรับ Flash firmware ลง ESP32)
- Wokwi (สำหรับทำใน Website)
- ESP32 + HX711 Load Cell (Hardware จริง)

---

### 1. ติดตั้ง Web Dashboard

```bash
# Clone repository
git clone https://github.com/jordijack/CS423_Project_Weight-based-Smart-Inventory-System.git
cd CS423_Project
```

เปิด VS Code แล้วทำตามขั้นตอน:

1. กด `File → Open Folder` เลือกโฟลเดอร์ `CS423_Project`
2. คลิกขวาที่ `inventory_platform.html`
3. เลือก **Open with Live Server**
4. Browser จะเปิดที่ `http://127.0.0.1:5500`

> **หากยังไม่มี Live Server Extension**  
> กด `Ctrl+Shift+X` → ค้นหา **Live Server** → กด Install

---

### 2. Login เข้าระบบ

| Username | Password | Role  |
|----------|----------|-------|
| admin    |   1234   | Admin |

---

### 3. Flash Firmware ลง ESP32

เปิดไฟล์ `sketch.ino` ใน Arduino IDE และแก้ไขค่าต่อไปนี้:

```cpp
// HX711 pins
#define DOUT_PIN 21
#define SCK_PIN  22

// Calibration factor (ปรับตาม Hardware จริง)
float calibrationFactor = 420.0;
```

ขั้นตอน Upload:
1. เชื่อมต่อ ESP32 ผ่าน USB
2. เลือก Board: `ESP32 Dev Module`
3. เลือก Port ที่ถูกต้อง
4. กด **Upload**

---

### 4. Calibrate เซ็นเซอร์

1. เปิด Serial Monitor ที่ baud rate **115200**
2. กด `t` เพื่อ Tare (ตั้งศูนย์)
3. วางน้ำหนักมาตรฐาน เช่น 500g
4. กด `c` เพื่อเริ่ม Calibrate
5. บันทึกค่า `calibrationFactor` ที่ได้ลงในโค้ด

---

### ควบคุมอุปกรณ์

| ปุ่ม | หน้าที่ |
|---|---|
| Tare | รีเซ็ตน้ำหนักเป็น 0 |
| Calibrate | ปรับค่า Calibration Factor |
| Pause / Resume | หยุด/เริ่มอ่านค่าจาก ESP32 |
| + วางสินค้า | จำลองวางสินค้าเพิ่ม +2 ชิ้น |
| − หยิบสินค้า | จำลองหยิบสินค้าออก −1 ชิ้น |
| Reset all | คืนค่าสต็อกทั้งหมดเป็นค่าเริ่มต้น |

### Serial Monitor
แสดง log จาก ESP32 แบบ Real-time แยกสีตามประเภท:
- 🟢 **สีเขียว** — ข้อมูลปกติ (SCALE, SYSTEM)
- 🟡 **สีเหลือง** — คำเตือน (ALERT, ACTION)
- 🔴 **สีแดง** — ข้อผิดพลาด (สินค้าหมด)
- 🔵 **สีฟ้า** — คำสั่ง (CMD)

---

## ESP32 Wiring

```
ESP32       HX711
─────────────────────
GPIO 21  →  DOUT
GPIO 22  →  SCK
3.3V     →  VCC
GND      →  GND
```

---

## การแก้ไขปัญหาที่พบบ่อย

| ปัญหา | วิธีแก้ไข |
|-------|--------|
| Live Server ไม่ทำงาน | กด File → Open Folder ก่อนเสมอ |
| ค่าน้ำหนักไม่นิ่ง | ใช้ค่าเฉลี่ย 5 readings: `scale.get_units(5)` |
| น้ำหนักติดลบ | เพิ่ม `if (weight < 0) weight = 0` ใน firmware |
| กราฟไม่ขึ้น | รอ 5–10 วินาทีให้ข้อมูลสะสมก่อนกดหน้ากราฟ |
| Calibration ผิด | ทำ Tare ก่อนทุกครั้ง แล้วใช้น้ำหนักมาตรฐาน |

---

## License

MIT License — สามารถนำไปใช้และดัดแปลงได้อย่างอิสระ