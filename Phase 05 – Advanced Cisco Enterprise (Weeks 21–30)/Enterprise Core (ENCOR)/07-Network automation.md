# الدورة: Phase 5 – Advanced Cisco Enterprise
## الأسبوع 21-30: Module 6 - Enterprise Core (ENCOR)
### الموضوع: 07-Network Automation (أتمتة الشبكات)

---

### مقدمة (Introduction)

في البنية التحتية للشبكات الحديثة، لم تعد الطرق التقليدية لإدارة الأجهزة (Manual Management) كافية بسبب التعقيد والحجم المتزايد. تهدف الأتمتة إلى تقليل التدخل البشري، تقليل الأخطاء، وتسريع العمليات. في سياق الـ (ENCOR)، يتم التركيز على فهم البروتوكولات، نماذج البيانات، وأدوات إدارة التكوين.

---

### 1. قابلية البرمجة الشبكية (Network Programmability)

تشير قابلية البرمجة إلى قدرة الأجهزة على التواصل مع أنظمة التحكم الخارجية باستخدام واجهات برمجية (Application Programming Interfaces - APIs).

#### أ. أنواع واجهات البرمجة (API Types)
1.  **REST (Representational State Transfer):**
    *   تعتمد على بروتوكول (HTTP).
    *   تستخدم صيغ البيانات (JSON) بشكل أساسي.
    *   تتميز بالبساطة وتوافقها مع تقنيات الويب.
2.  **RPC (Remote Procedure Call):**
    *   تسمح لبرنامج باستدعاء دالة أو إجراء على جهاز آخر.
    *   تستخدم عادة في بروتوكولات مثل (NETCONF).
3.  **SOAP (Simple Object Access Protocol):**
    *   بروتوكول قديم يعتمد على (XML).
    *   يستخدم بشكل أقل في الشبكات الحديثة مقارنة بـ (REST).

#### ب. نماذج البيانات (Data Modeling)
قبل نقل البيانات، يجب تحديد هيكلها. اللغة المستخدمة في Cisco هي:
*   **YANG (Yet Another Next Generation):**
    *   لغة لنمذجة البيانات (Data Modeling Language) معتمدة من قبل (IETF).
    *   تفصل بين "هيكل البيانات" (Schema) و"قيمة البيانات" (Data Instance).
    *   تستخدم لتحديد كيفية قراءة وكتابة التكوينات (Configuration) والحالة (State) عبر البروتوكولات مثل (NETCONF) و (RESTCONF).

---

### 2. بروتوكولات إدارة الشبكات (Network Management Protocols)

يجب التمييز بين البروتوكولات التي تقوم بالإدارة (Configuration) وتلك التي تقوم بالمراقبة (Monitoring).

#### أ. بروتوكولات الإدارة (Configuration Management)
1.  **NETCONF (Network Configuration):**
    *   يعتمد على (RPC) و (XML).
    *   يستخدم قاعدة بيانات (XML) لنقل التكوينات.
    *   يدعم المعاملات (Transactions) لإلغاء التغييرات في حال حدوث خطأ.
    *   يعتمد على بروتوكول نقل (SSH) للتحقق والتشفير.
2.  **RESTCONF:**
    *   يعتمد على (RESTful API) وبروتوكول (HTTP/HTTPS).
    *   يستخدم صيغتي (JSON) و (XML) لنقل البيانات.
    *   أسرع من (NETCONF) في التعامل مع البيانات الصغيرة لأنه لا يحتاج إلى تحليل (Parsing) معقد لـ (XML).
3.  **SNMP (Simple Network Management Protocol):**
    *   يستخدم بشكل أساسي للمراقبة (Monitoring) وليس للتكوين.
    *   يعتمد على نموذج (Manager-Agent).
    *   يعتمد على صيغة (MIB - Management Information Base).

#### ب. بروتوكولات التيليمتري (Telemetry Protocols)
*   **gRPC (gRPC Remote Procedure Call):**
    *   بروتوكول مفتوح المصدر عالي الأداء.
    *   يستخدم مبادئ (Protocol Buffers) لضغط البيانات.
    *   يدعم نمط (Push) حيث يرسل الجهاز البيانات تلقائياً عند تغيرها (Streaming Telemetry).

---

### 3. أدوات إدارة التكوين (Configuration Management Tools)

تستخدم هذه الأدوات لتوزيع التكوينات (Configuration) على عدد كبير من الأجهزة.

#### أ. Ansible
*   **النوع:** (Agentless) لا يحتاج إلى تثبيت عميل على الأجهزة المستهدفة.
*   **اللغة:** يستخدم (YAML) لكتابة (Playbooks).
*   **الآلية:** يعتمد على (Push Model) حيث يرسل الأوامر عبر (SSH).
*   **وحدات Cisco:**
    *   `ios_config`: لإدارة أجهزة (Cisco IOS/IOS-XE).
    *   `nxos_command`: لإدارة أجهزة (Cisco NX-OS).

#### ب. Puppet & Chef
*   تعتمد على (Agent-based) (تحتاج لتثبيت برنامج على الجهاز).
*   تستخدم لغة (Ruby) في التكوين.
*   تعتمد على (Pull Model) حيث يتواصل العميل مع السيرفر لطلب التكوينات.

---

### 4. التحكم في الإصدارات (Version Control)

أداة أساسية لأي مهندس شبكات يعمل في بيئة أتمتة:
*   **Git:**
    *   نظام للتحكم في الإصدارات (Version Control System).
    *   يسمح بتخزين التغييرات في التكوينات ومتابعتها.
    *   **المفاهيم الأساسية:** (Repository, Branching, Merging, Commit).

---

### 5. التكامل المستمر والنشر المستمر (CI/CD)

*   **CI/CD (Continuous Integration / Continuous Deployment):**
    *   منهجية لتسريع عمليات نشر التكوينات.
    *   **Jenkins:** أداة شائعة لإنشاء (Pipelines) للتكامل والنشر.
    *   **GitLab:** منصة توفر (Git Repository) وأدوات (CI/CD) مدمجة.

---

### ملخص الفروقات التقنية (Summary Table)

| الميزة | NETCONF | RESTCONF | SNMP | gRPC |
| :--- | :--- | :--- | :--- | :--- |
| **البروتوكول** | SSH | HTTP/HTTPS | UDP/TCP | TCP |
| **صيغة البيانات** | XML | JSON / XML | MIB (Text) | Protocol Buffers |
| **الوظيفة** | Configuration | Configuration | Monitoring | Monitoring (Push) |
| **نمذجة البيانات** | YANG | YANG | MIB | YANG |

---

### خاتمة

في امتحان الـ (ENCOR)، يجب أن تكون قادراً على التمييز بين متى تستخدم (NETCONF) ومتى تستخدم (RESTCONF)، وفهم أهمية (YANG) في فصل البيانات عن الهيكل، ومعرفة الأساسيات حول أدوات مثل (Ansible) وكيفية عملها بدون عميل (Agentless).
