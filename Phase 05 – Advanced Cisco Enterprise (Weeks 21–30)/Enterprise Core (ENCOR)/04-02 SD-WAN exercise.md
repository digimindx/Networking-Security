# تدريب عملي: نشر وتكوين Cisco SD-WAN في بيئة CML

يهدف هذا التدريب العملي إلى تطبيق المفاهيم النظرية لبنية Cisco SD-WAN على بيئة عملية باستخدام برنامج Cisco Modeling Labs (CML). سيقوم الطالب ببناء الطوبولوجيا، وتكوين المستويات الأربعة (Management, Control, Orchestration, Data)، والتحقق من عمل بروتوكولات التراكب (Overlay Protocols).

## المتطلبات الأساسية (Prerequisites)
يجب توفر الصور التالية (Node Definitions) في برنامج CML:
1. vManage (Viptela image)
2. vSmart (Viptela image)
3. vBond (Viptela image)
4. cEdge (IOS XE image مثل Catalyst 8000v أو CSR1000v)
5. Internet Cloud أو L2 Switch لمحاكاة طبقة النقل (Transport Plane).



## دور كل Node
**1. SD-WAN Manager (vManage)**
* **المستوى:** مستوى الإدارة (Management Plane).
* **الدور:** يعمل كواجهة إدارة مركزية (Centralized Management GUI). من خلاله يقوم مسؤولو الشبكة بتعريف السياسات (Policies)، ومراقبة أداء الشبكة (Monitoring and Telemetry)، وإدارة الشهادات الرقمية (Certificates)، وتكوين الأجهزة الطرفية (Edge Devices).

**2. Orchestrator (vBond)**
* **المستوى:** مستوى التنسيق والمصادقة (Orchestration Plane).
* **الدور:** يُعد نقطة الدخول الآمنة للشبكة، ويقوم بالمهام التالية أثناء عملية التشغيل الأولي (Bootstrapping):
  - المصادقة الأولية (Initial Authentication) للأجهزة الجديدة باستخدام الشهادات الرقمية.
  - تسهيل عملية اكتشاف الأجهزة (Device Discovery)، خاصة عند وجود الأجهزة خلف بوابات ترجمة عناوين الشبكة (NAT Traversal).
  - توزيع عناوين IP الخاصة بـ vManage و vSmart على الأجهزة الطرفية الجديدة لتتمكن من الاتصال بهما.

**3. vSmart**
* **المستوى:** مستوى التحكم (Control Plane).
* **الدور:** يُعتبر "عقل" الشبكة. مسؤوليته الأساسية هي تبادل معلومات التوجيه (Routing Information) وسياسات التوجيه (Routing Policies) بين الأجهزة الطرفية عبر بروتوكول OMP. كما يقوم بإنشاء أنفاق البيانات (Data Tunnels) وتوزيع مفاتيح التشفير (Encryption Keys) لضمان اتصال آمن.

**4. vEdge**
* **المستوى:** مستوى التوجيه والتنفيذ (Data Plane).
* **الدور:** هو جهاز توجيه افتراضي (Virtual Appliance) يعمل على منصات الخوادم الافتراضية (Hypervisors) مثل VMware أو KVM، ويُستخدم غالباً في مراكز البيانات (Data Centers). وظيفته الأساسية هي التعامل مع حركة المرور الفعلية (Actual Traffic)، وتطبيق السياسات محلياً (Local Policy Enforcement)، وإنشاء أنفاق البيانات المشفرة (Encrypted Data Tunnels) مثل IPsec للاتصال ببقية الشبكة.
---

## أولاً: طوبولوجيا الشبكة (Network Topology)

توضح الصورة التالية كيفية ربط مكونات SD-WAN بطبقة النقل (Underlay) التي تمثل الإنترنت أو شبكة النقل.

```mermaid
graph TD
    subgraph MCP["Management and Control Planes"]
        vManage["vManage<br/>Management Plane"]
        vSmart["vSmart Controller<br/>Control Plane"]
        vBond["vBond Orchestrator<br/>Orchestration Plane"]
    end
    
    subgraph OP["Overlay / Data Plane"]
        cEdge1["cEdge Router 1<br/>Branch 1 - Data Plane"]
        cEdge2["cEdge Router 2<br/>Branch 2 - Data Plane"]
    end
    
    subgraph TP["Transport Plane - Underlay"]
        Cloud["Internet / Transport Cloud"]
    end
    
    vManage --- Cloud
    vSmart --- Cloud
    vBond --- Cloud
    cEdge1 --- Cloud
    cEdge2 --- Cloud
```

---

## ثانياً: خطة العنونة والمعاملات (Addressing & Parameters Plan)

قبل البدء في التكوين، يجب تحديد عناوين IP ومعاملات التهيئة الأولية (Bootstrap Parameters).

| المكون (Component) | دور الجهاز (Role) | عنوان IP للواجهة (Transport IP) | عنوان IP للنظام (System IP) |
| :--- | :--- | :--- | :--- |
| vBond | Orchestration Plane | 10.0.0.1/24 | 192.168.1.1 |
| vSmart | Control Plane | 10.0.0.2/24 | 192.168.1.2 |
| vManage | Management Plane | 10.0.0.3/24 | 192.168.1.3 |
| cEdge1 | Data Plane (Branch 1) | 10.0.0.11/24 | 192.168.1.11 |
| cEdge2 | Data Plane (Branch 2) | 10.0.0.12/24 | 192.168.1.12 |

**المعاملات المشتركة (Common Parameters):**
* اسم المنظمة (Organization Name): `Lab_Org`
* اسم النطاق (Domain Name): `lab.local`

---

## ثالثاً: تكوين الأجهزة (Node Configurations)

تم تجميع إعدادات كل جهاز على حدة مع شرح تفصيلي للوظيفة التي يؤديها كل أمر.

### 1. مكون vBond Orchestrator (مستوى التنسيق والمصادقة)
يعتبر vBond نقطة الدخول الأولى. يقوم بالمصادقة الأولية (Initial Authentication) وتوزيع عناوين الـ Controllers.

**التكوين (Viptela CLI):**
```text
# الدخول لوضع التكوين
configure

# تحديد عنوان IP الخاص بـ vBond والذي ستعرفه الأجهزة مسبقاً
orchestrator
  local-address 10.0.0.1
  exit

# تحديد اسم المنظمة والنطاق للمصادقة
organization
  name Lab_Org
  domain-name lab.local
  exit

# حفظ التكوين وإعادة تشغيل الخدمة
commit
```
**الشرح:** 
تم تعيين `local-address` ليعرف الجهاز عنوانه في طبقة النقل (Transport Plane). تم تحديد `organization name` و `domain-name` لأن أي جهاز يحاول الانضمام للشبكة يجب أن يتطابق مع هذه المعاملات لضمان الأمان الهوياتي (Identity Security).

### 2. مكون vSmart Controller (مستوى التحكم)
يعمل vSmart كعقل الشبكة، حيث يبادل معلومات التوجيه عبر بروتوكول OMP ويوزع سياسات التوجيه.

**التكوين (Viptela CLI):**
```text
configure

# توجيه vSmart ليعرف عنوان vBond للاتصال به
vbond
  local-address 10.0.0.2
  address 10.0.0.1
  exit

# إعدادات بروتوكول OMP لتوزيع المسارات
omp
  advertise ipv4 bgp
  advertise ipv4 ospf
  exit

organization
  name Lab_Org
  domain-name lab.local
  exit

commit
```
**الشرح:**
أمر `vbond address` يخبر الـ Control Plane بعنوان الـ Orchestrator لبدء عملية الاكتشاف (Device Discovery). تم تفعيل `omp` لتوزيع مسارات BGP و OSPF القادمة من الفروع عبر طبقة التراكب (Overlay Plane).

### 3. مكون vManage (مستوى الإدارة)
vManage هو الواجهة المركزية (Centralized Management GUI). في CML، يتم التكوين المبدئي عبر CLI ثم إكمال الباقي عبر المتصفح.

**التكوين المبدئي (Viptela CLI):**
```text
configure

# توجيه vManage ليعرف عنوان vBond
vbond
  local-address 10.0.0.3
  address 10.0.0.1
  exit

organization
  name Lab_Org
  domain-name lab.local
  exit

commit
```
**الشرح:**
بعد هذا التكوين المبدئي، يمكن الوصول إلى vManage عبر المتصفح باستخدام العنوان `https://10.0.0.3`. من خلال الواجهة الرسومية، سيتم لاحقاً اعتماد الشهادات الرقمية (Certificates) لأجهزة cEdge، وإنشاء قوالب الأجهزة (Device Templates)، وتطبيق السياسات المركزية (Centralized Policies).

### 4. أجهزة cEdge Routers (مستوى التوجيه والتنفيذ - Data Plane)
أجهزة cEdge تعمل بنظام IOS XE. سنقوم بتكوين cEdge1، وcEdge2 بنفس المنطق مع تغيير عناوين IP.

**التكوين لـ cEdge1 (IOS XE CLI):**
```text
! الدخول لوضع SD-WAN
sdwan

! تحديد عنوان النظام (System IP) ومعرف الجهاز
system
  system-ip 192.168.1.11
  site-id 11
  exit

! تحديد معلومات المنظمة
organization
  name Lab_Org
  domain-name lab.local
  exit

! تحديد عنوان vBond للاتصال المبدئي (Bootstrapping)
vbond
  address 10.0.0.1
  local-address 10.0.0.11
  exit

! تفعيل واجهة النقل (Transport Interface)
interface GigabitEthernet1
  no shutdown
  ip address 10.0.0.11 255.255.255.0
  exit

! ربط الواجهة بـ SD-WAN كواجهة نقل
interface GigabitEthernet1
  tunnel-interface
    encapsulation ipsec
    color biz-internet
    no allow-service all
    allow-service vbond
    allow-service omp
    exit
  exit

! تفعيل التوجيه (OMP) لاستقبال المسارات من vSmart
router omp
  no shutdown
  exit

! حفظ التكوين
end
write memory
```

**التكوين لـ cEdge2 (IOS XE CLI):**
```text
sdwan
system
  system-ip 192.168.1.12
  site-id 12
  exit
organization
  name Lab_Org
  domain-name lab.local
  exit
vbond
  address 10.0.0.1
  local-address 10.0.0.12
  exit
interface GigabitEthernet1
  no shutdown
  ip address 10.0.0.12 255.255.255.0
  exit
interface GigabitEthernet1
  tunnel-interface
    encapsulation ipsec
    color biz-internet
    no allow-service all
    allow-service vbond
    allow-service omp
    exit
  exit
router omp
  no shutdown
  exit
end
write memory
```

**الشرح التفصيلي لإعدادات cEdge:**
1. `system system-ip` و `site-id`: يحددان هوية الجهاز في شبكة SD-WAN.
2. `vbond address`: يوجه الجهاز للتواصل مع Orchestration Plane لعملية المصادقة الأولية (Initial Authentication).
3. `tunnel-interface`: تحول الواجهة الفيزيائية إلى واجهة نفق (Tunnel Interface) في طبقة التراكب (Overlay Plane). تم تحديد `encapsulation ipsec` لتشفير حركة مرور البيانات (Data Traffic).
4. `allow-service vbond` و `omp`: يسمح بمرور حركة مرور التحكم (Control Traffic) الخاصة بالمصادقة وتبادل المسارات عبر هذه الواجهة.

---

## رابعاً: التحقق من التشغيل واستكشاف الأخطاء (Verification & Troubleshooting)

بعد تطبيق التكوينات، يجب التحقق من أن المستويات المختلفة تعمل بشكل صحيح.

### 1. التحقق من حالة الاتصال بـ vBond (Orchestration Plane)
على جهاز cEdge، استخدم الأمر التالي:
```text
show sdwan orchestrator connections
```
**النتيجة المتوقعة:** يجب أن تظهر حالة الاتصال بـ vBond كـ `Up`. إذا كانت `Down`، فهذا يعني وجود مشكلة في المصادقة (Authentication Failure) أو أن الشهادة الرقمية (Certificate) غير معتمدة من vManage.

### 2. التحقق من حالة بروتوكول OMP (Control Plane)
على جهاز cEdge، استخدم الأمر:
```text
show sdwan omp summary
```
**النتيجة المتوقعة:** يجب أن تظهر جلسة OMP مع vSmart بحالة `Up`. هذا يؤكد أن نفق التحكم المشفر بـ DTLS/TLS قد تم إنشاؤه بنجاح، وأن vSmart يقوم بتوزيع مسارات التراكب (Overlay Routes).

### 3. التحقق من أنفاق البيانات والتشفير (Data Plane & Security)
للتحقق من أنفاق IPsec بين cEdge1 و cEdge2:
```text
show sdwan security-info
```
أو للتحقق من حالة النفق:
```text
show sdwan tloc summary
```
**النتيجة المتوقعة:** يجب أن تظهر TLOCs (Transport Locators) للفروع الأخرى بحالة `Active`. هذا يثبت أن بروتوكول IPsec يقوم بتشفير حركة مرور البيانات الفعلية (Data Traffic) عبر طبقة التراكب (Overlay Plane).

### 4. التحقق من تبادل المسارات (Forwarding Plane)
للتحقق من المسارات المستلمة من الفروع الأخرى:
```text
show sdwan route omp
```
**النتيجة المتوقعة:** ستظهر مسارات الشبكات المحلية (LAN) الخاصة بالفرع الآخر، مما يدل على أن Forwarding Plane جاهز لتوجيه الحزم (Packets) بناءً على سياسات التوجيه (Forwarding Policies).

---

## خلاصة التدريب
من خلال هذا التطبيق العملي في CML، تم تطبيق الفصل الكامل بين مستويات الشبكة:
* تم استخدام **Transport Plane** (شبكة CML السحابية) كبنية تحتية فيزيائية.
* تم استخدام **vBond** لضمان الأمان الهوياتي (Identity Security) والمصادقة الأولية.
* تم استخدام **vSmart** لتبادل معلومات التوجيه عبر بروتوكول OMP (Control Plane).
* تم استخدام **vManage** للإدارة المركزية (Management Plane).
* تم استخدام **cEdge** لإنشاء أنفاق IPsec وتوجيه البيانات الفعلية (Data/Forwarding Plane)، مما يوضح كيف تحل تقنية SD-WAN مشاكل الشبكات التقليدية وتوفر مرونة وأماناً عاليين.