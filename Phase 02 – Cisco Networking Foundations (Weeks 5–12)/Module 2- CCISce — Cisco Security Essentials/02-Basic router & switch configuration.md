# التهيئة الأساسية للراوتر والسويتش (Basic Router & Switch Configuration)

يهدف هذا الدليل إلى شرح الخطوات الأساسية لتهيئة أجهزة الشبكة من نوع (Cisco) بدءاً من الوصول إليها وصولاً إلى إعداد العناوين البروتوكولية (IP Addresses) وكلمات المرور. سنقسم المحتوى إلى جزأين رئيسيين: الأول للراوتر (Router) والثاني للسويتش (Switch).

---

## التهيئة الأساسية للراوتر (Router Configuration)

### 1. الوصول إلى الجهاز (Accessing the Device)
لبدء التهيئة، يجب الاتصال بجهاز الراوتر عبر كابل (Console Cable) واستخدام برنامج طرفية (Terminal Software) مثل (PuTTY). عند الاتصال، ستظهر لك شاشة الأوامر.
![[Pasted image 20260713160915.png | 256]]
### 2. التنقل بين الأوضاع (Modes Navigation)
![[Pasted image 20260713161331.png]]

![[Pasted image 20260713161417.png]]
تتبع أجهزة (Cisco) تسلسلاً محدداً للأوضاع:
*   **(User EXEC Mode)**: الوضع الافتراضي عند الدخول، يُسمح فيه فقط بالأوامر الأساسية للمراقبة.
*   **(Privileged EXEC Mode)**: الوضع الذي يمنح صلاحيات كاملة للإدارة والتعديل.
    *   *الأمر*: `enable`
*   **(Global Configuration Mode)**: الوضع الذي يسمح بتعديل إعدادات الجهاز الأساسية.
    *   *الأمر*: `configure terminal` أو `conf t` 

### 3. إعدادات الأمان الأساسية (Basic Security Settings)
يجب تأمين الجهاز لمنع الوصول غير المصرح به:

1.  **تغيير اسم الجهاز (Hostname):**
    *   *الأمر*: `hostname [Name]`
2.  **تأمين وضع الإدارة (Privileged Mode Password):**
    *   *الأمر*: `enable secret [Password]`
3.  **تأمين خط الكونسول (Console Line):**
    *   *الأمر*: `line console 0`
    *   *الأمر*: `password [Password]`
    *   *الأمر*: `login`
4.  **تأمين الوصول عن بعد (VTY Lines):**
    *   *الأمر*: `line vty 0 15`
    *   *الأمر*: `password [Password]`
    *   *الأمر*: `login`

### 4. تهيئة الواجهات (Interface Configuration)
لكي يعمل الراوتر، يجب تعيين عنوان (IP Address) لكل واجهة متصلة بشبكة.

1.  **دخول وضع الواجهة:**
    *   *الأمر*: `interface [Type] [Number]` (مثال: `interface GigabitEthernet0/0`)
2.  **تعيين عنوان الـ IP:**
    *   *الأمر*: `ip address [IP] [Subnet Mask]`
3.  **تفعيل الواجهة (Activation):**
    *   *الأمر*: `no shutdown`
    *   *ملاحظة*: الواجهات تكون معطلة افتراضياً، لذا يجب استخدام هذا الأمر لتفعيلها.
4.  **تعيين قناع البوابة الافتراضي (Default Gateway):**
    *   *الأمر*: `ip default-gateway [IP Address]` (يستخدم عادة في السويتشات، ولكن يمكن ذكره هنا للإيضاح).

---

## التهيئة الأساسية للسويتش (Switch Configuration)

تتشابه خطوات تهيئة السويتش مع الراوتر، ولكن مع اختلافات طفيفة خاصة بالـ (VLANs).

### 1. الدخول إلى الأوضاع (Entering Modes)
*   الانتقال من **(User EXEC Mode)** إلى **(Privileged EXEC Mode)** باستخدام أمر `enable`.
*   الانتقال إلى **(Global Configuration Mode)** باستخدام أمر `configure terminal`.

### 2. الإعدادات الأساسية (Basic Settings)
*   **تغيير الاسم**: `hostname [Name]`
*   **كلمة مرور الإدارة**: `enable secret [Password]`

### 3. تهيئة واجهة الإدارة (Management Interface)
السويتش يحتاج إلى عنوان (IP Address) ليس للربط بين الأجهزة، بل لإدارته عن بعد عبر بروتوكول (SSH) أو (Telnet).

1.  **دخول وضع VLAN الافتراضي:**
    *   *الأمر*: `interface vlan 1`
    *   *ملاحظة*: الـ (VLAN 1) هو الـ (Native VLAN) الافتراضي في السويتشات.
2.  **تعيين عنوان الـ IP:**
    *   *الأمر*: `ip address [IP] [Subnet Mask]`
3.  **تفعيل الواجهة:**
    *   *الأمر*: `no shutdown`

### 4. تهيئة المنافذ (Port Configuration)
تحديد نوع المنفذ (Access Port) المتصل بجهاز العميل.

1.  **دخول وضع المنفذ:**
    *   *الأمر*: `interface [Type] [Number]` (مثال: `interface FastEthernet0/1`)
2.  **تحديد وضع المنفذ:**
    *   *الأمر*: `switchport mode access`
3.  **تسمية المنفذ (Description):**
    *   *الأمر*: `description [Description]` (مثال: `description PC-Lab-1`)

---

## حفظ الإعدادات (Saving Configuration)

بعد الانتهاء من جميع الإعدادات، يجب حفظ التكوين الحالي (Running Configuration) في الذاكرة الدائمة (Startup Configuration) لضمان بقائه بعد إعادة تشغيل الجهاز.

*   **الأمر**: `copy running-config startup-config`
*   **أو**: `write memory`

---

## ملخص الأوامر الرئيسية (Command Summary)

| الوظيفة | الأمر (Command) |
| :--- | :--- |
| الدخول لوضع الإدارة | `enable` |
| الدخول للإعدادات العامة | `configure terminal` |
| تغيير اسم الجهاز | `hostname [Name]` |
| تعيين كلمة مرور الإدارة | `enable secret [Pass]` |
| تعيين عنوان IP | `ip address [IP] [Mask]` |
| تفعيل الواجهة | `no shutdown` |
| الخروج إلى الوضع السابق | `exit` |
| عرض الإعدادات الحالية | `show running-config` |
| عرض عناوين IP | `show ip interface brief` |

---
---
