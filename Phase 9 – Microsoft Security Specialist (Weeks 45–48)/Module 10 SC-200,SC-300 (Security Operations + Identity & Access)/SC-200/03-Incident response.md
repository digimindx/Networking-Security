# محتوى تدريبي: SC-200 - Chapter 03: Incident Response
**Phase 9 – Microsoft Security Specialist (Weeks 45–48)**
**Module 10: SC-200 & SC-300**

## 1. مقدمة (Introduction)
في هذا الفصل، نركز على العملية المنهجية للتعامل مع الحوادث الأمنية (Incident Response) باستخدام أدوات Microsoft المتطورة. الهدف هو تمكين المحلل (Security Analyst) من تقليل وقت الاستجابة (MTTR) وتقليل الضرر الناجم عن الهجمات الإلكترونية.

## 2. دورة حياة استجابة الحوادث (Incident Response Lifecycle)
تعتمد استجابة الحوادث في بيئات Microsoft الحديثة على إطار عمل (NIST 800-61) المعدل ليتناسب مع السحابة (Cloud):

1.  **التحضير (Preparation):** إعداد البيئات، الربط (Connectors)، وقواعد الكشف (Analytics Rules).
2.  **الكشف والتحليل (Detection & Analysis):** رصد التنبيهات (Alerts) وتقييم مصداقيتها.
3.  **الاحتواء، الإزالة، والاستعادة (Containment, Eradication, Recovery):** عزل التهديد واستعادة النظام.
4.  **نشاط ما بعد الحادث (Post-Incident Activity):** التعلم من الحادث وتحسين السياسات.

---

## 3. الأدوات الرئيسية في Microsoft Sentinel (Core Tools)

يعتبر (Microsoft Sentinel) المنصة الأساسية لـ (SIEM) و (SOAR) في هذا الفصل.

### 3.1. إدارة الحوادث (Incident Management)
*   **تجميع الحوادث (Incident Grouping):** يقوم (Microsoft Sentinel) بربط التنبيهات المتعددة المتعلقة بنفس الحادث تلقائياً لتقليل (Alert Fatigue).
*   **مستوى الخطورة (Severity Levels):** تحديد أولوية الحوادث بناءً على (High, Medium, Low).
*   **ملاحظات الحادث (Incident Notes):** إمكانية إضافة ملاحظات داخل المنصة لتوثيق خطوات التحقيق (Investigation Steps).

### 3.2. لوحات العمل للتحقيق (Investigation Workbooks)
*   **Incident Response Workbook:** لوحة مخصصة توفر نظرة شاملة عن الحادث، تشمل:
    *   تفاصيل التهديد (Threat Details).
    *   الأصول المتأثرة (Compromised Assets).
    *   مؤشرات الاختراق (IOCs - Indicators of Compromise).
*   **Investigation Graph:** استخدام (Graph Query Language) لرسم العلاقات بين الكيانات (Entities) مثل (User, IP, Device, Application).

---

## 4. التكامل مع Microsoft Defender XDR
في عامي 2025-2026، أصبح التكامل بين (Microsoft Sentinel) و (Microsoft Defender XDR) جوهرياً.

*   **Defender for Endpoint:** عزل الأجهزة (Isolate Device) مباشرة من خلال (Microsoft Sentinel).
*   **Defender for Identity:** كشف الهجمات على (Active Directory) وهجمات (Pass-the-Hash).
*   **Defender for Office 365:** التعامل مع هجمات التصيد (Phishing) والبرمجيات الخبيثة في المرفقات.

---

## 5. الأتمتة والأوركسترة (SOAR - Microsoft Sentinel Playbooks)
تعتبر (Playbooks) هي الأداة الرئيسية لأتمتة استجابة الحوادث.

### 5.1. كيفية عمل الـ Playbooks
تعتمد على (Logic Apps) وتُطلق استجابةً لحدث معين (Trigger).

### 5.2. أمثلة على السيناريوهات التلقائية (Use Cases):
1.  **عزل الجهاز تلقائياً:** عند اكتشاف تهديد (High Severity)، يتم استدعاء (Playbook) لعزل الجهاز عبر (Microsoft Defender XDR).
2.  **إعادة تعيين كلمة المرور:** إذا كان الحادث يتعلق بـ (Compromised Account)، يقوم الـ (Playbook) بإعادة تعيين كلمة المرور وإلغاء جلسات الدخول النشطة في (Entra ID).
3.  **حظر عنوان IP:** إرسال عنوان IP الضار إلى جدار الحماية (Firewall) أو (DDoS Protection).
4.  **إرسال تنبيهات:** إشعار فرق الاستجابة عبر (Teams) أو (Email).

---

## 6. التحقيق الجنائي الرقمي (Digital Forensics)
في سياق (SC-200)، يجب أن يعرف المحلل كيفية جمع الأدلة:

*   **Defender Incident Response Workbook:** تتيح هذه الأداة تصدير البيانات (Export Data) للتحليل الخارجي.
*   **Microsoft Sentinel Notebooks:** استخدام (Notebooks) القائمة على (Python) لإجراء تحليلات متقدمة (Advanced Analytics) و (Threat Hunting) على البيانات الضخمة.
*   **جمع السجلات (Log Collection):** التأكد من جمع (Syslog, Windows Event Logs, Audit Logs).

---

## 7. أحدث الميزات والتحديثات (2025-2026 Context)
بناءً على آخر تحديثات Microsoft، يجب التركيز على النقاط التالية في التدريب:

### 7.1. الذكاء الاصطناعي التوليدي (Copilot in Microsoft Sentinel)
*   استخدام (Copilot) لكتابة استعلامات (KQL) بلغة طبيعية.
*   تحليل الحوادث تلقائياً باستخدام الذكاء الاصطناعي (AI-driven Incident Summarization).
*   اقتراح خطوات استجابة (Response Recommendations) بناءً على حوادث مشابهة.

### 7.2. إدارة مسار الهجوم (Attack Path Management)
*   ربط البيانات من (Microsoft Sentinel) و (Entra ID) لرسم مسار المهاجم (Kill Chain).
*   تحديد نقاط الضعف (Vulnerabilities) التي استغلها المهاجم.

### 7.3. الاستجابة للحوادث (Automated Investigation & Response - AIR)
*   تحسين خوارزميات الـ (AIR) في (Microsoft Defender) للتحقيق في الحوادث المعقدة دون تدخل بشري في المراحل الأولى.

---

## 8. سيناريوهات عملية للتدريب (Lab Scenarios)

**السيناريو 1: استجابة لهجوم تصيد (Phishing Response)**
1.  استقبال تنبيه (Microsoft Defender for Office 365).
2.  تحليل الرابط الخبيث في (Microsoft Sentinel).
3.  استخدام (Playbook) لحذف الرسالة من صندوق البريد لجميع المستخدمين.
4.  تحديث (Block List) في (Entra ID).

**السيناريو 2: استجابة لاختراق حساب (Account Compromise)**
1.  رصد نشاط غير طبيعي (Anomalous Sign-in) في (Entra ID).
2.  عزل الجهاز المتأثر عبر (Microsoft Sentinel Playbook).
3.  فرض إعادة تعيين كلمة المرور (MFA Reset).
4.  مراجعة (Audit Logs) للتحقق من سرقة البيانات.

---

## 9. خلاصة الفصل
يجب على المتدرب إتقان الربط بين أدوات الكشف (Microsoft Sentinel) وأدوات الاستجابة (Microsoft Defender XDR) وأدوات الأتمتة (Logic Apps/Playbooks) لضمان استجابة فعالة وسريعة للحوادث الأمنية.

**المصطلحات الأساسية للمراجعة:**
*   (SIEM)
*   (SOAR)
*   (KQL - Kusto Query Language)
*   (IOCs - Indicators of Compromise)
*   (MITRE ATT&CK Framework)
*   (MTTR - Mean Time To Respond)
