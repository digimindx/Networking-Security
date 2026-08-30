# DHCP snooping
## 1. What is DHCP Snooping?

هي ميزة أمان مدمجة في مفاتيح الشبكة من الطبقة الثانية (Layer 2 Switches). تعمل بمثابة جدار حماية (Firewall) مصغر لمراقبة حركة مرور رسائل بروتوكول DHCP داخل الشبكة المحلية، حيث تقوم بفحص وتصفية هذه الرسائل للتأكد من سلامتها ومنع أي تلاعب بها.

## 2. Why is it important?

تكمن أهمية هذه الميزة في سد ثغرة أمنية خطيرة في بروتوكول DHCP الافتراضي (لأنه لا يشترط أي مصادقة). وتوفر الفوائد التالية:

- **منع الخوادم المزيفة (Anti-Rogue DHCP):** تمنع أي شخص من توصيل راوتر أو خادم DHCP غير مصرح به في الشبكة لمنع هجمات الـ _Man-in-the-Middle_.
    
- **منع هجمات النفاذ (DHCP Starvation):** تحمي الشبكة من استنزاف العناوين المتاحة باستخدام خاصية تقييد المعدل (_Rate Limiting_).
    
- **بناء قاعدة بيانات أمنية (Binding Table):** تنشئ جدولاً يربط الـ _MAC Address_ بالـ _IP Address_ والمنفذ (_Port_)، وهو أمر أساسي لتشغيل ميزات أمان أخرى مثل _Dynamic ARP Inspection (DAI)_ لمنع التجسس.
    

## 3. How it Works & Used?

يعتمد تشغيلها على تصنيف منافذ السويتش (Switch Ports) إلى نوعين:

1. **المنافذ الموثوقة (Trusted Ports):** وهي المنافذ المتصلة بالخادم الحقيقي (DHCP Server) أو الراوترات الشرعية. يُسمح لها بإرسال واستقبال جميع رسائل DHCP.
    
2. **المنافذ غير الموثوقة (Untrusted Ports):** وهي المنافذ المتصلة بأجهزة المستخدمين (PCs). إذا حاول أي منفذ منها إرسال رسائل توزيع عناوين (_DHCP Offer_ أو _DHCP Ack_)، يتم حظرها فوراً (_Drop_).
    

## 4. مثال تدريبي باستخدام Cisco Packet Tracer

في هذا السيناريو، سنقوم بتهيئة شبكة تحتوي على راوتر شرعي، ومهاجم يحاول توزيع آي بي مزيف.

### المكونات المطلوبة في التوبولوجي:

- **R1** (الراوتر الشرعي): متصل بمنفذ `GigabitEthernet 0/1` على السويتش.
    
- **SW1** (السويتش الرئيسي).
    
- **PC1** (جهاز المستخدم): متصل بمنفذ `FastEthernet 0/2`.
    
- **Rogue_R** (الراوتر المهاجم/المزيف): متصل بمنفذ `FastEthernet 0/3`.
    

### خطوات الإعداد والتكوين على السويتش (SW1):

**الخطوة الأولى: تفعيل الميزة عالمياً وتحديد الـ VLAN**


```
SW1(config)# ip dhcp snooping
SW1(config)# ip dhcp snooping vlan 1
```

**الخطوة الثانية: تحديد المنفذ الموثوق المتصل بالراوتر الشرعي (R1)**

بما أن `G0/1` هو المنفذ المتصل بالراوتر الحقيقي، سنجعله موثوقاً:


```
SW1(config)# interface gigabitEthernet 0/1
SW1(config-if)# ip dhcp snooping trust
SW1(config-if)# exit
```

**الخطوة الثالثة: حماية المنافذ غير الموثوقة وتقييد المعدل (اختياري ولكن موصى به)**

جميع المنافذ الأخرى تصبح تلقائياً غير موثوقة (_Untrusted_). سنقوم بتقييد معدل الطلبات على منفذ المستخدم `F0/2` لحمايته من هجمات الاستنزاف:


```
SW1(config)# interface fastTemplate 0/2
SW1(config-if)# ip dhcp snooping limit rate 10
SW1(config-if)# exit
```

#### Legal Router

```
Router> enable
Router# configure terminal
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.10.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! إنشاء نطاق توزيع العناوين
Router(config)# ip dhcp pool LEGIT_POOL
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.10.1
Router(dhcp-config)# exit
```

#### Illegal Router

```
Router> enable
Router# configure terminal
Router(config)# interface fastEthernet 0/0
Router(config-if)# ip address 10.0.0.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! إنشاء نطاق التوزيع المزيف
Router(config)# ip dhcp pool ROGUE_POOL
Router(dhcp-config)# network 10.0.0.0 255.255.255.0
Router(dhcp-config)# default-router 10.0.0.1
Router(dhcp-config)# exit
```
### النتيجة المتوقعة في الاختبار:

1. عند قيام **PC1** بطلب IP، سيمر الطلب عبر السويتش إلى **R1** (الشرعي)، ويستقبل الـ IP بنجاح لأن منفذ الراوتر _Trusted_.
    
2. إذا حاول الراوتر المزيف **Rogue_R** إرسال IP عبر منفذ `F0/3`، سيقوم السويتش **SW1** بعمل **Drop** للرسالة فوراً لأن المنفذ _Untrusted_، وبذلك تحمى الشبكة من الاختراق.
    

للتأكد من عمل الجدول واكتشاف الأجهزة المتصلة، يمكنك كتابة الأمر التالي في السويتش:


```
SW1# show ip dhcp snooping binding
```

---
---
