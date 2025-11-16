# คู่มือการติดตั้งและใช้งาน Appium สำหรับทดสอบแอป Android (Python + VS Code)

เวอร์ชัน: 1.0  
ผู้จัดทำ: (เติมชื่อ)  
วันที่: (เติมวันที่)

## สารบัญ
(จะสร้าง TOC อัตโนมัติเมื่อเปิดใน Word หรือใช้ pandoc --toc)

## 1. ภาพรวม (Overview)
Appium คือ Mobile Automation Framework บนมาตรฐาน W3C WebDriver รองรับ Native / Hybrid / Mobile Web ทั้ง Android และ iOS โดยผู้ใช้สามารถเลือกภาษาโปรแกรมที่ถนัด (ที่นี่ใช้ Python) ช่วยลดการซ้ำซ้อนและเพิ่มความคล่องตัวในการพัฒนา Automated Test

### คุณสมบัติสำคัญ
- Cross-platform (Android / iOS)
- ใช้มาตรฐาน WebDriver (API คล้าย Selenium)
- ภาษาโปรแกรมหลากหลาย (Python / JS / Java / C# ฯลฯ)
- ทำงานกับ Native, Hybrid, Mobile Web

## 2. Prerequisites
- JDK 11 หรือ 17
- Android Studio + SDK (Platform Tools, Build-Tools, Emulator)
- Node.js LTS
- Appium Server 2.x + driver: uiautomator2
- Appium Inspector
- Python 3.10+
- VS Code (Extensions: Python, Pylance)
- อุปกรณ์จริง หรือ Emulator

## 3. การติดตั้งเครื่องมือ
### 3.1 ติดตั้ง JDK (ตัวอย่าง Ubuntu)
```
sudo apt update
sudo apt install -y openjdk-17-jdk
java -version
```
### 3.2 Android Studio
ติดตั้ง SDK + สร้าง AVD Emulator

### 3.3 ติดตั้ง Appium & Driver
```
npm install -g appium
appium -v
appium driver install uiautomator2
```
### 3.4 Virtual Environment + Libraries
```
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install --upgrade pip
pip install Appium-Python-Client pytest pytest-html pytest-xdist allure-pytest pyyaml
```

## 4. Environment Variables (ตัวอย่าง macOS/Linux)
เพิ่มใน ~/.zshrc หรือ ~/.bashrc:
```
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$JAVA_HOME/bin
export PATH=$PATH:$ANDROID_HOME/platform-tools:$ANDROID_HOME/emulator:$ANDROID_HOME/tools:$ANDROID_HOME/tools/bin
```
ตรวจสอบ:
```
adb version
emulator -list-avds
```

## 5. การเตรียมอุปกรณ์ (Device)
Real Device: เปิด Developer Options + USB Debugging → `adb devices`
Emulator: เปิดจาก Android Studio หรือ `emulator -avd <NAME>`

## 6. การเปิด Appium Server
```
appium
# หรือกำหนดพอร์ต
appium --port 4723
```
ตรวจสอบ: http://127.0.0.1:4723/status

## 7. ใช้ Appium Inspector
เตรียม Desired Capabilities กด Start Session → ตรวจ element attributes (resource-id, content-desc, text) → เลือก Locator ที่เสถียร

ตัวอย่าง capabilities (ติดตั้ง APK):
```json
{
  "platformName": "Android",
  "automationName": "UiAutomator2",
  "deviceName": "emulator-5554",
  "app": "/absolute/path/to/app-debug.apk",
  "appWaitActivity": "*",
  "unicodeKeyboard": true,
  "resetKeyboard": true
}
```

## 8. โครงสร้างโปรเจกต์
```
android-appium-demo/
  tests/
    test_sample_login.py
  pages/
    login_page.py
    home_page.py
  utils/
    waits.py
    logger.py
  config/
    capabilities.json
  reports/
  screenshots/
```

## 9. Desired Capabilities (สรุปสำคัญ)
| Key | คำอธิบาย | หมายเหตุ |
|-----|-----------|-----------|
| platformName | แพลตฟอร์ม | Android / iOS |
| automationName | Engine automation | UiAutomator2 สำหรับ Android สมัยใหม่ |
| deviceName | ชื่ออุปกรณ์ | emulator-5554 หรือ serial | 
| app | พาธไฟล์ .apk | หากไม่ใช้ ให้ระบุ appPackage/appActivity |
| appPackage | แพ็กเกจหลัก | com.example.demo |
| appActivity | Activity เริ่มต้น | .MainActivity หรือเต็ม | 
| noReset | ไม่ลบข้อมูลแอป | ลดเวลารันทดสอบระหว่าง dev |
| fullReset | ติดตั้งใหม่เต็ม | ใช้เมื่อ state มีผล |
| unicodeKeyboard/resetKeyboard | รองรับไทย | ใช้พิมพ์ภาษาไทย |

## 10. สคริปต์พื้นฐาน (Demo)
```python
from appium import webdriver
from appium.webdriver.common.appiumby import AppiumBy
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

caps = {
  "platformName": "Android",
  "automationName": "UiAutomator2",
  "deviceName": "emulator-5554",
  "app": "/absolute/path/to/app-debug.apk",
  "appWaitActivity": "*",
  "newCommandTimeout": 300
}

driver = webdriver.Remote("http://127.0.0.1:4723", caps)
try:
    wait = WebDriverWait(driver, 20)
    username = wait.until(EC.presence_of_element_located((AppiumBy.ID, "com.example.demo:id/et_username")))
    password = driver.find_element(AppiumBy.ID, "com.example.demo:id/et_password")
    login_btn = driver.find_element(AppiumBy.ID, "com.example.demo:id/btn_login")

    username.send_keys("admin")
    password.send_keys("123456")
    login_btn.click()

    welcome = wait.until(EC.presence_of_element_located(
        (AppiumBy.ANDROID_UIAUTOMATOR, 'new UiSelector().textContains("Welcome")')
    ))
    assert "Welcome" in welcome.text
finally:
    driver.quit()
```

## 11. Pytest + Page Object
ตัวอย่าง login_page.py, home_page.py + test_sample_login.py ใช้ fixture driver

### login_page.py (ย่อ)
```python
class LoginPage:
    def __init__(self, driver):
        self.driver = driver
    def input_username(self, text):
        el = self.driver.find_element(AppiumBy.ID, "com.example.demo:id/et_username")
        el.send_keys(text)
```

## 12. Locator Strategies
ลำดับแนะนำ: resource-id > accessibility id > UIAutomator > xpath

## 13. Waiting Strategies
ใช้ Explicit Wait ผ่าน WebDriverWait + expected_conditions / เฉพาะจุด

## 14. Unicode / ภาษาไทย
เพิ่ม capability unicodeKeyboard / resetKeyboard → ส่งคีย์ภาษาไทยได้

## 15. Screenshot & Logs
```
driver.save_screenshot("screenshots/login_success.png")
source = driver.page_source
```

## 16. Permission Handling
autoGrantPermissions หรือคลิก dialog ด้วย locator ของปุ่ม Allow

## 17. Performance Tips
- ปิดระบบ Animation บนอุปกรณ์
- ใช้ noReset ช่วงพัฒนา
- รัน parallel ผ่าน pytest -n 2

## 18. Utilities
waits.py รวม helper explicit wait, logger.py สร้างโครง logging

## 19. Troubleshooting (ตัวอย่าง)
| อาการ | สาเหตุ | แนวแก้ |
|-------|--------|--------|
| device offline | การเชื่อมต่อไม่เสถียร | replug / adb kill-server |
| SessionNotCreated | package/activity mismatch | adb shell dumpsys window | grep -i mCurrentFocus |
| INSTALL_FAILED_VERSION_DOWNGRADE | ติดตั้งเวอร์ชันต่ำกว่า | uninstall แอปก่อน |

## 20. Best Practices
Page Object + explicit waits + แยก config + logging + CI integration

## 21. Config & capabilities.json
รวมชุด base + profile (เช่น emulator, real) แล้ว merge ตอนสร้าง driver

## 22. CI Integration
GitHub Actions: ติดตั้ง JDK, SDK, สร้าง AVD, รัน emulator headless, pytest + รายงาน

## 23. Checklist
1) ติดตั้งเครื่องมือ  
2) ตั้ง PATH  
3) สร้าง emulator / ตรวจ device  
4) ติดตั้ง Appium + driver  
5) Inspector หา locator  
6) basic script  
7) Page Object + pytest  
8) รายงาน / screenshot  
9) CI workflow  
10) ขยาย parallel / Allure

## 24. FAQ (ย่อ)
Q: หา element ไม่เจอ?
A: ตรวจ context (Native/WebView) + ใช้ resource-id

Q: ทำไม timeout?
A: ปรับ implicit=0 ใช้ explicit wait

## 25. ขั้นต่อไป (Next Steps)
Allure, Parallel matrix, Hybrid (context switch), Visual regression, Remote device farm

## ภาคผนวก A – โค้ด
วางไฟล์จริง (basic_demo.py, test_sample_login.py, login_page.py, home_page.py)

## ภาคผนวก B – แผนภาพ
Workflow, Locator Strategy, Test Pyramid

---
(End)