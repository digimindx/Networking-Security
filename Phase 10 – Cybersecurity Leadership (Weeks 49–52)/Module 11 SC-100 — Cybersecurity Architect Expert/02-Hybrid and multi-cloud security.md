# Chapter: 02-Hybrid and multi-cloud security

---

## 1. مقدمة وأهداف الوحدة (Introduction & Objectives)

في إطار دورات القيادة السيبرانية، يركز هذا الفصل على كيفية تصميم معماريات أمنية قوية للبيئات التي لا تقتصر على سحابة واحدة. مع تحول المؤسسات إلى نموذج "Cloud-First" أو "Cloud-Native"، أصبح تصميم الحلول التي تدمج بين البنية التحتية المحلية (On-Premises Infrastructure) مع خدمات سحابة عامة متعددة (AWS, Google Cloud, Microsoft Azure) تحدياً هندسياً واستراتيجياً.

**الأهداف التعليمية:**
*   فهم مبادئ تصميم الأمن في البيئات الهجينة.
*   تطبيق نموذج "الصفر ثقة" (Zero Trust) عبر حدود السحابة المختلفة.
*   إدارة الهوية والوصول بشكل موحد (Unified Identity Management).
*   تصميم حلول المراقبة والامتثال المركزي (Centralized Monitoring & Compliance).

---

## 2. المبادئ الأساسية للأمن متعدد السحابة (Multi-Cloud Security Principles)

عند التصميم لبيئة متعددة السحابة (Multi-Cloud)، يجب على المهندس المعماري (Solution Architect) الالتزام بالمبادئ التالية:

### أ. الهوية كحدود أمنية جديدة (Identity as the New Perimeter)
لم يعد جدار الحماية التقليدي (Traditional Firewall) كافياً. يجب اعتبار الهوية (Identity) هي الخط الدفاعي الأول.
*   **المفهوم:** يجب توحيد إدارة المستخدمين والأجهزة عبر جميع البيئات.
*   **الحل التقني:** استخدام مركز هوية رئيسي (Central Identity Provider) مثل **Microsoft Entra ID** (سابقاً Azure AD) لمصادقة المستخدمين في البيئات المحلية والسحابية المختلفة.
*   **التطبيق:** تفعيل المصادقة متعددة العوامل (Multi-Factor Authentication - MFA) بشكل إلزامي عبر جميع المنصات.

### ب. نموذج الصفر ثقة (Zero Trust Model)
لا يجب الوثوق بأي طلب قادم من داخل أو خارج الشبكة.
*   **Verify Explicitly:** التحقق من كل طلب وصول بناءً على السياق (Context) مثل موقع المستخدم، جهازه، وسلوكه.
*   **Least Privilege Access:** منح أقل صلاحية ممكنة (Least Privilege) فقط عند الحاجة (Just-in-Time Access).
*   **Assume Breach:** افتراض أن الاختراق قد حدث وتصميم الشبكة بعزل (Micro-segmentation) لمنع الحركة الجانبية (Lateral Movement).

---

## 3. استراتيجيات الربط والشبكة (Connectivity & Network Architecture)

تصميم الربط الآمن بين السحابة المحلية والسحابات العامة هو حجر الزاوية في الـ SC-100.

### أ. شبكات السحابة الهجينة (Hybrid Networking)
*   **ExpressRoute / Direct Connect:** إنشاء خطوط اتصال خاصة ومخصصة تربط الشبكة المحلية بالسحابة لضمان السرية (Confidentiality) وتجنب الإنترنت العام.
*   **Site-to-Site VPN:** استخدام التشفير (Encryption) لنقل البيانات عبر قنوات غير موثوقة (مثل الإنترنت) بين الفروع المختلفة.

### ب. إدارة نقاط النهاية (Endpoint Management)
*   يجب ضمان أن الأجهزة التي تتصل بالبيئات السحابية تفي بالمتطلبات الأمنية (Compliance).
*   استخدام أدوات مثل **Microsoft Intune** أو **Azure Arc** لمراقبة حالة الأجهزة (Device Health) قبل السماح بالوصول إلى الموارد الحساسة.

---

## 4. إدارة البيانات والتشفير (Data Protection & Encryption)

في البيئات متعددة السحابة، تتركز البيانات عبر مناطق جغرافية ومنصات مختلفة.

### أ. إدارة مفاتيح التشفير (Key Management)
*   **Customer-Managed Keys (CMK):** يجب أن تحتفظ المؤسسة بمفاتيح التشفير الخاصة بها بدلاً من الاعتماد كلياً على مفاتيح مزود الخدمة.
*   **Azure Key Vault / AWS KMS:** استخدام خدمات إدارة المفاتيح المركزية أو المتزامنة لضمان عدم فقدان البيانات أو وصول غير مصرح به إليها.

### ب. حماية البيانات (Data Security)
*   **Data Classification:** تصنيف البيانات (Public, Internal, Confidential) وتطبيق سياسات الحماية بناءً على التصنيف.
*   **Data Loss Prevention (DLP):** تطبيق سياسات لمنع تسرب البيانات الحساسة إلى بيئات غير مصرح بها.

---

## 5. الحوكمة والامتثال (Governance & Compliance)

إدارة السياسات عبر بيئات متعددة تتطلب أدوات مركزية.

### أ. إدارة السياسات (Policy Management)
*   استخدام **Azure Policy** أو أدوات مطابقة السياسات (Policy-as-Code) لفرض معايير أمنية موحدة.
*   **مثال:** منع إنشاء أي خادم سحابي (VM) في منطقة جغرافية غير مسموحة، أو إجبار تشفير الأقراص الصلبة (Encryption at Rest).

### ب. إدارة المخاطر والامتثال (Risk & Compliance Management)
*   استخدام منصات مثل **Microsoft Purview** أو **Microsoft Defender for Cloud** لمراقبة الامتثال المستمر (Continuous Compliance).
*   التأكد من توافق البيانات مع القوانين المحلية والدولية (مثل GDPR أو PDPL) بغض النظر عن مكان تخزين البيانات.

---

## 6. المراقبة والاستجابة للحوادث (Security Operations)

في العام 2026، أصبحت المراقبة الموحدة (Unified Monitoring) ضرورة حتمية.

### أ. المراقبة المركزية (Centralized Monitoring)
*   **Microsoft Sentinel:** استخدام منصة SIEM (Security Information and Event Management) مبنية على السحابة لجمع السجلات (Logs) من السحابة المحلية، AWS، و Google Cloud في مكان واحد.
*   **Microsoft Defender for Cloud:** دمج أدوات الحماية (CSPM - Cloud Security Posture Management) و (CWPP - Cloud Workload Protection Platform) لمراقبة الثغرات (Vulnerabilities) والتهديدات.

### ب. الاستجابة الآلية (Automated Response)
*   استخدام **Playbooks** (عبر Azure Logic Apps) للاستجابة التلقائية للحوادث الأمنية عبر البيئات المختلفة.
*   **مثال:** عند اكتشاف هجوم DDoS على AWS، يقوم النظام تلقائياً بتفعيل حواجز الحماية في Azure وتعليق الحسابات المخترقة في Entra ID.

---

## 7. التقنيات الحديثة والأدوات (Modern Tools & Technologies)

يجب على المهندس المعماري (Architect) الإلمام بالأدوات التالية لدعم الـ SC-100:

1.  **Azure Arc:** أداة حاسمة تربط موارد الحوسبة (Servers, Kubernetes, Databases) سواء كانت في السحابة المحلية أو سحابة أخرى، لتمكين إدارة وسياسات Azure عليها.
2.  **Microsoft Entra ID:** مركز الهوية الذي يربط بين البيئات المختلفة.
3.  **Kubernetes Security (K8s):** حماية الحاويات (Containers) في البيئات متعددة السحابة باستخدام أدوات مثل **Azure Kubernetes Service (AKS)** أو **OpenShift**.
4.  **Infrastructure as Code (IaC):** استخدام **Terraform** أو **Bicep** لضمان أن البنية الأمنية يتم نشرها بشكل متسق وآمن دون تدخل يدوي.

---

## 8. ملخص للنقاط الحرجة في الامتحان (Exam Focus)

عند التقدم لامتحان **SC-100**، ركز على فهمك للسيناريوهات التالية:
*   كيفية تصميم هوية موحدة (Unified Identity) لبيئة هجينة.
*   كيفية تأمين الاتصال بين السحابة المحلية والسحابة العامة (Secure Connectivity).
*   كيفية تطبيق سياسات الحوكمة (Governance) عبر بيئات متعددة.
*   كيفية اختيار أدوات المراقبة (Monitoring) المناسبة لتكامل البيانات (Data Integration).

---
*تم إعداد هذا المحتوى بناءً على أحدث معايير الأمن السيبراني لعام 2026 ومعايير الامتحان SC-100.*
