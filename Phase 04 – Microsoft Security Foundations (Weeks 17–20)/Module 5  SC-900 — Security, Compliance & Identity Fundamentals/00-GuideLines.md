## 01. Zero Trust Model

The Zero Trust model shifts security from a traditional "castle-and-moat" perimeter defense to a proactive, continuous verification strategy. It operates on the assumption that threats exist both outside and inside the network boundary.

- **Three Core Principles:**
    
    - **Verify Explicitly:** Always authenticate and authorize based on all available data points, including user identity, location, device health, service or workload, data classification, and anomalies.
        
    - **Use Least Privilege Access:** Limit user access with Just-In-Time (JIT) and Just-Enough-Access (JEA) models, risk-based adaptive polices, and data protection to secure both data and productivity.
        
    - **Assume Breach:** Minimize blast radius and segment access. Utilize end-to-end encryption, continuous analytics, and threat detection to improve defenses.
        
- **The Six Pillars (Foundational Components):** Identities, Devices, Applications, Data, Infrastructure, and Networks.
    

## 02. Microsoft Entra ID Basics

Microsoft Entra ID (formerly Azure Active Directory) is a cloud-based identity and access management service that serves as the backbone of Microsoft's cloud security ecosystem.

- **Identity Types:**
    
    - **Human Identities:** Internal users (employees) and external identities (partners, vendors, customers via Entra External ID).
        
    - **Non-Human Identities:** Service Principals, Managed Identities (system-assigned and user-assigned for secure resource-to-resource authentication without secrets), and Devices.
        
- **Hybrid Identity Models:** Managing how on-premises Active Directory Domain Services (AD DS) integrates with Microsoft Entra ID using **Microsoft Entra Connect** (Password Hash Synchronization, Pass-Through Authentication, or Federation).
    
- **Tenant Concept:** A dedicated, trusted instance of Microsoft Entra ID created automatically when an organization signs up for a Microsoft cloud service subscription.
    

## 03. Authentication vs. Authorization

Understanding the distinct boundaries between verifying identity and granting permissions is crucial for implementing secure access control loops.

- **Authentication (AuthN):** The process of proving an identity claim (verifying _who_ you are).
    
    - **Multi-Factor Authentication (MFA):** Requiring two or more elements from distinct categories: something you know (password), something you have (FIDO2 key, authenticator app), and something you are (biometrics).
        
    - **Passwordless Authentication:** Transitioning away from passwords using Windows Hello for Business, Microsoft Authenticator, or FIDO2 security keys.
        
- **Authorization (AuthZ):** The process of granting an authenticated identity permission to do something (verifying _what_ you are allowed to do).
    
    - **Role-Based Access Control (RBAC):** Fine-grained access management where permissions are assigned to specific roles (e.g., Reader, Contributor, Owner) scoped to management groups, subscriptions, resource groups, or individual resources.
        

## 04. Microsoft Defender Solutions

Microsoft Defender provides an integrated suite of Extended Detection and Response (XDR) solutions designed to prevent, detect, and respond to multi-stage attacks across diverse vectors.

- **Microsoft Defender XDR (Unified Portal):** Combines signals across endpoints, identities, email, and cloud apps into a centralized incident view.
    
- **Core Component Solutions:**
    
    - **Defender for Identity:** Monitors on-premises Active Directory signals to identify advanced threats, compromised identities, and malicious insider actions.
        
    - **Defender for Endpoint:** An enterprise endpoint security platform delivering preventative protection, post-breach detection, and automated investigation.
        
    - **Defender for Office 365:** Safeguards email and collaboration tools (Teams, SharePoint, OneDrive) against advanced threats like phishing and business email compromise.
        
    - **Defender for Cloud Apps:** A Cloud Access Security Broker (CASB) that provides visibility into shadow IT, enforces data protection policies, and assesses compliance across cloud environments.
        
    - **Microsoft Defender for Cloud:** A Cloud Security Posture Management (CSPM) and Cloud Workload Protection Platform (CWPP) that secures multi-cloud and hybrid environments (Azure, AWS, GCP).
        

## 05. Compliance Manager

Compliance Manager is a feature within the Microsoft Purview compliance portal that helps organizations track, assign, and verify regulatory compliance activities related to Microsoft cloud services.

- **Compliance Score:** A risk-based, quantified measure calculated based on the completion of specific improvement actions aimed at reducing data protection risks and meeting regulatory standards.
    
- **Improvement Actions:** Actionable recommendations provided by Microsoft to improve compliance posture. These are divided into **Microsoft-managed actions** (infrastructure security handled by Microsoft) and **Customer-managed actions** (configurations required by your organization).
    
- **Templates and Assessments:** Ready-to-use blueprints for setting up assessments aligned with specific global, national, and industry-specific regulations (such as GDPR, ISO 27001, HIPAA, or NIST).
    

## 06. Information Protection Basics

Information protection focuses on discovering, classifying, and safeguarding sensitive information throughout its lifecycle, ensuring data remains secure regardless of where it travels.

- **Data Lifecycle Phases:** Know your data (discovery), protect your data (encryption and restrictions), and prevent data loss.
    
- **Sensitivity Labels:** Persistent metadata tags applied manually or automatically to documents and emails. They enforce specific actions such as visual markings (watermarks, headers), encryption, and access restrictions that remain with the file even when shared externally.
    
- **Data Loss Prevention (DLP):** Policies configured to detect and prevent the accidental or unauthorized sharing of sensitive data (like financial records, credit card numbers, or internal intellectual property) across endpoints, cloud apps, emails, and networks.

---

## 01. نموذج الثقة الصفرية (Zero Trust Model)

ينقل نموذج الثقة الصفرية (Zero Trust) المفهوم الأمني من الدفاع التقليدي عن المحيط المستند إلى أسلوب "القلعة والخندق" إلى استراتيجية تحقق استباقية ومستمرة. وهو يعمل على افتراض أن التهديدات موجودة بالفعل داخل حدود الشبكة وخارجها على حد سواء.

- **المبادئ الثلاثة الأساسية:**
    
    - **التحقق الصريح (Verify Explicitly):** المصادقة والترخيص دائمًا بناءً على جميع نقاط البيانات المتاحة، بما في ذلك هوية المستخدم، والموقع، وسلامة الجهاز، والخدمة أو حمل العمل، وتصنيف البيانات، وحالات الشذوذ.
        
    - **استخدام وصول الحد الأدنى من الصلاحيات (Use Least Privilege Access):** تقييد وصول المستخدم باستخدام نماذج الوصول في الوقت المناسب (Just-In-Time - JIT) والوصول الكافي فقط (Just-Enough-Access - JEA)، والسياسات التكيفية القائمة على المخاطر، وحماية البيانات لتأمين البيانات والإنتاجية معًا.
        
    - **افتراض الاختراق (Assume Breach):** تقليل نطاق التأثير (Blast Radius) وتقسيم الوصول. الاستفادة من التشفير الشامل (End-to-End Encryption)، والتحليلات المستمرة، ورصد التهديدات لتحسين الدفاعات.
        
- **الركائز الست (المكونات التأسيسية):** الهويات (Identities)، والأجهزة (Devices)، والتطبيقات (Applications)، والبيانات (Data)، والبنية التحتية (Infrastructure)، والشبكات (Networks).
    

## 02. أساسيات Microsoft Entra ID

إن Microsoft Entra ID (المعروف سابقًا باسم Azure Active Directory) هو خدمة سحابية لإدارة الهويات والوصول، تمثل الركيزة الأساسية لمنظومة الأمان السحابي من Microsoft.

- **أنواع الهويات:**
    
    - **الهويات البشرية:** المستخدمون الداخليون (الموظفون) والهويات الخارجية (الشركاء، والموردون، والعملاء عبر Entra External ID).
        
    - **الهويات غير البشرية:** معرفات الخدمة الأساسية (Service Principals)، والهويات المدارة (Managed Identities) - بنوعيها المعينة من قِبل النظام والمعينة من قِبل المستخدم للمصادقة الآمنة من مورد إلى آخر دون استخدام أسرار/كلمات مرور - والأجهزة.
        
- **نماذج الهوية الهجينة (Hybrid Identity Models):** إدارة كيفية تكامل خدمات الدليل النشط المحلية (AD DS) مع Microsoft Entra ID باستخدام أداة **Microsoft Entra Connect** (عبر مزامنة تجزئة كلمة المرور، أو المصادقة الممررة، أو الاتحاد/Federation).
    
- **مفهوم المستأجر (Tenant Concept):** مثيل مخصص وموثوق من Microsoft Entra ID يتم إنشاؤه تلقائيًا عندما تسجل المؤسسة في اشتراك خدمات Microsoft السحابية.
    

## 03. المصادقة مقابل التخويل (Authentication vs. Authorization)

يعد فهم الحدود الفاصلة والمميزة بين التحقق من الهوية ومنح الأذونات أمرًا بالغ الأهمية لتنفيذ حيازات التحكم الآمن في الوصول.

- **المصادقة (Authentication - AuthN):** عملية إثبات ادعاء الهوية (التحقق من _من تكون_).
    
    - **المصادقة متعددة العوامل (MFA):** اشتراط عنصرين أو أكثر من فئات مميزة: شيء تعرفه (كلمة المرور)، شيء تملكه (مفتاح FIDO2، تطبيق المصادقة)، وشيء يمثلك (المقاييس الحيوية/البصمة).
        
    - **المصادقة بدون كلمة مرور (Passwordless Authentication):** الانتقال بعيدًا عن كلمات المرور باستخدام Windows Hello for Business، أو Microsoft Authenticator، أو مفاتيح الأمان FIDO2.
        
- **التخويل (Authorization - AuthZ):** عملية منح الهوية التي تم مصادقتها الإذن للقيام بإجراء ما (التحقق مما _يُسمح لك_ بالقيام به).
    
    - **التحكم في الوصول القائم على الأدوار (RBAC):** إدارة الوصول بدقة وتفصيل حيث يتم تعيين الأذونات لأدوار محددة (مثل: القارئ، المساهم، المالك) بنطاق محدد لمجموعات الإدارة، أو الاشتراكات، أو مجموعات الموارد، أو الموارد الفردية.
        

## 04. حلول Microsoft Defender

يوفر Microsoft Defender مجموعة متكاملة من حلول الكشف والاستجابة الموسعة (XDR) المصممة لمنع الهجمات متعددة المراحل عبر ناقلات التهديد المتنوعة، واكتشافها، والاستجابة لها.

- **Microsoft Defender XDR (البوابة الموحدة):** يدمج الإشارات عبر الأجهزة الطرفية، والهويات، والبريد الإلكتروني، والتطبيقات السحابية في عرض مركزي واحد للحوادث.
    
- **حلول المكونات الأساسية:**
    
    - **Defender for Identity:** يراقب إشارات الدليل النشط المحلي (Active Directory) لتحديد التهديدات المتقدمة، والهويات المخترقة، والإجراءات الضارة للمخترقين الداخليين.
        
    - **Defender for Endpoint:** منصة لأمن الأجهزة الطرفية للمؤسسات تقدم حماية وقائية، واكتشاف ما بعد الاختراق، والتحقيق التلقائي.
        
    - **Defender for Office 365:** يحمي البريد الإلكتروني وأدوات التعاون (Teams و SharePoint و OneDrive) من التهديدات المتقدمة مثل التصيد الاحتيالي واختراق البريد الإلكتروني للأعمال.
        
    - **Defender for Cloud Apps:** وسيط أمان الوصول إلى السحاب (CASB) يوفر رؤية شاملة لتقنية المعلومات غير المصرح بها (Shadow IT)، ويفرض سياسات حماية البيانات، ويقيم الامتثال عبر البيئات السحابية.
        
    - **Microsoft Defender for Cloud:** نظام إدارة وضع الأمان السحابي (CSPM) ومنصة حماية حمل العمل السحابي (CWPP) لتأمين البيئات متعددة السحاب والبيئات الهجينة (Azure و AWS و GCP).
        

## 05. مدير الامتثال (Compliance Manager)

مدير الامتثال هو ميزة داخل بوابة امتثال Microsoft Purview تساعد المؤسسات على تتبع أنشطة الامتثال التنظيمي وتعيينها والتحقق منها فيما يتعلق بخدمات Microsoft السحابية.

- **درجة الامتثال (Compliance Score):** مقياس كمي قائم على المخاطر يتم احتسابه بناءً على إكمال إجراءات تحسين محددة تهدف إلى تقليل مخاطر حماية البيانات وتلبية المعايير التنظيمية.
    
- **إجراءات التحسين (Improvement Actions):** توصيات قابلة للتنفيذ تقدمها Microsoft لتحسين وضع الامتثال. وتنقسم هذه الإجراءات إلى **إجراءات تدار بواسطة Microsoft** (أمن البنية التحتية الذي تتولاه Microsoft) و**إجراءات يدار من قبل العميل** (التكوينات والإعدادات المطلوبة من قِبل مؤسستك).
    
- **القوالب والتقييمات:** مخططات جاهزة للاستخدام لإعداد تقييمات متوافقة مع لوائح تنظيمية عالمية، ووطنية، وقطاعية محددة (مثل: GDPR أو ISO 27001 أو HIPAA أو NIST).
    

## 06. أساسيات حماية المعلومات

تركز حماية المعلومات على اكتشاف المعلومات الحساسة وتصنيفها وحمايتها طوال دورة حياتها، مما يضمن بقاء البيانات آمنة بغض النظر عن المكان الذي تنتقل إليه.

- **مراحل دورة حياة البيانات:** معرفة بياناتك (الاكتشاف)، وحماية بياناتك (التشفير والقيود)، ومنع فقدان البيانات.
    
- **تسميات الحساسية (Sensitivity Labels):** علامات بيانات وصفية ثابتة (Metadata tags) يتم تطبيقها يدويًا أو تلقائيًا على المستندات ورسائل البريد الإلكتروني. وهي تفرض إجراءات محددة مثل العلامات المرئية (العلامات المائية، الرؤوس)، والتشفير، وقيود الوصول التي تظل مصاحبة للملف حتى عند مشاركته خارجيًا.
    
- **منع فقدان البيانات (DLP):** سياسات يتم تكوينها لاكتشاف ومنع المشاركة العرضية أو غير المصرح بها للبيانات الحساسة (مثل السجلات المالية، أو أرقام بطاقات الائتمان، أو الملكية الفكرية الداخلية) عبر الأجهزة الطرفية، والتطبيقات السحابية، ورسائل البريد الإلكتروني، والشبكات.