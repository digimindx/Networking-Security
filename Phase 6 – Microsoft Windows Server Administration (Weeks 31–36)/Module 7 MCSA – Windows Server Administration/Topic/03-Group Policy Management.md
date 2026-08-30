# الوحدة التدريبية: إدارة سياسات المجموعة (Group Policy Management)
**المرجع:** Phase 6 – Module 7 – Windows Server Administration

## 1. المقدمة (Introduction)
تُعد سياسات المجموعة (Group Policy) إحدى الركائز الأساسية في إدارة البنية التحتية لتقنية المعلومات داخل شبكة تعمل بنظام (Active Directory). تسمح هذه الأداة للمسؤولين بتطبيق إعدادات موحدة ومحددة على المستخدمين (Users) والأجهزة (Computers) عبر الشبكة، مما يضمن الأمان، والامتثال، والتوحيد القياسي.

### المفاهيم الأساسية:
*   **Group Policy Object (GPO):** هو حاوية للإعدادات التي يتم تطبيقها على كائنات (Active Directory).
*   **Linking:** عملية ربط (GPO) بوحدة تنظيمية (Organizational Unit - OU) أو موقع (Site) أو نطاق (Domain).
*   **Inheritance:** الوراثة، حيث ترث الكائنات الفرعية الإعدادات من الكائنات الأب (Parent).

---

## 2. الأدوات المستخدمة (Management Tools)

لإدارة سياسات المجموعة، نعتمد على الأدوات التالية في نظام (Windows Server):

1.  **Group Policy Management Console (GPMC):** الأداة الرئيسية لإدارة، إنشاء، ربط، ومراقبة سياسات المجموعة. يتم تشغيلها عبر (rsop.msc) أو من خلال (Server Manager).
2.  **Group Policy Management Editor:** واجهة تظهر عند تعديل إعدادات (GPO)، وتحتوي على جزأين رئيسيين:
    *   **Computer Configuration:** الإعدادات التي تطبق على الجهاز بغض النظر عن المستخدم المسجل.
    *   **User Configuration:** الإعدادات التي تطبق على المستخدم بغض النظر عن الجهاز المستخدم.

---

## 3. إنشاء وربط سياسة المجموعة (Creating and Linking GPOs)

### خطوات الإنشاء:
1.  افتح أداة (Group Policy Management).
2.  قم بتوسيع شجرة (Active Directory).
3.  انقر بزر الماوس الأيمن على النطاق (Domain) أو الوحدة التنظيمية (OU) المستهدفة.
4.  اختر (Create a GPO in this domain, and Link it here).
5.  سمِّ السياسة (مثلاً: "Security-Baseline-GPO").

### نطاق التطبيق (Scope of Application):
يمكن ربط (GPO) بمستويات الهيكلية التالية:
*   **Site:** للمواقع الجغرافية للشبكة.
*   **Domain:** لكل النطاق في (Active Directory).
*   **Organizational Unit (OU):** للوحدات التنظيمية المحددة (الأكثر استخداماً للدقة).

---

## 4. إعدادات التكوين (Configuration Settings)

عند فتح (GPMC Editor)، نجد الفئات التالية:

### أ. Computer Configuration (تكوين الكمبيوتر):
*   **Policies:** تتضمن إعدادات النظام، جدار الحماية (Windows Defender Firewall)، والتسجيل (Registry).
*   **Preferences:** تتيح إعدادات ديناميكية (مثل تعيين محركات أقراص مشتركة Mapped Drives) دون الحاجة لكتابة (Group Policy Preferences - GPP).
*   **Startup Scripts:** نصوص برمجية تُنفذ عند بدء تشغيل النظام.

### ب. User Configuration (تكوين المستخدم):
*   **Policies:** تتضمن إعدادات سطح المكتب، المجلدات المحمية (Folder Redirection)، والوصول للشبكات.
*   **Preferences:** إعدادات مخصصة للمستخدم (مثل تغيير خلفية الشاشة).
*   **Logon Scripts:** نصوص برمجية تُنفذ عند تسجيل الدخول.

**ملاحظة هامة:** إعدادات (User Configuration) لا يتم تطبيقها إلا إذا قام المستخدم بتسجيل الدخول إلى جهاز تم ربط (GPO) به.

---

## 5. التصفية والوراثة (Filtering and Inheritance)

### أ. ترابط الوراثة (Inheritance):
بشكل افتراضي، ترث الوحدات التنظيمية الفرعية (Child OUs) السياسات من الوحدات الأب.
*   **Block Inheritance:** يمكن تفعيله في مستوى (OU) لمنع الوراثة من الأعلى.
*   **Enforced (No Override):** عند تفعيل هذا الخيار على (GPO) في مستوى علوي، يجبر النظام تطبيقه حتى لو تم حظر الوراثة في الأسفل.

### ب. التصفية الأمنية (Security Filtering):
تحديد من يمكنه قراءة وتطبيق (GPO). الافتراضي هو (Authenticated Users). يمكن إزالة هذا المستخدم وإضافة مجموعة (Security Group) محددة (مثلاً: "Server-Admins") لضمان التطبيق فقط على فئة معينة.

### ج. التصفية باستخدام WMI (WMI Filtering):
تسمح بتطبيق (GPO) بناءً على خصائص الجهاز (مثل إصدار نظام التشغيل، أو حجم القرص الصلب، أو اسم الجهاز). يتم إنشاء (WMI Query) وربطه بالسياسة.

---

## 6. ترتيب المعالجة (Processing Order)

تُطبق السياسات وفق ترتيب محدد، وإذا تعارضت الإعدادات، فإن "آخر كاتب يفوز" (Last Writer Wins). الترتيب هو:

1.  **Local Policy:** سياسات الجهاز المحلي.
2.  **Site Policy:** سياسات الموقع.
3.  **Domain Policy:** سياسات النطاق.
4.  **Organizational Unit (OU) Policy:** سياسات الوحدات التنظيمية (تطبق من الأعلى للأسفل).

**مبدأ LSR/LSDOU:**
*   **LSR:** Local, Site, Domain.
*   **LSDOU:** Local, Site, Domain, Organizational Unit.

---

## 7. استكشاف الأخطاء وإصلاحها (Troubleshooting)

للتأكد من تطبيق السياسات وحل المشاكل، نستخدم الأوامر التالية في موجه الأوامر (Command Prompt) أو (PowerShell):

1.  **تحديث السياسات فوراً:**
    ```cmd
    gpupdate /force
    ```
    (يُجبر الجهاز على سحب أحدث نسخة من (Group Policy).)

2.  **عرض السياسات المطبقة:**
    ```cmd
    gpresult /r
    ```
    (يعرض قائمة بالـ (GPO) التي تم تطبيقها على المستخدم الحالي أو الجهاز.)

3.  **عرض تفاصيل مفصلة (Resultant Set of Policy):**
    ```cmd
    rsop.msc
    ```
    (أداة رسومية تعرض جميع الإعدادات المطبقة ونتائجها.)

4.  **مسح ذاكرة التخزين المؤقت (Cache):**
    في بعض الأحيان تحتاج لإزالة الملفات المؤقتة في `C:\Windows\System32\GroupPolicy` لإعادة تطبيق الإعدادات.

---

## 8. أفضل الممارسات (Best Practices)

1.  **تسمية واضحة (Naming Convention):** استخدم اسماً يصف محتوى السياسة (مثلاً: `GPO-Security-Win10` بدلاً من `New GPO`).
2.  **تجميع السياسات (Grouping):** اجمع السياسات المتشابهة في وحدات تنظيمية مخصصة (مثلاً: `GPOs` OU) لتسهيل الإدارة.
3.  **التجربة أولاً (Testing):** قم بإنشاء مجموعة أمان (Security Group) تحتوي على جهاز اختبار واحد، واربط (GPO) به أولاً قبل التطبيق على الكل.
4.  **توثيق الإعدادات (Documentation):** اكتب وصفاً (Description) لكل (GPO) يوضح الغرض منه ومن هو المسؤول عنه.

---
*نهاية الوحدة التدريبية.*
