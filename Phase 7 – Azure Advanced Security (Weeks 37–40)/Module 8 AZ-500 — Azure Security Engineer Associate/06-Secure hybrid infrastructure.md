# محتوى تدريبي: تأمين البنية التحتية الهجينة (Secure Hybrid Infrastructure)
**المرجع:** Phase 7 – Azure Advanced Security (Weeks 37–40) - Module 8 AZ-500
**الفصل:** 06-Secure hybrid infrastructure

## المقدمة
تهدف هذه الوحدة إلى تمكين مهندس الأمن من بناء جسر آمن بين البنية التحتية السحابية (Azure) والبنية التحتية المحلية (On-Premises) أو السحابات الأخرى. في عام 2026، أصبح مفهوم "Zero Trust" هو المعيار الذهبي، وتعتبر أدوات مثل (Azure Arc) و (Microsoft Defender for Cloud) هي الركائز الأساسية لهذا الفصل.

---

## 1. إدارة الموارد الهجينة باستخدام Azure Arc
أصبحت (Azure Arc) هي التقنية الأساسية لربط الخوادم وقواعد البيانات والشبكات الموجودة خارج Azure إلى لوحة تحكم واحدة.

*   **المفهوم:** تسمح (Azure Arc) لمهندسي الأمن بتطبيق سياسات الأمان (Security Policies) ومعايير الحوكمة (Governance) على الخوادم المحلية (On-Premises Servers) والخوادم في السحابات الأخرى (AWS/GCP) كما لو كانت في Azure.
*   **التطبيق الأمني:**
    *   تفعيل (Microsoft Defender for Cloud) على خوادم (Azure Arc-enabled Servers) لاكتشاف التهديدات والهجمات المحتملة.
    *   استخدام (Azure Policy) لفرض معايير تشفير البيانات وتثبيت جدران الحماية على الخوادم الهجينة.
    *   إدارة الهوية من خلال ربط (Microsoft Entra ID) مع الخوادم الهجينة للوصول الآمن.

## 2. الحماية الشاملة مع Microsoft Defender for Cloud
يعتبر (Microsoft Defender for Cloud) (المعروف سابقاً بـ Azure Security Center) المحور الرئيسي لمراقبة البنية الهجينة.

*   **المهام الأساسية:**
    *   **تقييم الثغرات (Vulnerability Assessment):** فحص الخوادم المحلية والسحابية بحثاً عن تحديثات أمنية ونقاط ضعف معروفة.
    *   **إدارة التصحيح (Patch Management):** استخدام (Azure Automanage) أو (Update Management) لتحديث أنظمة التشغيل الهجينة بشكل آلي.
    *   **كشف التهديدات (Threat Detection):** مراقبة سلوكيات الشبكة (Network Traffic) واكتشاف الأنشطة المشبوهة مثل محاولات الاختراق (Brute Force Attacks).
    *   **التكامل مع Microsoft Sentinel:** إرسال سجلات الأمان (Security Logs) إلى (Microsoft Sentinel) لتحليلها باستخدام الذكاء الاصطناعي والكشف عن الهجمات المعقدة.

## 3. الشبكات الهجينة الآمنة (Secure Hybrid Networking)
يجب ضمان اتصال آمن ومشفّر بين الشبكة المحلية وموارد Azure.

*   **ExpressRoute:**
    *   يوفر اتصالاً خاصاً ومخصصاً (Private Connection) بين الشبكة المحلية ومراكز بيانات Microsoft.
    *   لا يمر عبر الإنترنت العام (Public Internet)، مما يضمن سرقة أقل وزمن استجابة أقل.
    *   **الأمان:** يستخدم (ExpressRoute Gateway) لإدارة التوجيه وتطبيق السياسات.
*   **Site-to-Site VPN:**
    *   حل اقتصادي لإنشاء نفق مشفر (IPsec/IKE Tunnel) عبر الإنترنت العام.
    *   يجب استخدام بروتوكولات تشفير قوية (IKEv2) لضمان أمان البيانات المنقولة.
*   **Azure Private Link:**
    *   يسمح بالوصول إلى خدمات Azure (مثل Storage Accounts و SQL Database) عبر عنوان IP خاص (Private IP) داخل الشبكة الافتراضية (VNet)، مما يمنع الوصول العام تماماً.

## 4. إدارة جدران الحماية المركزية (Azure Firewall Manager)
عند العمل في بيئة هجينة معقدة، يصبح من الصعب إدارة جدران الحماية في كل موقع. هنا يأتي دور (Azure Firewall Manager).

*   **الميزات الأمنية:**
    *   **تطبيق السياسات الموحدة (Unified Policy Application):** كتابة سياسة أمان واحدة ونشرها عبر (Azure Firewall) في عدة مناطق (Regions) وفي الشبكة المحلية.
    *   **توجيه حركة المرور (Traffic Steering):** توجيه حركة المرور من الشبكة المحلية عبر (Azure Firewall) لفحصها قبل الوصول إلى الإنترنت أو الخدمات السحابية.
    *   **الحماية من التهديدات المتقدمة:** استخدام (Azure Firewall Premium) لتفعيل ميزات متقدمة مثل فحص بروتوكول TLS (TLS Inspection) واكتشاف البرامج الضارة (IDS/IPS).

## 5. إدارة الهوية والوصول (Identity and Access Management)
في البيئات الهجينة، تعتمد الأمان بشكل كلي على هوية المستخدم والجهاز.

*   **Microsoft Entra ID (Azure Active Directory):**
    *   هو "هوية" البنية الهجينة. يجب مزامنة المستخدمين المحليين مع السحابة باستخدام (Microsoft Entra Connect).
    *   تفعيل المصادقة متعددة العوامل (MFA) لجميع الحسابات.
*   **Privileged Identity Management (PIM):**
    *   تطبيق مبدأ "الحد الأدنى من الامتيازات" (Least Privilege).
    *   تمكين الوصول المؤقت (Just-in-Time Access) للمسؤولين فقط عند الحاجة، بدلاً من إعطائهم صلاحيات دائمة.
*   **Conditional Access Policies:**
    *   فرض شروط للوصول، مثل: "لا يمكن للموظف الوصول إلى الموارد إلا إذا كان متصلاً بشبكة الشركة (On-Premises Network) أو باستخدام جهاز موثوق (Compliant Device)".

## 6. إدارة المفاتيح والاسرار (Key Management)
حماية البيانات المخزنة في السحابة والبيئات الهجينة يتطلب إدارة دقيقة للمفاتيح.

*   **Azure Key Vault:**
    *   مكان مركزي لتخزين كلمات المرور (Secrets) ومفاتيح التشفير (Encryption Keys) وتوقيعات الشهادات (Certificates).
    *   يسمح للتطبيقات المحلية والسحابية بالوصول إلى المفاتيح بشكل آمن دون الحاجة لكتابتها في الكود المصدري.
*   **Bring Your Own Key (BYOK):**
    *   تقنية تتيح لمهندسي الأمن الاحتفاظ بمفاتيح التشفير الخاصة بهم في (Azure Key Vault) أو في خزائن مفاتيح محلية (HSM) بدلاً من الاعتماد كلياً على مفاتيح Microsoft.

---

## ملخص النقاط الجوهرية للاختبار (Key Takeaways)
1.  استخدم **(Azure Arc)** لإدارة وتأمين الخوادم خارج Azure.
2.  استخدم **(Microsoft Defender for Cloud)** كمنصة مراقبة أمنية موحدة للبيئة الهجينة.
3.  استخدم **(Azure Private Link)** لإزالة التعرض العام لخدمات Azure.
4.  استخدم **(Azure Firewall Manager)** لتطبيق سياسات أمان الشبكة بشكل مركزي.
5.  اعتمد على **(Microsoft Entra ID)** و **(Conditional Access)** للتحكم في هوية المستخدم والجهاز في البيئة الهجينة.
