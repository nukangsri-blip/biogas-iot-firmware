# BioGas IoT — Firmware Releases

เก็บเฉพาะไฟล์ release สำหรับ OTA ของ [biogas-iot](https://github.com/nukangsri-blip/biogas-iot)
(repo ซอร์สโค้ดหลัก เป็น private) — แยก repo นี้ออกมาให้เป็น **public** เพื่อให้บอร์ด ESP32
ดึงไฟล์ `.bin` ได้เองผ่าน HTTPS โดยไม่ต้องฝัง GitHub token ไว้ในเฟิร์มแวร์

## โครงสร้าง

- `manifest.json` — เวอร์ชันล่าสุด + ลิงก์ไฟล์ `.bin` (บอร์ดอ่านไฟล์นี้ทุกครั้งที่สั่ง `/update`)
- แต่ละ [Release](../../releases) แนบไฟล์ `BioGas_IoT.ino.bin` ของเวอร์ชันนั้น

## URL ที่บอร์ดใช้ (ตั้งค่าผ่านเมนู Serial `c` ครั้งเดียว หลัง flash)

```
https://raw.githubusercontent.com/nukangsri-blip/biogas-iot-firmware/main/manifest.json
```

ไม่ต้องตั้งค่า OTA Authorization header — repo นี้ public, GET เปล่าไม่ต้องมี token

## วิธีออก release ใหม่ (ทำจากเครื่องไหนก็ได้ ไม่ต้องอยู่หน้างาน)

1. Build `.bin` จาก repo หลัก (Arduino IDE หรือ `arduino-cli compile`) — **ต้องทดสอบบนฮาร์ดแวร์
   จริงก่อนเสมอ** อย่า release ตัวที่ยังไม่เคย flash ทดสอบ
2. สร้าง release พร้อมแนบไฟล์:
   ```bash
   gh release create v2.3.1 BioGas_IoT.ino.bin \
     --repo nukangsri-blip/biogas-iot-firmware \
     --title "v2.3.1" --notes "รายละเอียดที่เปลี่ยน"
   ```
3. แก้ `manifest.json` ให้ `version` ตรงกับ `FW_VERSION` ในไฟล์ `.ino` ที่ build จริง และ `url`
   ชี้ไปที่ไฟล์ในขั้นตอนที่ 2 (`.../releases/download/v2.3.1/BioGas_IoT.ino.bin`) แล้ว commit + push
4. บอร์ดจะเห็นเวอร์ชันใหม่ทันทีที่มีคนพิมพ์ `/update` ใน Telegram — ยืนยันด้วย `/update install`

## ⚠️ ข้อควรระวัง

- **เช็คแค่ "เท่ากันไหม" ไม่ได้เทียบว่าใหม่กว่า/เก่ากว่า** — ถ้า `manifest.json.version` ผิด (เช่น
  ใส่เลขเวอร์ชันเก่ากว่าที่บอร์ดรันอยู่โดยไม่ตั้งใจ) บอร์ดจะเสนอ "มีอัปเดต" ให้ทันที โดยไม่รู้ว่า
  จริง ๆ คือการ downgrade — ตรวจเลขเวอร์ชันให้ตรงกับ `FW_VERSION` ในซอร์สทุกครั้งก่อน push
- อย่า push `.bin` ที่ยังไม่ได้ทดสอบบนฮาร์ดแวร์จริงขึ้น release — บอร์ด production จะดึงไปติดตั้ง
  ทันทีที่มีคนสั่ง `/update install` ไม่มีขั้นตอนตรวจสอบอื่นคั่นอีก
