# تثبيت وإعداد Windows Server
**Module 7: Installing & Configuring Windows Server**
**المرجع:** Windows Server 2022 / Windows Server 2025

## المقدمة
تهدف هذه الوحدة إلى غرس المهارات اللازمة لمهندس الشبكات لإجراء عمليات التثبيت الصحيحة والخارجة عن المألوف (Advanced Installation) لأنظمة التشغيل الخادمية، مع التركيز على معايير الأمان الحديثة والإعدادات الأساسية لإدارة النظام.

---

## أولاً: المتطلبات المسبقة للتثبيت (Prerequisites)

قبل البدء في عملية التثبيت، يجب التأكد من توفر الشروط التالية:

1.  **الوسائط (Installation Media):**
    *   تحميل ملف (ISO Image) الرسمي لنظام التشغيل من موقع Microsoft أو عبر Microsoft Evaluation Center.
    *   التأكد من مطابقة نسخة الوسيط مع الترخيص (License Key) المتاح.

2.  **العتاد (Hardware Requirements):**
    *   **المعالج (CPU):** معالج 64-bit (x64) بتردد 1.4 GHz أو أعلى.
    *   **الذاكرة العشوائية (RAM):**
        *   لنظام (Server Core): الحد الأدنى 512 MB (غير موصى به للإنتاج).
        *   لنظام (GUI): الحد الأدنى 2 GB (للإنتاج).
    *   **التخزين (Storage):** مساحة فارغة لا تقل عن 32 GB (يفضل استخدام (NTFS) كملف نظام).

3.  **الإعدادات المسبقة (BIOS/UEFI):**
    *   تفعيل (Virtualization Technology) إذا كان التثبيت يتم عبر آلة افتراضية (Virtual Machine).
    *   ضبط ترتيب الإقلاع (Boot Order) ليتم الإقلاع من وسائط التثبيت (USB/DVD).

---

## ثانياً: طرق التثبيت (Installation Methods)

في البيئات الحديثة (2024)، يُنصح بشدة بالاعتماد على التثبيت المصغر لتقليل سطح الهجوم (Attack Surface).

### 1. التثبيت الكامل (GUI Installation)
*   **الوصف:** يوفر واجهة رسومية كاملة (Desktop Experience) تشبه Windows 10/11.
*   **الاستخدام:** مناسب للمبتدئين أو للخوادم التي تتطلب تطبيقات متوافقة مع الواجهة الرسومية.

### 2. التثبيت المصغر (Server Core)
*   **الوصف:** تثبيت بدون واجهة رسومية (GUI)، يعتمد كلياً على (PowerShell) و (Command Prompt).
*   **المميزات:** استهلاك أقل للموارد، تحديثات أقل (تقليل عمليات إعادة التشغيل)، وأمان أعلى.
*   **الاستخدام:** هو الخيار المعياري للخوادم التي تعمل بدور (AD DS)، (DNS)، (DHCP)، و (Hyper-V).

---

## ثالثاً: خطوات التثبيت (Installation Process)

1.  **الإقلاع (Boot):**
    *   تشغيل الخادم من وسائط التثبيت.
    *   اختيار اللغة ومنطقة الوقت وطريقة لوحة المفاتيح.

2.  **بدء التثبيت:**
    *   الضغط على زر (Install Now).
    *   إدخال مفتاح المنتج (Product Key) أو اختيار (I don't have a product key) للتفعيل لاحقاً.

3.  **اختيار النسخة (Edition Selection):**
    *   اختيار النسخة المناسبة (Standard أو Datacenter).
    *   **Standard:** للخوادم المادية أو عدد محدود من الـ Virtual Machines.
    *   **Datacenter:** لعدد غير محدود من الـ VMs (ملائم للحوسبة السحابية).

4.  **قبول الشروط:**
    *   اختيار (I accept the license terms).

5.  **نوع التثبيت (Installation Type):**
    *   اختيار (Custom: Install Windows only). **هذا الخيار ضروري لإدارة الأقراص.**

6.  **تقسيم القرص (Disk Partitioning):**
    *   حذف الأقسام القديمة (Delete Partitions) لتوحيد المساحة.
    *   إنشاء قسم جديد (New) وتخصيصه كـ (System Reserved) و (OS Drive).
    *   **ملاحظة:** في (Server Core)، يتم تثبيت النظام تلقائياً على القسم الذي يحتوي على الملفات القابلة للتثبيت.

7.  **إعدادات ما بعد التثبيت (Post-Installation Configuration):**
    *   **كلمة المرور (Administrator Password):** يجب أن تكون قوية ومعقدة.
    *   **اسم الخادم (Computer Name):** تغيير الاسم الافتراضي (WindowsServer).
    *   **الإعدادات الشبكية (Network Settings):**
        *   تعيين (Static IP Address) للخادم بدلاً من (DHCP) لضمان ثبات الهوية.
        *   تعيين (DNS Server) الخاص بالشبكة الداخلية.

---

## رابعاً: إدارة الخادم بعد التثبيت (Post-Install Management)

بعد الانتهاء من التثبيت، ننتقل لمرحلة الإعداد الفعلي.

### 1. أداة التكوين السريعة (Sconfig)
*   إذا تم تثبيت (Server Core)، تظهر لك أداة سطر الأوامر (Sconfig).
*   تتيح هذه الأداة تغيير اسم الخادم، تفعيل الـ Remote Desktop، وتفعيل الـ Windows Update دون الحاجة لكتابة أوامر PowerShell معقدة.

### 2. إضافة الأدوار والميزات (Roles and Features)
يتم ذلك عبر (Server Manager) أو PowerShell.

*   **الأدوار الأساسية (Core Roles):**
    *   **Active Directory Domain Services (AD DS):** لإدارة المستخدمين والهوية.
    *   **DNS Server:** لحل أسماء النطاقات داخل الشبكة.
    *   **DHCP Server:** لتوزيع عناوين IP بشكل ديناميكي.

*   **الميزات الأساسية (Essential Features):**
    *   **.NET Framework 3.5:** ضروري لتشغيل العديد من التطبيقات القديمة والجديدة.
    *   **RSAT (Remote Server Administration Tools):** أدوات الإدارة عن بعد.
    *   **Hyper-V:** منصة التخيل (Virtualization).

### 3. الترقية إلى Domain Controller
*   بعد تثبيت دور (AD DS)، يجب تشغيل معالج الترقية (Post-Deployment Configuration).
*   اختيار (Add a new forest).
*   تعيين اسم النطاق الجذري (Root Domain Name).
*   تعيين كلمة مرور (DSRM Password).

---

## خامساً: أدوات الإدارة الحديثة (Modern Management Tools)

في سياق تحديث 2024، لا تعتمد فقط على (MMC Snap-ins) التقليدية.

1.  **Windows Admin Center (WAC):**
    *   بوابة إدارية مبنية على المتصفح (Web-based gateway).
    *   تسمح بإدارة الخوادم المحلية (On-premises) والسحابية (Azure) من واجهة واحدة.
    *   تدعم إدارة (Hyper-V)، (Failover Clustering)، و (Storage Spaces).

2.  **PowerShell:**
    *   لغة الأتمتة الأساسية. يجب إتقان cmdlets الخاصة بـ (ADDS)، (DNS)، و (Hyper-V).
    *   مثال: `Install-WindowsFeature` لإضافة الأدوار.

---

## سادساً: التفعيل والتراخيص (Activation & Licensing)

1.  **أنواع التفعيل:**
    *   **KMS (Key Management Service):** تفعيل عبر خادم محلي في الشبكة الداخلية.
    *   **MAK (Multiple Activation Key):** تفعيل لكل خادم على حدة عبر الإنترنت.
    *   **Retail:** الترخيص التجزئة (قابل للنقل).

2.  **التحقق من التفعيل:**
    *   استخدام الأمر `slmgr.vbs /dlv` في سطر الأوامر لعرض حالة التفعيل.

---

## ملخص الوحدة (Module Summary)

*   التثبيت يجب أن يتم دائماً عبر وسائط (ISO) رسمية.
*   يُفضل استخدام (Server Core) للأمان والأداء، والاعتماد على (PowerShell) أو (Windows Admin Center) للإدارة.
*   يجب دائماً تعيين (Static IP) للخوادم.
*   الأدوار الأساسية (AD DS, DNS, DHCP) هي حجر الزاوية في أي بنية تحتية لنظام Windows Server.
