# Chapter: 03 - Application Access Policies

**مقدمة:**
في إطار إدارة الهوية والوصول (Identity and Access Management)، يُعد التحكم في كيفية وصول التطبيقات إلى الموارد أمراً بالغ الأهمية. في السياق الحديث لـ Microsoft Entra ID، تم تطوير آليات إدارة هذه السياسات بشكل جذري. يجب على مسؤولي الأمان فهم الفرق بين المفهوم التاريخي لـ (Application Access Policies) وبين المنهجية الحديثة المعتمدة في (Microsoft Entra ID Enterprise Applications).

---

### 1. التحول من Application Access Policies إلى Enterprise Applications

في الماضي، كانت (Application Access Policies) ميزة محددة تتطلب تعديل ملفات تعريف التطبيق (Manifest) لإضافة أدوار مخصصة (appRoles). ومع ذلك، في الإصدارات الحديثة (2024-2026)، تم توحيد هذا المفهوم ضمن (Microsoft Entra ID Enterprise Applications).

**النقاط الأساسية:**
*   **Microsoft Entra ID Enterprise Applications:** هي الواجهة الإدارية المركزية لإدارة تطبيقات الجهات الخارجية (Third-party) والتطبيقات المسجلة (Registered Applications).
*   **إدارة الصلاحيات:** بدلاً من الاعتماد فقط على (Application Access Policies) التقليدية، يتم الآن التحكم في الوصول عبر تبويب (Permissions) داخل إعدادات التطبيق.

---

### 2. أنواع الصلاحيات (Permissions Types)

لفهم كيفية عمل سياسات الوصول، يجب التمييز بين نوعين رئيسيين من الصلاحيات في Microsoft Entra ID:

#### أ. الصلاحيات التطبيقية (Application Permissions)
*   تُستخدم للتطبيقات التي تعمل كخدمات خلفية (Daemon applications) أو تطبيقات خادم إلى خادم (Server-to-server).
*   لا تتطلب وجود مستخدم مسجل الدخول (User context).
*   تتطلب دائماً موافقة المسؤول (Admin Consent) ولا يمكن للمستخدم العادي منحها.

#### ب. الصلاحيات المفوضة (Delegated Permissions)
*   تُستخدم للتطبيقات التي تعمل نيابة عن مستخدم مسجل (User context).
*   تمثل صلاحية يقوم بها المستخدم (مثل: قراءة البريد الإلكتروني للمستخدم).
*   يمكن منحها عبر (User Consent) أو (Admin Consent).

---

### 3. إطار العمل للموافقة (Consent Framework)

إدارة (Application Access Policies) تعتمد بشكل أساسي على عملية الموافقة (Consent).

#### أ. موافقة المسؤول (Admin Consent)
*   يمنح المسؤول (Global Admin أو Privileged Role Admin) الصلاحيات لجميع المستخدمين في المؤسسة أو لمجموعة محددة.
*   يُستخدم للتطبيقات الحساسة أو الأساسية للشركة.
*   **الإجراء:** داخل (Enterprise Applications)، يتم اختيار (Permissions) ثم النقر على (Grant admin consent for [Tenant Name]).

#### ب. موافقة المستخدم (User Consent)
*   يسمح للمستخدمين بمنح الصلاحيات للتطبيقات بأنفسهم.
*   **الاستخدام:** يُستخدم عادةً للتطبيقات غير الحساسة (مثل تطبيقات الإنتاجية أو أدوات التعاون).
*   **التقييد (Restricted User Consent):**
    *   يمكن للمسؤولين تحديد قائمة بيضاء (Allow list) للتطبيقات المسموح للمستخدمين بالموافقة عليها.
    *   يمكن تحديد قائمة سوداء (Block list) للتطبيقات المحظورة.
    *   يمكن تقييد الصلاحيات التي يمكن للمستخدم الموافقة عليها (مثلاً: السماح بالموافقة على "Read user profile" فقط ومنع "Read and write all items").

---

### 4. إدارة سياسات الوصول عبر (Enterprise Applications)

لتطبيق سياسات الوصول الحديثة (التي تحل محل تطبيق (Application Access Policies) القديم)، تتبع الخطوات التالية:

1.  **تعيين المالكين (Owners):**
    *   يجب تعيين مالكين للتطبيق (Application Owner) لضمان إدارة الصلاحيات بشكل صحيح.
    *   المالك لديه صلاحية إدارة (Users and Groups) و (Properties) للتطبيق.

2.  **تعيين المستخدمين (User Assignment):**
    *   لتقييد الوصول للتطبيق، يتم تفعيل خيار "Require user assignment".
    *   عند التفعيل، لا يمكن لأي مستخدم في المؤسسة الدخول للتطبيق إلا إذا تمت إضافته يدوياً إلى قائمة (Users and Groups).

3.  **إدارة الصلاحيات (Permissions Blade):**
    *   **Microsoft Graph Permissions:** الصلاحيات للوصول إلى خدمات Microsoft.
    *   **API Permissions:** الصلاحيات للوصول إلى واجهات برمجة التطبيقات للجهات الخارجية (Third-party APIs).
    *   يتم إضافة الصلاحيات من هنا، ثم طلب (Grant Admin Consent).

---

### 5. السيناريوهات والأمان (Security Implications)

*   **تسرب الصلاحيات (Permission Creep):**
    *   عند إضافة صلاحيات للتطبيق، يجب مراجعة القائمة بانتظام.
    *   في SC-300، يُنصح باستخدام (Just-in-Time access) أو مراجعة دورية لـ (Enterprise Applications) لإزالة الصلاحيات غير المستخدمة.

*   **التحقق من هوية التطبيق (App Identity):**
    *   كل تطبيق له (Application ID) (Client ID) فريد.
    *   عند تهيئة (Application Access Policies)، يجب التأكد من أن التطبيق لا يمتلك صلاحيات أدمن (Admin Consent) غير ضرورية.

---

### ملخص الفصل (Chapter Summary)

*   مصطلح (Application Access Policies) القديم تحول ليصبح جزءاً من إدارة (Permissions) في (Microsoft Entra ID Enterprise Applications).
*   يجب التمييز بين (Application Permissions) و (Delegated Permissions).
*   (Admin Consent) ضروري للتطبيقات الحساسة، بينما يمكن تفعيل (Restricted User Consent) للتطبيقات العامة.
*   تفعيل (User Assignment) في (Enterprise Applications) هو الطريقة الحديثة لتطبيق سياسات الوصول (Access Policies) التي تسمح فقط للمجموعة المحددة باستخدام التطبيق.

---
**ملاحظة هامة للممتحن (SC-300):**
في الامتحان، إذا طُلب منك كيفية تقييد تطبيق معين ليتمكن مستخدمين محددين فقط من استخدامه، فالإجابة الصحيحة تعتمد على إعدادات (Enterprise Applications) -&gt; (Users and Groups) -&gt; تفعيل (Require user assignment)، وليس عبر تعديل ملف التعريف (Manifest) القديم.
