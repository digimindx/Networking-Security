# المحتوى التدريبي: تنفيذ الوصول القائم على الهوية (Implement Identity-Based Access)
**من Module 6 - CCNP Enterprise Security (Core + Concentration)**

## 1. المقدمة (Introduction)
في الشبكات المؤسسية الحديثة، لم يعد الاعتماد على عنوان الـ IP (IP Address) كافياً لتحديد صلاحيات المستخدم أو الجهاز. الانتقال إلى نموذج "الوصول القائم على الهوية" يعني أن قرارات الأمان (Access Control Decisions) تعتمد على هوية المستخدم (User Identity) أو هوية الجهاز (Device Identity) بدلاً من الموقع الجغرافي أو عنوان الشبكة.

يعتمد هذا المفهوم بشكل أساسي على تقنية (Cisco Identity Services Engine) أو (Cisco ISE) وبروتوكول (AAA) المتطور.

---

## 2. إطار عمل AAA (AAA Framework)
أساس الوصول الآمن هو خدمة (AAA) التي تتكون من ثلاثة أركان:

1.  **المصادقة (Authentication):** التحقق من صحة هوية المستخدم أو الجهاز.
    *   السؤال: "هل أنت الشخص الذي تزعم أنك؟"
2.  **الترخيص (Authorization):** تحديد ما الذي يسمح للمستخدم أو الجهاز بالقيام به بعد المصادقة.
    *   السؤال: "ما الذي يسمح لك بالقيام به؟" (مثل: الـ VLAN، أو الـ ACL).
3.  **المحاسبة (Accounting):** تسجيل الأنشطة التي قام بها المستخدم.
    *   السؤال: "ماذا فعلت؟" (مثل: وقت الاتصال، حجم البيانات المنقولة).

### الفرق بين بروتوكولي RADIUS و TACACS+
في سياق CCNP Enterprise، يجب التمييز بينهما بوضوح:
*   **RADIUS (Remote Authentication Dial-In User Service):** يدمج المصادقة والترخيص في عملية واحدة، ويستخدم بروتوكول (UDP). يُستخدم غالباً للوصول العام للمستخدمين.
*   **TACACS+ (Terminal Access Controller Access-Control System Plus):** يفصل بين المصادقة والترخيص والمحاسبة، ويستخدم بروتوكول (TCP). يُفضل لإدارة أجهزة الشبكة (Cisco Devices) لأنه يدعم تشفير كامل الحزمة (Payload Encryption).

---

## 3. تقنيات الوصول إلى الشبكة (Network Access Control - NAC)
لتنفيذ الوصول القائم على الهوية، نستخدم عدة آليات (Mechanisms) تعتمد على نوع الجهاز:

### أ. بروتوكول 802.1X (802.1X Protocol)
هو المعيار القياسي للمصادقة على المنافذ (Port-Based Network Access Control). يتكون من ثلاثة أطراف:
1.  **العميل (Supplicant):** الجهاز الذي يطلب الدخول (مثل Laptop).
2.  **الوسيط (Authenticator):** الجهاز الذي يتحكم في المنفذ (مثل Cisco Switch).
3.  **الخادم (Authentication Server):** الخادم الذي يتحقق من البيانات (مثل Cisco ISE).

*   **أنواع بروتوكولات EAP (Extensible Authentication Protocol):**
    *   **EAP-FAST:** يستخدم "PAC" (Protected Access Credential).
    *   **PEAP (Protected EAP):** يستخدم شهادة خادم TLS لإنشاء قناة آمنة.
    *   **EAP-TLS:** يستخدم شهادات رقمية (Digital Certificates) لكل من العميل والخادم (الأكثر أماناً).

### ب. المصادقة عبر عنوان MAC (MAC Authentication Bypass - MAB)
تُستخدم للأجهزة التي لا تدعم بروتوكول 802.1X (مثل الطابعات، كاميرات المراقبة، أجهزة VoIP). هنا، يستخدم الوسيط (الـ Switch) عنوان الـ MAC للجهاز كاسم مستخدم وكلمة مرور لإرسال طلب المصادقة للخادم.

### ج. المصادقة عبر الويب (Web Authentication - WebAuth)
تُستخدم للأجهزة العامة (Guest Devices) التي لا يمكن تثبيت عميل عليها. يقوم المستخدم بفتح المتصفح، ويتم تحويله لصفحة تسجيل دخول ( captive portal)، وبعد إدخال البيانات، يتم فتح المنفذ.

---

## 4. دور Cisco ISE في الوصول القائم على الهوية
يعتبر (Cisco ISE) العمود الفقري للحل. في الامتحان العملي (Lab)، يجب أن تفهم المكونات التالية:

1.  **Policy Administration Node (PAN):** مركز إدارة السياسات (Policies).
2.  **Monitoring Node (MnT):** لتسجيل البيانات والتحليل (Monitoring & Troubleshooting).
3.  **Profiling:** قدرة الـ ISE على تحديد نوع الجهاز (Endpoint) بناءً على حركة المرور (Fingerprinting) قبل حتى أن يحاول الدخول.

**آلية العمل (Workflow):**
1.  يحاول الجهاز الاتصال بالـ Switch.
2.  الـ Switch يرسل طلب (RADIUS Access-Request) إلى الـ ISE.
3.  الـ ISE يطابق البيانات مع قاعدة البيانات (Identity Store) (مثل Active Directory).
4.  الـ ISE يعيد نتيجة المصادقة (Access-Accept) مع سياسات (Authorization Profile) تحدد الـ VLAN أو الـ ACL.

---

## 5. سيناريو عملي: تطبيق الوصول القائم على الهوية (Lab Scenario)

في هذا القسم، نوضح الخطوات التقنية لتطبيق الوصول الآمن باستخدام (Cisco ISE) و (Cisco Switch).

### الخطوة الأولى: إعداد الـ Switch (Authenticator)
يجب على السويتش أن يعرف كيفية التحدث مع خادم المصادقة.

```bash
! 1. إنشاء مفتاح سري لبروتوكول RADIUS
radius server ISE-Server
 address ipv4 10.10.10.1 auth-port 1812 acct-port 1813
 key SharedSecretKey123

! 2. تفعيل 802.1X عالمياً
dot1x system-auth-control

! 3. إعداد المنفذ (Port) ليقبل 802.1X
interface GigabitEthernet1/0/1
 switchport mode access
 dot1x pae authenticator
 dot1x timeout tx-period 10
! إذا كان الجهاز قد لا يدعم 802.1X نستخدم MAB كخيار احتياطي
 dot1x host-mode multi-domain
 mab
```

### الخطوة الثانية: إعداد الـ ISE (Policy)
في لوحة تحكم الـ ISE (Administration Node):

1.  **Identity Stores:** التأكد من أن (Active Directory) متصل ومضاف كـ (Internal Endpoints) أو (External Identity Source).
2.  **Endpoints:** إضافة الأجهزة يدوياً أو تفعيل (Profiling) لاكتشافها تلقائياً.
3.  **Access Policies:** إنشاء قواعد (Policy Sets).
    *   *Condition:* Device Type is "Windows Workstation".
    *   *Result:* Authorization Profile = "VLAN-Corporate" + "ACL-Standard".

---

## 6. مفهوم TrustSec و SGT (Security Group Tags)
في إطار "Concentration" الخاص بالأمن المتقدم، لا يكفي وضع المستخدم في VLAN. ننتقل إلى مفهوم (TrustSec).

*   **SGT (Security Group Tag):** هو وسم (Tag) يُضاف إلى حزم البيانات (Frame Tagging) لتحديد هوية المجموعة الأمنية للمستخدم.
*   **SXP (Security Group Tag Exchange Protocol):** هو البروتوكول الذي ينقل معلومات الـ SGT عبر الشبكة (بين الـ ISE والـ Switches) عندما لا تدعم الأجهزة تقنية (MACsec) أو (Dot1ad).

**الفائدة:** يسمح هذا بفصل المستخدمين عن بعضهم البعض (Micro-segmentation) بغض النظر عن موقعهم في الشبكة (VLAN)، مما يعزز الأمان في بيئات (SD-Access).

---

## 7. التحقق من العمل (Verification Commands)

للتأكد من نجاح المصادقة في الـ Switch:

```bash
show authentication sessions interface Gi1/0/1
```
*   يجب أن تظهر الحالة: `authenticated`.
*   يجب أن تظهر الـ VLAN المخصصة.
*   يجب أن تظهر اسم المستخدم أو الـ MAC Address.

للتحقق من تفاصيل الاتصال مع الـ ISE:
```bash
show radius server summary
show dot1x all
```

---

## 8. الخلاصة (Conclusion)
تنفيذ الوصول القائم على الهوية (Identity-Based Access) في بيئة Cisco Enterprise يتطلب دمجاً دقيقاً بين:
1.  إعدادات الـ Switch (Authenticator).
2.  تكوين الـ ISE (Policy & Identity Store).
3.  اختيار البروتوكول المناسب (802.1X للمعدات الحديثة، MAB للأجهزة البسيطة).
4.  تطبيق سياسات الـ SGT للتجزئة الأمنية المتقدمة.

هذا الموضوع هو حجر الزاوية في شهادة CCNP Enterprise Security (ENSA)، حيث ينتقل المهندس من إدارة "الاتصال" (Connectivity) إلى إدارة "الهوية" (Identity).
