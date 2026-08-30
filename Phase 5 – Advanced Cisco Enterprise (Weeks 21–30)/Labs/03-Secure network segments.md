# المحتوى التدريبي: تأمين مقاطعات الشبكة (Secure Network Segments)
**المرجع:** CCNP Enterprise Security (Core + Concentration) - Module 6

## مقدمة
في بيئات الشبكات الحديثة، لا يكفي تأمين الحدود الخارجية للشبكة فقط. يجب تأمين المقاطعات الداخلية (Internal Segments) لمنع انتشار الهجمات (Lateral Movement) وعزل الأجهزة المصابة. يركز هذا الدليل على تقنيات الأمان الخاصة بطبقة البيانات (Layer 2 Security) التي تتحكم في وصول الأجهزة إلى الشبكة وتضمن سلامة الجداول الديناميكية.

---

## 1. أمان منفذ المبدل (Switch Port Security)
يهدف هذا المكون إلى منع هجمات (MAC Address Flooding) والتحكم في الأجهزة المتصلة بمنفذ معين.

### المفاهيم الأساسية:
*   **منفذ آمن (Secure Port):** المنفذ الذي تم تفعيل ميزة الأمان عليه.
*   **عنوان MAC الافتراضي (Default MAC Address):** عدد عناوين MAC المسموح بها (عادة 1).
*   **وضع انتهاك (Violation Mode):** الإجراء الذي يتخذه المبدل عند تجاوز عدد عناوين MAC المحدد:
    *   **(Shutdown):** يقوم بإيقاف المنفذ فوراً (يجب إعادة تشغيله يدوياً).
    *   **(Restrict):** يقوم بإسقاط الحزم المخالفة، تسجيل الحدث في الـ Log، وزيادة عداد الـ Violation.
    *   **(Protect):** يقوم بإسقاط الحزم المخالفة بصمت دون تسجيل أي رسالة خطأ.

### إعدادات المعامل (Lab Configuration):
```bash
Switch(config)# interface gigabitethernet 1/0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 2
Switch(config-if)# switchport port-security violation restrict
Switch(config-if)# switchport port-security mac-address sticky
```
*ملاحظة:* الأمر `(sticky)` يقوم بحفظ عناوين MAC النشطة حالياً كعناوين آمنة ديناميكياً.

---

## 2. تفتيش DHCP (DHCP Snooping)
تعتبر هذه التقنية خط الدفاع الأول ضد خوادم (DHCP) المزيفة (Rogue DHCP Servers). تقوم ببناء جدول ربط (Binding Table) يربط بين عنوان IP، وعنوان MAC، والمنفذ، و VLAN.

### آليات العمل:
*   **منفذ موثوق (Trusted Port):** يسمح باستقبال حزم (DHCP Offer) و (DHCP Ack). عادة ما يكون المنفذ المتصل بخادم DHCP الشرعي.
*   **منفذ غير موثوق (Untrusted Port):** جميع المنافذ الأخرى (Access Ports) التي يتصل بها المستخدمون. يتم رفض أي حزم (DHCP Offer) أو (DHCP Ack) تأتي من هذا المنفذ.

### إعدادات المعامل:
```bash
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10,20
Switch(config)# interface gigabitethernet 1/0/24
Switch(config-if)# ip dhcp snooping trust
```

---

## 3. تفتيش ARP الديناميكي (Dynamic ARP Inspection - DAI)
تحمي الشبكة من هجمات (ARP Spoofing) أو (ARP Poisoning). تقوم DAI بفرز حزم (ARP Request) و (ARP Reply) والتحقق من صحتها مقابل جدول (DHCP Snooping Binding Table).

### شروط التشغيل:
1.  يجب تفعيل (DHCP Snooping) أولاً.
2.  يجب تحديد المنافذ الموثوقة (Trusted Ports) للخادم أو الروابط التجميعية (Trunks).

### إعدادات المعامل:
```bash
! تفعيل DAI على فيلات محددة
Switch(config)# ip arp inspection vlan 10,20

! تحديد المنفذ الموثوق
Switch(config)# interface gigabitethernet 1/0/24
Switch(config-if)# ip arp inspection trust

! التحقق من صحة الحزم (اختياري ولكن موصى به)
Switch(config-if)# ip arp inspection validate src-mac dst-ip ip
```
*   **(src-mac):** يتحقق من تطابق عنوان MAC المصدر مع الجدول.
*   **(dst-ip):** يتحقق من تطابق عنوان IP الوجهة.
*   **(ip):** يتحقق من أن عنوان IP المصدر صحيح.

---

## 4. حارس المصدر IP (IP Source Guard)
يمنع الأجهزة من استخدام عناوين IP مزيفة (IP Spoofing). يعمل بناءً على جدول (DHCP Snooping Binding Table).

### آليات العمل:
*   يقوم بفلترة حركة المرور بناءً على عنوان IP وعنوان MAC.
*   إذا لم يكن الجهاز قد حصل على عنوان IP عبر (DHCP)، يجب إنشاء ربط ثابت (Static Binding) يدوياً.

### إعدادات المعامل:
```bash
! الربط الثابت (لأجهزة لا تستخدم DHCP)
Switch(config)# ip source binding 0000.1111.2222 vlan 10 192.168.1.100 interface gigabitethernet 1/0/5

! تفعيل الفلترة على المنفذ
Switch(config)# interface gigabitethernet 1/0/5
Switch(config-if)# ip verify source vlan-acl
```

---

## 5. الشبكات المحلية الخاصة (Private VLANs - PVLANs)
تسمح بعزل الأجهزة (Hosts) داخل نفس (VLAN) ومنطقتها الفرعية (Subnet) عن بعضها البعض، مع السماح لها بالتواصل مع البوابة (Gateway) أو الخوادم المشتركة.

### أنواع المنافذ في PVLAN:
1.  **(Promiscuous Port):** يمكنه التحدث مع جميع المنافذ الأخرى (Isolated و Community و Promiscuous). عادة ما يكون متصلاً بالراوتر أو البوابة.
2.  **(Community Port):** يمكنه التحدث مع المنافذ الأخرى في نفس الـ Community ومع المنفذ (Promiscuous). لا يمكنه التحدث مع المنافذ في الـ Isolated VLAN.
3.  **(Isolated Port):** لا يمكنه التحدث مع أي منفذ آخر في نفس الـ Secondary VLAN، فقط مع المنفذ (Promiscuous).

### إعدادات المعامل:
```bash
! إنشاء VLAN أساسي وثانوي
Switch(config)# vlan 100
Switch(config-vlan)# private-vlan primary
Switch(config-vlan)# exit
Switch(config)# vlan 200
Switch(config-vlan)# private-vlan isolated
Switch(config-vlan)# exit
Switch(config)# vlan 300
Switch(config-vlan)# private-vlan community
Switch(config-vlan)# exit

! ربط الفيلاات
Switch(config)# private-vlan association 200,300
Switch(config)# exit

! تعيين نوع المنفذ
Switch(config)# interface range gigabitethernet 1/0/1-24
Switch(config-if-range)# switchport mode private-vlan host
Switch(config-if-range)# switchport private-vlan host-association 100 200
```

---

## سيناريو معمل عملي (Lab Scenario)

**الهدف:** تأمين شبكة المكتب (VLAN 10) ومنع هجمات ARP و DHCP المزيفة.

**المتطلبات:**
*   تفعيل (DHCP Snooping).
*   تفعيل (Dynamic ARP Inspection).
*   تعيين المنفذ المتصل بالخادم كموثوق (Trusted).
*   تعيين المنافذ المتصلة بالمستخدمين كمنافذ آمنة (Port Security).

**خطوات التنفيذ:**

```bash
! 1. إعداد بيئة DHCP
Switch(config)# ip dhcp pool OFFICE-NET
Switch(dhcp-config)# network 192.168.10.0 255.255.255.0
Switch(dhcp-config)# default-router 192.168.10.1
Switch(dhcp-config)# dns-server 8.8.8.8

! 2. تفعيل DHCP Snooping
Switch(config)# ip dhcp snooping
Switch(config)# ip dhcp snooping vlan 10

! 3. إعداد الخادم (Trusted Port)
Switch(config)# interface gigabitethernet 1/0/1
Switch(config-if)# ip dhcp snooping trust
Switch(config-if)# ip arp inspection trust

! 4. إعداد منافذ المستخدمين (Untrusted)
Switch(config)# interface range gigabitethernet 1/0/2 - 10
Switch(config-if-range)# switchport mode access
Switch(config-if-range)# switchport access vlan 10
Switch(config-if-range)# ip dhcp snooping limit rate 15 ! حماية ضد DHCP starvation

! 5. تفعيل Port Security
Switch(config-if-range)# switchport port-security
Switch(config-if-range)# switchport port-security maximum 2
Switch(config-if-range)# switchport port-security violation shutdown

! 6. تفعيل DAI
Switch(config)# ip arp inspection vlan 10
```

---

## ملخص للمراجعة (Summary)
*   **(Port Security)** يمنع الأجهزة غير المصرح بها من الاتصال.
*   **(DHCP Snooping)** يبني جدول الربط ويحمي من خوادم DHCP المزيفة.
*   **(Dynamic ARP Inspection)** يستخدم جدول DHCP Snooping لمنع التلاعب بجداول ARP.
*   **(IP Source Guard)** يمنع استخدام عناوين IP غير مسموح بها.
*   **(Private VLANs)** توفر العزل عند الطبقة الثانية داخل نفس الشبكة الفرعية.

يجب على المهندسين فهم الترابط بين هذه التقنيات، حيث يعتمد بعضها (مثل DAI) على وجود تقنيات أخرى (مثل DHCP Snooping) لتعمل بشكل صحيح.
