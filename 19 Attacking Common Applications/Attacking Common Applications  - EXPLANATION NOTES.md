
# Introduction to Attacking Common Applications 

This section introduces the importance of web applications in penetration testing and why they are one of the most common entry points during assessments.

Web applications follow a client-server architecture, where the browser handles the front-end and servers handle logic and data. Because they are widely exposed (especially with remote work trends), they have become prime targets for attackers.

The key idea is that **most applications—whether open-source, commercial, or custom—share the same vulnerability classes**, such as those listed in the OWASP Top 10. This means that learning how to exploit one system often translates to others.

The module emphasizes two critical attacker mindsets:

- **Do not rely only on known exploits**  
    Many real-world attacks succeed due to misconfigurations or abuse of built-in features rather than public CVEs.
    
- **Understand how applications work internally**  
    This allows you to adapt and find attack paths even in unfamiliar systems.

  
|**Category**|**Applications**|
|---|---|
|[Web Content Management](https://enlyft.com/tech/web-content-management)|Joomla, Drupal, WordPress, DotNetNuke, etc.|
|[Application Servers](https://enlyft.com/tech/application-servers)|Apache Tomcat, Phusion Passenger, Oracle WebLogic, IBM WebSphere, etc.|
|[Security Information and Event Management (SIEM)](https://enlyft.com/tech/security-information-and-event-management-siem)|Splunk, Trustwave, LogRhythm, etc.|
|[Network Management](https://enlyft.com/tech/network-management)|PRTG Network Monitor, ManageEngine Opmanger, etc.|
|[IT Management](https://enlyft.com/tech/it-management-software)|Nagios, Puppet, Zabbix, ManageEngine ServiceDesk Plus, etc.|
|[Software Frameworks](https://enlyft.com/tech/software-frameworks)|JBoss, Axis2, etc.|
|[Customer Service Management](https://enlyft.com/tech/customer-service-management)|osTicket, Zendesk, etc.|
|[Search Engines](https://enlyft.com/tech/search-engines)|Elasticsearch, Apache Solr, etc.|
|[Software Configuration Management](https://enlyft.com/tech/software-configuration-management)|Atlassian JIRA, GitHub, GitLab, Bugzilla, Bugsnag, Bitbucket, etc.|
|[Software Development Tools](https://enlyft.com/tech/software-development-tools)|Jenkins, Atlassian Confluence, phpMyAdmin, etc.|
|[Enterprise Application Integration](https://enlyft.com/tech/enterprise-application-integration)|Oracle Fusion Middleware, BizTalk Server, Apache ActiveMQ, etc.|


The hosts file configuration shown in the COMMAND NOTES is essential in lab environments. Since multiple virtual hosts (vhosts) are mapped to a single IP, adding them manually ensures proper resolution when accessing targets through their domain names.

Failure to configure this correctly can prevent access to applications, making it a common beginner pitfall.

Finally, the section highlights how common applications (WordPress, Jenkins, Tomcat, GitLab, etc.) are frequently encountered in real engagements. Many of them:

- Have large attack surfaces
    
- Use plugins/extensions that introduce vulnerabilities
    
- Allow functionality abuse (e.g., script execution, task schedulers)
    

The takeaway is clear:  
**Applications should never be ignored during an assessment-they are often the easiest path to initial access or lateral movement.


# Application Discovery & Enumeration 

This section focuses on building a **structured and efficient methodology for discovering web applications** across a target environment.

The process starts with **initial enumeration using port scanning** (see _NMAP SCAN_). Instead of scanning everything blindly, the focus is on **common web ports**, allowing quick identification of systems likely hosting web applications.

The _SCOPE FILE_ represents the starting point of a real engagement—targets provided by the client. These include multiple subdomains and an IP address, simulating a realistic corporate environment.

After identifying live hosts and open ports, the next step is **service enumeration** (see _NMAP SCAN with -sV_). This reveals technologies behind services (e.g., IIS, Splunk, PRTG), which helps prioritize targets and identify potential attack vectors.

However, manually visiting each discovered host is inefficient. This is where tools like **EyeWitness and Aquatone** come in:

- **EyeWitness** (see _EYEWITNESS RUN_)  
    Converts scan results into a visual report with screenshots, application fingerprinting, and possible default credentials. This dramatically speeds up identifying interesting targets.
    
- **Aquatone** (see _AQUATONE RUN_)  
    Performs similar functionality but adds clustering and response analysis, helping identify patterns across hosts.
    

The workflow chain looks like this:

1. **Collect targets** → (Scope)
    
2. **Scan for web services** → (Nmap)
    
3. **Identify technologies** → (Service scan)
    
4. **Visualize attack surface** → (EyeWitness / Aquatone)
    
5. **Prioritize targets** → (Manual analysis)
    

A key mindset emphasized here is:

- **Enumeration is iterative**  
    You don’t scan once—you refine and rescan based on findings.
    
- **Do not rely only on automated tools**  
    Scanners provide data, but **real vulnerabilities are often found through manual analysis**.
    
- **Stay organized**  
    Proper note-taking, timestamping scans, and structuring findings ensures nothing is missed and simplifies reporting later.
    

Important attacker insights from this section:

- Hosts labeled **“dev”** often contain weaker security controls (debug modes, test features)
    
- Tools like GitLab may expose sensitive data (repos, credentials)
    
- Applications like Tomcat or Jenkins may allow direct code execution if misconfigured
    
- Even simple upload features can lead to full compromise if validation is weak
    

The overall goal is to **transform raw scan data into actionable intelligence**, building a clear map of the application attack surface before exploitation begins.



# WordPress - Discovery & Enumeration

This section focuses on identifying and enumerating WordPress installations, which are extremely common and often vulnerable due to plugins, themes, and misconfigurations.

The process begins with **basic discovery techniques**. Files like `robots.txt` (see _ROBOTS.TXT ENUMERATION_) often reveal WordPress-specific paths such as `/wp-admin` and `/wp-content`, confirming the CMS in use.

The login panel (see _WORDPRESS LOGIN_) is another key indicator and also becomes a future attack surface for brute-force or credential attacks.

Next comes **manual enumeration using HTTP requests**. By inspecting page content (see _CURL WORDPRESS DETECTION_), attackers can identify the WordPress version, which is critical for vulnerability research.

Themes and plugins are then enumerated (see _THEME ENUMERATION_ and _PLUGIN ENUMERATION_). These components are especially important because:

- Plugins account for the majority of WordPress vulnerabilities
    
- Themes may expose additional attack surfaces
    
- Version numbers can reveal known exploits
    

Checking multiple pages (see _PLUGIN ENUMERATION (SECOND PAGE)_) helps uncover hidden plugins not visible on the homepage.

Accessing plugin directories directly (see _PLUGIN DIRECTORY ENUMERATION_) can expose sensitive files like `readme.txt`, which often contain version information and sometimes even vulnerabilities.

User enumeration is also possible due to differences in login error messages, allowing attackers to confirm valid usernames—an important step for password attacks.

Automation complements manual work using tools like **WPScan** (see _WPSCAN ENUMERATION_). WPScan:

- Identifies WordPress version
    
- Enumerates plugins, themes, and users
    
- Detects known vulnerabilities
    
- Highlights misconfigurations (e.g., directory listing, XML-RPC enabled)
    

However, a key lesson is that **automation is not enough**. WPScan may miss certain plugins or misinterpret themes, so combining manual and automated techniques is essential.

Key attacker mindset:

- Always confirm CMS type before deeper testing
    
- Focus heavily on plugins and themes (largest attack surface)
    
- Extract version numbers whenever possible
    
- Do not jump into exploitation too early—fully enumerate first
    
- Use multiple sources (manual + automated) for accuracy
    

The methodology chain becomes:

1. Identify WordPress
    
2. Locate login and core paths
    
3. Enumerate version
    
4. Identify themes/plugins
    
5. Extract versions
    
6. Enumerate users
    
7. Validate findings with WPScan
    
8. Map vulnerabilities → prepare for exploitation
    

This structured approach ensures maximum coverage and sets the foundation for successful WordPress exploitation.

# Attacking WordPress

This section transitions from enumeration to **active exploitation of WordPress**, demonstrating multiple attack paths that can lead to full system compromise.

The first step is **credential access via brute force** (see _WPSCAN BRUTEFORCE_). Using known usernames from enumeration, attackers leverage WPScan to perform password attacks. The XML-RPC method is preferred because it is faster and less detectable compared to traditional login attempts.

Once valid credentials are obtained, attackers move to **post-authentication exploitation**. With admin-level access, WordPress allows direct editing of theme files. By injecting a simple PHP command execution payload (see _WORDPRESS WEBSHELL_), attackers create a web shell.

This shell is then accessed remotely (see _WEBSHELL EXECUTION_), allowing execution of system commands. This provides an initial foothold on the server.

For automation and more advanced access, **Metasploit** can be used (see _METASPLOIT MODULE_). This module uploads a malicious plugin and establishes a reverse shell, giving interactive access to the system. It simplifies exploitation but also highlights the importance of cleaning up artifacts afterward.

The section also demonstrates **exploitation of vulnerable plugins**, which represent the largest attack surface in WordPress:

- **mail-masta (LFI vulnerability)**  
    The plugin allows arbitrary file inclusion due to lack of input validation (see _MAIL-MASTA LFI_). This enables attackers to read sensitive files like `/etc/passwd`.
    
- **wpDiscuz (RCE via file upload bypass)**  
    This vulnerability allows attackers to upload malicious PHP files despite restrictions (see _WPDISCUZ EXPLOIT_). Once uploaded, the file acts as a web shell (see _WPDISCUZ WEBSHELL EXECUTION_), enabling command execution.
    

Key attacker insights:

- Credential attacks are often the easiest entry point
    
- Admin access in WordPress equals near-complete control
    
- Plugins are the most common source of critical vulnerabilities
    
- File upload flaws frequently lead to remote code execution
    
- Manual verification is sometimes required even after automated exploitation
    

The full attack chain typically follows:

1. Enumerate users and plugins
    
2. Brute-force credentials
    
3. Gain admin access
    
4. Upload or inject web shell
    
5. Execute commands → gain foothold
    
6. Escalate privileges and move laterally
    

This section highlights how **multiple weak points can chain together** into a full compromise, reinforcing the importance of thorough enumeration and methodical exploitation.


# Joomla - Discovery & Enumeration

This section focuses on identifying and enumerating Joomla installations, another widely used CMS with a significant attack surface.

The process begins with **CMS fingerprinting**. By inspecting the page source (see _JOOMLA DETECTION_), attackers can confirm Joomla through metadata tags. This is the first step before deeper enumeration.

The **robots.txt file** (see _ROBOTS.TXT ENUMERATION_) is particularly useful in Joomla, as it exposes many internal directories such as `/administrator`, `/components`, and `/plugins`. These paths help map the application structure and identify potential attack surfaces.

Next comes **version enumeration**, which is critical for vulnerability mapping. This can be done through:

- README files (see _README VERSION ENUMERATION_)
    
- XML manifests (see _XML VERSION ENUMERATION_)
    
- JavaScript or cache files
    

Knowing the exact version allows attackers to search for known exploits.

For automation, tools like **Droopescan** (see _DROOPESCAN ENUMERATION_) help identify:

- Possible Joomla versions
    
- Interesting endpoints (e.g., admin panel, manifests)
    
- Exposed files
    

Although useful, droopescan often provides approximate results, so manual verification is still required.

Another tool, **JoomlaScan** (see _JOOMLASCAN_), helps enumerate:

- Components (extensions)
    
- Accessible directories
    
- Potentially exposed files
    

Even though it is outdated, it can still uncover useful information about installed components.

The **administrator portal** (see _JOOMLA ADMIN LOGIN_) is the primary attack surface for authentication attacks. Unlike WordPress, Joomla does not easily allow username enumeration due to generic error messages, making brute force more challenging.

However, weak credentials remain a common issue. Using brute force tools (see _JOOMLA BRUTEFORCE_), attackers can attempt login with common passwords. In this case, default credentials (`admin:admin`) were successfully identified, highlighting poor security practices.

Key attacker insights:

- Joomla can be reliably fingerprinted via metadata and structure
    
- robots.txt reveals valuable internal paths
    
- Version enumeration is essential for exploit research
    
- Extensions (components) expand the attack surface
    
- Lack of username enumeration increases reliance on password attacks
    
- Weak/default credentials are still common in real environments
    

The methodology flow is:

1. Identify Joomla
    
2. Extract directory structure
    
3. Determine version
    
4. Enumerate components/extensions
    
5. Locate admin panel
    
6. Attempt authentication attacks
    

This structured approach ensures effective discovery and prepares for exploitation in later stages.


# Attacking Joomla 

This section demonstrates how to move from enumeration to **full compromise of a Joomla application**, primarily by abusing built-in functionality and known vulnerabilities.

The first attack path is **leveraging valid credentials**. Once access to the admin panel is obtained (see _JOOMLA ADMIN PANEL_), the attacker gains significant control over the application.

Joomla allows administrators to **modify templates directly from the backend**, which becomes a powerful exploitation vector. By editing template files such as `error.php` (see _TEMPLATE EDITOR_), attackers can inject a PHP payload (see _JOOMLA WEBSHELL_).

This payload enables **remote command execution**, which is then triggered via HTTP requests (see _WEBSHELL EXECUTION_). This effectively provides a web shell and initial system access.

An important practical detail is handling errors in the admin panel. Disabling problematic plugins (see _PLUGIN FIX_) ensures the interface works properly, which is necessary to continue exploitation.

The second attack path involves **leveraging known vulnerabilities**, specifically a Joomla core vulnerability (CVE-2019-10945). Using an exploit script (see _JOOMLA DIRECTORY TRAVERSAL EXPLOIT_), attackers can:

- Traverse directories
    
- List files in the web root
    
- Access sensitive files (e.g., configuration files)
    
- Potentially delete files
    

While this vulnerability requires authentication, it becomes highly useful when admin access is already obtained but direct code execution is not possible.

Key attacker insights:

- Admin access in Joomla allows direct file modification → leads to RCE
    
- Template editing is a built-in feature that can be abused easily
    
- Web shells should be hidden (non-obvious names/parameters)
    
- Always clean up artifacts after exploitation
    
- Joomla core vulnerabilities are rarer than extension vulnerabilities
    
- Directory traversal can expose sensitive configuration and credentials
    

The attack chain typically looks like:

1. Obtain valid credentials
    
2. Access admin panel
    
3. Modify template files
    
4. Inject web shell
    
5. Execute commands → gain foothold
    

Alternative path:

1. Obtain credentials
    
2. Exploit directory traversal
    
3. Access sensitive files
    
4. Pivot to further exploitation
    

This section highlights how **misuse of legitimate functionality** can be just as dangerous as exploiting vulnerabilities, reinforcing the importance of secure configurations and strong credential policies.



# Drupal - Discovery & Enumeration

This section focuses on identifying and enumerating Drupal, the third major CMS commonly encountered during penetration testing.

The first step is **fingerprinting the CMS**. Drupal can often be identified through page source indicators such as meta tags or footer messages (see _DRUPAL DETECTION_). These reveal that the application is powered by Drupal and sometimes include version hints.

Another strong indicator is Drupal’s **node-based URL structure** (see _NODE ENUMERATION_). URLs in the format `/node/<id>` are characteristic of Drupal and can help confirm its presence even when custom themes obscure other indicators.

Once Drupal is identified, the next step is **version enumeration**, which is critical for mapping vulnerabilities. This can be done by accessing files like `CHANGELOG.txt` (see _CHANGELOG VERSION ENUMERATION_). Older Drupal installations often expose this file, revealing exact version details.

However, newer versions may block access to such files, requiring alternative techniques such as:

- Inspecting page source
    
- Analyzing HTTP responses
    
- Using automated tools
    

For automation, **Droopescan** (see _DROOPESCAN ENUMERATION_) is particularly effective for Drupal. It can:

- Identify possible Drupal versions
    
- Detect installed modules
    
- Locate admin login pages
    
- Highlight interesting endpoints
    

Unlike its limited Joomla capabilities, droopescan provides more reliable results for Drupal environments.

User roles in Drupal also define attack surface:

- **Administrator**: Full control → high-value target
    
- **Authenticated users**: Limited but useful access
    
- **Anonymous users**: Default visitors
    

Understanding these roles helps prioritize attack strategies.

Key attacker insights:

- Drupal can be fingerprinted through multiple subtle indicators
    
- Node-based URLs are a strong identifying feature
    
- Version enumeration may require multiple techniques
    
- Automated tools enhance but do not replace manual analysis
    
- Core vulnerabilities may be limited → modules become key targets
    

The methodology flow is:

1. Identify Drupal
    
2. Confirm via nodes and source
    
3. Attempt version enumeration
    
4. Use automated tools (droopescan)
    
5. Identify modules and entry points
    
6. Prepare for exploitation
    

This structured approach ensures accurate identification and thorough enumeration, setting the stage for effective attacks against Drupal environments.


# Attacking Drupal

This section explores multiple ways to achieve **remote code execution (RCE) in Drupal**, combining misconfigurations, module abuse, and known critical vulnerabilities.

The first technique leverages the **PHP Filter module** (see _PHP FILTER WEBSHELL_). In older Drupal versions, this module allows execution of embedded PHP code within pages. Once enabled, attackers can create content containing malicious PHP and execute commands via HTTP requests (see _WEBSHELL EXECUTION_).

In newer versions where the module is not installed, attackers can **manually install it** (see _PHP FILTER MODULE DOWNLOAD_), highlighting how administrative access can still lead to RCE even when protections are in place.

Another powerful method is **uploading a backdoored module**. By modifying a legitimate module (see _BACKDOORED MODULE DOWNLOAD_ and _WEBSHELL FILE_), attackers embed a web shell and repackage it. The addition of an `.htaccess` file ensures access to restricted directories (see _HTACCESS FILE_). Once installed, the attacker gains direct command execution (see _MODULE WEBSHELL EXECUTION_).

The section also covers three major Drupal vulnerabilities known as **Drupalgeddon**:

- **Drupalgeddon (CVE-2014-3704)**  
    A pre-authentication SQL injection that allows attackers to create admin accounts (see _DRUPALGEDDON (ADMIN CREATION)_). This bypasses authentication entirely and leads to full control.
    
- **Drupalgeddon2 (CVE-2018-7600)**  
    A remote code execution vulnerability caused by insufficient input sanitization. Attackers can upload arbitrary files, including web shells, by crafting payloads (see _DRUPALGEDDON2 (BASE64 PAYLOAD)_ and execution steps). This does not require authentication.
    
- **Drupalgeddon3 (CVE-2018-7602)**  
    An authenticated RCE requiring a valid session (see _DRUPALGEDDON3 (METASPLOIT)_). Once exploited, it provides a reverse shell through Metasploit.
    

Key attacker insights:

- Drupal is harder to exploit via admin UI compared to WordPress/Joomla, but still exploitable
    
- Modules are a major attack vector and can be weaponized
    
- Admin access enables installation of malicious functionality
    
- Critical vulnerabilities (Drupalgeddon series) can lead to full compromise
    
- Pre-auth vulnerabilities are especially dangerous
    
- Obfuscating parameters reduces risk of unintended exposure during testing
    

The attack chains vary:

**Misconfiguration path:**

1. Gain admin access
    
2. Enable/install PHP execution
    
3. Inject web shell
    
4. Execute commands
    

**Module abuse path:**

1. Gain admin access
    
2. Upload backdoored module
    
3. Execute web shell
    

**Vulnerability path:**

1. Exploit Drupalgeddon
    
2. Gain admin or direct RCE
    
3. Execute commands → foothold
    

This section highlights how Drupal, while more restrictive by default, can still be compromised through **creative abuse of features and critical vulnerabilities**.



# Tomcat - Discovery & Enumeration 

This section focuses on identifying and enumerating Apache Tomcat, a widely used Java-based web server often encountered during internal and sometimes external penetration tests.

The first step is **fingerprinting Tomcat**. This can be done by inspecting HTTP responses or accessing default pages such as the documentation endpoint (see _TOMCAT DETECTION (DOCS PAGE)_). These pages often reveal the exact version, which is critical for vulnerability research.

When version leakage is not available via headers or error pages, default endpoints like `/docs` become valuable indicators, as they are frequently left exposed.

Next comes **directory enumeration** (see _GOBUSTER ENUMERATION_). This helps identify important Tomcat-specific paths such as:

- `/manager` → administrative interface
    
- `/host-manager` → host management interface
    
- `/examples` → sample applications (often vulnerable)
    

These endpoints are high-value targets because they may allow authentication attempts or direct exploitation.

A critical configuration file in Tomcat is `tomcat-users.xml` (see _TOMCAT USERS CONFIG_). This file defines:

- Users
    
- Passwords
    
- Roles and permissions
    

Weak or default credentials (e.g., `tomcat:tomcat`, `admin:admin`) are very common and can grant access to management interfaces.

Understanding the **Tomcat directory structure** is also essential. The `webapps` directory acts as the web root, where applications are deployed. Each application follows a structured format, including:

- `WEB-INF/web.xml` → defines routes and application logic
    
- `classes/` → compiled Java classes
    
- `lib/` → required libraries
    

The `web.xml` file (see _WEB.XML STRUCTURE_) is particularly important because it maps URLs to backend Java classes. This reveals:

- Application endpoints
    
- Internal logic
    
- Potential attack surfaces
    

If an attacker gains access to this file (e.g., via LFI), it can lead to full understanding of the application and possible compromise.

Key attacker insights:

- Tomcat is often found internally and may have weak configurations
    
- Default credentials are extremely common and should always be tested
    
- Management interfaces provide direct paths to exploitation
    
- The `web.xml` file exposes critical application logic
    
- Enumeration of directories is crucial for discovering hidden functionality
    

The methodology flow is:

1. Fingerprint Tomcat and identify version
    
2. Enumerate directories and endpoints
    
3. Locate management interfaces
    
4. Attempt authentication (default/weak creds)
    
5. Analyze configuration files and application structure
    
6. Prepare for exploitation (e.g., WAR upload, RCE)
    

This structured approach allows attackers to efficiently identify high-value entry points and move toward gaining code execution on Tomcat servers.

# Attacking Tomcat 

This section demonstrates how to move from discovery to **full exploitation of a Tomcat server**, primarily through weak credentials, file upload abuse, and known vulnerabilities.

The first step is **credential brute-forcing** against the Tomcat Manager interface (see _METASPLOIT TOMCAT BRUTEFORCE_). Since Tomcat commonly uses basic authentication, tools automate testing default credentials. Successful authentication grants access to the management panel, which is a critical turning point in the attack.

Debugging through a proxy (see _METASPLOIT PROXY DEBUG_) helps attackers understand how authentication works, including base64 encoding of credentials (see _BASE64 DECODE CHECK_). This reinforces understanding of the protocol rather than blindly relying on tools.

Once access is obtained, attackers exploit built-in functionality by **uploading a WAR file**. A WAR file is essentially a packaged web application. By embedding a JSP web shell (see _JSP WEBSHELL_), attackers can execute system commands via a browser or HTTP requests (see _WEBSHELL EXECUTION_).

This provides immediate remote code execution. For more advanced access, tools like msfvenom can generate a WAR payload that establishes a reverse shell (see _MSFVENOM WAR PAYLOAD_), allowing interactive control of the server.

Another attack path involves **Ghostcat (CVE-2020-1938)**, a vulnerability in the AJP protocol. By targeting port 8009 (see _AJP PORT SCAN_), attackers can exploit a misconfiguration to read sensitive files (see _GHOSTCAT LFI_). This can expose configuration files like `web.xml`, which may contain application logic and sensitive data.

Key attacker insights:

- Tomcat Manager is a high-value target due to direct deployment capabilities
    
- Weak/default credentials are extremely common and often overlooked
    
- WAR file upload leads directly to RCE
    
- JSP web shells are equivalent to PHP shells in Apache environments
    
- Reverse shells provide more stable and interactive access
    
- Ghostcat allows file disclosure without authentication in vulnerable setups
    

The attack chains are:

**Credential abuse path:**

1. Brute-force Tomcat Manager
    
2. Gain access
    
3. Upload WAR file
    
4. Execute JSP web shell → RCE
    

**Automated payload path:**

1. Gain access
    
2. Upload msfvenom WAR
    
3. Trigger reverse shell
    

**Vulnerability path (Ghostcat):**

1. Identify AJP service
    
2. Exploit LFI
    
3. Read sensitive files
    
4. Use gathered data for further compromise
    

This section highlights how Tomcat can quickly lead to **full system compromise**, especially when misconfigured or using weak credentials, making it a high-priority target in both internal and external assessments.



# Jenkins - Discovery & Enumeration

This section introduces Jenkins as a high-value target in penetration testing, especially in internal environments where it is commonly deployed.

The first step is **identification**. Jenkins can be easily recognized through its login interface (see _JENKINS LOGIN PAGE_), which has a distinct appearance. Additionally, it typically runs on port 8080, though in this lab it is exposed on port 8000 (see _JENKINS URL_).

Another important indicator is the **security configuration page** (see _JENKINS SECURITY CONFIG_). This page reveals how authentication is handled, which is critical for determining the attack path.

Jenkins supports multiple authentication methods:

- Internal user database
    
- LDAP or external directory services
    
- OS-level authentication
    
- No authentication (misconfiguration)
    

Misconfigurations are common, especially in internal environments. Attackers often find:

- Weak credentials (see _DEFAULT CREDENTIALS_)
    
- Default credentials left unchanged
    
- Completely unauthenticated dashboards
    

Once access is obtained, Jenkins becomes extremely dangerous because it often runs with **high privileges** (frequently SYSTEM on Windows or root-equivalent on Linux). This means any command execution can lead directly to full system compromise.

Port 5000 (see _COMMON PORTS_) is also notable, as it is used for communication between Jenkins master and agents. While not always directly exploitable, it can provide additional attack surface during deeper assessments.

Key attacker insights:

- Jenkins is a prime target for initial foothold in internal networks
    
- Authentication misconfigurations are very common
    
- Default credentials should always be tested first
    
- Gaining access often leads directly to high-privilege execution
    
- Enumeration should focus on authentication and exposed interfaces
    

The methodology flow is:

1. Identify Jenkins via interface and ports
    
2. Access login page
    
3. Check authentication configuration
    
4. Attempt default/weak credentials
    
5. Verify access level
    
6. Prepare for exploitation (command execution, job abuse)
    

This sets the stage for the next phase, where Jenkins functionality can be abused to achieve remote code execution and pivot further داخل the environment.

# Attacking Jenkins 

This section demonstrates how Jenkins can be quickly turned into a **remote code execution (RCE) vector** once access is obtained.

The most powerful feature is the **Script Console** (see _SCRIPT CONSOLE URL_). This interface allows administrators to execute Groovy code directly on the Jenkins server. Since Groovy runs on the JVM, it can interact with the underlying operating system.

The simplest use case is executing system commands (see _GROOVY COMMAND EXECUTION_). This confirms code execution and reveals the privilege level of the Jenkins process, which is often root or SYSTEM.

From there, attackers escalate to a **reverse shell** (see _GROOVY REVERSE SHELL (LINUX)_). This creates an outbound connection to the attacker’s machine (see _NETCAT LISTENER_), providing interactive shell access. This is more stable and flexible than running single commands.

For Windows environments, similar execution is possible using command-line utilities (see _GROOVY COMMAND EXECUTION (WINDOWS)_). A more advanced approach is using a **Java-based reverse shell** (see _JAVA REVERSE SHELL (WINDOWS)_), which establishes a persistent interactive session.

Beyond built-in functionality, Jenkins has also suffered from several critical vulnerabilities:

- Some allow **pre-authentication RCE**, meaning no login is required
    
- Others allow **sandbox bypass**, enabling execution of restricted scripts
    
- Certain versions allow users with limited permissions to escalate to full system access
    

However, even without exploiting vulnerabilities, **legitimate features alone are enough to compromise the system**.

Key attacker insights:

- Script Console = instant RCE if accessible
    
- Jenkins often runs with high privileges → full system compromise
    
- Reverse shells provide stable control compared to single commands
    
- Both Linux and Windows targets can be exploited similarly
    
- Misconfigurations (weak creds, open access) are more common than CVEs
    

Attack chain:

1. Gain access to Jenkins (weak/default creds)
    
2. Access Script Console
    
3. Execute command → confirm access
    
4. Launch reverse shell
    
5. Gain interactive control → pivot further
    

This highlights why Jenkins is considered a **critical asset** in penetration testing—once accessed, it often leads directly to complete system compromise.

# Splunk - Discovery & Enumeration 

This section focuses on identifying Splunk, a powerful log analytics platform that often becomes a **high-value target** during penetration tests.

The first step is **service identification** using scanning (see _NMAP SERVICE ENUMERATION_). Splunk is typically detected running on port 8000 for the web interface and port 8089 for its management API. These ports are strong indicators of a Splunk instance.

Once identified, accessing the web interface (see _SPLUNK URL_) reveals the login panel or, in some cases, direct access if authentication is disabled.

A critical misconfiguration to look for is **lack of authentication**. Splunk trial versions automatically convert to a free version after 60 days, which does not require login. This creates a major security risk because:

- Anyone can access the interface
    
- Sensitive logs and data become exposed
    
- Administrative functionality may still be available
    

If authentication is enabled, testers should attempt **default credentials** (see _DEFAULT CREDENTIALS_) and weak password combinations (see _COMMON PASSWORDS_). These are frequently successful in poorly secured environments.

Splunk is particularly dangerous because it often runs with **high privileges** (root on Linux or SYSTEM on Windows). This means any code execution vulnerability or feature abuse can lead to full system compromise.

Another important concept is **Splunk’s extensibility**. It allows:

- Installing apps from Splunkbase
    
- Creating custom scripts
    
- Running automated tasks (scripted inputs)
    

These features are designed for functionality but can be abused by attackers to execute arbitrary code.

Key attacker insights:

- Splunk is commonly found in internal networks
    
- Free version = no authentication → immediate access
    
- Default credentials are still widely used
    
- Runs with high privileges → high impact compromise
    
- Built-in features (not vulnerabilities) are often the real attack vector
    

The methodology flow is:

1. Identify Splunk via ports and service scan
    
2. Access web interface
    
3. Check for authentication bypass (free version)
    
4. Attempt default/weak credentials
    
5. Explore accessible functionality
    
6. Prepare for exploitation (scripted inputs, app upload)
    

This sets the stage for the next phase, where Splunk’s built-in capabilities can be abused to gain remote code execution and deeper access into the environment.


# Attacking Splunk

This section demonstrates how to exploit Splunk by abusing its **application and scripted input functionality** to achieve remote code execution.

The attack begins by creating a **custom Splunk application structure** (see _DIRECTORY STRUCTURE_). Splunk apps are modular, and this structure allows attackers to package malicious scripts for execution.

The key component is the **reverse shell payload**. On Windows systems, a PowerShell one-liner (see _POWERSHELL REVERSE SHELL_) is used to establish a connection back to the attacker. This payload is embedded inside the application.

Execution is controlled via the **inputs.conf file** (see _INPUTS.CONF_). This configuration tells Splunk to execute specific scripts at defined intervals. Without this file, the payload would not run.

A batch file (see _BATCH FILE_) is used as a launcher to execute the PowerShell script silently and bypass restrictions. This ensures the payload runs reliably once deployed.

The malicious application is then packaged (see _CREATE ARCHIVE_) and uploaded through the Splunk web interface (see _UPLOAD PATH_). Splunk allows installing custom apps, which becomes the primary attack vector.

Before uploading, a listener is prepared (see _NETCAT LISTENER_) to receive the incoming connection. Once the app is uploaded and enabled, Splunk automatically executes the configured scripts, triggering the reverse shell.

On Linux systems, a Python-based reverse shell (see _PYTHON REVERSE SHELL (LINUX)_) can be used instead, leveraging the fact that Splunk installations include Python by default.

Key attacker insights:

- Splunk apps provide a built-in mechanism for executing code
    
- Scripted inputs are designed for automation but can be abused for RCE
    
- Uploading a malicious app is often easier than exploiting vulnerabilities
    
- Splunk frequently runs as SYSTEM/root → high-impact compromise
    
- Reverse shells provide full interactive access to the host
    

An advanced consideration is when Splunk acts as a **deployment server**. In this case, malicious apps can be pushed to multiple connected systems, enabling lateral movement across the network.

Attack chain:

1. Access Splunk (no auth or weak creds)
    
2. Create malicious app structure
    
3. Embed reverse shell payload
    
4. Configure execution via inputs.conf
    
5. Upload app
    
6. Receive reverse shell → SYSTEM/root access
    

This highlights how **legitimate features can be weaponized**, making Splunk a critical target when misconfigured.




# PRTG Network Monitor 

This section focuses on identifying and exploiting PRTG Network Monitor, a widely used infrastructure monitoring tool that can become a **high-impact target** when misconfigured or outdated.

The process begins with **service discovery** (see _NMAP FULL SCAN_). PRTG is typically identified running on web ports such as 8080, where it appears as an Indy HTTP server tied to Paessler PRTG.

Once identified, accessing the web interface (see _PRTG WEB URL_) reveals the login panel. Default credentials (see _DEFAULT CREDENTIALS_) are often pre-filled and frequently left unchanged in real environments. Even if defaults fail, weak passwords (see _VALID CREDENTIALS_) are commonly successful.

Version enumeration (see _VERSION ENUMERATION_) is critical. By extracting the version number from the web interface, attackers can determine if the target is vulnerable to known exploits. In this case, version 17.3.33.2830 is vulnerable to **CVE-2018-9276**, an authenticated command injection flaw.

The vulnerability exists in the **notification feature**, where user input is passed directly into a PowerShell script without sanitization. This allows attackers to inject arbitrary commands (see _COMMAND INJECTION PAYLOAD_).

The attack works by:

1. Creating a new notification
    
2. Selecting a program execution option
    
3. Injecting malicious commands into the parameter field
    
4. Triggering execution via the “Test” button
    

Because this is a **blind command injection**, no output is returned. Therefore, attackers must verify success indirectly. In this case, a new administrative user is created and then validated using SMB authentication (see _CRACKMAPEXEC VALIDATION_).

Key attacker insights:

- PRTG is commonly found internally and often overlooked
    
- Default or weak credentials are frequently exploitable
    
- Version enumeration directly leads to known vulnerabilities
    
- Command injection provides full system-level execution
    
- Blind execution requires creative verification techniques
    
- Persistence can be achieved by scheduling notifications
    

Attack chain:

1. Discover PRTG via scan
    
2. Access login page
    
3. Attempt default/weak credentials
    
4. Enumerate version
    
5. Identify vulnerability (CVE-2018-9276)
    
6. Inject command via notification feature
    
7. Execute payload → gain admin/system access
    

This demonstrates how **a single misconfiguration combined with a known vulnerability can lead to full compromise**, especially in tools that run with elevated privileges.

# osTicket 

This section highlights how support ticketing systems like osTicket can be abused **without exploiting traditional vulnerabilities**, focusing instead on **information disclosure and social engineering vectors**.

The process begins with **application discovery** (see _OSTICKET URL_). osTicket can be identified through its interface, branding (“powered by osTicket”), and session cookies such as `OSTSESSID`.

Next comes **credential gathering from external sources** (see _DEHASHED ENUMERATION_). Leaked credentials from breaches are extremely valuable, especially because users often reuse passwords across multiple services.

These credentials are then tested against the osTicket admin portal (see _ADMIN LOGIN_). A key insight is that osTicket allows login via **email address or username**, which increases the chance of successful authentication.

Once access is obtained (e.g., via `kgrimes`), the attacker explores the system. Even without administrative privileges, a support agent account can access **ticket history**, which often contains sensitive data.

A critical discovery comes from **ticket conversations**, where a support agent mistakenly shares a password with a user. This demonstrates a common real-world issue:

- Helpdesk staff leaking credentials
    
- Use of standard “default” passwords
    
- Lack of secure communication practices
    

This password can then be reused against other services, such as VPN portals (identified during _SUBDOMAIN ENUMERATION_), potentially leading to full network access.

Another powerful attack vector is **ticket creation** (see _OPEN TICKET PAGE_). By submitting a ticket, attackers may receive:

- A valid internal email address
    
- A ticket-linked email (e.g., `940288@inlanefreight.local`)
    

This email can be used to:

- Register on internal services (Slack, GitLab, etc.)
    
- Receive confirmation emails through the ticket system
    
- Pivot into other applications
    

Key attacker insights:

- osTicket rarely has critical exploits → focus on abuse
    
- Credential reuse is a major weakness
    
- Support tickets often contain sensitive operational data
    
- Helpdesk mistakes can lead to credential exposure
    
- Internal email generation can be leveraged for lateral access
    
- Subdomain enumeration helps identify additional targets (VPN, portals)
    

Attack chain:

1. Discover osTicket instance
    
2. Gather leaked credentials
    
3. Attempt login (email + password reuse)
    
4. Access ticket system
    
5. Extract sensitive data (passwords, emails)
    
6. Pivot to other services (VPN, internal apps)
    

This section reinforces a crucial lesson:

**Not all compromises come from exploits—many come from abusing normal business processes and human mistakes.


# GitLab - Discovery & Enumeration

This section focuses on enumerating GitLab, a powerful code hosting platform that often contains **high-value sensitive data rather than direct vulnerabilities**.

The first step is identifying the application via its **login interface** (see _LOGIN PAGE_). GitLab has a very recognizable UI, making fingerprinting straightforward.

Next, attackers attempt to determine whether **registration is allowed** (see _REGISTER PAGE_). This is a critical step because:

- If open registration is enabled → attackers gain internal visibility
    
- If restricted → user enumeration is still possible
    

GitLab allows **username and email enumeration** through registration errors. When attempting to register:

- “Username already taken” → confirms valid user
    
- “Email already taken” → confirms valid email
    

This is useful for building a **targeted user list** for password attacks.

Once logged in (see _DEFAULT TEST CREDENTIALS_), attackers gain access to more content. A key page is `/explore` (see _EXPLORE PROJECTS_), which lists:

- Public repositories (no auth needed)
    
- Internal repositories (require login)
    

Repositories are the **primary objective** because they may contain:

- Hardcoded credentials
    
- API keys
    
- Database passwords
    
- SSH private keys
    
- Configuration files
    

Even if no exploit exists, this data can lead to full compromise.

Version enumeration is done through the `/help` page (see _HELP PAGE (VERSION)_), which is only accessible after authentication. Knowing the version allows mapping to known vulnerabilities.

The admin settings page (see _ADMIN SETTINGS_) reveals:

- Sign-up restrictions
    
- Password policies
    
- Authentication settings (e.g., 2FA disabled by default)
    

Key attacker insights:

- GitLab is a **data goldmine**, not just an attack surface
    
- Enumeration focuses on **access and data**, not exploits
    
- Open registration = major security risk
    
- Repositories often contain secrets accidentally committed
    
- Username/email enumeration aids credential attacks
    
- Internal projects are more valuable than public ones
    

Attack chain:

1. Identify GitLab instance
    
2. Check registration availability
    
3. Enumerate users/emails
    
4. Register account (if possible)
    
5. Access `/explore` and internal repos
    
6. Search for secrets (credentials, keys, configs)
    
7. Use discovered data to pivot
    

This section reinforces a key lesson:

**Sometimes the most critical vulnerability is not in the application—but in the data it exposes.

# Attacking GitLab

This section demonstrates how to move from enumeration to **full remote code execution against a GitLab instance**, combining user enumeration and a known vulnerability.

The first step is **username enumeration** (see _USER ENUMERATION SCRIPT_). Even though GitLab does not consider this a vulnerability, it provides valuable intelligence. By identifying valid usernames (see _VALID USERS_), attackers can:

- Attempt password spraying
    
- Reuse leaked credentials
    
- Target specific users
    

GitLab includes **account lockout protections** (see _ACCOUNT LOCKOUT SETTINGS_), which limit brute-force attempts. However, these controls can still be bypassed using slow, distributed password spraying techniques.

Once valid credentials are obtained (or a new account is registered), attackers can target **known vulnerabilities**. In this case, GitLab versions ≤ 13.10.2 are vulnerable to an authenticated RCE due to improper handling of image metadata.

The exploit (see _RCE EXPLOIT_) works by:

1. Authenticating to GitLab
    
2. Crafting a malicious payload
    
3. Uploading it via a feature like snippets
    
4. Triggering execution
    

The payload establishes a **reverse shell**, connecting back to the attacker (see _NETCAT LISTENER_). This provides direct command execution on the server.

Once access is gained, the attacker typically lands as the `git` user, which allows:

- Access to repositories
    
- Reading configuration files
    
- Searching for credentials
    
- Pivoting to privilege escalation
    

Key attacker insights:

- Username enumeration is highly valuable even if “not a vuln”
    
- Credential attacks often precede exploitation
    
- GitLab vulnerabilities can lead directly to RCE
    
- Reverse shells provide immediate foothold
    
- Git repositories may contain further secrets for escalation
    

Attack chain:

1. Enumerate valid users
    
2. Obtain credentials (guessing, reuse, registration)
    
3. Identify vulnerable GitLab version
    
4. Execute RCE exploit
    
5. Gain shell access
    
6. Enumerate system → escalate privileges
    

This section reinforces an important concept:

**Enumeration + weak access control + known vulnerability = full system compromise.** 

# Attacking Tomcat CGI

This section demonstrates exploitation of **CVE-2019-0232**, a critical command injection vulnerability in Tomcat’s CGI servlet on Windows systems.

The attack begins with **service discovery** (see _NMAP SCAN_), where Tomcat is identified running on port 8080 with a vulnerable version.

Next is **content discovery via fuzzing**. Since CGI scripts are typically located under `/cgi`, fuzzing is performed with different extensions:

- `.cmd` (see _FFUF FUZZ (.CMD)_) → no results
    
- `.bat` (see _FFUF FUZZ (.BAT)_) → reveals a valid script
    

This leads to discovery of a vulnerable endpoint (see _DISCOVERED CGI SCRIPT_).

The vulnerability arises because Tomcat improperly handles input when **enableCmdLineArguments is enabled**, allowing attackers to inject commands using the `&` separator.

Initial testing confirms command execution (see _COMMAND INJECTION (DIR)_), proving the vulnerability exists.

To better understand the environment, attackers enumerate **environment variables** (see _ENV VARIABLES ENUMERATION_). This reveals important constraints, such as:

- The `PATH` variable is unset
    
- Commands must be called with full paths
    

Attempts to execute commands directly (see _WHOAMI (FULL PATH)_) fail due to input filtering. Tomcat introduced protections that block certain characters.

However, this filter can be bypassed using **URL encoding** (see _URL-ENCODED PAYLOAD_), allowing execution of commands like `whoami`.

Key attacker insights:

- CGI scripts are high-risk when improperly configured
    
- Windows systems are specifically vulnerable in this case
    
- Fuzzing is essential for discovering hidden endpoints
    
- Command injection relies on chaining commands with `&`
    
- Environment constraints (like missing PATH) must be handled
    
- Input filters can often be bypassed with encoding
    

Attack chain:

1. Identify Tomcat service
    
2. Discover CGI endpoint via fuzzing
    
3. Confirm command injection
    
4. Enumerate environment variables
    
5. Adjust payloads (full paths)
    
6. Bypass filters using encoding
    
7. Execute arbitrary commands → RCE
    

This section highlights how **misconfiguration + input validation failure** can lead to full system compromise, even without authentication.


# Attacking CGI Applications - Shellshock

This section demonstrates exploitation of **Shellshock (CVE-2014-6271)**, a critical vulnerability in older versions of Bash that allows command execution via environment variables.

The attack begins with **enumeration of CGI endpoints** (see _GOBUSTER ENUMERATION_). Since CGI scripts are typically located under `/cgi-bin`, directory brute-forcing helps identify accessible scripts such as `access.cgi`.

Once a CGI script is found (see _DISCOVERED CGI SCRIPT_), a simple request (see _CURL REQUEST (BASIC)_) is used to confirm it is reachable, even if it returns no visible output.

Shellshock works by injecting malicious payloads into **HTTP headers**, commonly the `User-Agent`. The vulnerability occurs because the server passes these headers into environment variables, which are then interpreted by Bash.

The test payload (see _SHELLSHOCK TEST PAYLOAD_) confirms vulnerability by executing a command (`/etc/passwd`) and returning its output. This proves that arbitrary command execution is possible.

After confirming the vulnerability, attackers escalate to a **reverse shell** (see _REVERSE SHELL PAYLOAD_). This payload forces the target system to connect back to the attacker’s machine, where a listener (see _NETCAT LISTENER_) is waiting.

Once the connection is established, the attacker gains shell access as the web server user (commonly `www-data`). From here, they can:

- Explore the filesystem
    
- Search for sensitive data
    
- Attempt privilege escalation
    
- Pivot to other systems
    

Key attacker insights:

- Shellshock exploits environment variable handling in Bash
    
- CGI scripts are a common attack vector
    
- HTTP headers can be used for payload injection
    
- Even “empty” responses can still be exploitable
    
- Reverse shells provide full interactive access
    
- Often results in low-privileged access → requires escalation
    

Attack chain:

1. Enumerate CGI endpoints
    
2. Identify accessible script
    
3. Inject Shellshock payload via headers
    
4. Confirm command execution
    
5. Launch reverse shell
    
6. Gain foothold → escalate privileges
    

This section highlights how **legacy vulnerabilities can still provide easy system compromise**, especially in outdated systems and embedded devices.

# Attacking Thick Client Applications

This section demonstrates how thick client applications can hide **sensitive data such as credentials**, and how reverse engineering can uncover them.

The process begins with **initial access to an executable** (see _SMB ENUMERATION ARTIFACT_). Since the binary does not show visible behavior, dynamic analysis is required.

Using **Process Monitor** (see _PROC MONITOR TOOL_), we observe that the application creates temporary files (see _TEMP PATH_), which leads to discovering generated artifacts (see _GENERATED FILES_).

Inspecting the batch file (see _BATCH FILE (ORIGINAL)_) reveals:

- Username checks (access control logic)
    
- Creation of a file containing **base64-encoded data**
    
- Execution of a PowerShell script to decode it
    
- Cleanup steps to delete evidence
    

To analyze further, the deletion steps are removed (see _MODIFIED BATCH FILE_), allowing recovery of intermediate files.

The PowerShell script (see _POWERSHELL SCRIPT_) decodes the base64 content into a new executable. This reveals that the original application is simply a **wrapper that reconstructs another binary in memory**.

Next comes **memory analysis and reverse engineering**:

- The dumped binary is analyzed using `strings` → reveals .NET indicators
    
- `de4dot` is used to deobfuscate the binary
    
- `dnSpy` allows viewing the source code
    

Through this process, the application is identified as a **custom runas-like tool**, which executes commands using **hardcoded credentials**.

Key attacker insights:

- Thick clients often store sensitive data locally
    
- Temporary files can reveal hidden logic
    
- Base64 encoding is commonly used to obfuscate payloads
    
- Deleting artifacts is a common anti-analysis technique
    
- Memory analysis can reveal hidden executables
    
- .NET binaries are relatively easy to reverse engineer
    

Attack chain:

1. Obtain executable from SMB/share
    
2. Monitor execution (ProcMon)
    
3. Capture temporary files
    
4. Modify scripts to prevent cleanup
    
5. Extract encoded payload
    
6. Decode and reconstruct executable
    
7. Reverse engineer binary
    
8. Extract hardcoded credentials
    

This section highlights a critical lesson:

**Client-side applications cannot be trusted to securely store secrets—attackers can always reverse them.** 

# Exploiting Web Vulnerabilities in Thick-Client Applications

This section demonstrates a **full-chain attack against a three-tier thick client**, combining client-side modification, path traversal, and SQL injection.

The attack begins with **environment correction**. The client fails to connect due to a port mismatch, which is resolved by modifying local DNS resolution (see _HOSTS FILE ENTRY_). This ensures traffic is routed correctly to the target server.

Next, the application is **reverse engineered**. Extracting the JAR (see _EXTRACT JAR_) and searching for hardcoded values reveals the backend configuration (see _SEARCH PORT_ and _BEANS.XML_). This exposes:

- Server hostname
    
- Incorrect port (8000 → must be changed to 1337)
    
- Hardcoded secret
    

After modifying the configuration, the application fails due to **JAR signing protections**. Removing integrity checks (see _MANIFEST CLEAN_) allows rebuilding the client (see _REBUILD JAR_), enabling successful authentication.

With access, the attacker explores functionality and identifies **file browsing features**, which become the first attack surface.

A **path traversal attempt** (see _PATH TRAVERSAL PAYLOAD_) initially fails due to input filtering. Instead of bypassing the filter directly, the attacker modifies the client logic (see _MODIFIED CLIENT (TRAVERSAL)_) to send malicious input at the application level.

After recompiling and rebuilding the client (see _COMPILE JAVA_ → _BUILD TRAVERSE JAR_), traversal succeeds, exposing sensitive directories and files on the server.

Next, the attacker escalates by modifying functionality to **download server-side files** (see _FILE DOWNLOAD MODIFICATION_), retrieving the backend application (`fatty-server.jar`) for deeper analysis.

Reverse engineering the server reveals a **SQL injection vulnerability**:

- User input is directly embedded into SQL queries
    
- No sanitization is applied (see _SQL INJECTION TEST_)
    

Simple injection fails due to password hashing logic. The client hashes passwords before sending them, preventing authentication bypass.

To overcome this, the attacker modifies the client to **disable hashing** (see _PASSWORD FUNCTION (MODIFIED)_), sending plaintext passwords.

Finally, a **UNION-based SQL injection** (see _SQL INJECTION PAYLOAD_) is used to:

- Inject a fake admin user
    
- Control password and role
    
- Bypass authentication
    

This results in full administrative access to the application.

Key attacker insights:

- Thick clients cannot be trusted → logic can be modified
    
- Hardcoded configs and secrets are common
    
- Client-side protections (hashing, validation) can be bypassed
    
- JAR signing can be removed to allow tampering
    
- Path traversal can be achieved by modifying client behavior
    
- SQL injection becomes powerful when combined with client control
    

Full attack chain:

1. Fix connectivity (hosts file + port)
    
2. Extract and analyze client
    
3. Modify configuration
    
4. Remove integrity protections
    
5. Rebuild application
    
6. Gain access
    
7. Abuse file browsing (path traversal)
    
8. Download server-side code
    
9. Reverse engineer backend
    
10. Identify SQL injection
    
11. Modify client hashing logic
    
12. Execute UNION injection → admin access
    

This demonstrates a powerful concept:

**When you control the client, you control the attack surface.**

# ColdFusion - Discovery & Enumeration 

This section focuses on identifying ColdFusion, a Java-based web application platform that often exposes **distinct indicators during enumeration**.

The process begins with **port scanning** (see _NMAP SCAN_). ColdFusion environments frequently expose multiple services, but port **8500 is especially important**, as it is commonly used for ColdFusion over SSL.

Once a relevant port is found, accessing the web interface (see _TARGET URL_) often reveals **directory listings or default content**, which is a strong indicator of ColdFusion.

Key directories such as _DISCOVERED DIRECTORIES_ are highly indicative:

- `/CFIDE/` → core ColdFusion components
    
- `/cfdocs/` → documentation files
    

These directories are rarely present in other technologies, making them reliable fingerprints.

Further confirmation comes from **default administrative endpoints** (see _ADMIN PANEL_). Accessing this path often reveals the ColdFusion Administrator login page, which can also disclose the exact version (e.g., ColdFusion 8).

Additional fingerprinting methods include:

- File extensions (see _COMMON FILE EXTENSIONS_)
    
    - `.cfm` → ColdFusion markup pages
        
    - `.cfc` → ColdFusion components
        
- Default files (see _DEFAULT FILES_)
    
    - Common in misconfigured or exposed installations
        
- Error messages and headers
    
    - Often contain ColdFusion-specific references
        

Another important aspect is understanding **default ports** (see _COMMON PORTS_). Among them:

- 8500 → ColdFusion SSL/web interface
    
- 5500 → **Server Monitor (remote administration service)**
    

This is critical because management services often become **high-value attack surfaces** later in exploitation.

Key attacker insights:

- ColdFusion has very **distinct fingerprints** (directories, extensions, admin panel)
    
- Port 8500 is a strong indicator of ColdFusion
    
- Default directories can expose sensitive functionality
    
- Admin panels often reveal version → leads to known exploits
    
- Multiple services increase the attack surface
    

Methodology flow:

1. Scan for open ports
    
2. Identify ColdFusion-specific ports (8500, 5500)
    
3. Browse web interface
    
4. Locate default directories
    
5. Access admin panel
    
6. Determine version
    
7. Prepare for exploitation
    

This structured approach ensures accurate identification and prepares the ground for exploiting ColdFusion-specific vulnerabilities.


# Attacking ColdFusion

This section demonstrates how to exploit ColdFusion by chaining **known vulnerabilities with misconfigurations**, leading to full system compromise.

The process begins with **exploit discovery** (see _SEARCHSPLOIT ENUMERATION_). Since the target is identified as ColdFusion 8, searching for public exploits reveals two key attack paths:

- Directory Traversal (CVE-2010-2861)
    
- Unauthenticated RCE (CVE-2009-2265)
    

---

### Directory Traversal

The first attack abuses vulnerable ColdFusion endpoints (see _TRAVERSAL ENDPOINTS_). These endpoints fail to properly validate user input, specifically the `locale` parameter.

By injecting traversal sequences (see _TRAVERSAL PAYLOAD_), the attacker can escape the intended directory and read arbitrary files.

Using the automated exploit (see _RUN DIRECTORY TRAVERSAL_), sensitive files such as `password.properties` are retrieved. This file contains:

- Encrypted credentials
    
- Service authentication data
    
- Internal configuration
    

This confirms the vulnerability and provides valuable data for further attacks.

---

### Unauthenticated RCE

The second and more critical attack is **remote code execution without authentication**.

This vulnerability exists in the FCKeditor file upload component (see _UPLOAD ENDPOINT_). It allows attackers to upload malicious files (e.g., JSP shells) directly to the server.

The exploit script (see _RCE CONFIGURATION_ → _RUN RCE EXPLOIT_) automates:

1. Payload generation
    
2. File upload
    
3. Execution trigger
    
4. Reverse shell connection
    

Once executed, the attacker gains a shell on the system, typically with the privileges of the ColdFusion service.

---

### Command Injection Concept

The section also highlights a general ColdFusion weakness where user input is passed directly into execution functions.

The example payload (see _UNAUTHENTICATED RCE PAYLOAD_) shows how command chaining works:

- `%3B` → encoded semicolon (`;`)
    
- Allows execution of additional commands
    
- Writes files or executes system-level actions
    

---

### Attacker Mindset & Chain

Key insights:

- ColdFusion exposes many **legacy vulnerabilities**
    
- Default components (CFIDE, FCKeditor) are high-risk
    
- Input validation failures lead to traversal and RCE
    
- Public exploits make exploitation trivial
    
- Unauthenticated RCE = full compromise immediately
    

Attack chain:

1. Identify ColdFusion version
    
2. Search for known exploits
    
3. Exploit directory traversal → gather sensitive data
    
4. Exploit file upload → gain RCE
    
5. Establish reverse shell
    
6. Enumerate and escalate
    

---

This section reinforces a critical concept:

**Outdated enterprise software + exposed default components = immediate compromise.** 


# IIS Tilde Enumeration 

This section demonstrates how to enumerate hidden files and directories on IIS servers using **8.3 short filename disclosure**, a technique specific to Windows-based web servers.

The process begins with **service identification** (see _NMAP SCAN_), confirming the presence of Microsoft IIS. IIS version 7.5 is particularly relevant because it is vulnerable to short name enumeration.

Next, instead of manually guessing filenames, the attack leverages an automated tool (see _IIS SHORTNAME SCANNER_). This tool abuses how Windows generates **8.3 short filenames**, which can unintentionally expose:

- Hidden directories
    
- Restricted files
    
- Partial filenames
    

The scan reveals short names (see _DISCOVERED SHORT NAMES_), including a critical one:

- `TRANSF~1.ASP` → indicates a file starting with “transf”
    

However, short names are not the full filenames. At this stage, the attacker only has a **partial view** of the actual file.

To resolve this, a **custom wordlist** is generated (see _WORDLIST GENERATION_), filtering existing wordlists for entries starting with "transf". This drastically reduces brute-force time and increases accuracy.

With this refined list, directory brute-forcing is performed (see _GOBUSTER ENUMERATION_). By combining:

- Known prefix (`transf`)
    
- Possible extensions (`.asp`, `.aspx`)
    

the attacker successfully reconstructs the full filename (see _DISCOVERED FILE_).

---

### Key Attacker Insights

- IIS generates **8.3 short filenames automatically**, even for hidden files
    
- Short names can leak sensitive file structure information
    
- Enumeration is a **two-step process**:
    
    1. Discover short names
        
    2. Expand them into full filenames
        
- Custom wordlists significantly improve brute-force efficiency
    
- This technique works even when directory listing is disabled
    

---

### Attack Chain

1. Identify IIS server
    
2. Run shortname scanner
    
3. Extract partial filenames
    
4. Generate targeted wordlist
    
5. Brute-force full filenames
    
6. Discover hidden resources
    

---

This technique highlights a powerful concept:

**Even when direct access is blocked, underlying OS behaviors can leak critical information.**

# LDAP Injection

---

**Enumeration**

The nmap scan reveals two critical services: an Apache HTTP server on port 80 and an OpenLDAP server on port 389. The presence of OpenLDAP strongly implies that the web application on port 80 uses LDAP as its authentication backend, making LDAP injection the primary attack vector to explore.

---

**How LDAP Authentication Queries Work**

The reference query in Output A shows how the application constructs its authentication check. It wraps the username and password inside an AND (`&`) expression combined with an objectClass filter. The server evaluates this expression against directory entries — if a match is found, authentication succeeds. The vulnerability arises because user input is concatenated directly into this query string without sanitization.

---

**Why the Wildcard Bypasses Authentication**

In LDAP filter syntax, the asterisk (`*`) is a wildcard that matches any value or any number of characters. When injected into the username field, the filter `(sAMAccountName=*)` becomes true for every single user entry in the directory. The password condition becomes secondary or irrelevant depending on implementation. The result is that the compound AND expression evaluates to true for the first matching record, granting access without knowing any real credentials.

The same logic applies when the wildcard is placed in the password field — the `(userPassword=*)` segment matches any non-empty password value.

Using `*` in both fields simultaneously is the most aggressive form of the bypass and is what the lab demonstrates.

---

**Attack Chain**

The exploitation flow is direct: enumerate open ports to identify LDAP presence, infer backend authentication mechanism, submit wildcard characters in the login form, and observe that the application grants access without valid credentials. There is no need for brute force or credential harvesting.

---

**Why This Works in This Lab**

The application does not sanitize or escape LDAP special characters before building the query. It also does not use parameterized LDAP queries, which would treat input as literal data rather than filter syntax. Both mitigations are absent, making the injection trivially exploitable.

---

**Relationship to SQL Injection**

The conceptual model is identical to SQL injection — unsanitized user input modifies the structure of a backend query. The difference is the query language (LDAP filter syntax vs SQL) and the target system (a directory service vs a relational database). Testers familiar with SQLi can apply the same intuition here.

---

**Common Pitfalls**

Injecting parentheses or pipe characters without understanding the filter structure can produce malformed queries that return errors rather than bypass authentication. The wildcard approach is the most reliable starting point because it does not alter filter structure, only the value being matched.

# Web Mass Assignment Vulnerabilities

---

**What the Vulnerability Is**

Mass assignment occurs when a framework or application automatically binds HTTP request parameters directly to model or database fields without restricting which fields are permitted. If the application does not explicitly whitelist allowed fields, an attacker can inject additional parameters that map to sensitive fields — fields the developer never intended to expose.

---

**How This Lab's Application Works**

The Python application stores users in a SQLite database with three columns: username, password, and a boolean confirmation flag (`k` in the query). When a user logs in, the code checks whether `k` is truthy. If it is, login succeeds. If not, the account is flagged as pending approval. Normally, `cond` defaults to `False` at registration — meaning new users cannot log in until an admin approves them.

---

**The Exploitation Path**

The registration handler contains a `try/except` block that checks whether the incoming POST request contains a `confirmed` field. If that field is present with any value, `cond` is set to `True`, which is then written directly into the database as the confirmation flag. The developer intended this field to be absent from normal registration requests, but because there is no enforcement preventing a user from adding it, anyone who intercepts and modifies the registration request can include it freely.

Burp Suite is used to capture the POST request to `/register` and append `confirmed=test` to the parameters. This causes `cond=True` to be inserted, and the newly registered account bypasses the approval requirement entirely.

---

**The Lab Question**

The live target has had the parameter name changed from `confirmed` to something else. The SSH credentials in Output A are used to log into the target machine and read the actual source at `/opt/asset-manager/app.py`. The `try/except` block is the section to focus on — whatever field name appears in `request.form[...]` is the parameter that must be injected during registration.

---

**Attack Chain**

SSH into the target, read the source, identify the renamed parameter in the `try/except` block, intercept the registration POST with Burp Suite, append the parameter with any value, register successfully, then log in with the new credentials.

---

**Why Prevention Requires Whitelisting**

The fix is to never trust that absent fields stay absent. Strong parameter patterns (shown in the Ruby example in Output A) explicitly enumerate which fields are accepted and discard everything else. The Python equivalent would be to only read specific known fields from `request.form` rather than checking for the presence of an arbitrary field name.

# Attacking Applications Connecting to Services 

---

**Why Applications Leak Credentials**

Applications that connect to backend services (databases, APIs) must store connection strings somewhere. If those strings are embedded in compiled binaries without encryption or obfuscation, they can be extracted through static analysis or dynamic debugging. The connection string format for ODBC-based SQL Server connections includes plaintext UID and PWD fields, making them immediately usable once recovered.

---

**ELF Binary Analysis with GDB/PEDA**

The `octopus_checker` binary is a compiled ELF executable that connects to a SQL Server instance via ODBC. Because it has no debugging symbols, direct source inspection is unavailable. Instead, disassembling `main` reveals the program flow and highlights calls to library functions — in this case, `SQLDriverConnect`, the ODBC function responsible for establishing the database connection.

The connection string is assembled in memory just before `SQLDriverConnect` is called. By setting a breakpoint at the PLT entry for `SQLDriverConnect` (the address in Output A), execution pauses at the moment the function is about to be called. At this point, the second argument — passed in the RDX register on x86-64 Linux — holds a pointer to the fully assembled connection string, which is readable in plaintext from the register dump.

---

**Endianness Note**

The section mentions that string fragments visible during disassembly appear reversed due to endianness. This is why reading the string directly from the disassembly output is unreliable — the breakpoint approach reads the string from memory after it has been properly assembled, bypassing the endianness issue entirely.

---

**DLL Analysis with dnSpy**

The `MultimasterAPI.dll` is a .NET assembly, which means its source code (C# or VB.NET) is recoverable with tools like dnSpy. Unlike native compiled binaries, .NET assemblies contain intermediate language (IL) that can be decompiled back to near-original source. Navigating to the `ColleagueController` class exposes the database connection string directly in the code, including credentials.

---

**Post-Exploitation Value**

Credentials extracted from connection strings are not only useful for connecting to the database directly. They represent real credentials that may be reused by the same user or service account across other systems on the network. Password spraying these credentials against other services (SMB, WinRM, RDP, etc.) is a standard lateral movement technique following this type of finding.

---

**Attack Chain**

SSH into the target, locate the binary, load it in GDB, disassemble main to find the `SQLDriverConnect` call address, set a breakpoint there, run the program, and read the connection string from the RDX register when execution pauses. The credentials are embedded in plaintext in the UID and PWD fields of that string.



# Other Notable Applications

---

**Methodology Over Application-Specifics**

This section reinforces that the exploitation methodology taught throughout the module applies universally. The core loop is: enumerate, fingerprint, check default credentials, identify version-specific CVEs, and probe built-in functionality for abuse. The specific application changes, but the approach does not.

---

**Enumeration First**

The lab question asks what application is running on the target. The nmap scan in Output A is the starting point. Service version detection (`-sV`) and default script execution (`-sC`) will fingerprint the web server, application server, or monitoring platform and return version banners that identify the software.

---

**Default Credentials as Primary Attack Vector**

Several of the applications listed rely on default credentials as the entry point. For Websphere, `system:manager` grants access to the admin console where a WAR file can be deployed — the same technique used against Tomcat. For Nagios, `nagiosadmin:PASSW0RD` is the documented default. Trying these immediately after fingerprinting costs nothing and frequently works against forgotten or unpatched installs.

---

**Built-in Functionality for RCE**

Many of these applications have legitimate administrative features that double as RCE vectors once authenticated. Websphere's WAR deployment, Axis2's AAR service upload, and Zabbix's API are all examples of features designed for administrators that an attacker can abuse after gaining credentials. This is distinct from CVE-based exploitation — no vulnerability needs to exist if the attacker has valid credentials and the application provides deployment functionality.

---

**CVE-Heavy Targets**

WebLogic has 190 reported CVEs at time of writing, many involving Java Deserialization — a class of vulnerability where crafted serialized Java objects trigger code execution during deserialization. vCenter's CVE-2021-22005 is an unauthenticated OVA upload vulnerability that scanners like Nessus miss, making manual enumeration essential. For both, checking the version after fingerprinting determines which public exploits apply.

---

**Post-Exploitation Context**

vCenter running as SYSTEM or as a domain admin on Windows appliances represents a single-compromise-to-domain scenario. If a shell is obtained on a Windows vCenter instance, tools like JuicyPotato can escalate to SYSTEM if not already there. The flag path in Output A reflects a Windows Administrator Desktop location, consistent with a Windows-based target in this lab.

# Application Hardening 
---

**Application Inventory as the Foundation**

Before any hardening can occur, an organization must know what is running in its environment. Nmap and EyeWitness are the same tools used offensively for discovery and can be repurposed by blue teams for this exact purpose. Shadow IT — unauthorized or forgotten installs — represents a persistent blind spot. The Splunk example in the section illustrates how a trial version silently converting to a free tier can eliminate authentication entirely, creating an unintended exposure.

---

**Secure Authentication**

Default credentials are the most consistently exploited weakness across all applications covered in this module. Changing them at deployment time, disabling default admin accounts, and enforcing 2FA for administrator-level users addresses the most common initial access vector. Several lab scenarios throughout this module succeeded entirely because default passwords were in place.

---

**Access Controls**

Restricting which interfaces are exposed to the internet is a high-value hardening measure. Admin login pages for Tomcat Manager, Jenkins, Drupal, and osTicket have no business reason to be reachable from the open internet in most deployments. The Joomla secret key URL pattern in Output A is an example of obscuring the admin login path to reduce automated exploitation. Tomcat hardening specifically requires restricting Manager and Host-Manager to localhost or IP-whitelisted sources, since those panels are the direct path to WAR deployment and RCE.

---

**Disabling Unsafe Features**

The WordPress PHP code editor example is representative of a class of built-in features that are legitimate for administrators but become RCE vectors once credentials are compromised. If code editing via the browser is unnecessary, disabling it removes an entire exploitation chain. The principle applies broadly: reduce the attack surface by disabling features that are not actively needed.

---

**Regular Updates and Patch Management**

Many exploits demonstrated throughout this module target specific known CVEs. Prompt patching removes these from the attack surface. The section notes that organizations often perform well at vulnerability management but neglect credential hygiene — both are required.

---

**LDAP / Active Directory Integration**

Centralizing authentication through AD SSO reduces credential sprawl, enables consistent password policy enforcement, and improves audit logging. From an attacker's perspective, a single set of AD credentials found in one application often provides lateral movement to others. Centralization also means revoking a compromised account is a single operation rather than per-application.

---

**Penetration Testing as a Feedback Loop**

The section closes with the recommendation to treat penetration test findings as recurring checks, not one-time fixes. Process-level vulnerabilities — such as weak credential management practices — require organizational mindset changes, not just technical patches. Reassessing for the same vulnerability classes found in prior engagements validates that remediation was effective.



#
#
#