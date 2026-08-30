# أدوات نظام التشغيل (Cisco IOS Tools)

توفر أجهزة شبكة سيسكو (Cisco) مجموعة من الأدوات المساعدة (Utilities) المدمجة داخل نظام التشغيل الخاص بها (IOS). يستخدم مهندسي الشبكات هذه الأدوات لإجراء عمليات التشخيص (Troubleshooting)، وفحص الاتصال (Connectivity)، واختبار الخدمات المختلفة مباشرة من سطر الأوامر (CLI) للجهاز.

**ملاحظة هامة:** تعمل معظم هذه الأوامر فقط عند الدخول إلى وضع التنفيذ المميز (Privileged EXEC Mode)، والذي يظهر عادةً علامة `#` في نهاية سطر الأوامر.

---

## أداة (ping)
تُستخدم هذه الأداة للتحقق من الاتصال (Reachability) بين الجهاز الحالي والوجهة المحددة. تعتمد على بروتوكول (ICMP).

*   **الغرض:** معرفة ما إذا كانت الوجهة متاحة وما إذا كانت الشبكة تعمل بشكل سليم.
*   **التركيب (Syntax):**
    `Router# ping &lt;ip_address&gt;`
*   **مثال:**
  
```
    Router# ping 8.8.8.8
    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5)
```
    *ملاحظة:* الرموز `!` تعني نجاح الرد، والرمز `.` يعني انتهاء المهلة (Timeout).

---

## أداة (traceroute)
تُستخدم لتتبع المسار الذي تسلكه الحزم (Packets) من المصدر إلى الوجهة.

*   **الغرض:** معرفة عدد القفزات (Hops) والمسارات التي تمر بها البيانات، واكتشاف نقاط التوقف أو التأخير.
*   **التركيب (Syntax):**
    `Router# traceroute &lt;ip_address&gt;`
*   **مثال:**
  
```
    Router# traceroute 8.8.8.8
    Type escape sequence to abort.
    Tracing the route to 8.8.8.8
    1 10.1.1.2 4 msec 0 msec 0 msec
    2 192.168.1.1 4 msec 4 msec 4 msec
    ...
    
```
    
---

## أداة (telnet)
تُستخدم للاتصال عن بُعد بجهاز آخر عبر الشبكة.

*   **الغرض:** الدخول إلى إعدادات جهاز آخر (مثل راوتر أو سويتش آخر).
*   **ملاحظة أمنية:** بيانات الاتصال (بما فيها كلمة المرور) تنتقل كنص واضح (Plaintext)، لذا لا ينصح باستخدامها في البيئات غير الآمنة.
*   **التركيب (Syntax):**
    `Router# telnet &lt;ip_address&gt;`
*   **مثال:**
```
    Router# telnet 192.168.1.10
    User Access Verification
    Password:
```

---

## أداة (ssh)
تُستخدم للاتصال الآمن عن بُعد بجهاز آخر.

*   **الغرض:** الدخول لإعدادات جهاز آخر بشكل مشفر وآمن.
*   **المتطلبات:** يجب إعداد اسم المستخدم وكلمة المرور وتفعيل خدمة (SSH) على الجهاز المراد الاتصال به مسبقاً.
*   **التركيب (Syntax):**
    `Router# ssh -l &lt;username&gt; &lt;ip_address&gt;`
*   **مثال:**
```
    Router# ssh -l admin 192.168.1.10
    Password:
```

---

## أداة (tcp connect)
تُستخدم لاختبار الاتصال بمنفذ (Port) محدد على جهاز بعيد.

*   **الغرض:** التأكد من أن خدمة معينة (مثل الويب أو البريد) تعمل وتستقبل الاتصالات على المنفذ المحدد.
*   **التركيب (Syntax):**
    `Router# tcpconnect &lt;ip_address&gt; &lt;port_number&gt;`
*   **مثال:** (فحص منفذ الويب 80)
```
    Router# tcpconnect 192.168.1.10 80
    TCP connection to 192.168.1.10:80 established
```

---

## أداة (dns lookup)
تُستخدم للبحث عن عنوان IP الخاص باسم نطاق (Domain Name).

*   **الغرض:** تحويل اسم الموقع (مثل www.google.com) إلى عنوان IP رقمي.
*   **المتطلبات:** يجب ضبط خادم (DNS) على الراوتر باستخدام أمر (ip name-server).
*   **التركيب (Syntax):**
    `Router# dns lookup &lt;domain_name&gt;`
*   **مثال:**
```
    Router# dns lookup www.google.com
    www.google.com has address 142.250.189.206
```

---

## أداة (whois)
تُستخدم للحصول على معلومات تسجيل النطاق (Domain Registration Information).

*   **الغرض:** معرفة مالكي النطاق، تواريخ التسجيل، وخوادم الاسم الخاصة بموقع معين.
*   **التركيب (Syntax):**
    `Router# whois &lt;domain_name&gt;`
*   **مثال:**
```
    Router# whois google.com
    ...
    Registrant Name: Google LLC
    ...
```

---

## أداة (trace)
هي اختصار أو بديل لأداة (traceroute) في بعض إصدارات النظام.

*   **التركيب (Syntax):**
    `Router# traceroute <ip_address>`

---

## أداة (ping) مع خيارات متقدمة
يمكن استخدام أداة (ping) مع خيارات إضافية لتحديد عدد مرات الإرسال أو حجم الحزمة.

*   **مثال (إرسال 10 حزم بحجم 1000 بايت):**
    `Router# ping 8.8.8.8 count 10 size 1000`

---
---
