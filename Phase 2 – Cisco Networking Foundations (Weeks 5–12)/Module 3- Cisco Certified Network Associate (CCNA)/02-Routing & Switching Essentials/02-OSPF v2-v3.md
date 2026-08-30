# فهم OSPFv2 و OSPFv3 (Open Shortest Path First)

## 1. ما هو OSPF؟

هو بروتوكول (أو نظام ذكي) يركب على أجهزة الراوتر في الشركات والشبكات الكبيرة.

كلمة **Link-state** (حالة الوصلة) تعني أن هذا النظام لا يوجه البيانات بشكل عشوائي، بل يقوم **برسم خريطة كاملة وحقيقية للشبكة** بكل أجهزتها والمسارات المتاحة فيها. كل راوتر في الشبكة يملك نسخة من هذه الخريطة، وبالتالي "يرى" الشبكة بالكامل أمامه.

## 2. ما هي وظيفته؟

وظيفته الأساسية هي: **توصيل البيانات من نقطة (أ) إلى نقطة (ب) بأسرع وأقصر طريق ممكن.**

كيف يفعل ذلك؟

- **حساب التكلفة (Cost):** الـ OSPF ذكي، فهو ينظر إلى سرعة الكابلات (Bandwidth). الكابل السريع (مثل الفايبر) يعطيه "تكلفة قليلية"، والكابل البطيء يعطيه "تكلفة عالية". تماماً مثل "جي بي إس" عندما يختار لك طريقاً سريعاً ومفتوحاً بدلاً من طريق مزدحم ومكسر.
    
- **التعامل مع الطوارئ:** لو انقطع كابل في الشبكة فجأة، يلاحظ الـ OSPF ذلك خلال ثوانٍ، ويعيد رسم الخريطة، ويبلغ باقي الراوترات فوراً لتغيير مسار البيانات إلى طريق بديل.
    

## 3. كيف يُستخدم؟

يُستخدم عن طريق تفعيله داخل الراوترات لكي تبدأ بالكلام مع بعضها وتبادل الخرائط. وهنا ينقسم إلى جيلين (حسب نوع عناوين الـ IP المستخدمة):

### أ. الجيل القديم (OSPFv2)

- **مخصص لـ:** شبكات عناوين **IPv4** التقليدية (مثل `192.168.1.1`).
    
- **طريقة تشغيله:** تدخل لبرمجة الراوتر وتكتب أمر اسمه `network` وتحدد له أرقام الشبكات المحيطة بك ليقوم بالإعلان عنها لجيرانه.
    

### ب. الجيل الحديث (OSPFv3)

- **مخصص لـ:** شبكات عناوين **IPv6** الحديثة والطويلة جداً.
    
- **طريقة تشغيله:** هنا استغنينا عن أمر `network`. بدلاً من ذلك، تدخل مباشرة على "المنفذ" أو الفتحة (Interface) في الراوتر وتقول له: _"يا منفذ، اشتغل فوراً بنظام OSPFv3"_.
    
- **ميزته الذكية:** الراوترات في هذا الجيل تتحدث مع جيرانها باستخدام عناوين محلية تلقائية تسمى (Link-Local) تبدأ دائماً بـ `FE80::` دون الحاجة لضبط عناوين عالمية معقدة.
    

**باختصار شديد:** الـ OSPF هو مهندس الطرق الذكي في الشبكة، v2 منه يخدم سيارات الـ IPv4، و v3 يخدم سيارات الـ IPv6!

## 4. المفاهيم الأساسية (Key Concepts)

قبل الدخول في الاختلافات، يجب تثبيت المفاهيم التالية:

*   **(Metric):**
   يعتمد OSPF على قيمة التكلفة (Cost) لتحديد أفضل مسار (Best Path). يتم حساب التكلفة عكسياً بناءً على عرض النطاق الترددي (Bandwidth).
*   **(Area):**
   يتم تقسيم الشبكة الكبيرة إلى مناطق (Areas) لتقليل حجم جدول التوجيه (Routing Table) وتقليل الحمل على المعالج. المنطقة صفر (Area 0) تسمى (Backbone Area) ويجب أن تكون موجودة في الشبكة المتعددة المناطق.
*   **(DR/BDR):**
   في الشبكات متعددة الوصول (Multi-access networks) مثل الإيثرنت، يتم اختيار (Designated Router) و (Backup Designated Router) لتقليل عدد الاتصالات (Adjacency) بين الراوترات.
*   **(Router ID):
  ** هو رقم فريد (32-bit) يميز الراوتر داخل عملية (OSPF Process)، يشبه عنوان (IP Address) لكنه لا يرتبط بواجهة فيزيائية بالضرورة.

## 5. الاختلافات الجوهرية بين OSPFv2 و OSPFv3

هذا هو الجزء الأهم في منهج CCNA الخاص بالتحديثات:

### أ. بروتوكول الشبكة المستهدف (Target Protocol)
*   **OSPFv2:** مصمم خصيصاً للعمل مع عناوين (IPv4).
*   **OSPFv3:** مصمم أصلاً للعمل مع عناوين (IPv6).

### ب. التكوين على الواجهات (Interface Configuration)
*   **في OSPFv2:** يتم تفعيل البروتوكول باستخدام الأمر `network` في وضع التوجيه (Routing Configuration Mode)، حيث نحدد شبكة الـ IP الخاصة بالواجهة.
*   **في OSPFv3:** لا نستخدم الأمر `network`. بدلاً من ذلك، يتم تفعيل البروتوكول مباشرة داخل وضع الواجهة (Interface Configuration Mode) باستخدام الأمر `ipv6 ospf`.

### ج. الاعتماد على عناوين الواجهة (Dependency on Interface IP)
*   **في OSPFv2:** يعتمد البروتوكول على عنوان الـ IP الخاص بالواجهة للتواصل.
*   **في OSPFv3:** لا يحتاج البروتوكول إلى عنوان (IPv6 Global Unicast Address) على الواجهة ليعمل. يعتمد OSPFv3 على عنوان (Link-Local Address) المُولد تلقائياً على كل واجهة (عادة يبدأ بـ `FE80::`) للتواصل مع الجيران (Neighbors) وبناء جوار (Adjacency).

### د. المصادقة (Authentication)
*   **في OSPFv2:** تعتمد المصادقة على حقول خاصة في رأس البروتوكول (Protocol Header).
*   **في OSPFv3:** يعتمد على آلية (IPsec) أو (Authentication Header - AH) المتوفرة في بروتوكول (IPv6).

## 6. التكوين العملي (Practical Configuration)

### أولاً: تكوين OSPFv2 (للشبكات IPv4)

```bash
Router(config)# router ospf 10
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
```
*ملاحظة:* الأمر `network` يستخدم (Wildcard Mask) لتحديد الواجهات التي ستشارك في بروتوكول الـ OSPF.

### ثانياً: تكوين OSPFv3 (للشبكات IPv6)

**الخطوة 1: تفعيل الـ IPv6 Routing**
```bash
Router(config)# ipv6 unicast-routing
```

**الخطوة 2: إنشاء عملية OSPF**
```bash
Router(config)# ipv6 router ospf 10
Router(config-rtr)# router-id 1.1.1.1
```
*ملاحظة:* يجب تحديد (Router ID) يدوياً في OSPFv3 غالباً لأنه لا يعتمد على عناوين IPv4 للواجهة.

**الخطوة 3: تفعيل البروتوكول على الواجهة**
```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ipv6 ospf 10 area 0
```

## 7. أوامر التحقق (Verification Commands)

يجب على الطالب معرفة الأوامر التالية للتحقق من حالة البروتوكول:

1.  **التحقق من جدول التوجيه:**
    *   `show ip route ospf` (للبحث عن مسارات v2).
    *   `show ipv6 route ospf` (للبحث عن مسارات v3).

2.  **التحقق من حالة الجيران (Neighbors):**
    *   `show ip ospf neighbor`
    *   `show ipv6 ospf neighbor`
    *   *ملاحظة:* في OSPFv3، سترى عنوان الـ (Link-Local Address) للجيران في قائمة الجيران، وليس عناوين الـ IPv6 العالمية.

3.  **التحقق من تفاصيل الـ LSA:**
    *   `show ip ospf database`
    *   `show ipv6 ospf database`

## 8. ملخص سريع للمقارنة (Quick Summary)

| وجه المقارنة | OSPFv2 | OSPFv3 |
| :--- | :--- | :--- |
| **بروتوكول الشبكة** | IPv4 | IPv6 |
| **طريقة التفعيل** | عبر الأمر `network` | عبر الواجهة `ipv6 ospf` |
| **العنوان المستخدم للجيران** | عنوان الواجهة (Interface IP) | عنوان (Link-Local Address) |
| **المصادقة** | داخل رأس البروتوكول | عبر (IPsec) |

---
**نصيحة للامتحان (Exam Tip):** في أسئلة CCNA، انتبه جيداً إلى السؤال. إذا كان يتحدث عن بروتوكول التوجيه لشبكة IPv6، فهو حتماً (OSPFv3). وإذا سُئلت عن الأمر المسؤول عن تفعيل OSPF على واجهة محددة في بيئة IPv6، فالإجابة هي أمر موجود داخل interface mode وليس وضع التوجيه العام.

---
---
# التدريب العملي على الدرس
![[Pasted image 20260620215232.png]]


## 1. توزيع الـ IP على أجهزة الكمبيوتر (مهم جداً أولاً)

ادخل على كل جهاز كمبيوتر من واجهة السطح المكتبي (`Desktop` -> `IP Configuration`) واكتب التالي:

- **جهاز PC0:**
    
    - IP Address: `192.168.1.10`
        
    - Subnet Mask: `255.255.255.0`
        
    - Default Gateway: `192.168.1.1`
        
- **جهاز PC1:**
    
    - IP Address: `192.168.2.10`
        
    - Subnet Mask: `255.255.255.0`
        
    - Default Gateway: `192.168.2.1`
        

## 2. الإعدادات الكاملة للراوترات (CLI)

###  الراوتر العلوي (R1):

_المنفذ المتجه لليسار هو `Gig0/0` والمنفذ المتجه لليمين هو `Gig0/1`._



```
Router> enable
Router# configure terminal

! ضبط المنفذ المتصل بـ R2 (الجهة اليسرى)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 10.1.1.1 255.255.255.252
Router(config-if)# no shutdown

! ضبط المنفذ المتصل بـ R3 (الجهة اليمنى)
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 10.2.2.1 255.255.255.252
Router(config-if)# no shutdown

! تفعيل بروتوكول الـ OSPF للإعلان عن الشبكتين
Router(config)# router ospf 10
Router(config-router)# router-id 1.1.1.1
Router(config-router)# network 10.1.1.0 0.0.0.3 area 0
Router(config-router)# network 10.2.2.0 0.0.0.3 area 0
```

### الراوتر الأيسر (R2):

_المنفذ المتجه لأعلى هو `Gig0/0` والأفقي لليمين هو `Gig0/1` والنازل للسويتش هو `Gig0/2`._



```
Router> enable
Router# configure terminal

! ضبط المنفذ المتصل بـ R1 (الأعلى)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 10.1.1.2 255.255.255.252
Router(config-if)# no shutdown

! ضبط المنفذ المتصل بـ R3 (الأفقي لليمين)
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 10.3.3.1 255.255.255.252
Router(config-if)# no shutdown

! ضبط المنفذ المتصل بالسويتش SW1 (الأسفل)
Router(config)# interface gigabitEthernet 0/2
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown

! تفعيل بروتوكول الـ OSPF للإعلان عن الشبكات الثلاث
Router(config)# router ospf 10
Router(config-router)# router-id 2.2.2.2
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 10.1.1.0 0.0.0.3 area 0
Router(config-router)# network 10.3.3.0 0.0.0.3 area 0
```

###  الراوتر الأيمن (R3):

_المنفذ المتجه لأعلى هو `Gig0/0` والأفقي لليسار هو `Gig0/1` والنازل للسويتش هو `Gig0/2`._

Plaintext

```
Router> enable
Router# configure terminal

! ضبط المنفذ المتصل بـ R1 (الأعلى)
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 10.2.2.2 255.255.255.252
Router(config-if)# no shutdown

! ضبط المنفذ المتصل بـ R2 (الأفقي لليسار)
Router(config)# interface gigabitEthernet 0/1
Router(config-if)# ip address 10.3.3.2 255.255.255.252
Router(config-if)# no shutdown

! ضبط المنفذ المتصل بالسويتش SW2 (الأسفل)
Router(config)# interface gigabitEthernet 0/2
Router(config-if)# ip address 192.168.2.1 255.255.255.0
Router(config-if)# no shutdown

! تفعيل بروتوكول الـ OSPF للإعلان عن الشبكات الثلاث
Router(config)# router ospf 10
Router(config-router)# router-id 3.3.3.3
Router(config-router)# network 192.168.2.0 0.0.0.255 area 0
Router(config-router)# network 10.2.2.0 0.0.0.3 area 0
Router(config-router)# network 10.3.3.0 0.0.0.3 area 0
```

### الخطوة الأخيرة للتحقق:

بعد إدخال الأوامر السابقة، ستتحول كافة المثلثات الحمراء في رسمتك إلى **اللون الأخضر**.

انتظر 20 ثانية ثم افتح **PC0** وقم بعمل **Ping** على الـ IP الخاص بـ **PC1** (`192.168.2.10`).

ستجد أول قفزة قد تفشل (بسبب الـ ARP) ثم يعمل الـ Ping بنجاح تام