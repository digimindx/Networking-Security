# VLANs, Trunking, Inter-VLAN Routing

في الشبكات التقليدية (Flat Network)، تكون جميع الأجهزة في نفس نطاق البث (Broadcast Domain)، مما يؤدي إلى مشاكل في الأداء والأمان. لحل هذه المشاكل، نستخدم تقنية VLANs. ولكن بمجرد تقسيم الشبكة، تتوقف الأجهزة عن التواصل مع بعضها البعض، مما يستدعي استخدام تقنيات أخرى مثل Trunking و Inter-VLAN Routing.

---

## VLANs (الشبكات المحلية الافتراضية)

### 1. المفهوم والأهمية
VLAN هو وسيلة لربط الأجهزة معًا كما لو كانت متصلة بنفس الكابل، رغم أنها قد تكون موزعة على عدة مفاتيح (Switches) فيزيائية.
*   **الهدف الرئيسي:** تقسيم الشبكة الكبيرة إلى شبكات أصغر (Subnets) لتقليل **Broadcast Traffic**.
*   **المنطق:** كل VLAN يمثل **Broadcast Domain** منفصل. إذا أرسل جهاز في VLAN 1 بثًا، فلن يصل إلى الأجهزة في VLAN 2.

### 2. أنواع المنافذ (Port Types)
لفهم كيفية عمل VLANs، يجب معرفة نوعي المنافذ الرئيسيين:

1.  **Access Port (منفذ الوصول):**
    *   يستخدم للاتصال بالأجهزة الطرفية (End Devices) مثل (PC, Printer, IP Phone).
    *   ينتمي إلى VLAN واحد فقط.
    *   لا يحمل وسوم (Untagged)؛ لأنه عند إرسال البيانات للجهاز، يتم إزالة الـ Tag.

2.  **Trunk Port (منفذ الرابط):**
    *   يستخدم للاتصال بمفاتيح أخرى (Switch-to-Switch) أو بالراوتر (Router).
    *   يحمل بيانات عدة VLANs عبر كابل واحد.
    *   يستخدم تقنية الـ **Tagging** لتمييز البيانات القادمة من كل VLAN.

### 3. تكوين VLAN يدويًا (Static VLAN)
في بيئة العمل الحقيقية، يتم إنشاء VLANs يدويًا على كل مفتاح لضمان الأمان والاستقرار.

**خطوات التكوين (Cisco IOS):**
1.  إنشاء الـ VLAN (يتم تخزينه في ملف `vlan.dat`، وليس في `running-config`)
```bash
    Switch(config)# vlan 10
    Switch(config-vlan)# name Sales
    Switch(config-vlan)# exit
```
2.  تعيين المنفذ ليصبح Access Port.
```bash
    Switch(config)# interface GigabitEthernet0/1
    Switch(config-if)# switchport mode access
    Switch(config-if)# switchport access vlan 10
```

---

## Trunking (الروابط الحاملة)

### 1. معيار 802.1Q
لربط عدة VLANs عبر كابل واحد، نستخدم معيار **802.1Q** (IEEE 802.1Q).
*   يقوم هذا المعيار بإضافة **Tag** (وسم) بحجم 4 بايت داخل رأس إطار الـ Ethernet.
*   يحتوي الـ Tag على رقم الـ VLAN (VLAN ID).
*   عندما تصل البيانات إلى الطرف الآخر، يقوم المفتاح بقراءة الـ Tag ليحدد إلى أي VLAN تنتمي البيانات.

### 2. الـ Native VLAN
*   هو VLAN افتراضي (VLAN 1) لا يتم وسمة (Untagged) عند عبور الرابط الحامل.
*   **ملاحظة أمنية:** يُفضل تغيير الـ Native VLAN إلى رقم غير مستخدم (مثلاً 999) لتجنب هجمات **VLAN Hopping**.

### 3. بروتوكول DTP (Dynamic Trunking Protocol)
*   بروتوكول من Cisco يستخدم للتفاوض تلقائيًا حول ما إذا كان المنفذ سيكون Access أو Trunk.
*   **أفضل الممارسات:** في الامتحان وفي العمل، يُفضل تعطيل DTP وتعيين نوع المنفذ يدويًا (`switchport mode trunk`) لضمان عدم حدوث مشاكل غير متوقعة.

### 4. تكوين الـ Trunk
```bash
Switch(config)# interface GigabitEthernet0/24
Switch(config-if)# switchport mode trunk
Switch(config-if)# switchport trunk native vlan 99
```

---

## Inter-VLAN Routing (التوجيه بين الشبكات المحلية الافتراضية)

بما أن كل VLAN هو نطاق بث منفصل، فإن الأجهزة لا يمكنها التواصل مع بعضها البعض افتراضيًا. لكي تتحدث VLAN 10 مع VLAN 20، نحتاج إلى جهاز يقوم بالتوجيه (Router).

هناك طريقتان رئيسيتان لتحقيق ذلك في شهادة CCNA:

### الطريقة الأولى: Router-on-a-Stick (RoaS)
تستخدم راوترًا واحدًا متصلًا بالمفتاح عبر كابل واحد (Trunk)، ويقوم الراوتر بإنشاء واجهات افتراضية (Sub-interfaces) لكل VLAN.

**مكونات التكوين:**
1.  **الراوتر:**
    *   تقسيم الفيزيائية إلى Sub-interfaces (مثلاً `G0/0.10` لـ VLAN 10).
    *   تعيين الـ Encapsulation لكل واجهة.
    *   تعيين عنوان IP (IP Address) ليكون هو البوابة (Default Gateway) للأجهزة.

```bash
Router(config)# interface GigabitEthernet0/0.10
Router(config-subif)# encapsulation dot1Q 10
Router(config-subif)# ip address 192.168.10.1 255.255.255.0
```

2.  **المفتاح:**
    *   يجب أن يكون المنفذ المتصل بالراوتر من نوع Trunk.
    *   يجب تفعيل الـ Native VLAN على المفتاح (عادة VLAN 1) لأن الـ RoaS يعتمد على الـ Native VLAN لإرسال الباكجيتات غير المرسومة (Untagged) للراوتر.

### الطريقة الثانية: Layer 3 Switch (SVI - Switch Virtual Interface)
استخدام مفتاح طبقة 3 (Layer 3 Switch) للقيام بعملية التوجيه بدلاً من الراوتر الخارجي. هذه الطريقة أسرع وأكثر كفاءة.

**مكونات التكوين:**
1.  إنشاء واجهة افتراضية (SVI) لكل VLAN.
2.  تعيين عنوان IP لها.
3.  تفعيل التوجيه (Routing) عالميًا.

```bash
Switch(config)# vlan 10
Switch(config-vlan)# exit
Switch(config)# interface vlan 10
Switch(config-if)# ip address 192.168.10.1 255.255.255.0
Switch(config-if)# no shutdown

Switch(config)# vlan 20
Switch(config-vlan)# exit
Switch(config)# interface vlan 20
Switch(config-if)# ip address 192.168.20.1 255.255.255.0
Switch(config-if)# no shutdown

! تفعيل التوجيه هو الخطوة الأهم
Switch(config)# ip routing
```

---

## ملخص ومقارنة سريعة

| الميزة              | Router-on-a-Stick                     | Layer 3 Switch (SVI)         |
| :------------------ | :------------------------------------ | :--------------------------- |
| **الجهاز المستخدم** | راوتر خارجي                           | مفتاح طبقة 3 (L3 Switch)     |
| **الأداء**          | أبطأ (يعتمد على سرعة الكابل والراوتر) | أسرع (Switching Hardware)    |
| **التعقيد**         | يحتاج إلى إعدادات Sub-interfaces      | يحتاج إلى تفعيل `ip routing` |
| **التكلفة**         | يتطلب جهاز راوتر إضافي                | لا يتطلب أجهزة إضافية        |

---
**ملاحظة ختامية للطلاب:**
في الامتحان العملي (Lab) أو النظري، انتبه جيدًا للنقاط التالية:
1.  التأكد من أن جميع VLANs موجودة على كلا المبدلات (Switches).
2.  التأكد من توافق نوع الـ Encapsulation (يجب أن يكون `dot1q` في الراوتر).
3.  التأكد من أن الـ Native VLAN متطابق على الطرفين في الـ Trunk Link.
4.  التأكد من أن الأجهزة الطرفية (PCs) لها عنوان IP صحيح و Gateway يشير إلى واجهة التوجيه.

---
---
