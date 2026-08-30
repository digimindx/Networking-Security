# المحتوى التدريبي: تأمين شبكات Azure (Securing Azure Networks)

## المقدمة
تعتبر البنية التحتية للشبكات حجر الأساس في الأمن السيبراني السحابي. في إطار الامتحان (AZ-500)، يتوقع من المهندس فهم كيفية عزل الموارد، مراقبة الحركة المرورية، وحماية التطبيقات من الهجمات مثل هجمات حجب الخدمة (DDoS) والتسريبات البيانات.

---

## أولاً: مجموعات أمان الشبكة (Network Security Groups - NSGs)

تُعد (Network Security Groups) بمثابة جدران حماية افتراضية تتحكم في تدفق الشبكة (Network Traffic) في (Virtual Network).

### 1. آلية العمل
تقوم (NSG) بتصفية حركة المرور الواردة (Inbound) والصادرة (Outbound) بناءً على عناوين IP، والمنافذ (Ports)، والبروتوكولات. يمكن ربط (NSG) بـ:
*   (Subnet): لتطبيق القواعد على جميع الخوادم داخل الشبكة الفرعية.
*   (Network Interface): لتطبيق القواعد على خادم (VM) محدد.

### 2. قواعد الأمان (Security Rules)
تحتوي (NSG) على ثلاث أنواع من القواعد:
*   **Default Rules:** قواعد افتراضية لا يمكن حذفها، تسمح بحركة المرور الخاصة (Private) وتمنع حركة المرور العامة (Public) افتراضياً.
*   **Custom Rules:** قواعد يضيفها المستخدم لتلبية متطلبات العمل، ويتم تقييمها حسب الأولوية (Priority).
*   **Service Tags:** تتيح تجميع عناوين IP الخاصة بخدمات Azure (مثل (Storage) و (SQL)) في اسم واحد لتسهيل إدارة القواعد دون الحاجة لتحديث عناوين IP يدوياً.

---

## ثانياً: مجموعات أمان التطبيق (Application Security Groups - ASGs)

تُستخدم (Application Security Groups) لتبسيط إدارة (NSG). بدلاً من كتابة قواعد بناءً على عناوين IP، تقوم (ASG) بتجميع (Virtual Machines) بناءً على وظيفتها (مثل: ويب، قاعدة بيانات، تطبيق).

*   **الميزة:** عند إضافة خادم جديد إلى مجموعة (ASG)، تنتقل إليه القواعد الأمنية تلقائياً دون الحاجة لتعديل (NSG).

---

## ثالثاً: جدار الحماية (Azure Firewall) و (Web Application Firewall - WAF)

### 1. Azure Firewall
هي خدمة جدار حماية شبكة (Firewall) بالكامل ومُدارة (Fully Managed) من مايكروسوفت، تعمل على طبقة 7 (Layer 7) بالإضافة إلى طبقة 3 و 4.

*   **الميزات الرئيسية:**
    *   **Threat Intelligence:** تدمج قوائم IP محظورة (Blocked IP lists) من (Mandiant).
    *   **Nat Rules:** ترجمة عناوين الشبكة (Network Address Translation).
    *   **FQDN Filtering:** السماح أو منع حركة المرور بناءً على اسم النطاق (Fully Qualified Domain Name).
    *   **Integration:** تعمل بتكامل مع (Azure Route Table) لتوجيه حركة المرور (Honeypot deployment).

### 2. Web Application Firewall (WAF)
تصمم (WAF) خصيصاً لحماية تطبيقات الويب من الهجمات الشائعة مثل (OWASP Top 10) (مثل حقن SQL و XSS).

*   **أماكن العمل:**
    *   **Application Gateway:** حماية تطبيقات الويب الموزعة.
    *   **Azure CDN:** حماية المحتوى المنشر عبر الشبكة العالمية.
    *   **Front Door:** حماية التطبيقات السريعة (Global HTTP/HTTPS).

---

## رابعاً: حماية DDoS (Azure DDoS Protection)

توفر مايكروسوفت مستويين من الحماية ضد هجمات حجب الخدمة الموزعة (Distributed Denial of Service):

### 1. DDoS Protection Standard
*   **التكلفة:** مجانية (Included) لأي عنوان IP عام (Public IP) مرتبط بموارد (Virtual Network).
*   **الميزات:** كشف تلقائي للهجمات وتطبيق سياسات الحماية الافتراضية.

### 2. DDoS Protection Premium
*   **التكلفة:** باشتراك إضافي.
*   **الميزات:**
    *   حماية متقدمة ضد الهجمات المعقدة.
    *   تكامل مع (Azure Monitor) لتحليل تفصيلي للهجمات.
    *   **Incident Response:** فريق دعم متخصص من مايكروسوفت لمساعدتك أثناء الهجوم.

---

## خامساً: الربط الخاص (Private Link) والمحطات الطرفية الخاصة (Private Endpoints)

هذا الموضوع حيوي جداً في الامتحان الحديث لتأمين الوصول إلى الخدمات السحابية (PaaS).

### 1. Azure Private Link
تقنية تربط (Virtual Network) الخاص بك بخدمات (Azure PaaS) (مثل (Azure SQL)، (Key Vault)، (Storage)) عبر البنية التحتية الخاصة لمايكروسوفت، مما يمنع الوصول عبر الإنترنت العام.

### 2. Private Endpoint
هو عنوان IP خاص (Private IP) يتكون داخل (Subnet) محدد في شبكتك الافتراضية، ويرتبط بـ (Azure Private Link).
*   **الفائدة:** يمكنك الوصول للخدمة السحابية وكأنها جزء من شبكتك المحلية (On-Premises) باستخدام بروتوكول (VNET Peering) أو (ExpressRoute).

---

## سادساً: المراقبة والتشخيص (Network Watcher)

أداة (Network Watcher) ضرورية لتدقيق الشبكات (Network Auditing).

### 1. Topology
تعرض رسمًا بيانيًا لجميع موارد الشبكة، مما يساعد في فهم كيفية ارتباط (NSG) و (Route Tables) بالمصادر.

### 2. NSG Flow Logs
تسجيلات تدفق الشبكة التي تسجل معلومات عن حركة المرور التي تمر عبر (NSG).
*   **الأهمية:** تُستخدم لتحليل الهجمات، ومعرفة من يحاول الوصول للموارد (Audit Logs).
*   **التخزين:** يمكن تخزين السجلات في (Azure Storage) أو (Log Analytics Workspace) لمعالجتها لاحقاً.

---

## سابعاً: Azure Bastion

خدمة (Azure Bastion) توفر وصولاً آمناً لـ (RDP) و (SSH) إلى (Virtual Machines) عبر المتصفح (Browser).

*   **الميزة الأمنية:** لا يتطلب وجود عنوان IP عام (Public IP) للخوادم، مما يغلق ثغرة الوصول البعيد غير الآمن.
*   **التكامل:** تعمل مع (Private Endpoint) لتأمين الاتصال.

---

## ملخص للنقاط الحرجة لامتحان AZ-500

1.  **عند الحاجة لعزل خوادم ويب عن قاعدة البيانات:** استخدم (Network Security Groups) مع (Inbound Rules).
2.  **عند الحاجة لحماية تطبيق ويب من SQL Injection:** استخدم (Web Application Firewall) على (Application Gateway).
3.  **عند الرغبة في منع تسرب البيانات من (Key Vault) للإنترنت:** استخدم (Private Endpoint).
4.  **عند طلب تحليل مفصل لحركة المرور المشبوهة:** استخدم (NSG Flow Logs) مع (Log Analytics).
5.  **عند إدارة قواعد أمان لمجموعات كبيرة من الخوادم ديناميكياً:** استخدم (Application Security Groups).

---
*هذا المحتوى يغطي النطاق (Domain 2: Implement security for data protection) و (Domain 3: Implement security for network protection) من منهج AZ-500.*
