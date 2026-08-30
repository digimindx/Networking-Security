# دورة CCNA - التدريب العملي
## موضوع المختبر: تكوين الراوترات والسويتشات (Configuring Routers & Switches)

### 1. مقدمة (Introduction)
في هذا المختبر، سنتعلم كيفية الدخول إلى واجهة سطر الأوامر (Command Line Interface - CLI) لأجهزة الشبكة (Cisco Devices)، وتطبيق الإعدادات الأساسية التي تجعل الجهاز قادراً على العمل والاتصال بالشبكة. سنركز على أهم أوامر التكوين (Configuration Commands) التي تشكل أساس أي مهندس شبكات.

### 2. المصطلحات الأساسية (Key Concepts)
قبل البدء، يجب فهم الوضعيات (Modes) المختلفة في الجهاز:
*   **وضع المستخدم (User EXEC Mode):** يظهر الرمز `&gt;`، يسمح فقط بأوامر العرض (View-only commands).
*   **وضع المميز (Privileged EXEC Mode):** يظهر الرمز `#`، يسمح بمراقبة الجهاز وتكوينه.
*   **وضع التكوين العام (Global Configuration Mode):** يظهر الرمز `(config)#`، يسمح بتغيير إعدادات النظام الأساسية.

---

### 3. الجزء الأول: تكوين الراوتر (Router Configuration)

الهدف هو تغيير اسم الجهاز، تأمين الدخول، وتعيين عناوين الـ IP للواجهات (Interfaces).

#### الخطوة 1: الدخول وتغيير الاسم (Hostname)
*   استخدم الأمر `enable` للدخول إلى وضع المميز.
*   استخدم الأمر `configure terminal` للدخول إلى وضع التكوين.
*   غير اسم الجهاز الافتراضي إلى اسم مميز.

**الأوامر (Commands):**
```text
Router&gt; enable
Router# configure terminal
Router(config)# hostname R1
R1(config)#
```

#### الخطوة 2: تأمين الدخول (Security)
يجب تعيين كلمة مرور لوضع المميز ولوحة المفاتيح (Console) والمنافذ عن بُعد (VTY).

**الأوامر (Commands):**
```text
! كلمة مرور وضع المميز (Enable Secret)
R1(config)# enable secret cisco123

! تأمين منفذ الكونسول (Console Port)
R1(config)# line console 0
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit

! تأمين المنافذ عن بُعد (VTY Lines)
R1(config)# line vty 0 4
R1(config-line)# password cisco
R1(config-line)# login
R1(config-line)# exit
```

#### الخطوة 3: تكوين الواجهات (Interface Configuration)
تعيين عناوين الـ IP (IP Address) وقناع الشبكة الفرعية (Subnet Mask) وتفعيل المنفذ (No Shutdown).

**الأوامر (Commands):**
```text
R1(config)# interface gigabitEthernet 0/0
R1(config-if)# description Link-to-Switch
R1(config-if)# ip address 192.168.1.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit

R1(config)# interface gigabitEthernet 0/1
R1(config-if)# ip address 10.0.0.1 255.255.255.0
R1(config-if)# no shutdown
R1(config-if)# exit
```
*ملاحظة: الأمر `no shutdown` ضروري لأن الواجهات تكون معطلة (Shutdown) افتراضياً.*

---

### 4. الجزء الثاني: تكوين السويتش (Switch Configuration)

السويتشات (Layer 2 Devices) تختلف قليلاً، حيث نحتاج لتعيين عنوان IP لإدارتها عن بُعد (Management IP).

#### الخطوة 1: الدخول وتغيير الاسم
```text
Switch&gt; enable
Switch# configure terminal
Switch(config)# hostname SW1
```

#### الخطوة 2: تعيين عنوان الإدارة (Management IP)
يتم وضع الـ IP داخل واجهة افتراضية (VLAN 1).

**الأوامر (Commands):**
```text
SW1(config)# interface vlan 1
SW1(config-if)# ip address 192.168.1.2 255.255.255.0
SW1(config-if)# no shutdown
SW1(config-if)# exit
```

#### الخطوة 3: تعيين البوابة الافتراضية (Default Gateway)
إذا كان السويتش يحتاج للاتصال بشبكات أخرى عن بُعد، يجب تعيين البوابة.

**الأمر (Command):**
```text
SW1(config)# ip default-gateway 192.168.1.1
```

#### الخطوة 4: تأمين الدخول (VTY Password)
```text
SW1(config)# line vty 0 15
SW1(config-line)# password cisco
SW1(config-line)# login
SW1(config-line)# exit
```

---

### 5. التحقق والحفظ (Verification & Saving)

بعد الانتهاء من التكوين، يجب التحقق من الإعدادات وحفظها حتى لا تضيع عند إعادة التشغيل (Reboot).

#### التحقق من الإعدادات (Verification)
استخدم هذه الأوامر للتأكد من صحة التكوين:

1.  **التحقق من حالة الواجهات (Interface Status):**
    ```text
    R1# show ip interface brief
    ```
    *يجب أن تظهر الحالة `up` و `up`.*

2.  **عرض التكوين الحالي (Running Configuration):**
    ```text
    R1# show running-config
    ```

3.  **عرض الجدول (ARP Table):**
    ```text
    R1# show arp
    ```

#### حفظ الإعدادات (Saving Configuration)
*   **الحفظ (Copy):**
    ```text
    R1# copy running-config startup-config
    ```
    أو باستخدام الأمر المختصر:
    ```text
    R1# write memory
    ```
    *هذا ينقل الإعدادات من الذاكرة العشوائية (RAM) إلى الذاكرة الدائمة (NVRAM).*

---

### 6. تمرين عملي مقترح (Lab Exercise)

**السيناريو (Scenario):**
لديك جهاز روتر واحد وجهاز سويتش واحد.
1.  غير اسم الراوتر إلى `R-Cisco` واسم السويتش إلى `SW-Cisco`.
2.  اجعل واجهة الجيجابت 0/0 في الراوتر تحمل الـ IP `172.16.10.1` وقناع الشبكة `255.255.255.0`.
3.  اجعل واجهة الجيجابت 0/1 في الراوتر تحمل الـ IP `172.16.20.1` وقناع الشبكة `255.255.255.0`.
4.  اجعل السويتش يحمل الـ IP `172.16.10.2` وقناع الشبكة `255.255.255.0` والبوابة الافتراضية هي `172.16.10.1`.
5.  قم بحفظ الإعدادات على كلا الجهازين.

**نصيحة (Tip):** تأكد دائماً من استخدام الأمر `no shutdown` عند إضافة أي واجهة جديدة (Interface)، وإلا لن تعمل الاتصال.

---
---
