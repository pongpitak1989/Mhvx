# Appium Guide Documentation

โฟลเดอร์นี้มีแค่ไฟล์ Markdown (แหล่งเนื้อหาหลัก) และสคริปต์สำหรับสร้างไฟล์ .docx ถ้าคุณอยากได้ภายหลัง เพื่อให้ใช้ง่ายที่สุด เราเอาไฟล์ Base64 placeholder (.docx.b64) ออกแล้ว ไม่ต้องสับสน

## ไฟล์ที่มี
- Appium_Full_Guide.md : ใช้อ่าน / แก้ไข / รีวิว (Source of Truth)
- ../scripts/build_full_guide.py : ตัวอย่างสคริปต์สร้าง .docx (คุณจะใช้หรือไม่ใช้ก็ได้)
- ../scripts/requirements-docs.txt : dependency สำหรับสคริปต์ (python-docx)

## ถ้าต้องการไฟล์ Word (.docx) ในภายหลัง (เลือกอย่างใดอย่างหนึ่ง)
1) ง่ายที่สุด (แนะนำ): เปิด Markdown ตรง ๆ ใน GitHub หรือ VS Code แล้ว Export (เช่น VS Code Extension: Markdown PDF / pandoc)
2) ใช้สคริปต์ที่ให้มา:
```
pip install -r scripts/requirements-docs.txt
python scripts/build_full_guide.py
# จะได้ไฟล์ Appium_Full_Guide_generated.docx
```
3) ใช้ pandoc (ถ้าติดตั้งไว้):
```
pandoc docs/Appium_Full_Guide.md -o Appium_Full_Guide.docx
```

## ทำไมลบไฟล์ .docx.b64
- เดิมเป็น placeholder ไม่มีเนื้อหาจริง
- ลดความสับสน / ไม่ต้อง decode
- ลด binary noise ใน PR

## แนวทางการแก้ไขเอกสาร
1. แก้ `docs/Appium_Full_Guide.md`
2. Commit & Push (PR จะอัปเดตอัตโนมัติ)
3. ถ้าวันหนึ่งต้องการแจก Word: สร้างตามขั้นตอนด้านบน แล้วส่งไฟล์นอก repo หรือแนบใน Release

## ถาม-ตอบสั้น
Q: ทำไมไม่มีไฟล์ .docx?  
A: เราทำให้ใช้ง่ายสุด — ใช้ Markdown ตรง ๆ ได้เลย; สร้าง Word เองเมื่อจำเป็น

Q: ถ้าผมอยากได้ Word ตอนนี้?  
A: รันคำสั่งสั้น ๆ (ดูหัวข้อด้านบน) หรือบอกผมให้สร้างให้ในการสนทนานี้

---
(End)