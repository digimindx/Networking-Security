# محتوى تدريبي: إدارة الهويات والوصول (Managing Identities)
**المرجع:** Phase 8 – Azure Engineer Associate (Weeks 41–44)
**المساق:** Azure Administrator-Engineer (AZ-104)
**الفصل:** 05-Managing identities

## مقدمة (Introduction)
يُعد هذا الفصل حجر الأساس في إدارة البنية التحتية السحابية. في عام 2026، تحول التركيز من "Azure Active Directory" إلى "Microsoft Entra ID" كنظام هويات موحد. تهدف إدارة الهويات (Identity Management) إلى التأكد من أن الأشخاص والتطبيقات لديهم الهوية الصحيحة (Identity) والصلاحيات المناسبة (Permissions) للوصول إلى الموارد (Resources) فقط عند الحاجة.

---

## 1. أنواع الهويات (Identity Types)
لفهم كيفية العمل في Azure، يجب التمييز بين الأنواع الأربعة الرئيسية للهويات:

### أ. المستخدمون (Users)
يمثلون الأشخاص الفعليين (مديرين، مطورين).
*   **User Principal Name (UPN):** هو اسم تسجيل الدخول (مثل: `admin@contoso.onmicrosoft.com`).
*   **Object ID:** معرف فريد ومركزي للهوية يستخدمه النظام داخليًا ولا يمكن تغييره.

### ب. المجموعات (Groups)
تُستخدم لتجميع المستخدمين وتبسيط إدارة الصلاحيات (Granting permissions).
*   **Security Groups:** تُستخدم لتعيين الصلاحيات على الموارد (مثل: SharePoint، Azure Resources).
*   **Microsoft 365 Groups:** تُستخدم لإدارة الخدمات مثل البريد الإلكتروني (Exchange)، والـ Teams.
*   **Dynamic Groups:** مجموعات يتم تحديثها تلقائيًا بناءً على خصائص المستخدم (مثال: جميع الموظفين في قسم "IT").

### ج. Service Principals (مبدأ الخدمة)
تمثل هوية التطبيق (Application Identity) التي تحتاج للوصول إلى موارد Azure.
*   تُنشأ تلقائيًا عند تسجيل تطبيق (App Registration).
*   تُستخدم عادةً في سيناريوهات الأتمتة (Automation) أو التطبيقات التي تتحدث مع Azure API.

### د. Managed Identities (الهويات المُدارة) - *مهم جداً لـ AZ-104*
تتيح للموارد السحابية (مثل Virtual Machines أو App Services) المصادقة مع خدمات Azure الأخرى دون الحاجة لتخزين كلمات المرور في الكود.
*   **System-assigned Managed Identity:** مرتبطة مباشرة بـ Resource معين، وتُحذف تلقائيًا عند حذف المورد.
*   **User-assigned Managed Identity:** هوية مستقلة يمكن مشاركتها بين عدة موارد.

---

## 2. التفويض والوصول (Authorization & Access Control)
بعد تحديد الهوية، كيف نمنحها الصلاحية؟

### أ. التحكم في الوصول القائم على الأدوار (Role-Based Access Control - RBAC)
هو نموذج تفويض في Azure يُستخدم لمنح الوصول إلى الموارد.
*   **المستويات (Scope):** يمكن تطبيق الأدوار على Management Groups، Subscriptions، Resource Groups، أو Resources فردية.
*   **الأدوار الأساسية:**
    1.  **Owner (المالك):** لديه صلاحيات كاملة بما في ذلك إدارة RBAC.
    2.  **Contributor (المساهم):** يمكنه إدارة الموارد ولكن لا يمكنه منح صلاحيات للآخرين.
    3.  **Reader (القارئ):** يمكنه عرض الموارد ولكن لا يمكنه التعديل.

### ب. الفرق بين RBAC و IAM
*   **Azure RBAC:** يُستخدم لإدارة الوصول إلى موارد Azure (مثل VMs، Databases).
*   **Entra ID IAM (Identity Access Management):** يُستخدم لإدارة الوصول إلى موارد Microsoft Entra ID نفسها (مثل: من يمكنه إدارة المستخدمين؟).

---

## 3. إدارة الهويات الامتيازية (Privileged Identity Management - PIM)
يُعد PIM أداة حاسمة لتقليل المخاطر الأمنية عبر منع الصلاحيات الدائمة (Standing Privileges).

### المفاهيم الأساسية في PIM:
1.  **Just-in-Time (JIT) Access:** تمنح المستخدم صلاحية (مثلاً Contributor) لمدة محددة (مثلاً ساعة واحدة) فقط عند الحاجة، ثم تنتهي تلقائيًا.
2.  **Approval Workflows:** تتطلب طلب الصلاحية موافقة مشرف (Approver) قبل تفعيلها.
3.  **Activation Duration:** أقصى مدة يمكن للمستخدم أن يظل فيها مفعلاً.
4.  **MFA Requirement:** إلزام المستخدم بإجراء التحقق بخطوتين (Multi-Factor Authentication) عند تفعيل الدور الامتيازي.

---

## 4. هويات العمل (Workload Identity) - *التحديث الحديث*
في عام 2026، يفضل استخدام **Workload Identity Federation** بدلاً من Service Principals التقليدية لتطبيقات CI/CD (مثل GitHub Actions).
*   **الميزة:** لا حاجة لتخزين Secrets أو Certificates.
*   **الآلية:** تربط هوية من نظام خارجي (مثل GitHub) بـ Microsoft Entra ID مباشرة عبر Federation.

---

## 5. السيناريوهات العملية (Training Scenarios)

### السيناريو الأول: إنشاء هوية لمورد سحابي
**المطلوب:** إنشاء Virtual Machine (VM) يمكنها الكتابة إلى Azure Blob Storage دون كلمات مرور.
**الحل:**
1.  تفعيل **System-assigned Managed Identity** على الـ VM.
2.  إنشاء **Blob Storage**.
3.  الذهاب إلى إعدادات الـ Storage، ثم **Access Control (IAM)** -&gt; **Add Role Assignment**.
4.  اختيار دور **Storage Blob Data Contributor**.
5.  البحث عن اسم الـ VM وتعيين الدور لها.

### السيناريو الثاني: إدارة صلاحيات الامتياز
**المطلوب:** التأكد من أن "المدير" (Admin) لا يستخدم حسابه اليومي لإدارة الموارد الحساسة، وأن صلاحياته تنتهي تلقائياً.
**الحل:**
1.  الذهاب إلى **Microsoft Entra ID** -&gt; **Privileged Identity Management**.
2.  اختيار الدور الامتيازي (مثلاً: Global Administrator).
3.  تفعيل **Azure Resource Roles** (إذا كان الدور خاصًا بالموارد).
4.  ضبط الإعدادات لتفعيل **Require Approval** و **Require MFA** و **Set Activation Duration** (مثلاً 2 ساعة).

---

## 6. المصطلحات التقنية (Technical Terms Reference)
*   **Azure AD / Microsoft Entra ID:** نظام الهوية السحابي (سحابة الهوية).
*   **RBAC:** التحكم في الوصول القائم على الأدوار.
*   **Managed Identity:** هوية مُدارة.
*   **Privileged Identity Management (PIM):** إدارة الهويات الامتيازية.
*   **Conditional Access:** الوصول المشروط (لضمان شروط الأمان مثل الموقع أو الجهاز).
*   **Service Principal:** مبدأ الخدمة (هوية التطبيق).
*   **User-Assigned Managed Identity:** هوية مُدارة من المستخدم.

---

## الخلاصة (Key Takeaways)
1.  لا تمنح الصلاحيات (Permissions) مباشرة للمستخدمين، بل استخدم **Groups**.
2.  استخدم **Managed Identities** لتطبيقاتك بدلاً من تخزين Secrets.
3.  اقلل من استخدام **Global Administrator** واستخدم **PIM** لإدارة الصلاحيات الحساسة بشكل مؤقت وآمن.
4.  تأكد من فهم الفرق بين **Azure RBAC** (للموارد) و **Entra ID IAM** (للهويات).
