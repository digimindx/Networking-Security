# المحتوى التدريبي: الشبكات اللاسلكية للمؤسسات (Wireless Enterprise Networks)
**المرجع:** Phase 5 – Advanced Cisco Enterprise (Module 6)
**الوحدة:** ENCOR – 06-Wireless enterprise networks

## 1. مقدمة في بنية الشبكة اللاسلكية (Wireless Network Architecture)

تتطلب الشبكات اللاسلكية المؤسسية بنية تحتية قوية لإدارة الوصول، والأمان، والأداء. تنقسم البنى الرئيسية إلى فئتين:

### أ. بنية المراقب (Controller-Based Architecture)
تعتمد هذه البنية على جهاز مركزي لإدارة نقاط الوصول (Access Points) وتوزيع الإعدادات الأمنية.
*   **المكونات الأساسية:**
    *   **مراقب الشبكة المحلية اللاسلكية (Wireless LAN Controller - WLC):** هو العقل المدبر الذي يتعامل مع نقاط الوصول (Access Points) عبر بروتوكول (CAPWAP).
    *   **نقطة الوصول (Access Point - AP):** تعمل كجسر بين الأجهزة اللاسلكية (Clients) والشبكة السلكية.

### ب. البنية السحابية (Cloud-Based Architecture)
تستخدم منصات مثل (Cisco Catalyst Center) أو (Cisco Meraki) لإدارة الأجهزة عن بعد دون الحاجة إلى أجهزة مراقبة محلية في كل فرع.

---

## 2. بروتوكول CAPWAP (Control And Provisioning of Wireless Access Points)

يعتبر بروتوكول (CAPWAP) هو المعيار الأساسي للاتصال بين (WLC) و (Access Points).

### أ. أقسام نفق CAPWAP (CAPWAP Tunnels)
ينقسم الاتصال إلى نفقين رئيسيين:
1.  **نفق التحكم (Control Tunnel):** يستخدم لتمرير رسائل التحكم (Control Messages) مثل إعدادات الـ SSID وتحميل الصور (Image). يعمل على المنفذ (UDP Port 5246).
2.  **نفق البيانات (Data Tunnel):** يستخدم لتمرير البيانات (Data Messages) من العملاء (Clients) عبر (WLC) إلى الشبكة. يعمل على المنفذ (UDP Port 5247).

### ب. أمان البروتوكول
يتم تشفير اتصال (CAPWAP) باستخدام بروتوكول (Datagram Transport Layer Security - DTLS) لمنع التنصت والتلاعب بالرسائل.

### ج. أوضاع العمل (Operational Modes)
تعمل نقاط الوصول (Access Points) في أوضاع مختلفة بناءً على التصميم:
*   **الوضع المحلي (Local Mode):** الوضع الافتراضي حيث يتم تحويل البيانات (Data Switching) عبر (WLC).
*   **وضع فليكسكونكت (FlexConnect Mode):** يسمح لنقطة الوصول بتمرير البيانات محلياً (Local Switching) إذا فقدت الاتصال بـ (WLC)، مما يوفر عرض النطاق الترددي (Bandwidth).
*   **الوضع المراقب (Monitor Mode):** تستخدمه نقاط الوصول لاكتشاف الأجهزة الضارة (Rogue Devices) ومراقبة الطيف (Spectrum Analysis)، ولا تقوم بتمرير بيانات العملاء.
*   **وضع المدافع (Rogue-Defender Mode):** يُستخدم لاكتشاف وتقليل إشارة نقاط الوصول غير المصرح بها (Rogue APs) عن طريق إرسال حزم (Deauth) ضدها.

---

## 3. الأمان في الشبكات اللاسلكية (Wireless Security)

تركز وحدة الأمان على حماية البيانات والهوية في البيئة اللاسلكية.

### أ. معايير التشفير (Encryption Standards)
*   **Wi-Fi Protected Access 3 (WPA3):** المعيار الأحدث الذي يوفر تشفيراً أقوى (192-bit) ومقاومة لهجمات التخمين (Brute-force attacks) عبر ميزة (Simultaneous Authentication of Equals - SAE).
*   **Wi-Fi Protected Access 2 (WPA2):** لا يزال مستخدماً على نطاق واسع ويعتمد على (Advanced Encryption Standard - AES).

### ب. المصادقة (Authentication)
تعتمد الشبكات المؤسسية على بروتوكول (IEEE 802.1X) للمصادقة على مستوى المنفذ، والذي يتطلب عادةً خادم (RADIUS).
*   **Extensible Authentication Protocol (EAP):** هو إطار عمل للمصادقة.
    *   **Protected EAP (PEAP):** يستخدم قناة مشفرة (Tunnel) لتأمين بيانات اعتماد المستخدم (مثل اسم المستخدم وكلمة المرور).
    *   **Transport Layer Security (TLS):** يعتمد على الشهادات الرقمية (Certificates) للمصادقة المتبادلة (Mutual Authentication).

### ج. دمج Identity Services Engine (ISE)
يعمل (Cisco ISE) كخادم (RADIUS) مركزي لإدارة سياسات الوصول (Access Policies) وتأمين الدخول للشبكات اللاسلكية (Wireless Networks).

---

## 4. نظام منع التسلل اللاسلكي (Wireless Intrusion Prevention System - WIPS)

يوفر (WIPS) حماية ضد الهجمات الداخلية والخارجية على الشبكة اللاسلكية.

### أ. اكتشاف الأجهزة الضارة (Rogue AP Detection)
*   **Rogue AP:** نقطة وصول غير مصرح بها يتم تثبيتها داخل المبنى، مما يشكل ثغرة أمنية.
*   **مبدأ العمل:** تقوم (WLC) بتحليل الطيف (Spectrum Analysis) للبحث عن توقيعات (Signatures) لنقاط الوصول غير المعروفة.
*   **تتبع الموقع (Location Tracking):** تساعد في تحديد الموقع الفعلي لجهاز (Rogue AP) باستخدام تقنية (Time of Arrival - ToA).

### ب. هجمات Denial of Service (DoS)
*   **Deauth Flood:** هجوم يقوم بإرسال حزم إلغاء اتصال (Deauthentication) لإرباك العملاء.
*   **Channel Jamming:** تشويش على قنوات معينة لمنع العمل.
*   **Mitigation:** تقوم (WLC) بتفعيل (Location Tracking) لمحاولة عزل الجهاز المهاجم أو إغلاق المنفذ (Port) الخاص به.

---

## 5. التنقل في الشبكة اللاسلكية (Wireless Roaming)

التنقل هو قدرة العميل (Client) على الانتقال من نقطة وصول (Access Point) إلى أخرى مع الحفاظ على الاتصال.

### أ. أنواع التنقل (Roaming Types)
1.  **تنقل طبقة الثانية (Layer 2 Roaming):** ينتقل العميل بين نقاط الوصول ضمن نفس الـ (VLAN) ونفس (Subnet).
2.  **تنقل طبقة الثالثة (Layer 3 Roaming):** ينتقل العميل بين (VLANs) مختلفة أو شبكات فرعية (Subnets). يتطلب هذا عادةً بروتوكولات مثل (Mobile IP) أو (Proxy ARP).

### ب. آليات تسريع التنقل (Fast Roaming Mechanisms)
لتقليل زمن التبديل (Latency) أثناء التنقل:
*   **PMK-Caching:** تخزين مفاتيح التشفير (Pairwise Master Key) مؤقتاً لتسريع إعادة المصادقة.
*   **Fast Transition (FT):** بروتوكول (IEEE 802.11r) يسمح بتبادل المفاتيح أثناء مرحلة الاستشعار (Scanning) قبل الاتصال الفعلي.
*   **Cisco Centralized Key Management (CCKM):** تقنية خاصة من سيسكو لتسريع تبادل المفاتيح بين (WLC) و (Access Points).

---

## 6. تخطيط الترددات (RF Planning)

لفهم أداء الشبكة، يجب فهم خصائص الطيف اللاسلكي (Radio Frequency).

### أ. النطاقات الترددية (Frequency Bands)
*   **2.4 GHz:** مداه طويل ولكن عدد القنوات غير المتداخلة (Non-Overlapping Channels) قليل (ثلاث قنوات فقط: 1, 6, 11 في المنطقة 1).
*   **5 GHz:** يوفر عرض نطاق ترددي (Bandwidth) أعلى وعدد قنوات غير متداخلة أكثر، ولكنه يتأثر بالعوائق (Obstacles) أكثر.
*   **6 GHz (Wi-Fi 6E):** يوفر قنوات واسعة جداً (80MHz, 160MHz) وخالية من التداخل مع الأجهزة القديمة.

### ب. Dynamic Frequency Selection (DFS)
*   تقنية تسمح لنقاط الوصول (Access Points) بالانتقال تلقائياً إلى قنوات (DFS) في نطاق 5 GHz لتجنب التداخل مع الرادارات الجوية (Weather Radar).
*   **Channel Switch Announcement (CSA):** رسالة تُرسل لإعلام العملاء بالانتقال لقناة جديدة.

---

**ملخص الاختبار (Exam Tips):**
*   احفظ منافذ (CAPWAP) جيداً (5246 للتحكم، 5247 للبيانات).
*   ركز على الفرق بين (Local Mode) و (FlexConnect Mode) ومتى تستخدم كل منهما.
*   افهم دور (Cisco ISE) في مصادقة الشبكات اللاسلكية باستخدام (802.1X).
*   تمييز (Rogue AP) عن (Adversary AP) (جهاز متسلل يقوم بمحاكاة نقطة وصول شرعية).
