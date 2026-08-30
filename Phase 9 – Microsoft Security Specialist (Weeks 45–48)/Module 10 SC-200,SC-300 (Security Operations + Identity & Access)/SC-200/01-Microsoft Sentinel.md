# Microsoft Sentinel

---

### 1. المقدمة (Introduction)

يُعد **Microsoft Sentinel** الحل الرائد للتحليلات الأمنية الذكية (Security Information and Event Management - SIEM) والأتمتة التشغيلية للأمن (Security Orchestration, Automation, and Response - SOAR). تم تصميمه ليكون حلاً سحابياً بالكامل (Cloud-native) يعتمد على قوة Azure، مما يتيح للمنظمات جمع بيانات من جميع بيئاتها (On-premises, Cloud, Hybrid) في مكان واحد.

في سياق تحديثات 2025-2026، أصبح **Microsoft Sentinel** هو العمود الفقري لدمج البيانات الأمنية مع حلول **Microsoft Defender XDR** (Extended Detection and Response).

---

### 2. الميزات الأساسية (Core Features)

لفهم الامتحان (SC-200) والبيئة العملية، يجب التركيز على المكونات التالية:

#### أ. جمع البيانات (Data Ingestion)
يعتمد **Microsoft Sentinel** على مساحة عمل (Workspace) مبنية على قاعدة بيانات **Azure Data Explorer**.
*   **موصلات البيانات (Data Connectors):** أدوات جاهزة لاستيراد البيانات من مصادر مختلفة (مثل: Microsoft Defender for Endpoint, Azure Active Directory/Entra ID, AWS, GCP, جدران الحماية، والخوادم).
*   **أنواع البيانات:** يدعم البيانات النصية (Logs)، ونظام الملفات (File System)، وبيانات الشبكة (Network Traffic).

#### ب. الكشف عن التهديدات (Threat Detection)
يتم الكشف عن الهجمات باستخدام **Analytic Rules**:
1.  **القواعد القائمة على القواعد (Rule-based):** استخدام لغة الاستعلام (KQL - Kusto Query Language) للبحث عن أنماط محددة (مثل: محاولات تسجيل دخول فاشلة متعددة).
2.  **القواعد القائمة على الذكاء الاصطناعي (ML-based):** استخدام خوارزميات التعلم الآلي (Machine Learning) لاكتشاف الشذوذ (Anomaly Detection) دون الحاجة لتعريف قواعد يدوية.

#### ج. إدارة الحوادث والاستجابة (Incident Management & Response)
*   **الحوادث (Incidents):** تجميع النتائج ذات الصلة في حادثة واحدة لتسهيل التحليل.
*   **أتمتة الاستجابة (SOAR):** استخدام **Playbooks** (المبنية على Azure Logic Apps) لاتخاذ إجراءات تلقائية عند اكتشاف تهديد (مثل: عزل جهاز، أو حظر عنوان IP، أو إرسال بريد إلكتروني).

---

### 3. أحدث التطورات في Microsoft Sentinel (2025-2026 Updates)

بناءً على خارطة الطريق الحالية لعام 2026، يجب عليك التركيز على النقاط التالية في الامتحان والتطبيق العملي:

#### أ. Microsoft Copilot for Security
أصبح **Copilot** جزءاً لا يتجزأ من **Microsoft Sentinel**.
*   **التوليد الذكي للاستعلامات:** كتابة استعلامات **KQL** المعقدة باستخدام اللغة الطبيعية.
*   **تحليل الحوادث:** يقوم الـ Copilot بملخص تلقائي للحوادث، واقتراح أسباب الجذر (Root Cause Analysis)، وتقديم خطوات الاستجابة المقترحة.
*   **التقارير:** إنشاء تقارير إدارية وتقنية تلقائياً.

#### ب. تكامل XDR المتقدم
في عام 2026، لم يعد **Microsoft Sentinel** يعمل بمعزل عن غيره. يتم دمج البيانات تلقائياً من:
*   **Microsoft Defender for Cloud Apps (MCAS):** لمراقبة التطبيقات السحابية.
*   **Defender for Identity:** لرصد الهجمات على هوية المؤسسة.
*   **Defender for Endpoint:** لبيانات الحوافز (Endpoints).

#### ج. الأتمتة المتقدمة (Advanced Automation)
*   استخدام **Copilot in Playbooks** لتحسين سير العمل (Workflows).
*   دعم **Bicep** و **Terraform** لنشر البنية التحتية ككود (Infrastructure as Code) لضمان التكرار والموثوقية.

---

### 4. بنية النظام (Architecture)

لفهم كيفية عمل النظام، يجب معرفة المكونات الهندسية:

1.  **Azure Tenant:** الحساب التنظيمي الأساسي.
2.  **Log Analytics Workspace:** قاعدة البيانات التي تخزن فيها البيانات المستوردة. (ملاحظة: يمكن ربط أكثر من مساحة عمل بـ Microsoft Sentinel، لكن مساحة عمل واحدة لكل مستخدم هي الأفضل للأداء).
3.  **Data Connectors:** المسؤولة عن نقل البيانات من المصدر إلى الـ Workspace.
4.  **Analytic Rules:** المحرك الذي يشغل الاستعلامات باستمرار (Real-time or Scheduled).
5.  **Watchlists:** قوائم مرجعية تحتوي على بيانات خارجية (مثل: قوائم العناوين IP المشبوهة) لمقارنتها بالبيانات الواردة.

---

### 5. سيناريو عملي: كشف هجوم Brute Force (Practical Scenario)

**الهدف:** إعداد **Microsoft Sentinel** لكشف محاولات اختراق حسابات **Entra ID** (Identity).

**الخطوات:**

1.  **تفعيل الموصل (Enable Connector):**
    *   الذهاب إلى **Microsoft Sentinel** &gt; **Content hub**.
    *   البحث عن **Azure Active Directory** وتفعيله. هذا سيجلب سجلات تسجيل الدخول (Sign-in logs).

2.  **إنشاء قاعدة كشف (Create Analytic Rule):**
    *   استخدام **KQL** للبحث عن فشل المصادقة.
    *   مثال للاستعلام:
        ```kql
        SigninLogs
        | where ResultType == "50055" or ResultType == "50074"
        | summarize count() by UserPrincipalName, bin(TimeGenerated, 1h)
        | where count_ &gt; 10
        ```
    *   تحويل هذا الاستعلام إلى **Analytic Rule** (Type: Scheduled).

3.  **إعداد الاستجابة (Response Configuration):**
    *   تفعيل **SOAR** على القاعدة.
    *   ربط **Playbook** يقوم بـ:
        *   إنشاء تذكرة في نظام Ticker (مثل Jira أو ServiceNow).
        *   إرسال تنبيه إلى مسؤولي الأمن عبر Teams.

4.  **التحقق (Verification):**
    *   استخدام **Incidents** في الواجهة لرؤية الحادثة التي تم إنشاؤها.
    *   استخدام **Copilot for Security** لطلب: "Give me a summary of this incident and suggest remediation steps."

---

### 6. ملخص للنجاح في الامتحان (Exam Tips for SC-200)

*   **KQL هو اللغة الأساسية:** يجب أن تكون قادراً على قراءة وفهم استعلامات **Kusto Query Language**.
*   **فهم الفرق بين SIEM و SOAR:**
    *   **SIEM:** يركز على التخزين والتحليل (Microsoft Sentinel).
    *   **SOAR:** يركز على الأتمتة والاستجابة (Playbooks/Logic Apps).
*   **الأمن والامتثال (Security & Compliance):**
    *   فهم كيفية إدارة **Workbooks** للتقارير.
    *   معرفة كيفية ربط **Microsoft Sentinel** بـ **Azure Monitor** و **Azure Log Analytics**.
*   **التكلفة (Cost Management):**
    *   فهم نماذج التسعير (Data ingestion costs, Analytic processing costs).

---
*تم إعداد هذا المحتوى كجزء من المرحلة 9 من دورة Microsoft Security Specialist.*
