# (أتمتة الشبكات) - النسخة الموسعة والتطبيقية

---

### مقدمة
في البنية التحتية للشبكات الحديثة، لم تعد الطرق التقليدية لإدارة الأجهزة (Manual CLI Management) كافية بسبب التعقيد، الحجم المتزايد، والحاجة إلى سرعة الاستجابة. تهدف الأتمتة (Automation) إلى تقليل التدخل البشري (Human Intervention)، القضاء على الأخطاء البشرية (Human Errors)، وضمان الاتساق (Consistency) في التكوينات. في سياق شهادة ENCOR، يجب فهم البروتوكولات، نماذج البيانات، وأدوات إدارة التكوين بشكل معمق.

---

### المستوى الأول: أساسيات قابلية البرمجة الشبكية (Network Programmability Foundations)

تشير قابلية البرمجة الشبكية إلى قدرة الأجهزة على فصل Plane التحكم (Control Plane) عن Plane البيانات (Data Plane)، والتواصل مع أنظمة التحكم الخارجية باستخدام واجهات برمجية (APIs).

#### 1. نماذج البيانات (Data Modeling)
قبل أن يتواصل جهازان، يجب أن يتفقا على "لغة" و"هيكل" البيانات.
*   **YANG (Yet Another Next Generation):**
    *   هي لغة نمذجة بيانات (Data Modeling Language) معتمدة من IETF.
    *   **المفهوم الجوهري:** تفصل بين "هيكل البيانات" (Schema) و"قيمة البيانات" (Data Instance). الـ Schema يحدد القواعد (مثل: اسم الـ Interface يجب أن يكون String، والـ IP يجب أن يكون Integer)، بينما الـ Instance هو القيمة الفعلية (مثل: GigabitEthernet1, 10.0.0.1).
    *   تُستخدم لتحديد التكوينات (Configuration) والحالة (State) وتُطبق عبر بروتوكولات مثل NETCONF و RESTCONF.

#### 2. أنواع واجهات البرمجة (API Types)
*   **REST (Representational State Transfer):**
    *   تعتمد على بروتوكول HTTP/HTTPS.
    *   تستخدم صيغ البيانات JSON (بشكل أساسي) أو XML.
    *   تعتمد على عمليات Stateless وتستخدم HTTP Methods (GET, POST, PUT, DELETE).
*   **RPC (Remote Procedure Call):**
    *   تسمح لبرنامج باستدعاء دالة أو إجراء على جهاز آخر كما لو كانت محلية.
    *   تُستخدم كآلية أساسية داخل بروتوكول NETCONF.
*   **SOAP (Simple Object Access Protocol):**
    *   بروتوكول قديم يعتمد على XML و WSDL.
    *   يستخدم بشكل نادر في الشبكات الحديثة بسبب تعقيده وثقل الـ Overhead مقارنة بـ REST.

---

### المستوى الثاني: بروتوكولات إدارة الشبكات والتيليمتري (Management & Telemetry Protocols)

يجب التمييز بدقة بين بروتوكولات التكوين (Configuration) وبروتوكولات المراقبة (Monitoring).

#### 1. بروتوكولات الإدارة والتكوين (Configuration Management)

**أ. NETCONF (Network Configuration Protocol)**
*   **البروتوكول:** يعتمد على RPC كآلية نقل، و XML كصيغة للبيانات.
*   **النقل:** يعمل فوق SSH (Port 830) لضمان التشفير والتحقق من الهوية.
*   **الوظيفة:** مخصص للتكوين (Configuration) وإدارة الأجهزة.
*   **الميزات:** يدعم المعاملات (Transactions) عبر آلية `<lock>` و `<commit>`، مما يسمح بإلغاء التغييرات (Rollback) في حال حدوث خطأ.
*   **نموذج البيانات:** يستخدم YANG.

**ب. RESTCONF**
*   **البروتوكول:** يعتمد على RESTful API وبروتوكول HTTP/HTTPS.
*   **صيغة البيانات:** يدعم JSON (المفضل) و XML.
*   **الوظيفة:** مخصص للتكوين (Configuration) كخيار بديل وأخف وزناً لـ NETCONF.
*   **الميزات:** أسرع في التعامل مع البيانات الصغيرة لأنه لا يحتاج إلى تحليل (Parsing) معقد لـ XML. يستخدم HTTP Methods (GET للقراءة، PUT/POST/PATCH للتعديل، DELETE للحذف).
*   **نموذج البيانات:** يستخدم YANG.

#### 2. بروتوكولات المراقبة والتيليمتري (Monitoring & Telemetry)

**أ. SNMP (Simple Network Management Protocol)**
*   **الوظيفة:** يستخدم بشكل أساسي للمراقبة (Monitoring) وجمع الإحصائيات، وليس للتكوين الشامل.
*   **الآلية:** يعتمد على نموذج Manager-Agent وصيغة MIB (Management Information Base).
*   **العيوب:** يعتمد على آلية الـ Polling (الاستطلاع الدوري) مما يستهلك موارد الجهاز والشبكة.

**ب. gRPC و Model-Driven Telemetry (MDT)**
*   **البروتوكول:** gRPC (gRPC Remote Procedure Call) بروتوكول مفتوح المصدر عالي الأداء من Google.
*   **صيغة البيانات:** يستخدم Protocol Buffers (Protobuf) لضغط البيانات بشكل فعال.
*   **الآلية:** يدعم نمط الـ Push (Streaming Telemetry).
*   **الميزة:** بدلاً من انتظار الـ Manager ليسأل (Polling)، يقوم الجهاز (Agent) بإرسال البيانات (Streaming) تلقائياً وبشكل مستمر عند تغير الحالة أو حسب جدول زمني دقيق، مما يوفر رؤية فورية (Real-time visibility).

#### جدول المقارنة الشامل (Summary Table)

| الميزة | NETCONF | RESTCONF | SNMP | gRPC (Telemetry) |
| :--- | :--- | :--- | :--- | :--- |
| **بروتوكول النقل** | SSH (Port 830) | HTTP/HTTPS | UDP (161/162) | TCP (HTTP/2) |
| **صيغة البيانات** | XML | JSON / XML | MIB (Text/ASN.1) | Protocol Buffers |
| **الوظيفة الأساسية** | Configuration | Configuration | Monitoring | Monitoring (Streaming) |
| **نمذجة البيانات** | YANG | YANG | MIB | YANG |
| **آلية العمل** | RPC / Request-Reply | REST / HTTP Methods | Polling / Traps | Push / Streaming |

---

### المستوى الثالث: أدوات إدارة التكوين (Configuration Management Tools)

تستخدم هذه الأدوات لتوزيع التكوينات (Configuration) على عدد كبير من الأجهزة بشكل متسق.

#### 1. Ansible (الأداة الأهم في ENCOR)
*   **النوع:** Agentless (لا يحتاج إلى تثبيت أي برنامج أو عميل على الأجهزة المستهدفة).
*   **اللغة:** يستخدم YAML لكتابة ملفات التشغيل (Playbooks).
*   **الآلية:** يعتمد على Push Model، حيث يرسل الـ Control Node الأوامر عبر SSH (للأجهزة التقليدية) أو APIs (للأجهزة الحديثة).
*   **المكونات الأساسية:**
    *   **Inventory:** ملف يحدد الأجهزة المستهدفة (IPs, Groups).
    *   **Playbook:** ملف YAML يصف الحالة المطلوبة (Desired State).
    *   **Modules:** أكواد برمجية تنفذ المهام (مثل `ios_config`, `nxos_command`).

#### 2. Puppet & Chef
*   **النوع:** Agent-based (تحتاج لتثبيت عميل على كل جهاز).
*   **اللغة:** تستخدم لغة Ruby (DSL).
*   **الآلية:** تعتمد على Pull Model، حيث يتواصل العميل (Agent) مع السيرفر المركزي لطلب التكوينات وتطبيقها.

---

### المستوى الرابع: DevOps للشبكات - Version Control & CI/CD

#### 1. التحكم في الإصدارات (Version Control)
*   **Git:** نظام للتحكم في الإصدارات (VCS).
*   **المفاهيم:** Repository (المستودع), Commit (حفظ التغييرات), Branch (فرع للتجربة), Merge (دمج الفروع).
*   **الأهمية:** يسمح بتتبع التغييرات في التكوينات (Configuration as Code)، التراجع عن الأخطاء (Rollback)، والعمل التعاوني.

#### 2. التكامل والنشر المستمر (CI/CD)
*   **CI (Continuous Integration):** دمج التغييرات بشكل مستمر واختبارها تلقائياً.
*   **CD (Continuous Deployment/Delivery):** نشر التكوينات تلقائياً على الأجهزة بعد نجاح الاختبارات.
*   **الأدوات:** Jenkins, GitLab CI.

---

### المستوى الخامس: تمارين عملية وتطبيقية (Practical Exercises)

#### تمرين 1: فهم بنية البيانات (JSON & YANG)
**المطلوب:** تحويل تكوين Cisco IOS إلى صيغة JSON لاستخدامه مع RESTCONF.
**التكوين التقليدي (CLI):**
```text
interface GigabitEthernet1
 description Management_Link
 ip address 192.168.1.1 255.255.255.0
 no shutdown
```
**الصيغة المطلوبة (JSON Instance):**
```json
{
  "Cisco-IOS-XE-native:interface": {
    "GigabitEthernet": [
      {
        "name": "1",
        "description": "Management_Link",
        "ip": {
          "address": {
            "primary": {
              "address": "192.168.1.1",
              "mask": "255.255.255.0"
            }
          }
        }
      }
    ]
  }
}
```
*ملاحظة: الـ Schema (YANG) هو من يحدد أن "name" يجب أن يكون رقماً أو نصاً، وأن "ip" يحتوي على كائن فرعي اسمه "address".*

#### تمرين 2: كتابة Ansible Playbook لإدارة Cisco IOS
**المطلوب:** إنشاء Playbook لتغيير hostname وتفعيل OSPF على مجموعة من الأجهزة.
**ملف Inventory (`hosts.ini`):**
```ini
[core_routers]
R1 ansible_host=10.0.0.1
R2 ansible_host=10.0.0.2

[core_routers:vars]
ansible_network_os=ios
ansible_connection=network_cli
ansible_user=admin
ansible_ssh_pass=cisco123
```

**ملف Playbook (`configure_network.yml`):**
```yaml
---
- name: Configure Core Routers
  hosts: core_routers
  gather_facts: no
  
  tasks:
    - name: Configure Hostname
      cisco.ios.ios_config:
        lines:
          - hostname {{ inventory_hostname }}
          
    - name: Configure OSPF
      cisco.ios.ios_config:
        lines:
          - router ospf 1
          - network 10.0.0.0 0.0.0.255 area 0
          - network 192.168.1.0 0.0.0.255 area 0
```
*شرح الآلية: Ansible سيتصل عبر SSH (ansible_connection=network_cli)، سيقارن الحالة الحالية بالحالة المطلوبة (Desired State في الـ lines)، وسيقوم بتطبيق التغييرات فقط إذا كانت مختلفة (Idempotency).*

#### تمرين 3: سير عمل Git لإدارة تكوينات الشبكة (Git Workflow)
**المطلوب:** محاكاة عملية تعديل تكوين شبكة وحفظه في Git.
**الأوامر التنفيذية في Terminal:**

```bash
# 1. إنشاء مستودع جديد أو استنساخ موجود
git clone https://gitlab.com/company/network-configs.git
cd network-configs

# 2. إنشاء فرع جديد لتجربة التعديلات (Best Practice)
git checkout -b feature/update-ospf-area

# 3. تعديل ملف التكوين (مثال باستخدام محرر نصوص)
# (يتم تعديل ملف routers_config.txt هنا)

# 4. إضافة التعديلات إلى Staging Area
git add routers_config.txt

# 5. حفظ التعديلات مع رسالة توضيحية (Commit)
git commit -m "Update OSPF area for R1 and R2 interfaces"

# 6. رفع التعديلات إلى المستودع المركزي (Push)
git push origin feature/update-ospf-area

# 7. بعد المراجعة، يتم دمج الفرع مع الفرع الرئيسي (Merge)
git checkout main
git merge feature/update-ospf-area
git push origin main
```

#### تمرين 4: تحليل بروتوكولات التيليمتري (Telemetry Analysis)
**سيناريو:** شركة تعاني من بطء في شبكة المراقبة (SNMP) بسبب الـ Polling كل 60 ثانية، مما يسبب فقدان بيانات الـ Micro-bursts.
**الحل المقترح:** 
1. الانتقال من SNMP إلى **Model-Driven Telemetry (MDT)** باستخدام **gRPC**.
2. تكوين الجهاز لإرسال البيانات (Push) كل 10 ثوانٍ أو عند تجاوز عتبة معينة (Event-driven).
3. استخدام **YANG** models لتحديد الـ Paths المراد مراقبةتها (مثل CPU, Interface Counters).
4. إرسال البيانات إلى Collector مثل Cisco Crosswork أو Elastic Stack لتحليلها.

---

### خاتمة وتوجيهات لامتحان ENCOR
في امتحان الـ ENCOR، ركز على النقاط التالية:
1.  **التمييز بين NETCONF و RESTCONF:** كلاهما للتكوين ويستخدمان YANG، لكن NETCONF يستخدم SSH/XML وRPC، بينما RESTCONF يستخدم HTTP/JSON/XML وREST.
2.  **مفهوم YANG:** تذكر دائماً أنه يفصل الـ Schema عن الـ Instance.
3.  **Ansible:** تأكد من حفظ ميزتي (Agentless) و (Push Model) واستخدامه لـ YAML.
4.  **Telemetry:** افهم أن gRPC و MDT يعتمدان على الـ Push Model و Protocol Buffers، بينما SNMP يعتمد على الـ Polling و MIB.