# الدورة: Phase 5 – Advanced Cisco Enterprise (Weeks 21–30)
## الوحدة 6: CCNP Enterprise Security (Concentration)
### الدرس: AAA Security (معمارية المصادقة والتفويض والمحاسبة)

---

### 1. مقدمة (Introduction)

تُعد عميلة **AAA** (Authentication, Authorization, and Accounting) حجر الزاوية في أمان الشبكات المؤسسية الحديثة. في امتحان التركيز (Concentration) الخاص بـ CCNP، لا يقتصر الأمر على فهم الأوامر الأساسية، بل يجب فهم كيفية تفاعل هذه الخدمات مع خوادم مركزية مثل **Cisco ISE** (Identity Services Engine) لتوفير أمان ديناميكي ومركزي.

---

### 2. مكونات AAA (Components of AAA)

لفهم الموضوع، يجب تفكيك المصطلح إلى ثلاثة أجزاء أساسية:

#### أ. التوثيق (Authentication)
هي عملية التحقق من هوية المستخدم أو الجهاز الذي يحاول الوصول إلى الشبكة.
*   **الهدف:** التأكد من أن "من أنت" هو بالفعل "من تدعي أنك".
*   **الطرق الشائعة:**
    1.  **Local Database:** استخدام قاعدة البيانات المحلية على الجهاز (Router/Switch).
    2.  **RADIUS (Remote Authentication Dial-In User Service):** بروتوكول يجمع بين التوثيق والتفويض والمحاسبة، ويعمل عادة عبر بروتوكول UDP.
    3.  **TACACS+ (Terminal Access Controller Access-Control System Plus):** بروتوكول من كسكو يفصل بين العمليات الثلاث (فصل التوثيق عن التفويض والمحاسبة). يعمل عبر TCP (Port 49) مما يجعله أكثر موثوقية في الشبكات المعقدة.
    4.  **Kerberos:** يستخدم في بيئات Microsoft Active Directory.

#### ب. التفويض (Authorization)
بعد نجاح عملية التوثيق، يحدد هذا الجزء ما الذي مسموح للمستخدم أو الجهاز بفعله.
*   **الهدف:** تحديد الصلاحيات (Privileges) والوصول (Access Rights).
*   **الأنواع:**
    1.  **Command Authorization:** تحديد أوامر CLI المسموح بها (مثلاً: منع مستخدم من استخدام أمر `configure terminal`).
    2.  **Security Policy Authorization:** تحديد معايير الأمان (مثلاً: تعيين VLAN معين، أو تطبيق ACL معين عند الاتصال).

#### ج. المحاسبة (Accounting)
عملية تتبع وتسجيل نشاط المستخدم بعد دخوله الشبكة.
*   **الهدف:** التدقيق (Auditing)، الفوترة (Billing)، والتحليل الأمني (Security Analysis).
*   **البيانات المسجلة:** وقت الدخول، وقت الخروج، عدد البكسلات المنقولة، الأوامر التي نفذها المستخدم.

---

### 3. إطار عمل AAA في كسكو (AAA Framework)

لتفعيل خدمات AAA على أجهزة كسكو (IOS/IOS-XE)، يجب اتباع الخطوات التالية بدقة:

#### الخطوة الأولى: تفعيل نموذج AAA
الأمر الأساسي لتفعيل الخدمات هو:
`aaa new-model`
*(بدون هذا الأمر، لن تعمل أوامر التكوين الخاصة بـ AAA).*

#### الخطوة الثانية: إعداد الخادم (Server Group)
يجب تعريف الخوادم التي ستقوم بالتوثيق. في بيئة CCNP Concentration، عادة ما نستخدم **Cisco ISE** كمجموعة خوادم.

```cisco
aaa group server radius ise-server-group
 server name ISE-Primary
 server name ISE-Secondary
!
aaa group server tacacs+ ise-tacacs-group
 server name ISE-TACACS
```

#### الخطوة الثالثة: تطبيق سياسات AAA
هنا نربط العمليات بالخوادم.

**1. إعداد التوثيق (Authentication):**
نستخدم الأمر `aaa authentication` لتحديد أولويات التحقق.
```cisco
! مثال: التوثيق عبر مجموعة ISE، وفي حالة الفشل نستخدم قاعدة البيانات المحلية
aaa authentication login default group ise-tacacs-group local
```

**2. إعداد التفويض (Authorization):**
يتم التفويض عادة لأوامر الـ Privilege Level 15 (المسؤولون).
```cisco
! تفويض الأوامر عبر ISE، والفشل عبر المحلي
aaa authorization commands 15 default group ise-tacacs-group local
```

**3. إعداد المحاسبة (Accounting):**
```cisco
! بدء المحاسبة عند الدخول والإنهاء عند الخروج
aaa accounting commands 15 default start-stop group ise-tacacs-group
```

---

### 4. التمييز بين RADIUS و TACACS+ (مهم لـ Concentration)

في امتحان التركيز، يجب أن تعرف متى تستخدم كل بروتوكول:

| الخاصية | RADIUS | TACACS+ |
| :--- | :--- | :--- |
| **البروتوكول الناقل** | UDP (Port 1812/1813) | TCP (Port 49) |
| **تشفير البيانات** | يشفر فقط كلمة المرور (Password) | يشفر الحزمة الكاملة (Full Payload Encryption) |
| **الفصل بين العمليات** | يجمع التوثيق والتفويض | يفصل التوثيق عن التفويض والمحاسبة |
| **دعم التوجيه** | يدعم التوجيه (Routing) بشكل أفضل | لا يدعم التوجيه (Routing) |
| **الاستخدام الرئيسي** | اتصالات Remote Access (VLANs, ACLs) | إدارة أجهزة الشبكة (Switches, Routers, Firewalls) |

---

### 5. تكامل AAA مع Cisco ISE (Concentration Focus)

في البيئات الحديثة، لا تُضبط سياسات AAA على الجهاز (Switch/Router) مباشرة، بل يتم إرسال الطلب إلى **Cisco ISE** الذي يقرأ السياسات (Profiling, Authorization Profiles) ويرسل الرد (Accept/Reject/Challenge).

#### سيناريوهات هامة في الامتحان:

1.  **MAB (MAC Authentication Bypass):**
    *   للأجهزة التي لا تدعم 802.1X (مثل الطابعات، الهواتف IP).
    *   يستخدم عنوان MAC كاسم مستخدم وكلمة مرور.
    *   يتم تفعيله عبر `aaa authentication dot1x default group radius`.

2.  **802.1X (Port-based Network Access Control):**
    *   يتطلب عميل (Supplicant) على الجهاز، وخادم (Authenticator) على السويتش، وخادم (Authentication Server) هو ISE.
    *   يستخدم بروتوكول EAP (Extensible Authentication Protocol).

3.  **Web Authentication (WebAuth):**
    *   يستخدم للشبكات الضيافة (Guest Access) حيث يتم توجيه المستخدم لصفحة ويب لإدخال بيانات الدخول.

---

### 6. أخطاء شائعة وحلول (Troubleshooting)

*   **فشل التوثيق:** تأكد من تطابق كلمة المرور السرية (Shared Secret) بين السويتش و ISE.
*   **عدم عمل التفويض:** تأكد من تفعيل `aaa new-model` وأنك حددت `group` بشكل صحيح في أمر الـ authorization.
*   **فقدان الاتصال بالخادم:** يجب دائماً وضع `local` أو `enable` كخيار احتياطي (Fallback) في أوامر الـ AAA لمنع إقفال نفسك عن الجهاز (Lockout).

---

### 7. ملخص الدرس (Summary)

*   **AAA** هي العمود الفقري لأمان الدخول.
*   **TACACS+** هو البروتوكول المفضل لإدارة أجهزة الشبكة (Device Management) لأنه يفصل العمليات ويشفر البيانات بالكامل.
*   **RADIUS** يستخدم غالباً لتوجيه المستخدمين (User Access) وتعيين السياسات (VLANs/ACLs).
*   في امتحان **CCNP Enterprise Security**، يجب أن تفهم كيف يرسل السويتش طلب الـ AAA إلى **Cisco ISE**، وكيف يقرأ ISE الـ Authorization Profile ويعيد النتائج.

---
*ملاحظة: تم تحديث المعلومات لتعكس الممارسات الحديثة في شبكات كسكو لعام 2024 وما بعده.*
