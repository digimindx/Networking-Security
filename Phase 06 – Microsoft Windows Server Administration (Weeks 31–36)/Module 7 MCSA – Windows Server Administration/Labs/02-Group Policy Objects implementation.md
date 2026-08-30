# محتوى تدريبي: تنفيذ سياسات المجموعة (GPO Implementation)

**المسار:** Phase 6 – Microsoft Windows Server Administration (Weeks 31–36)
**الوحدة:** Module 7 MCSA – Windows Server Administration
**التمرين العملي:** 02-GPO implementation

---

## أولاً: أهداف التمرين (Objectives)
يهدف هذا التمرين إلى إكساب المتدرب المهارات التالية:
1. فهم آلية عمل Group Policy Objects (GPOs) في بيئة Active Directory.
2. القدرة على إنشاء رابط لسياسة مجموعة (Link GPO) وربطه بوحدة تنظيمية (Organizational Unit - OU).
3. معرفة كيفية تهيئة إعدادات السياسة (Policy Settings) وتطبيقها على المستخدمين أو الأجهزة.
4. التعامل مع أولوية السياسات (Priority) والفلترة الأمنية (Security Filtering).

---

## ثانياً: المتطلبات المسبقة (Prerequisites)
1. وجود خادم يعمل بنظام **Windows Server** (2016, 2019, أو 2022).
2. خدمة **Active Directory Domain Services (AD DS)** مثبتة ومفعلة.
3. جهاز عميل متصل بالدومين (Domain Joined Client).
4. صلاحيات **Domain Admin** أو **Enterprise Admin**.

---

## ثالثاً: السيناريو العملي (Practical Scenario)
سنقوم في هذا التمرين بإنشاء سياسة مجموعة تهدف إلى فرض "سياسة كلمات المرور" (Password Policy) خاصة بقسم الموارد البشرية (HR Department)، بحيث تكون كلمة المرور معقدة ولا يمكن تغييرها إلا كل 30 يوماً.

---

## رابعاً: خطوات التنفيذ (Implementation Steps)

### الخطوة 1: فتح أداة إدارة سياسات المجموعة
1. قم بتسجيل الدخول إلى خادم الدومين بصلاحيات المسؤول.
2. من لوحة التحكم (Control Panel) أو عبر تشغيل الأمر (Run)، اكتب الأمر التالي لفتح وحدة الإدارة:
   `gpmc.msc`
3. ستظهر نافذة **Group Policy Management Console (GPMC)**.

### الخطوة 2: إنشاء كائن سياسة المجموعة (Create GPO)
1. في شجرة التنقل اليسرى (Tree View)، ابحث عن مجلد **Group Policy Objects**.
2. انقر بزر الفأرة الأيمن (Right-click) عليه واختر **New**.
3. في خانة الاسم (Name)، اكتب: `HR Password Policy`.
4. في خانة Link GPO here (اختياري حالياً)، اتركه فارغاً واختر **OK**.
   *ملاحظة: تم إنشاء الـ GPO ولكن لم يتم ربطه بمكان معين بعد.*

### الخطوة 3: تحرير إعدادات السياسة (Edit GPO Settings)
1. في شجرة التنقل، انتقل إلى مجلد **Group Policy Objects** وابحث عن الـ GPO الذي أنشأته حديثاً (`HR Password Policy`).
2. انقر بزر الفأرة الأيمن عليه واختر **Edit**.
   *سيتم فتح نافذة **Group Policy Management Editor (GPME)**.*
3. في الشجرة اليمنى، اذهب إلى المسار التالي:
   `User Configuration` -&gt; `Policies` -&gt; `Windows Settings` -&gt; `Security Settings` -&gt; `Account Policies` -&gt; `Password Policy`.
4. في الجانب الأيمن، ستجد الإعدادات المتاحة (مثل: *Password must meet complexity requirements*).
5. انقر بزر الفأرة الأيمن على أي خيار (مثلاً: *Minimum password length*) واختر **Properties**.
6. فعل الخيار (Enabled) واكتب الطول المطلوب (مثلاً: 10 أحرف).
7. قم بتطبيق الإعدادات الأخرى المطلوبة (مثل: *Maximum password age* بجعلها 30 يوماً).
8. أغلق النافذة.

### الخطوة 4: ربط السياسة بوحدة تنظيمية (Link GPO to OU)
1. أغلق نافذة المحرر (Editor).
2. في **GPMC**، انتقل إلى شجرة الدومين (Forest) واختر الـ **Organizational Unit (OU)** المخصص لقسم الموارد البشرية (مثلاً: `HR_Users`).
3. انقر بزر الفأرة الأيمن على الـ OU واختر **Link an existing GPO here**.
4. من القائمة المنبثقة، اختر الـ GPO الذي أنشأته (`HR Password Policy`).
5. اضغط **OK**.

### الخطوة 5: التحكم في الفلترة الأمنية (Security Filtering)
*هذا الجزء مهم جداً في الامتحانات والممارسة العملية لضمان تطبيق السياسة على الأشخاص الصحيحين فقط.*

1. في **GPMC**، تحت قسم **Organizational Units**، ستجد الـ OU الذي ربطت به السياسة.
2. في الجزء السفلي (Scope Tab)، ستجد الـ **Linked Group Policy Objects**.
3. في الأسفل (Details Tab)، ستجد قسم **Security Filtering**.
4. افتراضيًا، السياسة تطبق على `Authenticated Users`.
5. لإلزام السياسة على مجموعة محددة:
   - احذف `Authenticated Users`.
   - أضف مجموعة المستخدمين (مثلاً: `HR_Group`).
   - تأكد من تفعيل صلاحيات **Read** و **Apply Group Policy** لمجموعة المستخدمين الجديدة.

---

## خامساً: التحقق من التطبيق (Verification)

### 1. على الخادم (Server Side)
يمكنك استخدام أداة **Group Policy Results Wizard** لفحص التوقعات.

### 2. على العميل (Client Side)
1. قم بتسجيل الدخول على جهاز العميل بصفتك أحد المستخدمين التابعين لقسم الموارد البشرية.
2. افتح موجه الأوامر (Command Prompt) واكتب:
   `gpupdate /force`
3. بعد الانتهاء، تحقق من التطبيق عن طريق كتابة:
   `gpresult /r`
   أو لإنشاء تقرير مفصل بصيغة HTML:
   `gpresult /h C:\gpo_report.html`

---

## سادساً: أفضل الممارسات (Best Practices)

1. **تصميم هيكلية الـ OU:** لا تعتمد على ربط السياسات بالمجلدات الافتراضية (مثل `Users` أو `Computers`). قم بإنشاء هيكلة (Structure) منطقية تعتمد على الموقع أو القسم.
2. **تجميع السياسات:** لا تنشئ سياسات منفصلة لكل إعداد بسيط. اجمع الإعدادات المتشابهة في سياسة واحدة لتقليل التعقيد.
3. **تجنب تعارض السياسات:** عند ربط عدة سياسات، تذكر أن السياسة الأحدث في القائمة (الأقرب للأسفل) لها الأولوية الأعلى (Higher Precedence).
4. **استخدام Block Inheritance:** إذا كنت تريد أن لا تتأثر وحدة تنظيمية (OU) بالسياسات التابعة لها في الأعلى، يمكنك تفعيل خيار **Block Inheritance** عليها.
5. **استخدام Enforced (No Override):** إذا أردت أن تطبق سياسة معينة بغض النظر عن السياسات الأحدث في الأسفل، قم بتفعيل خيار **Enforced** على الـ GPO.

---
*نهاية المحتوى التدريبي.*
