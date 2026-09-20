# التدريب العملي: تطبيق جودة الخدمة (QoS) وإدارة الازدحام في CML

## 1. أهداف التدريب
- فهم كيفية تصنيف الحزم (Classification) ووضع العلامات (Marking).
- تطبيق تقنية **LLQ** لإعطاء أولوية قصوى لحركة الصوت.
- تطبيق تقنية **CBWFQ** لضمان حد أدنى من النطاق لبيانات الويب.
- محاكاة رابط WAN بطيء باستخدام **Shaping** لإجبار الراوتر على استخدام طوابير QoS.

## 2. مخطط الطوبولوجيا (Topology)

```mermaid
graph LR
    subgraph LAN_Side [الشبكة الداخلية LAN]
        PC1[PC1 - Data Host<br>Alpine Linux] --- SW1[SW1 - L2 Switch<br>IOSvL2]
        Phone1[IP Phone - Voice<br>Alpine Linux] --- SW1
    end
    
    SW1 ---|Gig0/0| R1[R1 - HQ Router<br>CSR1000v / IOSv]
    
    subgraph WAN_Edge [محاكاة رابط WAN بطيء]
        R1 ---|Gig0/1<br>Shaped to 2Mbps| Server1[Server1 - Web Server<br>Alpine Linux]
    end

    style R1 fill:#003366,stroke:#fff,stroke-width:2px,color:#fff
    style SW1 fill:#1f4e79,stroke:#fff,stroke-width:1px,color:#fff
    style PC1 fill:#2b579a,stroke:#fff,stroke-width:1px,color:#fff
    style Phone1 fill:#2b579a,stroke:#fff,stroke-width:1px,color:#fff
    style Server1 fill:#2b579a,stroke:#fff,stroke-width:1px,color:#fff
```

---
## الأجهزة والموديلات في CML

| الجهاز      | النوع في CML (Node Type)    | الوظيفـة                                       |
| ----------- | --------------------------- | ---------------------------------------------- |
| **R1**      | `csr1000v` أو `iosv`        | الراوتر الرئيسي (HQ-Router) لتطبيق سياسات MQC. |
| **SW1**     | `iosvl2`                    | سويتش الطبقة الثانية لربط الأجهزة الطرفية.     |
| **PC1**     | `alpine-linux` أو `desktop` | جهاز طرفي لتوليد حركة بيانات (HTTP/UDP).       |
| **Phone1**  | `alpine-linux`              | جهاز طرفي لتوليد حركة صوتية (UDP - DSCP EF).   |
| **Server1** | `alpine-linux`              | خادم الويب/الوجهة النهائية.                    |

---
## 3. الإعدادات الكاملة لكل جهاز (Node Configurations)

### الجهاز الأول: الراوتر الرئيسي (HQ-Router - R1)
**النوع في CML:** `csr1000v` أو `iosv`

#### شرح الإعدادات وأهميتها:
1. **إعداد الواجهات (Interfaces):** يتم تفعيل المنافذ وتوزيع الـ IPs لربط الشبكة المحلية (LAN) بشبكة الخادم (WAN).
2. **التصنيف (Classification - Class-Maps):** 
   - نعرف فئة `VOICE-CLASS` للبحث عن الحزم الموسومة بـ `DSCP EF` (وهي حزم الصوت).
   - نعرف فئة `DATA-CLASS` باستخدام `ACL` للبحث عن حزم الويب (HTTP/HTTPS/iperf). *ملاحظة: نستخدم ACL بدلاً من NBAR لضمان التوافق مع جميع صور CML.*
3. **تحديد السياسة (Policy-Map):**
   - `priority 512`: تقنية **LLQ**، تعطي الصوت أولوية قصوى وسعة 512Kbps.
   - `bandwidth percent 30`: تقنية **CBWFQ**، تضمن للبيانات 30% من النطاق وتعيد وسمها بـ `AF21`.
4. **تطبيق السياسة والازدحام (Interface & Shaping):** 
   - `service-policy output`: تطبق السياسة على حركة الخروج.
   - **`shape average 2000000`**: **(خطوة حاسمة)** في بيئة CML، المنافذ الافتراضية سريعة جداً (Gigabit). لن يحدث ازدحام (Congestion) ولن تعمل طوابير QoS إلا إذا قمنا بتحديد سرعة المنفذ (2 Mbps) لمحاكاة رابط WAN حقيقي.

#### الإعداد الكامل (انسخ والصق في Console الخاص بـ R1):
```cisco
enable
configure terminal
hostname HQ-Router

! --- 1. إعداد الواجهات الأساسية ---
interface GigabitEthernet1
 description Link-to-Server1-WAN
 ip address 10.0.0.1 255.255.255.0
 no shutdown
!
interface GigabitEthernet2
 description Link-to-SW1-LAN
 ip address 192.168.1.1 255.255.255.0
 no shutdown

! --- 2. التصنيف (Classification) ---
class-map match-all VOICE-CLASS
 match ip dscp ef
!
ip access-list extended DATA-TRAFFIC
 permit tcp any any eq 80
 permit tcp any any eq 443
 permit tcp any any eq 5201
!
class-map match-any DATA-CLASS
 match access-group name DATA-TRAFFIC

! --- 3. تحديد السياسة (Policy-Map) ---
policy-map QOS-EDGE-POLICY
 class VOICE-CLASS
  priority 512
 class DATA-CLASS
  bandwidth percent 30
  set dscp af21
 class class-default
  fair-queue

! --- 4. التطبيق ومحاكاة الازدحام (Application & Shaping) ---
interface GigabitEthernet2
 service-policy output QOS-EDGE-POLICY
 shape average 2000000

end
write memory
```

هذا الكود هو **القلب النابض** للتدريب العملي. هو يطبق نموذج **MQC (Modular QoS CLI)** الخاص بـ Cisco، والذي يعتمد على فلسفة بسيطة من 3 خطوات:
 **1. حدد من هو (Classify) ➔ 2. حدد ماذا تفعل به (Policy) ➔ 3. طبق ذلك على المنفذ (Apply).**

إليك شرح تفصيلي ومبسط لكل قسم، ولماذا كتبنا هذا الكود بالضبط:

---

### 1. الإعدادات الأساسية والواجهات (Basic Setup & Interfaces)
```cisco
enable
configure terminal
hostname HQ-Router

interface GigabitEthernet1
 description Link-to-Server1-WAN
 ip address 10.0.0.1 255.255.255.0
 no shutdown
!
interface GigabitEthernet2
 description Link-to-SW1-LAN
 ip address 192.168.1.1 255.255.255.0
 no shutdown
```
* **الدور:** هنا نقوم بتفعيل الراوتر، تغيير اسمه، وتوزيع عناوين IP على المنافذ لربط الشبكة المحلية (LAN) بشبكة الخادم (WAN).
* **ملاحظة بسيطة:** أسماء المنافذ هنا (`Gig1` و `Gig2`) تختلف قليلاً عن التدريب السابق (`Gig0/0`) وهذا طبيعي جداً في بيئة CML ويختلف حسب صورة الراوتر المستخدمة (مثل CSR1000v).

---

### 2. التصنيف (Classification) - "من هي هذه الحزمة؟"
هنا نلعب دور "شرطي المرور" الذي يميز بين السيارات (الحزم) قبل أن تدخل الطريق.

```cisco
class-map match-all VOICE-CLASS
 match ip dscp ef
```
* **الدور:** ننشئ فئة اسمها `VOICE-CLASS`.
* **الشرط `match ip dscp ef`:** أخبرنا الراوتر: "أي حزمة تأتي ومكتوب في رأسها (Header) وسم **EF** (وهي قيمة DSCP 46 المخصصة للصوت)، ضعها في هذه الفئة".

```cisco
ip access-list extended DATA-TRAFFIC
 permit tcp any any eq 80
 permit tcp any any eq 443
 permit tcp any any eq 5201
!
class-map match-any DATA-CLASS
 match access-group name DATA-TRAFFIC
```
* **الدور:** ننشئ فئة للبيانات العادية (الويب والاختبارات).
* **لماذا استخدمنا ACL؟** في بيئة CML الحقيقية، الأمر `match protocol http` يحتاج لمحرك معقد اسمه NBAR. لضمان عمل التدريب بنسبة 100% وبدون أخطاء، استخدمنا **ACL** لفتح المنافذ يدوياً:
  * `80`: بروتوكول HTTP (تصفح عادي).
  * `443`: بروتوكول HTTPS (تصفح آمن).
  * `5201`: منفذ أداة **iperf3** التي نستخدمها في التدريب لتوليد الضغط.
* **النتيجة:** أي حزمة تذهب لهذه المنافذ ستُجمع في فئة `DATA-CLASS`.

---

### 3. تحديد السياسة (Policy-Map) - "ماذا سنفعل بهذه الحزم؟"
هنا نحدد "قوانين الطريق" لكل فئة تم تصنيفها في الخطوة السابقة.

```cisco
policy-map QOS-EDGE-POLICY
```
* **الدور:** حاوية السياسة الرئيسية التي ستُطبق على المنفذ.

```cisco
 class VOICE-CLASS
  priority 512
```
* **الدور (تقنية LLQ):** حزم الصوت (`VOICE-CLASS`) هي الـ VIP.
* **`priority 512`:** ننشئ لها طابوراً ذا **أولوية قصوى (Strict Priority Queue)**. هذه الحزم تُرسل فوراً قبل أي شيء آخر.
* **لماذا حددنا 512؟** لمنع ظاهرة **Starvation (المجاعة)**. لو لم نحدد رقماً، واستمرت حزم الصوت بالتدفق بلا توقف، ستستنزف الرابط بالكامل (100%) وتمنع حزم البيانات من المرور أبداً. الرقم 512 يحمي باقي الشبكة.

```cisco
 class DATA-CLASS
  bandwidth percent 30
  set dscp af21
```
* **الدور (تقنية CBWFQ + Marking):** حزم البيانات (`DATA-CLASS`) ليست VIP، لكنها مهمة.
* **`bandwidth percent 30`:** نضمن لها **حد أدنى** من النطاق الترددي بنسبة 30% **فقط في حالة حدوث ازدحام (Congestion)**. (إذا كان الرابط فارغاً، يمكنها استخدام أكثر من 30%).
* **`set dscp af21`:** هذه عملية **وسم (Marking)**. الراوتر يغير وسم الحزمة إلى `AF21` قبل إرسالها. **لماذا؟** لكي يرى الراوتر *التالي* في الشبكة أن هذه الحزمة مهمة (أفضل من البيانات العادية) ويعاملها بنفس الاحترام (End-to-End QoS).

```cisco
 class class-default
  fair-queue
```
* **الدور:** هذه الفئة الافتراضية (Catch-all).
* **`fair-queue`:** أي حزمة لم تطابق "الصوت" ولم تطابق "البيانات" (مثل الـ ICMP/Ping أو أي بروتوكول آخر)، ستُوضع هنا. الأمر `fair-queue` يضمن توزيع النطاق المتبقي بشكل **عادل** بين جميع المحادثات، لكي لا يقوم مستخدم واحد بتحميل ملف ضخم ويحتكر الرابط بالكامل.

---

### 4. التطبيق ومحاكاة الازدحام (Application & Shaping) - "الخطوة الأذكى في CML"
هنا نطبق القوانين على المنفذ، ونقوم بـ "خدعة" ذكية لجعل بيئة CML تتصرف كالعالم الحقيقي.

```cisco
interface GigabitEthernet2
 service-policy output QOS-EDGE-POLICY
 shape average 2000000
```
* **`service-policy output`:** نخبر الراوتر: "طبق سياسة `QOS-EDGE-POLICY` على كل الحزمة التي **تخرج (output)** من هذا المنفذ". *(ملاحظة: في هذا الكود طُبقت على المنفذ المتجه للشبكة المحلية Gig2، وفي العادة نطبقها على منفذ الـ WAN لمحاكاة رابط مزود الخدمة، لكن المبدأ التقني واحد تماماً).*
* **`shape average 2000000`:** **(هذه هي الخطوة الحاسمة في CML)**.
  * **المشكلة:** منافذ الراوتر في CML سرعتها 1 Gigabit (1000 Mbps). عندما يرسل PC1 بيانات بسرعة 5Mbps، المنفذ يرسلها في أجزاء من الثانية. لا يحدث ازدحام (Congestion)، ولا تمتلئ الذاكرة المؤقتة (Buffer)، وبالتالي **طوابير QoS لن تُفعّل أبداً** وستظهر الإحصائيات فارغة!
  * **الحل (Shaping):** نخبر الراوتر: "تصرف كأن سرعة هذا المنفذ هي **2 Mbps** فقط".
  * **النتيجة:** عندما يرسل PC1 بسرعة 5Mbps، والمنفذ يسمح فقط بـ 2Mbps، **سيحدث ازدحام حقيقي (Congestion)**. ستتراكم الحزم في الذاكرة المؤقتة (Buffer)، وهنا **فقط** ستستيقظ طوابير QoS (LLQ و CBWFQ) وتبدأ بعملها الفعلي في ترتيب الحزم وإسقاط الزائد منها.

```cisco
end
write memory
```
* **الدور:** الخروج من وضع الإعدادات وحفظ التغييرات في الذاكرة (NVRAM) لكي لا تضيع عند إعادة تشغيل الراوتر.

---

### السيناريو الكامل:
1. الحزم تدخل الراوتر.
2. الراوتر ينظر في رؤوسها (**Classification**): يجد حزم صوت (EF) وحزم ويب (Ports 80/443/5201).
3. الحزم تصل لمنفذ الخروج المخنوق بـ 2Mbps (**Shaping**) فيحدث زحام.
4. الراوتر يفتح طوابيره (**Policy**):
   * يضع الصوت في طابور **LLQ** ويرسله فوراً (بدون فقدان).
   * يضع الويب في طابور **CBWFQ** ويضمن له 30% ويغير وسمه لـ **AF21**.
   * يضع الباقي في طابور **Fair-Queue** ويوزعهم بالعدل.


---

### الجهاز الثاني: السويتش (SW1)
**النوع في CML:** `iosvl2`

#### شرح الإعدادات وأهميته:
- **الدور:** يعمل كجهاز تجميع (Aggregation) من الطبقة الثانية (Layer 2).
- **الأهمية:** يقوم بربط الأجهزة الطرفية (PC1 و Phone1) بالراوتر R1. الإعدادات هنا بسيطة (Access Mode) لأننا نطبق سياسات QoS على الراوتر (Layer 3 Edge) وليس على السويتش في هذا السيناريو.

#### الإعداد الكامل (انسخ والصق في Console الخاص بـ SW1):
```cisco
enable
configure terminal
hostname SW1

interface GigabitEthernet0/0
 description Link-to-R1
 switchport mode access
 no shutdown
!
interface GigabitEthernet0/1
 description Link-to-PC1-Data
 switchport mode access
 no shutdown
!
interface GigabitEthernet0/2
 description Link-to-Phone1-Voice
 switchport mode access
 no shutdown

end
write memory
```

---

### الجهاز الثالث: خادم الاختبار (Server1)
**النوع في CML:** `alpine-linux`

#### شرح الإعدادات وأهميتها:
- **الدور:** يمثل الخادم النهائي (Destination) الذي يستقبل حركة البيانات والصوت.
- **الأهمية:** نقوم بتثبيته وتشغيل أداة `iperf3` في وضع الخادم (`-s`) وبشكل Daemon (`-D`) ليعمل في الخلفية، مما يسمح باستقبال الاتصالات من PC1 و Phone1 في نفس الوقت لقياس الأداء.

#### الإعداد الكامل (انسخ والصق في Console الخاص بـ Server1):
```bash
# الدخول لوضع Root
su root

# 1. إعداد عنوان IP والمسار الافتراضي
ip addr add 10.0.0.100/24 dev eth0
ip link set eth0 up
ip route add default via 10.0.0.1

# 2. تثبيت وتشغيل أداة iperf3 في الخلفية
apk add iperf3
iperf3 -s -D

# للتأكد من أن الخادم يعمل ويستمع:
netstat -tulnp | grep iperf
```

---

###  الجهاز الرابع: جهاز البيانات (PC1 - Data Host)
**النوع في CML:** `alpine-linux`

####  شرح الإعدادات وأهميتها:
- **الدور:** يمثل مستخدم عادي يقوم بتحميل ملفات أو تصفح الويب.
- **الأهمية:** سنستخدم `iperf3` لإرسال حركة **TCP** بسرعة `5Mbps`. بما أن الرابط بين R1 و Server1 محدود بـ `2Mbps` (بفضل الـ Shaping)، فإن هذا سيتسبب في **ازدحام (Congestion)** حقيقي، مما يجبر الراوتر على تفعيل طوابير QoS (CBWFQ) وإسقاط بعض حزم البيانات أو تأخيرها.

####  الإعداد الكامل (انسخ والصق في Console الخاص بـ PC1):
```bash
# الدخول لوضع Root
su root

# 1. إعداد عنوان IP والمسار الافتراضي
ip addr add 192.168.1.10/24 dev eth0
ip link set eth0 up
ip route add default via 192.168.1.1

# 2. اختبار الاتصال بالسيرفر
ping -c 4 10.0.0.100

# 3. توليد حركة بيانات كثيفة (TCP) لإغراق الرابط
apk add iperf3
# إرسال حركة TCP بسرعة 5Mbps لمدة 60 ثانية (المنفذ 5201 هو منفذ iperf3 الافتراضي)
iperf3 -c 10.0.0.100 -p 5201 -b 5M -t 60
```

---

### الجهاز الخامس: جهاز الصوت (Phone1 - Voice Host)
**النوع في CML:** `alpine-linux`

#### شرح الإعدادات وأهميتها:
- **الدور:** يمثل هاتف IP (VoIP) يرسل مكالمة صوتية.
- **الأهمية:** سنستخدم `iperf3` لإرسال حركة **UDP** بحجم حزمة `160 byte` (محاكاة دقيقة لحزم الصوت G.711). الأهم هو استخدام الخيار `--dscp 46` لوسم الحزم بـ **EF (Expedited Forwarding)**. هذا سيثبت أن طابور **LLQ** في الراوتر سيتعرف على هذا الوسم ويمنحه أولوية قصوى، مما يحميه من الـ Drops التي ستحدث لحزم البيانات.

#### الإعداد الكامل (انسخ والصق في Console الخاص بـ Phone1):
```bash
# الدخول لوضع Root
su root

# 1. إعداد عنوان IP والمسار الافتراضي
ip addr add 192.168.1.20/24 dev eth0
ip link set eth0 up
ip route add default via 192.168.1.1

# 2. اختبار الاتصال بالسيرفر
ping -c 4 10.0.0.100

# 3. توليد حركة صوتية (UDP) مع وسم DSCP EF
apk add iperf3
# إرسال حركة UDP بحجم 160 byte مع وسم DSCP 46 (EF)
iperf3 -c 10.0.0.100 -u -b 1M -l 160 --dscp 46 -t 60
```

---

## 4. خطوات التنفيذ والمراقبة (Execution & Verification)

### أ. ترتيب التشغيل وتوليد الازدحام
1. شغّل الأجهزة بالترتيب: **Server1** ➔ **SW1** ➔ **R1** ➔ **PC1 & Phone1**.
2. انتظر حتى تكتمل أوامر `ping` للتأكد من وصول الـ Routing.
3. **الخطوة الأهم:** قم بفتح Console لـ **PC1** و **Phone1** في نفس الوقت (استخدم Tabs في CML) وقم بتشغيل أوامر `iperf3` فيهما **بالتزامن**.

### ب. مراقبة النتائج على الراوتر (R1)
اذهب إلى Console الخاص بـ **HQ-Router** ونفذ الأمر التالي:
```cisco
show policy-map interface GigabitEthernet0/1
```

#### ماذا ستلاحظ في المخرجات؟
1. **صوت (VOICE-CLASS):** ستجد أن الحزم مرت عبر `Priority Queue`. ستلاحظ أن عدد الحزم (Packets) يزداد، ولكن **عدد الحزم المرفوضة (Drops) يساوي 0**، حتى مع امتلاء الرابط. هذا يثبت نجاح تقنية **LLQ**.
2. **بيانات (DATA-CLASS):** ستجد أن الحزم مرت عبر `CBWFQ`. بسبب أن PC1 يرسل بـ 5Mbps والنطاق المضمون هو 30% من 2Mbps، ستلاحظ وجود **Drops** أو تأخير في هذا الطابور.
3. **حالة المنفذ:** إذا نفذت الأمر `show interfaces GigabitEthernet0/1`، ستجد سطر `Output queue: size/total drops` يوضح أن هناك حزم تم إسقاطها بسبب الـ Shaping، ولكن حزم الصوت كانت محمية بفضل الـ Marking والـ Queuing.