# Cisco ISE Identity & Access Policies

---

### مقدمة (Introduction)

في عالم الشبكات الحديثة، لم يعد الاعتماد على عناوين IP أو موقع الدخول (Port) كافياً لأمن الشبكة. تأتي هنا أهمية Cisco ISE (Identity Services Engine) كأداة مركزية لإدارة الهوية والوصول. في هذا الجزء من الدورة (الأسابيع 21-30)، سنتعمق في قلب Cisco ISE، وهو المحرك الذي يقرر "من يمكنه الدخول؟" و"إلى ماذا يمكنه الوصول؟".

تتكون السياسات في Cisco ISE من جزأين رئيسيين:
1.  **Identity Policies (سياسات الهوية):** لتحديد هوية المستخدم أو الجهاز.
2.  **Access Policies (سياسات الوصول):** لتحديد الصلاحيات بناءً على تلك الهوية.

---

### 1. هيكلية سياسات الوصول (Access Policy Structure)

تعمل سياسات الوصول داخل ما يسمى بـ (Policy Set). يمكن للمدير إنشاء عدة (Policy Sets) إذا لزم الأمر، ولكن عادةً ما يكون هناك (Policy Set) رئيسي.

#### أ. الأقسام الرئيسية لـ (Policy Set):
1.  **Identity Policies:** تحدد "من أنت؟" (Who are you?).
2.  **Access Policies:** تحدد "ماذا يمكنك أن تفعل؟" (What can you do?).
3.  **Posture Policies:** للتحقق من صحة الجهاز (Health status).
4.  **Guest Policies:** لإدارة ضيوف الشبكة.

في هذا الدرس، سنركز على (Identity Policies) و (Access Policies).

---

### 2. سياسات الهوية (Identity Policies)

الهدف الأساسي من هذه السياسات هو تصنيف المستخدمين والأجهزة في مجموعات (Groups) لتسهيل تطبيق السياسات لاحقاً.

#### أ. Endpoint Identity Groups (مجموعات هوية نقاط النهاية)
هي مجموعات تُستخدم لتصنيف الأجهزة (Endpoints) بناءً على خصائصها.
*   **أمثلة على المجموعات:**
    *   (Unknown Endpoints): الأجهزة التي لم يتم التعرف عليها.
    *   (Servers): الخوادم.
    *   (Printers): الطابعات.
    *   (VoIP Phones): الهواتف الصوتية.
    *   (Windows Workstations): أجهزة ويندوز.
*   **كيفية العمل:** يتم تصنيف الجهاز تلقائياً (Automatically) بناءً على (Attributes) مثل (MAC Address)، (Certificate)، أو (MAB).

#### ب. User Identity Groups (مجموعات هوية المستخدمين)
تُستخدم لتصنيف المستخدمين بناءً على بيانات الدخول.
*   **أمثلة:** (HR Users)، (Engineering)، (Guests).
*   **مصدر البيانات:** غالباً ما تأتي من (External Identity Stores) مثل (Active Directory) أو (LDAP).

---

### 3. سياسات الوصول (Access Policies)

تُعتبر (Access Policies) هي المحرك المنطقي (Logic Engine) الذي يقرر نتيجة الاتصال. تتكون من ثلاثة عناصر أساسية:

#### أ. Conditions (الشروط)
هي المعايير التي يتم فحصها. في Cisco ISE، يتم استخدام (Condition Builder) لإنشاء الشروط.
*   **Attributes المشهورة:**
    *   **Endpoint Identity Group:** (مثلاً: إذا كانت المجموعة هي "Servers").
    *   **User Identity Group:** (مثلاً: إذا كان المستخدم من قسم "HR").
    *   **Network Device:** (اسم الجهاز الذي أرسل الطلب، مثل السويتش أو الـ WLC).
    *   **Time of Day:** (وقت اليوم، مفيد لسياسات الدخول في أوقات العمل فقط).
    *   **Posture Status:** (هل الجهاز مطعّم أم لا؟).
    *   **Authentication Type:** (هل الدخول عبر (MAB) أم (802.1X)).

#### ب. Rules (القواعد)
هي مجموعة من القواعد المنطقية (If-Then Logic) التي تترجم (Conditions) إلى نتائج.
*   **أهمية الترتيب (Order of Precedence):**
    *   تقوم Cisco ISE بمعالجة القواعد من **الأعلى إلى الأسفل**.
    *   بمجرد أن يطابق الطلب شرطاً (Match)، يتم تنفيذ الإجراء (Result) وتوقف المعالجة.
    *   لذلك، يجب وضع القواعد الأكثر تحديداً في الأعلى، والقواعد العامة (مثل (Allow All)) في الأسفل.
*   **Default Rule:** قاعدة افتراضية في الأسفل دائماً (Accept) إذا لم تتطابق أي قاعدة أخرى (تُستخدم عادةً للرفض Deny All).

#### ج. Authorization Profiles (ملفات التفويض)
عندما تتطابق القاعدة (Rule)، يتم تطبيق ملف التفويض هذا. ملف التفويض يحتوي على الإعدادات التي سيتم إرسالها إلى جهاز الشبكة (Network Access Device - NAD).
*   **مكونات ملف التفويض:**
    *   **VLAN Assignment:** تحديد الـ VLAN الذي سينتمي إليه الجهاز.
    *   **ACLs (Access Control Lists):** فلاتر أمنية (Ingress/Egress).
    *   **SGT (Security Group Tag):** وسوم الأمان الخاصة بـ Cisco TrustSec.
    *   **Quarantine:** وضع الجهاز في شبكة معزولة (إذا كان غير آمن).
    *   **CoA (Change of Authorization):** تغيير الصلاحيات أثناء الاتصال.

---

### 4. سيناريوهات تطبيق السياسات (Use Cases)

#### السيناريو الأول: جهاز غير معروف (Unknown Device)
1.  **Identity Policy:** الجهاز لا يظهر في المجموعات، يذهب إلى (Unknown Endpoints).
2.  **Access Policy:**
    *   **Condition:** (Endpoint Identity Group) is (Unknown Endpoints).
    *   **Result:** تطبيق ملف تفويض يرسل الجهاز إلى (Quarantine VLAN) أو (Guest VLAN).

#### السيناريو الثاني: موظف (User Authentication)
1.  **Identity Policy:** المستخدم يتطابق مع (Active Directory).
2.  **Access Policy:**
    *   **Rule 1:**
        *   **Condition:** (User Identity Group) is (HR Users).
        *   **Result:** (Allow HR Access Profile) -&gt; يمنح (VLAN) خاص بـ HR فقط.
    *   **Rule 2:**
        *   **Condition:** (User Identity Group) is (Engineering Users).
        *   **Result:** (Allow Engineering Access Profile) -&gt; يمنح (VLAN) خاص بـ Engineering و (SGT) خاص.

---

### 5. أدوات التحليل (ISE Analysis Tool)

أداة حيوية للمهندسين لفهم سبب رفض أو قبول اتصال معين.
*   **كيف تعمل:** يتم إدخال بيانات الاتصال (مثل اسم المستخدم، أو الـ MAC، أو الـ IP) في الأداة.
*   **المخرجات:** تظهر لك "سرد القواعد" (Rule Processing)، وتوضح أي (Rule) تطابق، وأي (Condition) سبب المطابقة، وما هو ملف التفويض (Authorization Profile) الذي تم تطبيقه.

---

### ملخص النقاط الفنية (Technical Summary)

*   **Policy Set:** الحاوية التي تجمع السياسات.
*   **Identity Policy:** تركز على التصنيف (Grouping).
*   **Access Policy:** تركز على المنطق (Logic) والصلاحيات (Permissions).
*   **Order Matters:** ترتيب القواعد في (Access Policy) حاسم جداً.
*   **Authorization Profile:** هو الجسر بين ISE وجهاز الشبكة (Switch/WLC) لنقل الإعدادات.
*   **SGT (Security Group Tag):** تستخدم في بيئات Cisco TrustSec لعزل الحركة بناءً على الهوية وليس الـ IP.

---
*ملاحظة للمدربين والطلاب:* عند تطبيق هذه السياسات عملياً، يُنصح دائماً باستخدام وضع (Monitor) أو (Sponsor) قبل تفعيل (Enforce) لتجنب قطع الاتصال عن الأجهزة المهمة أثناء الاختبار.
