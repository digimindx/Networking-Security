# الدليل الشامل: التوجيه المتقدم والأمن في Packet Tracer
**(OSPF + EIGRP | نظري + عملي + استكشاف أخطاء)**

## مقدمة: لماذا لا نستخدم SHA في Packet Tracer؟
قبل البدء، يجب توضيح نقطة جوهرية وردت في المحادثة والملف المرفق:
*   **نظرياً:** توصي شهادة ENCOR وملفك باستخدام **SHA** أو **TCP-AO** لأنها الآمنة والمعتمدة في IOS XE.
*   **عملياً في Packet Tracer:** البرنامج **لا يدعم SHA بشكل كامل**. لذلك، نستخدم **MD5** كـ "بديل تعليمي" لضمان نجاح التمرين ورؤية حالة الجوار (FULL)، مع التنويه دائماً أن البيئة الحقيقية تتطلب محاكيات متقدمة مثل EVE-NG أو GNS3.

---

## الوحدة الأولى: OSPF Multi-Area مع الأمن والتصفية

### الهدف التعليمي
بناء شبكة OSPF متعددة المناطق، تأمينها بالمصادقة، وتطبيق تصفية مسارات انتقائية (وليس عزلاً كاملاً).

### الطوبولوجيا والأجهزة
| الجهاز | النوع | الوظيفة | العناوين |
| :--- | :--- | :--- | :--- |
| **R1** | Router 4321 | Backbone (Area 0) + ABR | Gi0/0/0: `10.0.12.1/30` <br> Lo0: `1.1.1.1/32` |
| **R2** | Router 4321 | Area 1 | Gi0/0/0: `10.0.12.2/30` <br> Gi0/0/1: `192.168.10.1/24` |
| **SW1** | Switch 2960 | ربط المستخدمين | - |
| **PC1** | PC | جهاز اختبار | IP: `192.168.10.10/24` <br> GW: `192.168.10.1` |

### خطوات التنفيذ المبسطة

#### أ. الإعداد الأساسي والواجهات
```bash
! --- على R1 ---
enable
configure terminal
hostname R1
interface GigabitEthernet0/0/0
 ip address 10.0.12.1 255.255.255.252
 no shutdown
interface Loopback0
 ip address 1.1.1.1 255.255.255.255  ! واجهة مستقرة للـ Router-ID والاختبار
 exit

! --- على R2 ---
hostname R2
interface GigabitEthernet0/0/0
 ip address 10.0.12.2 255.255.255.252
 no shutdown
interface GigabitEthernet0/0/1
 ip address 192.168.10.1 255.255.255.0
 no shutdown
```

#### ب. تكوين OSPF مع مصادقة MD5
> **مفهوم مهم:** المصادقة هنا تؤمن *جلسة الجوار* لمنع راوترات غريبة من الانضمام، ولا تعزل الراوترات المعتمدة.

```bash
! --- على R1 ---
router ospf 1
 router-id 1.1.1.1                   ! معرف ثابت لا يتغير بتغير الواجهات
 network 10.0.12.0 0.0.0.3 area 0
 network 1.1.1.1 0.0.0.0 area 0
 area 0 authentication message-digest ! تفعيل MD5 للمنطقة 0
interface GigabitEthernet0/0/0
 ip ospf message-digest-key 1 md5 MySecurePass123 ! المفتاح المشترك

! --- على R2 ---
router ospf 1
 router-id 2.2.2.2
 network 10.0.12.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 1
 area 0 authentication message-digest
interface GigabitEthernet0/0/0
 ip ospf message-digest-key 1 md5 MySecurePass123 ! يجب أن يطابق R1 تماماً
```

#### ج. تصفية المسارات (Selective Filtering)
> **تصحيح مفاهيمي:** التصفية ≠ عزل. نحن نسمح بالاتصال لكن نمنع مساراً محدداً لأسباب أمنية.

```bash
! --- على R2 فقط ---
ip prefix-list BLOCK_LOOP seq 5 deny 1.1.1.1/32     ! منع مسار الـ Loopback
ip prefix-list BLOCK_LOOP seq 10 permit 0.0.0.0/0 le 32 ! السماح بباقي المسارات

router ospf 1
 distribute-list prefix BLOCK_LOOP in ! تطبيق القائمة على المسارات الواردة
```

### التحقق والفهم
| الاختبار       | الأمر                            | النتيجة             | الدلالة                                             |
| :------------- | :------------------------------- | :------------------ | :-------------------------------------------------- |
| Ping محلي      | `ping 10.0.12.1` (من R1)         | ناجح                | الواجهة نشطة؛ التصفية لا تؤثر على العناوين المحلية  |
| اتصال الجار    | `show ip ospf neighbor`          | State: FULL         | المصادقة تعمل بنجاح                                 |
| فعالية التصفية | `show ip route ospf` (على R2)    | `1.1.1.1` غير موجود | الـ Prefix-list منع دخول المسار لجدول التوجيه       |
| وجود في LSDB   | `show ip ospf database` (على R2) | `1.1.1.1` موجود     | المسار وصل لكن تم رفضه أمنياً (سلوك التصفية الصحيح) |

---

## الوحدة الثانية: EIGRP Named Mode مع Variance

### الهدف التعليمي
فهم خوارزمية DUAL، وتطبيق توزيع الحمل غير المتكافئ (Variance) مع تأمين الجوار بـ Keychain.

### الطوبولوجيا
*   **R1:** Hub رئيسي
*   **R2:** مسار أساسي (Bandwidth عالي)
*   **R3:** مسار احتياطي (Bandwidth منخفض عمداً لتجربة Variance)

### 📝 خطوات التنفيذ المبسطة

#### أ. التلاعب بالقياس (Metric Manipulation)
```bash
! --- على R1 ---
interface GigabitEthernet0/0/0       ! الرابط السريع (R2)
 bandwidth 100000                    ! 100 Mbps
 ip address 10.0.12.1 255.255.255.252
 no shutdown

interface GigabitEthernet0/0/1       ! الرابط البطيء (R3)
 bandwidth 1000                      ! 1 Mbps فقط لخلق فرق في القياس
 ip address 10.0.13.1 255.255.255.252
 no shutdown
```

#### ب. Keychain Authentication (البديل الآمن)
```bash
! --- على جميع الراوترات ---
key chain EIGRP_SECURE
 key 1
  key-string MySecretKey2026
  accept-lifetime 00:00:00 Jan 1 2026 infinite
  send-lifetime 00:00:00 Jan 1 2026 infinite
```

#### ج. EIGRP Named Mode + Variance
```bash
! --- على R1 ---
router eigrp LAB_EIGRP              ! الوضع الحديث (Named Mode)
 address-family ipv4 unicast autonomous-system 100
  topology base
   variance 3                       ! السماح بمسارات احتياطية بقياس ≤ 3× أفضل مسار
   
   af-interface GigabitEthernet0/0/0
    authentication mode md5 key-chain EIGRP_SECURE
   af-interface GigabitEthernet0/0/1
    authentication mode md5 key-chain EIGRP_SECURE
   
   network 10.0.12.0 0.0.0.3
   network 10.0.13.0 0.0.0.3
   passive-interface default        ! أمن: تعطيل EIGRP على كل الواجهات افتراضياً
   no passive-interface GigabitEthernet0/0/0
   no passive-interface GigabitEthernet0/0/1
```

### التحقق من DUAL و Variance
| الأمر | ماذا تبحث عنه؟ |
| :--- | :--- |
| `show ip eigrp topology` | المسار عبر R3 يجب أن يكون **Feasible Successor** (RD < FD الحالي) |
| `show ip route eigrp` | ظهور **مسارين** لنفس الشبكة بعد تفعيل Variance |
| `traceroute` من PC1 | توزع الحزم بين R2 و R3 بنسبة غير متساوية |

> **شرط Feasibility:** إذا لم يظهر R3 كمسار احتياطي رغم تفعيل Variance، فالسبب أن `RD الخاص بـ R3 ≥ FD الحالي`. هذا يعني أن DUAL يعتبر المسار غير آمن من الحلقات، ولن تستخدمه حتى مع Variance.

---

## ملخص المفاهيم الجوهرية (من الملف المرفق)

| المفهوم | OSPF | EIGRP |
| :--- | :--- | :--- |
| **الخوارزمية** | Dijkstra (Link-State) | DUAL (Advanced Distance Vector) |
| **الأمن في PT** | MD5 (بديل SHA) | Keychain MD5 |
| **توزيع الحمل** | Equal Cost فقط | Equal + Unequal (Variance) |
| **المناطق** | Area 0 إلزامية + Stub/NSSA | لا يوجد مناطق (Flat) |
| **التصفية** | Prefix-list + Distribute-list | Prefix-list + Distribute-list |
| **الاستقرار** | Router-ID ثابت | Feasible Successor جاهز |

### نصائح نهائية للتطبيق
1.  **ابدأ بدون أمن:** فعّل المصادقة فقط بعد التأكد من أن البروتوكول يعمل أساساً.
2.  **Passive Interface:** استخدمها دائماً على واجهات المستخدمين (SW1/PC1) لتقليل سطح الهجوم.
3.  **Loopback هو صديقك:** استخدمه كـ Router-ID وكهدف آمن لاختبار التصفية دون قطع الاتصال الحقيقي.
4.  **PT ≠ Production:** تذكر دائماً أن ما تطبقه هنا هو 80% من المهارة العملية؛ الـ 20% المتبقية (SHA, TCP-AO, LSA Flooding الحقيقي) تحتاج مختبرات متقدمة.

---

# التوجيه المتقدم والأمن في Packet Tracer
**(OSPF + EIGRP | نظري + عملي + مفاهيم جوهرية)**

## تمهيد أساسي: حدود المحاكاة والواقع
قبل البدء بأي تمرين، يجب تثبيت هذا المفهوم المستخلص من **المرحلة 5** و**الملاحظة الختامية** في الملف المرفق:

| الآلية                 | الواقع (IOS XE / GNS3)    | Packet Tracer          | السبب                              |
| :--------------------- | :------------------------ | :--------------------- | :--------------------------------- |
| **مصادقة SHA**         | المعيار الأمني الحالي     | غير مدعوم بالكامل      | يحتاج محاكي متقدم                  |
| **هجمات LSA Flooding** | قابلة للمحاكاة الحقيقية   | محاكاة سلوكية فقط      | لا يدعم توليد LSA ضار حقيقي        |
| **مصادقة MD5**         | قديمة وغير مستحسنة        | البديل التعليمي الوحيد | لضمان نجاح التمرين ورؤية حالة FULL |
| **Keychain**           |  الطريقة الحديثة لـ EIGRP | مدعومة جزئياً          | نستخدمها كبديل آمن عن MD5 البسيط   |

> **القاعدة الذهبية:** ما تطبقه هنا يمنحك **80% من المهارات العملية** لشهادة ENCOR. الـ 20% المتبقية تتطلب مختبرات متقدمة.

---

##  الوحدة الأولى: OSPF Multi-Area مع الأمن والتصفية

### الأهداف التعليمية (من الملف)
*   فهم المناطق الخاصة (Stub/NSSA) وأنواع LSA (القسم 1.2 و 1.3).
*   تطبيق المصادقة كدرع لحماية جلسات الجوار (القسم 1.4).
*   التمييز بين **التصفية الانتقائية** و**العزل الكامل**.

### الطوبولوجيا المقترحة
| الجهاز | النوع | المنطقة | العناوين |
| :--- | :--- | :--- | :--- |
| **R1** | Router 4321 | Area 0 (ABR) | Gi0/0/0: `10.0.12.1/30` <br> Lo0: `1.1.1.1/32` |
| **R2** | Router 4321 | Area 1 | Gi0/0/0: `10.0.12.2/30` <br> Gi0/0/1: `192.168.10.1/24` |
| **SW1** | Switch 2960 | - | ربط المستخدمين |
| **PC1** | PC | - | IP: `192.168.10.10/24` <br> GW: `192.168.10.1` |

### التنفيذ المبسط مع شرح كل سطر

#### أ. الإعداد الأساسي ودور الـ Loopback
```bash
! --- على R1 ---
interface Loopback0
 ip address 1.1.1.1 255.255.255.255
```

**لماذا هذا العنوان؟**
 1. Router-ID ثابت لا يتغير بتعطل الواجهات الفيزيائية
  2. هدف آمن لاختبار التصفية دون قطع الاتصال الحقيقي
  3. واجهة إدارة مستقرة (Up/Up دائماً)

#### ب. OSPF مع مصادقة MD5 (بديل SHA)
```bash
router ospf 1
 router-id 1.1.1.1           ! معرف ثابت لمنع إعادة حساب الشبكة عند تغير الواجهات
 network 10.0.12.0 0.0.0.3 area 0
 area 0 authentication message-digest ! يؤمن جلسة الجوار وليس البيانات نفسها
interface GigabitEthernet0/0/0
 ip ospf message-digest-key 1 md5 MySecurePass123
 ! key 1 = رقم المعرف (يجب أن يطابق الجار)
 ! md5 = نوع الخوارزمية (بديل SHA في PT)
 ! MySecurePass123 = الكلمة المشتركة
```

#### ج. التصفية الانتقائية (ليست عزلاً!)
> **تصحيح مفاهيمي مهم:** التصفية تمنع مساراً محدداً من دخول **جدول التوجيه**، لكنها تبقيه في **LSDB**. الراوتر "يعلم" بالشبكة لكنه "يرفض استخدامها".

```bash
! --- على R2 ---
ip prefix-list BLOCK_LOOP seq 5 deny 1.1.1.1/32      ! منع مسار محدد
ip prefix-list BLOCK_LOOP seq 10 permit 0.0.0.0/0 le 32 ! السماح بكل شيء آخر (إجباري!)

router ospf 1
 distribute-list prefix BLOCK_LOOP in
 ! 'in' = تطبيق على المسارات الواردة من الجيران
 ! لا يؤثر أبداً على العناوين المحلية للراوتر نفسه
```

### التحقق والفهم العميق
| الاختبار       | الأمر                        | النتيجة             | ماذا يثبت؟                                         |
| :------------- | :--------------------------- | :------------------ | :------------------------------------------------- |
| Ping محلي      | `ping 10.0.12.1` (من R1)     | ناجح                | الواجهة نشطة؛ التصفية لا تؤثر على Connected Routes |
| حالة الجوار    | `show ip ospf neighbor`      | FULL                | المصادقة تعمل بنجاح                                |
| فعالية التصفية | `show ip route ospf` (R2)    | `1.1.1.1` غير موجود | المنع من جدول التوجيه نجح                          |
| وجود في LSDB   | `show ip ospf database` (R2) | `1.1.1.1` موجود     | السلوك الصحيح للتصفية (ليس عزلاً)                  |

---

## الوحدة الثانية: EIGRP Named Mode مع Variance

### الأهداف التعليمية (من الملف)
*   فهم خوارزمية DUAL وشروط Feasibility (القسم 2.2).
*   تطبيق توزيع الحمل غير المتكافئ عبر **Variance** (القسم 2.3).
*   استخدام Keychain كأفضل ممارسة أمنية (القسم 2.4).

### الطوبولوجيا
*   **R1:** Hub رئيسي
*   **R2:** مسار أساسي (Bandwidth عالي = قياس منخفض)
*   **R3:** مسار احتياطي (Bandwidth منخفض عمداً = قياس مرتفع)

### التنفيذ المبسط

#### أ. التلاعب بالقياس لتجربة Variance
```bash
! --- على R1 ---
interface GigabitEthernet0/0/0       ! الرابط السريع (R2)
 bandwidth 100000                    ! 100 Mbps
 ip address 10.0.12.1 255.255.255.252

interface GigabitEthernet0/0/1       ! الرابط البطيء (R3)
 bandwidth 1000                      ! 1 Mbps فقط لخلق فرق في القياس
 ip address 10.0.13.1 255.255.255.252
 ! بدون هذا الفرق، لن تظهر فائدة Variance لأن المسارين سيكونان متساويين
```

#### ب. Keychain Authentication
```bash
key chain EIGRP_SECURE
 key 1
  key-string MySecretKey2026
  accept-lifetime 00:00:00 Jan 1 2026 infinite
  send-lifetime 00:00:00 Jan 1 2026 infinite
 ! Keychain تسمح بتغيير المفاتيح تلقائياً دون توقف الخدمة
 ! وهي الطريقة الحديثة والآمنة حسب القسم 2.4
```

#### ج. EIGRP Named Mode + Variance
```bash
router eigrp LAB_EIGRP              ! Named Mode (ليس الكلاسيكي)
 address-family ipv4 unicast autonomous-system 100
  topology base
   variance 3
   ! يسمح باستخدام مسار احتياطي بشرط: قياسه ≤ (أفضل قياس × 3)
   
   af-interface GigabitEthernet0/0/0
    authentication mode md5 key-chain EIGRP_SECURE
   passive-interface default        ! أمن: تعطيل EIGRP على واجهات المستخدمين
   no passive-interface GigabitEthernet0/0/0
   no passive-interface GigabitEthernet0/0/1
```

### التحقق من DUAL وشرط Feasibility
| الأمر | ماذا تبحث عنه؟ |
| :--- | :--- |
| `show ip eigrp topology` | المسار عبر R3 يجب أن يكون **Feasible Successor**: RD < FD الحالي |
| `show ip route eigrp` | ظهور مسارين لنفس الشبكة بعد تفعيل Variance |
| `traceroute` من PC1 | توزع الحزم بين R2 و R3 بنسبة غير متساوية |

> **شرط Feasibility (من القسم 2.2):** إذا لم يظهر R3 كمسار احتياطي رغم تفعيل Variance، فالسبب أن `RD الخاص بـ R3 ≥ FD الحالي`. DUAL يعتبر المسار غير آمن من الحلقات ولن تستخدمه حتى مع Variance.

---

## ملخص الاستراتيجيات الأمنية الشاملة (المرحلة 4)

| الاستراتيجية | الأداة | الهدف |
| :--- | :--- | :--- |
| **تصفية المسارات** | Prefix-list + Distribute-list | منع تسرب مسارات خاصة أو خارجية ضارة |
| **التحكم في Redistribution** | Route-map + Tag | التحكم الدقيق في ما يُنقل بين البروتوكولات |
| **حماية البروتوكول** | Passive Interface | منع إرسال تحديثات توجيه نحو شبكات المستخدمين |
| **مصادقة الجوار** | MD5 (PT) / SHA-Keychain (Real) | منع راوترات غير معتمدة من الانضمام |

### الخلاصة النهائية
1.  **التصفية ≠ عزل:** هي تحكم انتقائي يحافظ على اتصال البروتوكول مع منع مسارات محددة.
2.  **Ping المحلي لا يختبر التصفية:** لأنه يعالج محلياً ولا يمر عبر جدول التوجيه.
3.  **Loopback هو أساس الاستقرار:** استخدمه كـ Router-ID وكهدف اختبار آمن.
4.  **PT نقطة انطلاق وليس نهاية:** المهارات المكتسبة هنا تشكل الأساس، لكن الإتقان الحقيقي يحتاج بيئات متقدمة.

هل ترغب الآن في الانتقال إلى **الوحدة الثالثة: BGP** مع تمرين عملي مبسط يغطي معايير اختيار المسار (Best Path Selection) والأمن الحدودي؟