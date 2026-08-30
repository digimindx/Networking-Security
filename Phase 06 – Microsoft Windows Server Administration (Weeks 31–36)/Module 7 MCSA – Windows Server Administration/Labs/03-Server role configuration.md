# التدريب العملي 03: تكوين أدوار الخادم (Server Role Configuration)

## مقدمة الدورة
في هذا الجزء من الدورة (المرحلة 6 - الأسبوع 31-36)، سنتناول كيفية تثبيت وتكوين الخدمات الأساسية التي تجعل الخادم يعمل. يُعد فهم الفرق بين "الأدوار" (Roles) و"الميزات" (Features) خطوة حاسمة في إدارة الخوادم.

---

## الجزء الأول: المفاهيم الأساسية

### 1. الفرق بين الأدوار (Roles) والميزات (Features)
*   **دور الخادم (Server Role):** هو مجموعة من البرامج التي تعمل معًا لتوفير وظيفة رئيسية للخادم (مثل: تخزين ملفات، أو خدمة أسماء نطاقات).
*   **الميزات (Features):** هي مكونات إضافية تدعم الأدوار أو توفر وظائف ثانوية لا تشكل دورًا رئيسيًا بحد ذاتها (مثل: أدوات إدارة أو مكتبات برمجية).

### 2. أدوات التثبيت المتاحة
لإضافة أدوار إلى خادم ويندوز، نستخدم عادةً أحد الأدوات التالية:
*   **Server Manager (مدير الخوادم):** الواجهة الرسومية (GUI) الافتراضية.
*   **PowerShell (باورshell):** سطر الأوامر لإدارة الخوادم (خاصة في الإصدارات الحديثة مثل Server Core).
*   **DISM (Deployment Image Servicing and Management):** أداة لتثبيت المكونات دون تشغيل الخادم.

---

## الجزء الثاني: التثبيت باستخدام واجهة الخادم (Server Manager)

هذا هو الإجراء القياسي لإضافة الأدوار عبر الواجهة الرسومية.

### الخطوات العملية:
1.  افتح **Server Manager** من القائمة (Start Menu).
2.  في لوحة التنقل، انقر على **Manage** ثم اختر **Add Roles and Features**.
3.  في معالج التثبيت (Role-based or feature-based installation)، اختر **Role-based or feature-based installation** ثم انقر على **Next**.
4.  في قسم **Server Selection**، اختر الخادم المستهدف (Local Server) أو الخوادم من القائمة.
5.  في قسم **Server Roles**، حدد الأدوار المطلوبة (مثل: **Active Directory Domain Services**، **DHCP Server**، **DNS Server**).
    *   *ملاحظة:* عند تحديد دور، سيظهر مربع حوار يطلب تثبيت "Management Tools" (أدوات الإدارة). وافق عليها.
6.  في قسم **Features**، يمكنك اختيار ميزات إضافية إذا طُلب منك ذلك (مثل: **.NET Framework**).
7.  في قسم **Confirmation**، راجع الإعدادات واضغط على **Install**.

---

## الجزء الثالث: التثبيت باستخدام PowerShell (الأكثر احترافية)

يُعد استخدام الأوامر (Cmdlets) هو المعيار المتبع في بيئات الخوادم الحديثة (Windows Server 2019/2022).

### الأوامر الأساسية:
لإضافة أدوار وأدوات الإدارة معًا، نستخدم الأمر `Install-WindowsFeature`.

**مثال 1: تثبيت دور خدمات الدومين النشطة (AD DS) وأدوات الإدارة**
```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

**مثال 2: تثبيت دور DHCP Server**
```powershell
Install-WindowsFeature -Name DHCP -IncludeManagementTools
```

**مثال 3: تثبيت دور DNS Server**
```powershell
Install-WindowsFeature -Name DNS -IncludeManagementTools
```

**مثال 4: تثبيت دور File and Storage Services**
```powershell
Install-WindowsFeature -Name FS-FileServer -IncludeManagementTools
```

---

## الجزء الرابع: سيناريوهات تكوين الأدوار (Lab Scenarios)

بعد تثبيت الأدوار، يجب إجراء تكوين (Configuration) خاص لكل دور.

### السيناريو 1: تكوين Active Directory Domain Services (AD DS)
الهدف: تحويل الخادم إلى "Domain Controller".
1.  بعد تثبيت الدور، ستظهر أيقونة التنبيه (Notification) في **Server Manager**.
2.  اضغط على الأيقونة واختر **Promote this server to a domain controller**.
3.  اختر نوع التكوين:
    *   **Add a new forest:** لإنشاء دومين جديد (مثال: contoso.com).
    *   **Add a domain controller to an existing domain:** لربط الخادم بدومين موجود.
4.  سيتم طلب إدخال **DSRM Password** (كلمة مرور وضع الاسترداد).
5.  سيطلب النظام إعادة تشغيل الخادم لإكمال العملية.

### السيناريو 2: تكوين DHCP Server
الهدف: توزيع عناوين IP تلقائياً للعملاء.
1.  افتح **DHCP Manager** من أدوات **Server Manager**.
2.  انقر بزر الماوس الأيمن على اسم الخادم واختر **Configure DHCP**.
3.  اتبع المعالج (Wizard) لإضافة صلاحيات (Authorize).
4.  قم بإنشاء **Scope** (نطاق):
    *   أدخل اسم النطاق (Scope Name).
    *   حدد نطاق عناوين IP (Start IP, End IP).
    *   حدد استبعاد العناوين (Exclusions) إذا لزم الأمر.
    *   حدد الـ **Router** (Gateway) و **DNS Servers**.
5.  قم بتفعيل الـ Scope (Activate) ليبدأ العمل.

### السيناريو 3: تكوين DNS Server
الهدف: حل أسماء النطاقات إلى عناوين IP.
1.  افتح **DNS Manager**.
2.  للتأكد من عمل الـ Forward Lookup Zone، تأكد من إنشاء **A Record** (سجل A) يشير إلى اسم الخادم وعنوان IP الخاص به.
3.  لضبط **Forwarders** (المحوّلات):
    *   انقر بزر الماوس الأيمن على اسم الخادم واختر **Properties**.
    *   اذهب إلى تبويب **Forwarders**.
    *   أضف عناوين IP للخوادم العامة (مثل 8.8.8.8 أو 8.8.4.4) لتوجيه طلبات الإنترنت.

### السيناريو 4: تكوين File and Storage Services
الهدف: مشاركة الملفات (File Sharing).
1.  افتح **File and Storage Services** في **Server Manager**.
2.  اختر **Shares** ثم **All Tasks** &gt; **New Share**.
3.  اختر **SMB Share - Quick** (للمشاركة السريعة) أو **SMB Share - Advanced** (لتكوين دقيق).
4.  حدد المسار (Path) للمجلد.
5.  حدد اسم المشاركة (Share name).
6.  حدد الصلاحيات (Permissions) للمستخدمين (NTFS Permissions و Share Permissions).

---

## الجزء الخامس: أفضل الممارسات (Best Practices)

1.  **تقليل سطح الهجوم (Minimize Attack Surface):** لا تقم بتثبيت أدوار إلا إذا كانت ضرورية. كل دور مثبت يزيد من نقاط الدخول المحتملة للهجوم.
2.  **التحديثات (Updates):** تأكد من تثبيت أحدث التحديثات الأمنية (Security Patches) قبل تثبيت الأدوار الحرجة مثل **Active Directory**.
3.  **فصل الأدوار (Role Separation):** في البيئات الكبيرة، لا تقم بتركيب أدوار متعددة على نفس الخادم (مثلاً: لا تدمج **Exchange Server** مع **Active Directory** على نفس الآلة).
4.  **النسخ الاحتياطي (Backup):** قبل تشغيل معالج الترقية (Promote Wizard) لـ **Active Directory**، قم بعمل نسخ احتياطي لنظام الملفات.
5.  **أدوات الإدارة (Management Tools):** تأكد دائماً من تفعيل خيار **Include Management Tools** عند التثبيت، لأنك ستحتاج إلى أدوات مثل **Active Directory Users and Computers** لإدارة الدور لاحقاً.

---

## الجزء السادس: التحقق من نجاح التثبيت

للتأكد من أن الدور يعمل بشكل صحيح، استخدم الأوامر التالية في **PowerShell**:

1.  للتحقق من الأدوار المثبتة:
    ```powershell
    Get-WindowsFeature | Where-Object {$_.Installed -eq $true}
    ```

2.  للتحقق من حالة خدمات AD DS:
    ```powershell
    Get-Service -Name NTDS
    ```

3.  للتحقق من حالة خدمة DHCP:
    ```powershell
    Get-Service -Name DHCP
    ```

---

**خاتمة:**
في هذا التدريب العملي، تعلمنا كيفية إضافة الأدوار باستخدام الواجهة الرسومية و PowerShell، وكيفية التكوين الأساسي لأهم الأدوار في بيئة الشبكات (AD DS, DHCP, DNS, File Server). هذه المهارات هي أساس الحصول على الشهادات الإدارية للخوادم (MCSA / Azure Administrator).
