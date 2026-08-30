# المحتوى التدريبي: Azure Defender & Sentinel
**الدورة:** Phase 7 – Azure Advanced Security (Weeks 37–40)
**الوحدة:** Module 8 AZ-500 — Azure Security Engineer Associate
**الفصل:** 04 - Microsoft Defender for Cloud & Microsoft Sentinel

## مقدمة
في هذا الفصل، سنستعرض أدوات الحماية والتحليل المتقدمة في بيئة مايكروسوفت السحابية. لقد تم تحديث المصطلحات والأدوات بشكل كبير، حيث تم دمج "Azure Defender" في منصة "Microsoft Defender" الأوسع، وأصبحت "Microsoft Sentinel" هي العمود الفقري لمراقبة التهديدات.

---

## الجزء الأول: Microsoft Defender for Cloud
*(سابقاً Azure Defender)*

### 1. المفهوم العام
هي خدمة (Cloud Security Posture Management - CSPM) و (Cloud Workload Protection Platform - CWPP) مصممة لحماية الموارد السحابية (Hybrid Cloud). تعمل هذه الخدمة على تقييم بيئة السحابة باستمرار واكتشاف التهديدات المحتملة.

**ملاحظة هامة:** في سياق عام 2026، يُشار إلى هذه الخدمة باسم (Microsoft Defender for Cloud)، وقد تم استبدال مصطلح (Azure Defender) القديم تماماً.

### 2. الميزات الرئيسية
*   **تقييم الوضع الأمني (Secure Score):** تقيس مستوى أمان الموارد وتقدم توصيات لتحسينها.
*   **اكتشاف التهديدات (Threat Detection):** تستخدم الذكاء الاصطناعي للكشف عن الأنشطة المشبوهة في الوقت الفعلي.
*   **إدارة الثغرات (Vulnerability Management):** تكتشف الثغرات في أنظمة التشغيل والتطبيقات.

### 3. خطط الحماية (Defender Plans)
تتوفر خطط متعددة لتغطية أنواع مختلفة من الموارد:
*   (Defender for Servers): حماية الخوادم المحلية والسحابية (VMs).
*   (Defender for App Service): حماية تطبيقات الويب.
*   (Defender for Azure SQL): حماية قواعد البيانات.
*   (Defender for Storage): حماية تخزين الكائنات (Blob Storage).
*   (Defender for Kubernetes): حماية الحاويات والـ Clusters.
*   (Defender for Key Vault): حماية المفاتيح والاسرار.

### 4. الفرق بين الطبقات (Pricing Tiers)
1.  **Free Tier:** يوفر تقييمات الأمان الأساسية (CSPM) فقط.
2.  **Standard Tier:** يوفر حماية الأحمال العاملة (CWPP) واكتشاف التهديدات المتقدم.

---

## الجزء الثاني: Microsoft Sentinel
*(منصة SIEM و SOAR السحابية)*

### 1. المفهوم العام
(Microsoft Sentinel) هو حل (Security Information and Event Management - SIEM) و (Security Orchestration, Automation, and Response - SOAR) مبني على السحابة. يعتمد على (Azure Data Lake) لتخزين البيانات، مما يتيح سعة تخزين غير محدودة وتحليلاً قوياً.

### 2. المكونات الأساسية (Core Components)
لإعداد (Microsoft Sentinel) بنجاح، يجب فهم المكونات التالية:

*   **Workspace (مساحة العمل):** هي المكان الذي يتم فيه تخزين سجلات البيانات (Logs) واستعلامات (KQL). يجب أن تكون من نوع (Log Analytics Workspace).
*   **Data Connectors (موصلات البيانات):** مسؤولة عن جلب البيانات من مصادر خارجية (مثل (Azure Firewall)، (Entra ID)، أو (Microsoft Defender for Cloud)) إلى الـ Workspace.
*   **Analytics Rules (قواعد التحليل):** منطق الكشف عن التهديدات. تعمل هذه القواعد على فحص البيانات القادمة وتنشيط تنبيهات (Alerts) عند تطابقها مع شروط معينة.
*   **Workbooks (سجلات العمل):** أدوات مرئية لإنشاء لوحات تحكم (Dashboards) مخصصة لعرض مؤشرات الأداء الأمنية (Security Metrics).
*   **Automation Rules (قواعد الأتمتة):** تسمح بأتمتة الإجراءات عند وصول تنبيه جديد (مثل تغيير الأولوية أو إضافة علامة).
*   **Playbooks (الأدوار التشغيلية):** هي عبارة عن (Logic Apps) يتم استدعاؤها تلقائياً للاستجابة للحوادث الأمنية (مثل عزل خادم مصاب).

---

## الجزء الثالث: التكامل بين Defender for Cloud و Sentinel

يعتبر دمج هاتين الأداتين عنصراً حاسماً في شهادة AZ-500.

### 1. آلية التكامل
يمكن ربط (Microsoft Defender for Cloud) بـ (Microsoft Sentinel) لتمكين التدفق التلقائي للتنبيهات.
*   **الخطوة:** في إعدادات (Microsoft Defender for Cloud)، اذهب إلى (Workspace settings) واختر (Microsoft Sentinel) كمكان لتخزين التنبيهات.
*   **الفائدة:** بدلاً من التنقل بين بوابتين، يمكن للمهندس الأمني مراقبة كل التنبيهات في مكان واحد (Sentinel).

### 2. استعلامات KQL المشتركة
يجب أن يكون مهندس الأتمتة (Security Engineer) ملمًا بلغة (Kusto Query Language) لكتابة استعلامات معقدة في (Sentinel) للكشف عن سلوكيات غير عادية، مثل:
*   محاولات تسجيل الدخول الفاشلة المتكررة (Brute Force).
*   نشاط غير معتاد من عناوين IP مشبوهة.

---

## الجزء الرابع: التحديثات الحديثة (سياق 2026)

في إطار التحديثات الحالية، يجب ذكر دور الذكاء الاصطناعي:

1.  **Microsoft Security Copilot:**
    *   يتم دمج (Copilot) داخل (Sentinel) و (Defender for Cloud).
    *   يساعد في تحليل التنبيهات (Alert Analysis) وتلخيص الحوادث.
    *   يسمح للمهندسين بكتابة استعلامات (KQL) باستخدام اللغة الطبيعية (Natural Language).

2.  **AI-Driven Detection:**
    *   تعتمد (Microsoft Defender for Cloud) الآن بشكل أكبر على نماذج اللغة الكبيرة (LLMs) لتصنيف التهديدات بدقة أعلى وتقليل الإنذارات الكاذبة (False Positives).

---

## ملخص المصطلحات التقنية (Technical Glossary)

*   **CSPM:** Cloud Security Posture Management.
*   **CWPP:** Cloud Workload Protection Platform.
*   **SIEM:** Security Information and Event Management.
*   **SOAR:** Security Orchestration, Automation, and Response.
*   **Log Analytics Workspace:** مساحة العمل الأساسية لتخزين البيانات.
*   **KQL:** Kusto Query Language.
*   **Logic Apps:** خدمة الأتمتة السحابية المستخدمة في الـ Playbooks.

---

**نصيحة للامتحان (Exam Tip):**
في امتحان AZ-500، قد يُطلب منك تحديد الحل الأمثل لتخزين بيانات الحرائق (Firewall Logs) وتحليلها. الإجابة الصحيحة عادة ما تكون (Microsoft Sentinel) لأنه الحل المصمم لهذا الغرض (SIEM)، وليس (Defender for Cloud) الذي يركز على الحماية والتقييم المباشر.
