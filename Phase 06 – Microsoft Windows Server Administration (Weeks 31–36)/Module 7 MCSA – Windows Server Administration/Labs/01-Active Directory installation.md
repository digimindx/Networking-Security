# دليل عملي: تثبيت خدمات الدليل النشط (Active Directory Domain Services)

**السياق:** دورة Phase 6 – إدارة خوادم مايكروسوفت ويندوز (Windows Server Administration)
**الوحدة:** Module 7 - MCSA
**الممارسة:** 01 - تثبيت وتكوين (Active Directory)

## 1. مقدمة
تهدف هذه الممارسة إلى تعلم كيفية تحويل خادم ويندوز (Windows Server) عادي إلى "متحكم في مجال" (Domain Controller) عبر تثبيت دور (Role) خدمات الدليل النشط (Active Directory Domain Services) وتكوينه كجزء من "غابة" (Forest) جديدة.

## 2. المتطلبات المسبقة (Prerequisites)
قبل البدء، يجب التأكد من توفر التالي:
1.  **نظام التشغيل:** تم تثبيت Windows Server 2019 أو 2022.
2.  **عنوان IP ثابت (Static IP Address):** يجب أن يكون للخادم عنوان IP ثابت وليس ديناميكياً.
3.  **اسم المضيف (Hostname):** يجب تغيير اسم الخادم إلى اسم مناسب (مثلاً: `DC01`) وإعادة تشغيله قبل البدء.
4.  **صلاحيات المسؤول (Administrator Credentials):** يجب تسجيل الدخول بصلاحيات المسؤول المحلي.

---

## 3. الخطوات عبر واجهة المستخدم (GUI Method)

### أولاً: تثبيت الدور (Role Installation)
1.  افتح **Server Manager**.
2.  من القائمة العلوية، اختر **Manage** ثم **Add Roles and Features**.
3.  في شاشة **Before You Begin**، اضغط على **Next**.
4.  في شاشة **Installation Type**، اختر **Role-based or feature-based installation** ثم اضغط **Next**.
5.  في شاشة **Server Selection**، اختر الخادم المستهدف (هذا الخادم) ثم اضغط **Next**.
6.  في شاشة **Server Roles**:
    *   ضع علامة صح بجانب **Active Directory Domain Services**.
    *   سيظهر تنبيه (Pop-up) يطلب إضافة خدمات أخرى، اضغط **Add Features**.
    *   ستظهر أيضاً خيارات إضافية لـ **DNS Server**، ضع علامة صح بجانبها أيضاً واضغط **Add Features**.
    *   اضغط **Next**.
7.  في شاشات **Features** و **AD DS** و **DNS**، اضغط **Next** في كل منها.
8.  في شاشة **Confirmation**، اضغط على **Install**.
9.  انتظر حتى تظهر شريط التقدم باللون الأخضر (Installation Succeeded).

### ثانياً: الترقية إلى متحكم في مجال (Promote to Domain Controller)
بعد انتهاء التثبيت، ستظهر أيقونة علم أصفر في الجزء العلوي الأيمن من **Server Manager** (Post-deployment configuration). اضغط عليها.

1.  اختر **Promote this server to a domain controller**.
2.  في شاشة **Deployment Configuration**:
    *   اختر **Add a new forest**.
    *   في خانة **Root domain name**، اكتب اسم المجال الجديد (مثلاً: `corp.local`).
    *   اضغط **Next**.
3.  في شاشة **Domain Controller Options**:
    *   تأكد من تحديد **Domain Name System (DNS) server**.
    *   تأكد من تحديد **Global Catalog (GC)**.
    *   في خانة **Directory Services Restore Mode (DSRM) password**، اكتب كلمة مرور قوية (يجب حفظها، فهي لاستعادة النظام فقط).
    *   اضغط **Next**.
4.  في شاشة **DNS Options**:
    *   قد تظهر تحذيرات حول عدم وجود سجلات DNS (CNAME, PTR). هذا طبيعي في بيئة جديدة، اضغط **Next**.
5.  في شاشة **Additional Options**:
    *   تأكد من أن اسم **NetBIOS** مطابق للاسم الذي اخترته.
    *   اضغط **Next**.
6.  في شاشة **Paths**:
    *   اترك المسارات الافتراضية لملفات قاعدة البيانات (NTDS.dit) والسجلات (Logs).
    *   اضغط **Next**.
7.  في شاشة **Prerequisites Check**:
    *   سيقوم النظام بفحص المتطلبات. إذا ظهرت رسالة **Validation succeeded**، اضغط **Install**.
8.  سيبدأ التثبيت، وقد يطلب إعادة التشغيل. عند الانتهاء، سيتم إعادة تشغيل الخادم تلقائياً.

---

## 4. التحقق من التثبيت (Verification)

بعد إعادة التشغيل:
1.  قم بتسجيل الدخول باستخدام اسم المجال (مثلاً: `CORP\Administrator`) بدلاً من `.\Administrator`.
2.  افتح **Server Manager** مرة أخرى.
3.  في القائمة اليسرى، اضغط على **AD DS** يجب أن تظهر عبارة "Active Directory Domain Services is Running".
4.  افتح **DNS Manager** للتأكد من عمل الخدمة.

---

## 5. الطريقة البديلة عبر PowerShell (PowerShell Method)

للمديرين المحترفين (MCSA)، يُنصح باستخدام PowerShell لأتمتة العملية. قم بتشغيل PowerShell بصلاحيات المسؤول (Run as Administrator) وأدخل الأوامر التالية بالترتيب:

**الخطوة 1: تثبيت الدور**
```powershell
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

**الخطوة 2: تثبيت DNS (إذا لم يكن مثبتاً)**
```powershell
Install-WindowsFeature DNS -IncludeManagementTools
```

**الخطوة 3: الترقية إلى Domain Controller**
استخدم الأمر `Install-ADDSForest`. ملاحظة: يجب تحويل كلمة مرور DSRM إلى نص آمن (SecureString).

```powershell
$DSRMPassword = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
Install-ADDSForest `
    -DomainName "corp.local" `
    -DomainNetbiosName "CORP" `
    -InstallDns $true `
    -SafeModeAdministratorPassword $DSRMPassword `
    -Force
```

*سيقوم النظام بإعادة التشغيل تلقائياً بعد تنفيذ هذا الأمر.*

---

## 6. ملاحظات هامة للطلاب
*   **DNS Dependency:** الدليل النشط يعتمد كلياً على خدمة DNS. إذا فشلت خدمة DNS، سيفشل الدليل النشط.
*   **الامتداد (Extension):** يفضل استخدام امتدادات محلية مثل `.local` أو `.lan` للتجربة، واستخدام امتدادات عامة (مثل `.com`) فقط إذا كان الخادم متصلًا بالإنترنت مباشرة (وهو أمر غير مستحسن للأمان).
*   **DSRM Password:** لا تنسَ كلمة مرور الوضع الآمن (DSRM) أبداً، فهي السبيل الوحيد للدخول إلى الخادم إذا فشل الدليل النشط.
