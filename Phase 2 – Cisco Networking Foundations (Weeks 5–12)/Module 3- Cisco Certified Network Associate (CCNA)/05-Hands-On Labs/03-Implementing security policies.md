# محتوى تدريبي: تنفيذ سياسات الأمان (Implementing Security Policies)

## مقدمة
تهدف هذه الوحدة التدريبية إلى إكساب المتدرب المهارات اللازمة لتأمين أجهزة الشبكة (Routers and Switches) وحماية حركة البيانات. سنركز في هذا الدليل على ثلاث ركائز أساسية: تأمين الوصول عن بُعد، استخدام قوائم التحكم بالوصول (ACLs)، وتطبيق تقنية NAT.

## أهداف المختبر (Lab Objectives)
1.  تأمين الوصول إلى الراوتر والسايتش عبر بروتوكول (SSH).
2.  إنشاء وتطبيق قوائم التحكم بالوصول (ACLs) للتحكم في حركة المرور.
3.  تطبيق تقنية ترجمة عناوين الشبكة (NAT) لتوفير الإنترنت.
4.  تأمين منافذ السايتش (Port Security).

---

## الجزء الأول: تأمين الوصول عن بُعد (SSH Configuration)

يعد بروتوكول (Telnet) غير آمن لأنه يرسل البيانات كنص واضح (Plaintext). يجب استبداله بـ (SSH).

**الخطوات:**

1.  **توليد المفاتيح التشفيرية (Crypto Keys):**
    يجب على الراوتر توليد مفاتيح RSA لضمان التشفير.
    ```bash
    Router&gt; enable
    Router# configure terminal
    Router(config)# ip domain-name cisco.com
    Router(config)# crypto key generate rsa
    ```
    *ملاحظة: سيطلب منك اختيار حجم المفتاح، اختر 1024 أو أعلى.*

2.  **تكوين اسم المستخدم وكلمة المرور:**
    ```bash
    Router(config)# username admin privilege 15 secret cisco123
    ```

3.  **تفعيل (SSH) على منافذ (VTY):**
    ```bash
    Router(config)# line vty 0 4
    Router(config-line)# transport input ssh
    Router(config-line)# login local
    Router(config-line)# exit
    ```

---

## الجزء الثاني: قوائم التحكم بالوصول (Access Control Lists - ACLs)

تستخدم (ACLs) للسماح أو منع مرور الحزم (Packets) بناءً على قواعد محددة.

### 1. قوائم التحكم القياسية (Standard ACLs)
تفحص فقط عنوان الـ (Source IP Address) وتتراوح أرقامها من (1-99) و (1300-1999).

**المهمة:** منع جهاز الحاسوب (PC1) من الوصول لأي شبكة أخرى، مع السماح لجميع الأجهزة الأخرى.

```bash
Router(config)# access-list 10 deny host 192.168.1.2
Router(config)# access-list 10 permit any
```

*تطبيق القائمة:* يجب تطبيق الـ (Standard ACL) دائماً قريباً من الوجهة (Destination).
```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip access-group 10 in
```

### 2. قوائم التحكم الموسعة (Extended ACLs)
تفحص (Source IP)، (Destination IP)، والبروتوكول (Protocol) والمنفذ (Port). أرقامها من (100-199) و (2000-2699).

**المهمة:** السماح لـ (PC1) بالاتصال بخادم الويب (Web Server) فقط، ومنع بقية الوصول.

```bash
Router(config)# access-list 101 permit tcp host 192.168.1.2 host 192.168.2.3 eq 80
Router(config)# access-list 101 deny ip any any
Router(config)# access-list 101 permit ip any any
```
*ملاحظة: السطر الأخير ضروري لأن (ACLs) تحتوي على قاعدة "Implicit Deny" في النهاية.*

*تطبيق القائمة:* توضع الـ (Extended ACL) قريباً من المصدر (Source).
```bash
Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip access-group 101 out
```

---

## الجزء الثالث: ترجمة عناوين الشبكة (Network Address Translation - NAT)

تستخدم (NAT) لتحويل العناوين الخاصة (Private IPs) إلى عناوين عامة (Public IPs) للسماح بالاتصال بالإنترنت.

### 1. إنشاء (Access List) لتحديد العناوين الداخلية
```bash
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
```

### 2. تحديد واجهات NAT
*   **الواجهة الداخلية (Inside Interface):** المتصلة بالشبكة المحلية.
*   **الواجهة الخارجية (Outside Interface):** المتصلة بالإنترنت.

```bash
Router(config)# interface GigabitEthernet0/0
Router(config-if)# ip nat inside
Router(config-if)# exit

Router(config)# interface GigabitEthernet0/1
Router(config-if)# ip nat outside
Router(config-if)# exit
```

### 3. تطبيق (NAT Overload / PAT)
هذا النوع يسمح للعديد من الأجهزة الداخلية بمشاركة عنوان عام واحد.

```bash
Router(config)# ip nat inside source list 1 interface GigabitEthernet0/1 overload
```

*للتحقق من عمل الـ NAT:*
```bash
Router# show ip nat translations
```

---

## الجزء الرابع: أمن المنافذ (Port Security)

تستخدم هذه الخاصية في السايتش (Switch) لمنع الأجهزة غير المصرح بها من الاتصال بالشبكة عبر منفذ معين.

**المهمة:** السماح بمنفذ (Fa0/1) بقبول جهاز واحد فقط (بناءً على الـ MAC Address).

```bash
Switch(config)# interface FastEthernet0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport port-security
Switch(config-if)# switchport port-security maximum 1
Switch(config-if)# switchport port-security violation shutdown
Switch(config-if)# switchport port-security mac-address sticky
```

*شرح الأوامر:*
*   `violation shutdown`: يقوم بإغلاق المنفذ (Shut down) فوراً إذا حاول جهاز آخر الدخول.
*   `sticky`: يحفظ الـ MAC Address الحالي للجهاز المتصل كقيمة دائمة (Secure MAC Address).

---

## ملخص وتقييم (Summary & Assessment)

لقد تعلمنا في هذه الوحدة كيفية:
1.  تأمين الوصول الإداري باستخدام (SSH).
2.  التحكم في تدفق البيانات باستخدام (Standard ACLs) و (Extended ACLs).
3.  تمكين الأجهزة الداخلية من الوصول إلى الخارج باستخدام (NAT Overload).
4.  حماية المنافذ الفيزيائية في السايتش باستخدام (Port Security).

**نصائح للمختبر:**
*   دائماً استخدم أمر `show run` للتحقق من التكوينات.
*   تأكد من وضع قواعد الـ (Deny) قبل (Permit) في حالة وجود تعارض، إلا إذا كانت قاعدة "Implicit Deny" كافية.
*   عند اختبار (Port Security)، حاول توصيل جهاز آخر بنفس المنفذ وستلاحظ إغلاق المنفذ فوراً.

---
---
---
