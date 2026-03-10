# INTRODUCTION 

## Web Reconnaissance

Web reconnaissance is the **information gathering phase** of a penetration test. Its purpose is to collect as much intelligence as possible about a target before attempting exploitation.

This stage builds the **foundation for every later step** in a security assessment. If reconnaissance is incomplete, later phases such as vulnerability discovery or exploitation become significantly harder.

In the penetration testing lifecycle, reconnaissance occurs early and feeds data into later stages like vulnerability assessment and exploitation.

Typical penetration testing phases include:

Pre-Engagement  
Information Gathering  
Vulnerability Assessment  
Exploitation  
Post-Exploitation  
Lateral Movement  
Proof-of-Concept  
Post-Engagement

Reconnaissance primarily belongs to the **Information Gathering phase**.

---

## Primary Goals of Web Recon

### Identifying Assets

The first objective is discovering **all publicly reachable infrastructure** related to the target.

Examples include:

Web servers  
Subdomains  
IP addresses  
Technologies used by the site

This allows an attacker or tester to build a **map of the target environment**.

---

### Discovering Hidden Information

Sometimes sensitive files are accidentally exposed.

Examples include:

Backup files  
Configuration files  
Internal documentation

These artifacts may contain credentials, internal paths, or infrastructure details.

---

### Analyzing the Attack Surface

The attack surface represents **all possible entry points** into a system.

Recon attempts to identify:

Technologies used  
Framework versions  
Application endpoints  
Potential vulnerabilities

The larger the attack surface, the more opportunities exist for exploitation.

---

### Gathering Intelligence

Recon can also collect **human and organizational data** useful for attacks.

Examples include:

Employee names  
Email addresses  
Role descriptions  
Organizational structure

This information is often leveraged in **social engineering attacks**.

---

## Types of Reconnaissance

Web reconnaissance is divided into two major categories:

Active Reconnaissance  
Passive Reconnaissance

The key difference is **whether the target is directly interacted with**.

---

## Active Reconnaissance

Active reconnaissance involves **direct interaction with the target systems**.

Because the attacker sends traffic directly to the target infrastructure, these actions **can be logged or detected by security systems**.

Common techniques include:

Port scanning  
Vulnerability scanning  
Network mapping  
Banner grabbing  
OS fingerprinting  
Service enumeration  
Web crawling

---

### Port Scanning

Port scanning identifies **open ports and running services** on a system.

Open ports often reveal services like:

HTTP (80)  
HTTPS (443)

Tools like Nmap send network probes to determine which ports respond.

Because this involves sending many network requests, **intrusion detection systems may detect it**.

---

### Vulnerability Scanning

Vulnerability scanners check systems for **known weaknesses or misconfigurations**.

These tools often send **test payloads** that simulate exploitation attempts.

Examples include scanners checking for:

SQL injection  
Cross-site scripting  
Outdated software versions

These scanners generate noticeable traffic, making detection likely.

---

### Network Mapping

Network mapping attempts to understand **how systems are connected**.

Tools analyze network routes and hops between systems.

This can reveal:

Network infrastructure  
Gateway devices  
Internal architecture clues

---

### Banner Grabbing

Many services reveal information in **service banners** when connections are established.

For example, a web server might reveal:

Server software  
Software version

Banner grabbing simply connects to a service and reads the response.

Because interaction is minimal, detection risk is relatively low.

---

### OS Fingerprinting

OS fingerprinting attempts to determine **which operating system a host runs**.

Tools analyze how a system responds to specific network probes.

Different operating systems respond differently to certain packets, allowing fingerprinting tools to infer the OS.

---

### Service Enumeration

Service enumeration identifies **specific versions of running services**.

Knowing the exact version is extremely valuable because attackers can search for **known vulnerabilities affecting that version**.

---

### Web Spidering

Web spidering (web crawling) automatically explores a website to map:

Pages  
Directories  
Files  
Endpoints

Crawlers follow links recursively and build a structure of the application.

If poorly configured, spiders can generate unusual traffic patterns and be detected.

---

## Passive Reconnaissance

Passive reconnaissance gathers information **without directly interacting with the target systems**.

Instead, it analyzes **publicly available information sources**.

This approach is significantly stealthier because **no direct contact occurs with the target infrastructure**.

---

### Search Engine Queries

Search engines can reveal a surprising amount of information about organizations.

Examples include:

Employee profiles  
Public documents  
Old pages  
News mentions

Specialized search engines like Shodan can also reveal exposed services.

---

### WHOIS Lookups

WHOIS databases contain **domain registration information**.

These records often reveal:

Registrant names  
Contact email addresses  
Name servers  
Registration dates

This information helps identify ownership and infrastructure relationships.

---

### DNS Analysis

DNS records can expose significant infrastructure details.

Examples include:

Subdomains  
Mail servers  
IP addresses

DNS enumeration often leads to discovery of **additional attack targets**.

---

### Web Archive Analysis

Archived snapshots of websites allow researchers to view **historical versions of sites**.

Older versions may reveal:

Hidden pages  
Deprecated endpoints  
Sensitive data previously exposed

---

### Social Media Analysis

Social media platforms often expose organizational information.

Examples include:

Employee names  
Technologies used  
Company structure

Attackers can leverage this information for **targeted social engineering attacks**.

---

### Code Repository Analysis

Public repositories sometimes contain:

Source code  
API keys  
Credentials  
Configuration files

Searching these repositories can reveal **critical security leaks**.

---

## Active vs Passive Recon Trade-off

Passive Recon

Very low detection risk  
Relies on public data  
Less complete information

Active Recon

More comprehensive results  
Direct interaction with target  
Higher chance of detection

A good penetration tester typically **starts with passive recon and then moves into active recon once enough intelligence is gathered**.

---

## Why This Matters

Reconnaissance determines **how effective the rest of the attack will be**.

Well-executed recon can reveal:

Hidden attack paths  
Exposed infrastructure  
Misconfigurations  
Potential vulnerabilities

This module begins with one of the most important passive reconnaissance tools: **WHOIS**, which helps identify domain ownership and infrastructure relationships.

# WHOIS

### What WHOIS Is

WHOIS is a protocol used to query databases that store **registration information about internet resources**. These resources mainly include:

- Domain names
    
- IP address allocations
    
- Autonomous systems (AS)
    

It functions like a **directory for the internet**, allowing anyone to look up who owns or manages a particular domain.

During reconnaissance, this information helps identify **who controls a target's infrastructure and how it is managed**.

---

### Information Typically Found in WHOIS Records

WHOIS records usually contain several important data fields that help understand the ownership and structure of a domain.

**Domain Name**

The registered domain being queried.

**Registrar**

The company through which the domain was registered. Examples include GoDaddy, Namecheap, or Amazon Registrar.

**Registrant Contact**

The individual or organization that owns the domain.

**Administrative Contact**

The person responsible for administrative management of the domain.

**Technical Contact**

The person responsible for technical configuration and maintenance.

**Creation and Expiration Dates**

These timestamps reveal when the domain was registered and when it will expire.

This information can indicate:

- how long the domain has existed
    
- whether it may be abandoned soon
    
- how frequently it is renewed
    

**Name Servers**

Name servers translate domain names into IP addresses.

They can reveal:

- hosting providers
    
- DNS infrastructure
    
- third-party services used by the organization
    

---

### Why WHOIS Matters in Web Reconnaissance

WHOIS provides valuable intelligence early in the reconnaissance phase.

#### Identifying Key Personnel

Many WHOIS records include contact names, email addresses, and phone numbers.

This information can help identify:

- system administrators
    
- infrastructure managers
    
- domain owners
    

Attackers may use this information in **social engineering or phishing campaigns**.

---

#### Discovering Network Infrastructure

WHOIS records often expose infrastructure-related information such as:

- name servers
    
- registrar providers
    
- sometimes IP allocation data
    

These clues can help map **parts of the target's digital environment**.

---

#### Historical WHOIS Analysis

Historical WHOIS data can reveal how a domain has evolved over time.

Changes in:

- ownership
    
- hosting providers
    
- contact details
    
- DNS servers
    

may expose useful patterns or previously used infrastructure.

Some services store historical WHOIS records, allowing analysts to study the **timeline of a domain's changes**.

---

### Attacker Mindset

From a reconnaissance perspective, WHOIS answers several key questions:

- Who owns the domain?
    
- Who manages its infrastructure?
    
- Which DNS servers are used?
    
- Which company registered it?
    

Even small pieces of information can become **pivot points for further investigation**.

For example:

A discovered admin email may lead to:

- LinkedIn profiles
    
- leaked credentials
    
- GitHub accounts
    
- company structure information
    

Reconnaissance is about **building intelligence step by step**, and WHOIS is often one of the first sources used.


# UTILISING WHOIS 

## INSTALLATION

This section shows that the WHOIS client may need to be installed before use on a Linux system. The purpose is simply to make the lookup utility available locally so domain registration data can be queried from the terminal.

## WHOIS LOOKUPS

The lookup against the social media domain demonstrates the standard way WHOIS is used during reconnaissance: query a domain, then review registration metadata for ownership, age, registrar choice, DNS control, and operational protections.

The example matters because it teaches how to distinguish a legitimate, mature domain from a suspicious one. A long registration history, recognizable owner, protected domain status, and organization-controlled name servers all support trust and legitimacy.

## TARGETS

The first domain is the earlier simple example used to introduce WHOIS syntax. The second domain is used for a more detailed walkthrough of how to interpret real registration data.

These targets are not important because of the domains themselves, but because they show two phases of learning:  
first, how to run the lookup;  
second, how to analyze what comes back.

## SERVICES

WHOIS is the central service in this section. In practice, it provides domain registration intelligence rather than vulnerability data. That means it is most useful early in recon, where the goal is to profile ownership, infrastructure, and trust signals.

## SCENARIO 1: PHISHING INVESTIGATION

This scenario teaches how WHOIS helps triage suspicious domains. A recently registered domain, hidden registrant details, and suspicious name servers form a pattern commonly associated with phishing infrastructure.

The value here is not any single field alone. The power comes from correlation. New registration plus privacy shielding plus questionable hosting creates a stronger signal than any one attribute by itself.

From an attacker mindset perspective, phishing campaigns often need disposable infrastructure that can be set up quickly and abandoned easily. WHOIS helps defenders detect exactly that pattern.

## SCENARIO 2: MALWARE ANALYSIS

Here WHOIS is used to investigate command-and-control infrastructure. The lesson is that domain registration details can reveal clues about the operator’s tradecraft, hosting choices, and abuse tolerance.

This works because adversaries often rely on registrars, email providers, and hosting environments that allow malicious activity to persist longer. WHOIS can expose those supporting choices even when the malware itself is not yet fully understood.

The broader methodology point is that infrastructure analysis complements malware analysis. You are not only studying the sample; you are studying the ecosystem behind it.

## SCENARIO 3: THREAT INTELLIGENCE REPORT

This scenario shows WHOIS at scale. Instead of examining one domain in isolation, analysts compare many records to identify repeated behaviors.

That is how patterns emerge:  
registration clustering,  
alias reuse,  
shared name servers,  
and repeated takedowns.

These are useful because threat intelligence is not just about describing a past attack. It is about building indicators and behavioral patterns that help predict or detect future campaigns.

## INTERPRETING THE FACEBOOK EXAMPLE

The example domain appears legitimate because several independent signals align with that conclusion.

The age of the domain suggests long-term ownership and established presence.  
The registrar information is consistent and professional.  
The ownership fields point to the expected organization.  
The domain status values indicate change protection and transfer safeguards.  
The name servers suggest the organization manages its own DNS infrastructure.

This is an important recon lesson: legitimacy is not determined by one field. It is determined by whether multiple fields make sense together.

## WHAT WHOIS IS GOOD FOR

WHOIS is especially useful for:  
ownership identification,  
domain age assessment,  
registrar profiling,  
name server discovery,  
and infrastructure correlation.

It is often one of the first passive recon steps because it is low-noise and may immediately reveal pivot points for deeper investigation.

## WHAT WHOIS IS NOT GOOD FOR

WHOIS usually does not reveal direct vulnerabilities or detailed employee-level information in a reliable way. Privacy services, redaction, and corporate registration practices can limit the usefulness of contact fields.

That is why WHOIS should be combined with other recon sources such as DNS data, search engine discovery, public code repositories, and web archives.

## HOW THIS FITS INTO THE RECON FLOW

In the larger methodology, WHOIS sits near the beginning of passive reconnaissance.

A typical chain is:

start with WHOIS to understand ownership and DNS clues,  
pivot into DNS and subdomain enumeration,  
map technologies and exposed services,  
then expand into crawling, search discovery, and historical analysis.

WHOIS gives context. Later techniques give depth. Used together, they produce a far more complete picture of the target.


# DNS 

## DNS Purpose

DNS acts as the **translation system of the internet**, converting human-readable domain names into machine-readable IP addresses. Browsers and applications cannot communicate using domain names alone; they require IP addresses.

DNS allows users to access services using simple names instead of memorizing numerical addresses.

## DNS Resolution Process

When a user attempts to access a domain, several systems cooperate to resolve it.

First, the local machine checks its **DNS cache**. If the address has been recently resolved, it will already be stored locally.

If not found locally, the request is sent to a **DNS resolver**, usually operated by the user's ISP.

The resolver begins a recursive lookup through the DNS hierarchy:

1. **Root Name Server**  
    Directs the resolver to the correct Top-Level Domain server.
    
2. **TLD Name Server**  
    Identifies which authoritative server manages the requested domain.
    
3. **Authoritative Name Server**  
    Returns the actual IP address associated with the domain.
    

The resolver then returns the IP address to the user’s system and stores it temporarily in cache.

## Hosts File

Before DNS queries are performed, operating systems check a **local hosts file**.

This file allows manual mappings between hostnames and IP addresses. It effectively overrides DNS resolution.

Typical uses include:

Development testing  
Redirecting domains to local servers  
Blocking websites  
Troubleshooting network issues

Because hosts entries are local, they take precedence over DNS responses.

## DNS Zones

A DNS **zone** is a portion of the domain namespace managed by a specific administrator.

For example:

example.com  
mail.example.com  
blog.example.com

These may all exist within the same zone managed by a DNS server.

Zones are defined in **zone files**, which store records describing domain infrastructure.

## Zone Files

A zone file contains **resource records** that define how a domain operates.

These records include:

Name servers responsible for the domain  
Mail servers handling email  
IP addresses for hosts  
Aliases for services

Zone files essentially act as the **configuration database for DNS infrastructure**.

## DNS Record Types

Different DNS records store different types of infrastructure information.

### A Record

Maps a hostname to an IPv4 address.

This is the most common DNS record used for web servers.

### AAAA Record

Maps a hostname to an IPv6 address.

IPv6 is increasingly used as IPv4 addresses become scarce.

### CNAME Record

Creates an alias that points one hostname to another hostname.

This is often used for services hosted on external infrastructure or load-balanced platforms.

### MX Record

Defines which mail servers handle email for the domain.

These records reveal mail infrastructure and sometimes third-party services.

### NS Record

Specifies the authoritative DNS servers responsible for a domain.

These servers store the official zone file.

### TXT Record

Stores arbitrary text information.

These records are frequently used for:

Email authentication (SPF, DKIM)  
Domain ownership verification  
Security policies

### SOA Record

The **Start of Authority** record defines administrative information about the DNS zone.

It contains details such as:

Primary name server  
Administrative contact  
Zone serial number  
Update intervals

### SRV Record

Specifies service locations and ports for specific protocols.

These records are often used for communication systems like SIP or messaging infrastructure.

### PTR Record

Used for **reverse DNS lookups**, mapping IP addresses back to domain names.

Reverse lookups are frequently used in email validation and network diagnostics.

## Meaning of the "IN" Field

The **IN** class indicates that the record belongs to the Internet protocol family.

While other classes exist historically, almost all modern DNS records use the IN class.

## Why DNS Is Important for Web Recon

DNS provides valuable reconnaissance intelligence about an organization’s infrastructure.

### Asset Discovery

DNS records often reveal:

Subdomains  
Mail servers  
Load balancers  
Application servers

These assets expand the attack surface.

### Infrastructure Mapping

Analyzing NS, A, and CNAME records helps map:

Hosting providers  
DNS providers  
Traffic routing infrastructure

This helps identify potential weak points in the architecture.

### Monitoring Changes

Monitoring DNS over time may reveal new infrastructure components.

For example:

New VPN subdomains  
New development environments  
Third-party service integrations

Such discoveries may reveal **new entry points for attackers**.

DNS reconnaissance is therefore one of the most important steps in **mapping a target’s external infrastructure** before further enumeration or exploitation.

# DIGGING DNS 

## DNS TOOLS

This section introduces the main tooling used for DNS reconnaissance. The important idea is not that every tool does something completely different, but that they vary in depth, automation, and convenience.

The general progression is:  
simple lookup utilities for fast checks,  
more flexible utilities for deep inspection,  
then automated enumeration tools for scale.

The core tool in this section is the DNS query utility that provides very detailed output. The others are supporting options for quicker checks or larger-scale enumeration.

## COMMON DIG COMMANDS

These queries represent the main record types and query styles used during DNS recon.

The default lookup asks for the standard IPv4 mapping, which is usually the first step in understanding where a domain resolves.

The explicit record lookups let you inspect different parts of a target’s infrastructure:  
IPv4 and IPv6 addressing,  
mail routing,  
authoritative name servers,  
free-form text records,  
aliases,  
and authority metadata.

The query directed at a specific resolver is useful when you want to compare how different DNS servers answer or when you do not want to rely on the system default resolver.

The full trace option is valuable because it shows the path of resolution through the DNS hierarchy. This helps you understand delegation and trust boundaries rather than just getting the final answer.

The reverse lookup against an IP is useful when pivoting from infrastructure back to hostnames. That matters when you begin with an address found elsewhere and want to see whether it maps to a recognizable service name.

The short-output options matter because they strip away noise. In real recon, analysts often want either the full diagnostic view or only the exact answer, depending on the task.

The query for every available record is included because it is common in DNS learning and testing, but the source correctly notes that many servers restrict or ignore it. The reason is operational and defensive: broad record dumping can be abused.

## EXAMPLE COMMANDS

The first example demonstrates the full verbose DNS response. It is used to teach how to read DNS output structurally, not just how to obtain an IP address.

The second example demonstrates the minimalist answer-only approach. This is useful when the objective is speed, scripting, or quick validation rather than interpretation of the whole response structure.

Together, these two examples show the two most common operational modes:  
detailed inspection,  
and concise extraction.

## PARAMETERS

These options are important because they control how much intelligence is exposed and how usable the output becomes.

The resolver selector changes who answers the question.  
The trace option changes the visibility of the resolution path.  
The reverse lookup option changes the direction of investigation from address to name.  
The short and filtered output options change how much diagnostic information is shown.  
The record-type selectors change what part of the DNS data model is being queried.

Understanding these modifiers is what turns DNS lookups from a simple hostname-to-IP action into real reconnaissance.

## TARGETS

The domain examples serve two separate teaching purposes.

One target is used to demonstrate how verbose output is structured and interpreted field by field. The other is used to show concise answer retrieval.

The IP example exists to illustrate reverse lookup behavior, which is a useful pivoting technique during infrastructure analysis.

## READING THE FULL DIG OUTPUT

The module breaks the response into header, question, answer, and footer. That structure matters because each part answers a different recon question.

The header shows whether the query succeeded and what flags were involved. This helps identify whether recursion was requested, whether the answer is considered authentic, and whether the server responded normally.

The question section confirms exactly what was asked. This is important because mistakes in record type or target can easily lead to wrong conclusions.

The answer section contains the main intelligence. In the example, that is the address mapping and the time-to-live value. TTL matters because it tells you how stable or frequently refreshed the answer may be.

The footer provides operational context such as which DNS server answered, how long the request took, when it happened, and response size. These details are useful for troubleshooting, resolver comparison, and understanding network behavior.

## WHY THIS MATTERS FOR RECON

DNS is one of the most information-dense passive-to-lightly-active recon layers available. Even a single query can reveal service ownership, hosting choices, external providers, and segmentation of infrastructure.

Mail records can expose third-party email providers.  
Name server records can expose DNS hosting providers.  
Text records can expose security configurations or verification tokens.  
Authority records can reveal administrative structure.  
Alias records can reveal backend service relationships.

This means DNS recon is not just about resolving names. It is about mapping trust, dependencies, and external attack surface.

## OPERATIONAL CAUTION

The source warns that excessive DNS querying may be detected or blocked. That matters because DNS is often treated as low-noise, but large-scale or repetitive enumeration can still stand out.

This is especially relevant when moving from manual lookups into brute force, recursive discovery, or automated tooling. At that point, the activity becomes more visible and may trigger defensive controls.

## HOW THIS FITS INTO THE ATTACK FLOW

This section is the bridge between DNS theory and actionable enumeration.

The usual chain is:  
start with direct record lookups,  
identify authoritative servers and mail infrastructure,  
check aliases and text records for external dependencies,  
pivot into subdomain enumeration,  
then expand into deeper DNS attacks or related web recon methods.

In other words, this section teaches the transition from understanding DNS to using DNS as a structured reconnaissance source.

# SUBDOMAINS 

## Subdomains

Subdomains are extensions of a primary domain that allow organizations to separate services, applications, or environments under the same parent domain.

Examples might include areas dedicated to blogs, stores, email infrastructure, or development environments.

From a reconnaissance perspective, subdomains significantly expand the attack surface because they may host completely separate applications or services from the main website.

These systems are frequently overlooked during development or security reviews.

## Why Subdomains Matter in Recon

Subdomains often expose systems that are not linked from the main website and therefore remain hidden from normal users.

These systems can include testing environments, internal portals, and legacy applications.

Such systems frequently have weaker security configurations or outdated software.

## Development and Staging Environments

Organizations commonly deploy development or staging environments on subdomains to test new features before public release.

Because these systems are not meant for public exposure, security controls may be relaxed.

This can lead to vulnerabilities such as:

exposed debug interfaces  
misconfigured authentication  
test credentials  
incomplete access restrictions

These environments are therefore highly attractive targets during reconnaissance.

## Hidden Login Portals

Administrative interfaces or login panels are often placed on separate subdomains.

Examples include management dashboards, admin panels, or service portals.

If these systems are not properly secured, they may provide direct entry points into the infrastructure.

Even when authentication exists, discovering these panels is valuable because they become targets for credential attacks or further analysis.

## Legacy Applications

Older applications that are no longer actively maintained may remain deployed on subdomains.

These legacy systems often run outdated frameworks or libraries that contain known vulnerabilities.

Because they are rarely updated, they can represent one of the weakest points in an organization’s infrastructure.

## Sensitive Information Exposure

Subdomains sometimes expose sensitive information unintentionally.

Examples include:

configuration files  
internal documentation  
debug endpoints  
backup files

These exposures may provide insights into internal architecture or reveal credentials and system paths.

## Subdomain Enumeration

Subdomain enumeration is the process of discovering all subdomains associated with a target domain.

The goal is to build a comprehensive list of hosts that belong to the organization.

From a DNS perspective, subdomains typically appear in records mapping hostnames to IP addresses or alias relationships.

These DNS records allow reconnaissance tools to identify where each subdomain points and how it relates to other infrastructure.

## Active Subdomain Enumeration

Active enumeration involves directly interacting with the target’s DNS infrastructure.

This can include querying DNS servers repeatedly or attempting specific techniques designed to retrieve domain information.

One historical method involves attempting a DNS zone transfer.

If a DNS server is misconfigured, it may allow the transfer of the entire DNS zone file, revealing all subdomains at once.

However, modern DNS configurations typically block these requests.

A more practical active technique involves brute-force enumeration.

In this method, tools generate large numbers of potential subdomain names using wordlists and test whether they resolve.

Common wordlist entries might include names such as:

admin  
dev  
mail  
vpn  
test

If the DNS server resolves the hostname, the subdomain likely exists.

## Passive Subdomain Enumeration

Passive enumeration collects subdomain information from external data sources rather than querying the target directly.

This method is stealthier because it avoids sending requests to the target’s infrastructure.

One of the most valuable passive sources is certificate transparency logs.

When organizations obtain TLS certificates, the certificates often list associated subdomains in the Subject Alternative Name field.

These records are publicly logged, allowing analysts to discover subdomains used by the organization.

Search engines can also reveal subdomains through indexed pages.

By restricting search results to a specific domain, analysts may identify subdomains that appear in search engine results.

Additionally, specialized services collect DNS data from many sources and allow analysts to query these aggregated datasets.

## Active vs Passive Enumeration Strategy

Each enumeration approach has advantages and limitations.

Active enumeration can discover subdomains that have never been publicly indexed or logged.

However, it generates detectable traffic and may trigger monitoring systems.

Passive enumeration is stealthier because it relies on already available data.

However, it may miss newly created or private subdomains.

A comprehensive reconnaissance strategy typically combines both approaches.

Passive methods are often used first to collect existing intelligence.

Active methods are then used to expand the discovery set and identify additional assets.

# SUBDOMAIN BRUTEFORCING

## TOOLS

These are the main brute-force and enumeration tools named in the section. They all serve the same broad purpose: discovering subdomains, but they differ in workflow, automation depth, and how much they combine active and passive sources.

The important takeaway is that brute-force enumeration is not tied to one utility. It is a technique that can be implemented through several tools depending on speed, feature set, and operator preference.

## WORDLISTS

The wordlist is the core input that drives brute-force discovery. It contains candidate subdomain names that are appended to the target domain and tested one by one.

The listed wordlist is a common, high-value choice because it contains many frequently used subdomain names. This makes it useful when the target’s naming convention is unknown.

A good wordlist directly affects efficiency. Better guesses mean fewer wasted queries and faster discovery.

## COMMANDS

The single command shown is the practical demonstration of active subdomain enumeration with the DNS enumeration tool.

Its purpose is to:  
identify DNS records for the main domain,  
attempt broader DNS enumeration,  
and brute-force subdomains using the supplied wordlist.

The recursive option expands the discovery process by testing deeper levels once new subdomains are found. That matters because organizations sometimes nest services under already discovered hosts.

This command is valuable in the lab because it demonstrates a complete active enumeration workflow rather than a one-off lookup.

## FLAGS

The main enumeration flag enables the tool’s bundled recon behavior and tuning shortcuts.

The wordlist flag tells the tool which candidate names to test. Without this input, brute-force discovery would not have a structured source of guesses.

The recursive flag is important because it broadens coverage. Instead of stopping at first-level discoveries, the tool continues probing beneath newly found hosts.

This increases completeness, but it also increases noise and query volume.

## TARGETS

The target domain is the organization being enumerated for subdomains. It is used to demonstrate how brute-force discovery works in a realistic setting.

The point of the target here is instructional: the reader sees how a parent domain becomes the base for generating many candidate hostnames during enumeration.

## HOW SUBDOMAIN BRUTEFORCING WORKS

The section breaks the process into four stages, and that sequence matters.

First, a wordlist is selected. This determines the quality of the guesses.

Second, the tool iterates through the list and combines each candidate with the main domain to generate possible hostnames.

Third, DNS lookups are performed to check whether those generated names resolve.

Fourth, successful resolutions are filtered and treated as valid findings, which can later be validated at the web or service layer.

That sequence is the core logic of brute-force enumeration.

## WHY IT WORKS

This method works because many organizations use predictable subdomain naming conventions.

Common names such as development, staging, support, mail, admin, and test are reused across industries and environments. A strong wordlist exploits that predictability.

Even when a subdomain is not linked publicly, DNS may still resolve it, making brute-force discovery effective against hidden assets.

## WHY IT MATTERS IN RECON

Brute-force subdomain discovery is important because subdomains often host:  
development systems,  
legacy applications,  
support portals,  
internal tooling,  
or forgotten services.

These are frequently weaker than the main site and may contain vulnerabilities, default credentials, or outdated software.

So the real value is not the subdomain name itself. The value is the new attack surface it exposes.

## LIMITATIONS AND PITFALLS

This technique depends heavily on the quality of the wordlist. A poor list misses targets. An overly broad list creates unnecessary noise and slower scans.

Wildcard DNS can also create false positives, where many non-existent names appear to resolve. That means results may need validation.

Recursive enumeration can improve coverage, but it also increases request volume and detectability.

Another limitation is that brute-force only finds names you can guess. If the organization uses obscure naming conventions, passive sources or custom wordlists may be needed.

## HOW THIS FITS INTO THE METHODOLOGY

This is a direct continuation of DNS recon.

A normal workflow is:  
start with basic DNS record inspection,  
identify name servers and infrastructure clues,  
then use brute-force enumeration to expand the list of known hosts,  
then validate discovered hosts through HTTP probing, fingerprinting, and crawling.

So this stage turns DNS understanding into practical asset discovery.

## ATTACKER MINDSET

The attacker is not looking for random names. The attacker is looking for mistakes in exposure.

A newly discovered subdomain may reveal:  
an old login portal,  
an exposed testing environment,  
a staging API,  
or a support application not intended for public use.

That is why subdomain brute-forcing is so valuable. It finds systems defenders often forget are reachable.


# DNS ZONE TRANSFERS 

**OVERVIEW**

A DNS zone transfer is a replication mechanism used between DNS servers so a secondary server can copy the full contents of a DNS zone from a primary server.

In normal operation, this supports redundancy and consistency. In reconnaissance, it becomes extremely valuable when misconfigured because it can expose the entire DNS map of a target in one step.

**WHY IT MATTERS**

Unlike brute-forcing, which depends on guessing names from a wordlist, a successful zone transfer can reveal the full set of published DNS records directly from the authoritative source.

That makes it one of the most efficient DNS discovery opportunities during recon.

It may expose hidden hosts, infrastructure layout, and operational details that would otherwise require many separate lookups.

**HOW THE PROCESS WORKS**

The secondary DNS server requests a full transfer of the zone.

The primary server responds by sending the authority record and then the rest of the zone contents.

Once all records are transferred, the receiving server acknowledges completion.

The key issue is simple: if a DNS server allows this transfer to unauthorized clients, anyone can retrieve the zone contents.

**THE VULNERABILITY**

The weakness is not in DNS itself, but in access control.

If zone transfers are not restricted to trusted secondary servers, an external party may be able to retrieve:

all subdomains  
mapped IP addresses  
mail infrastructure  
name servers  
service records  
other internal naming clues

This gives an attacker a much more complete infrastructure map than normal DNS enumeration usually provides.

**WHAT CAN BE LEARNED**

A successful transfer may reveal:

hidden subdomains  
development or staging systems  
administrative services  
mail routing details  
service locations  
reverse mapping clues  
authoritative server information

This is especially useful because many of these systems may not be linked from the public website at all.

**THE COMMAND IN OUTPUT A**

The command requests a full zone transfer from a specific name server for the target domain.

It works by explicitly asking the authoritative server for the entire zone contents rather than a single record.

The important part is that this is only successful when the server is intentionally permissive or misconfigured.

In the lab example, the target is deliberately configured to demonstrate this risk.

**WHY THIS LAB WORKS**

The example domain is a training target created specifically to allow zone transfers.

That means the command returns the full zone data so the learner can see what such a disclosure looks like in practice.

In a real assessment, most properly configured servers will deny the request.

**RECORD TYPES SHOWN**

The records shown in the example illustrate how rich a successful transfer can be.

Authority records reveal administrative zone details.

Mail records reveal email handling infrastructure.

Address records reveal host-to-IP mappings.

Name server records reveal DNS delegation.

Text records may expose verification data or operational clues.

Service and pointer records can expose additional infrastructure relationships.

Even unusual record types are useful because they may hint at older technologies or niche internal services.

**DEFENSIVE TAKEAWAY**

Modern DNS servers should restrict zone transfers to approved secondary servers only.

If this is done correctly, unauthorized requests fail.

Because misconfigurations still happen, zone transfer testing remains a worthwhile recon step when authorized.

**METHODOLOGY FIT**

This technique fits naturally after discovering authoritative name servers.

A common flow is:

identify NS records  
test whether any authoritative server allows a transfer  
if denied, continue with brute-force and passive enumeration  
if allowed, pivot immediately into validating discovered hosts and services

So zone transfer testing is a high-value, low-effort check that can dramatically accelerate DNS reconnaissance when it works.


# VIRTUAL HOSTS

## CONFIGURATION EXAMPLES

This configuration demonstrates name-based virtual hosting, where multiple websites are hosted on the same server and port, and the web server decides which content to serve based on the requested hostname.

Each virtual host block maps a specific server name to a different document root. That means one IP address can serve multiple distinct sites as long as the incoming request contains the correct host value.

This matters in recon because hidden sites may exist on the same server even when normal DNS discovery does not reveal them.

## TOOLS

These tools automate virtual host discovery by sending many HTTP requests with different host values and comparing responses.

The goal is to identify additional sites or applications configured on the same server that are not obvious from public DNS records.

The main tool demonstrated here is a general-purpose brute-forcing utility used in virtual host mode. The other listed tools serve a similar purpose with different implementations and filtering options.

## COMMAND PATTERNS

This pattern shows the general structure used to brute-force hostnames against a target server.

The request is sent to a known target address while candidate hostnames are tested from a wordlist. The append-domain behavior is important because it builds full virtual hostnames from short wordlist entries.

This is what turns a raw list such as forum or dev into complete hostnames that the server may recognize.

## COMMANDS

The example command applies virtual host brute-forcing against the lab target using a large DNS-oriented wordlist.

Its purpose is to discover hostnames configured on the server that may not appear in public DNS data. This works because the server may respond differently when it receives a matching host value.

In the lab, the discovery of an additional hostname demonstrates the core idea: a site can exist on the server even if it was not previously visible through standard browsing.

## FLAGS

The target flag points the tool at the server to test.

The wordlist flag provides the candidate hostnames.

The append-domain flag is especially important in this lab because newer versions require it to construct full hostnames correctly.

The thread flag increases concurrency and makes scanning faster, though it also increases traffic volume.

The certificate-ignore flag helps when HTTPS targets use invalid or mismatched certificates.

The output flag is useful for preserving findings for later validation and note-taking.

## TARGETS

The placeholder target shows the generic structure of a scan against a known server address.

The lab target shows a practical example where the server is reachable on a specific host and port, and the tester brute-forces possible virtual hostnames on top of that.

The important recon lesson is that the server address alone is not always the full picture. Different content may appear depending on the host value supplied in the request.

## WORDLISTS

The wordlist is the source of candidate names used during host-header brute-forcing.

A larger wordlist increases the chance of finding non-obvious virtual hosts, especially when organizations use conventional names such as forum, dev, admin, support, or test.

The tradeoff is more traffic, more time, and potentially more noise.

## VHOSTS NEEDED

This identifies the base host relevant to the lab environment.

From a methodology standpoint, the base host serves as the namespace onto which candidate prefixes are attached during discovery.

That is why understanding the target naming pattern matters before brute-forcing begins.

## HOST HEADER

The host header is the central mechanism behind virtual host discovery.

Web servers inspect this header to determine which configured site should handle the request. If the header matches a valid virtual host, the server may return a different application or page than it would for the default site.

That is the key distinction between DNS-based subdomain discovery and virtual host discovery: the hostname may exist only in server configuration, not in public DNS.

## HOW THIS FITS INTO RECON

Virtual host discovery usually follows after DNS and subdomain enumeration.

A common workflow is:  
discover the main domain and IP,  
enumerate public subdomains,  
identify the web server,  
then fuzz host values to uncover hidden sites configured on the same server.

This technique is especially useful when DNS results appear limited but the server may still host additional internal, staging, or forgotten applications.

## ATTACKER MINDSET

The attacker is looking for applications that defenders assumed were obscure enough to stay unnoticed.

A hidden forum, development panel, or internal app may not be linked publicly and may not resolve in DNS, but if it is configured on the web server, host-header fuzzing can still uncover it.

That makes virtual host discovery a powerful way to expand the attack surface beyond what DNS alone reveals.

# CERTIFICATE TRANSPARENCY LOGS

## TOOLS

These tools allow analysts to search public certificate transparency databases.

One service provides a simple interface specifically designed to search certificate issuance records. Another provides a broader search engine for internet infrastructure and certificates with advanced filtering capabilities.

The command-line utilities are used to automate searching and filtering results when querying transparency log data through an API.

## COMMANDS

The pipeline retrieves certificate data related to a target domain from a public transparency log service and filters the results to extract subdomains containing a specific keyword.

The first stage requests certificate records associated with the target domain in JSON format.

The second stage processes the returned data and extracts only entries that contain a particular substring in the domain name.

The final stage sorts the results and removes duplicates to produce a clean list of unique findings.

This automated workflow allows analysts to quickly extract relevant subdomains from large certificate datasets.

## API ENDPOINTS

The endpoint used in the query returns certificate records associated with a domain in JSON format. The JSON output includes fields describing certificate details, including domain names listed in the certificate.

Using the API rather than the web interface allows automation and scripting, which is valuable during large reconnaissance engagements.

## JSON FIELDS

The specific JSON field referenced contains domain names associated with a certificate.

Certificates commonly include multiple domain names through Subject Alternative Name entries, which means a single certificate may reveal several subdomains.

Parsing this field enables analysts to extract those hostnames efficiently.

## KEYWORDS

Filtering for specific keywords can help focus reconnaissance on environments of interest.

For example, filtering for a development-related keyword can reveal testing environments or internal staging systems that might otherwise be hidden.

Such environments frequently have weaker security controls and therefore represent attractive targets during reconnaissance.

## TARGETS

The example target domain demonstrates how certificate transparency logs can expose numerous subdomains associated with a single organization.

These domains may include development systems, internal service hosts, or region-specific infrastructure.

Because certificates must be publicly logged when issued by trusted authorities, these records provide a historical record of domain usage.

## WHAT CERTIFICATE TRANSPARENCY LOGS ARE

Certificate Transparency logs are publicly accessible ledgers that record the issuance of SSL/TLS certificates.

Whenever a certificate authority issues a certificate for a domain, that certificate is submitted to multiple transparency logs.

These logs maintain append-only records, meaning entries cannot be deleted or modified after they are added.

This transparency allows anyone to verify which certificates have been issued for a domain.

## WHY CERTIFICATE TRANSPARENCY EXISTS

The primary purpose of certificate transparency is to detect misissued or malicious certificates.

If a certificate authority incorrectly issues a certificate for a domain to an attacker, the certificate will appear in the public log.

Security teams can monitor these logs and revoke suspicious certificates before they are abused.

This mechanism helps strengthen trust in the web’s public key infrastructure.

## HOW CT LOGS HELP RECONNAISSANCE

For reconnaissance purposes, certificate transparency logs provide a valuable data source for discovering subdomains.

When certificates are issued for subdomains, those names are recorded in the certificate’s subject alternative name list.

Because these certificates must be logged publicly, those subdomains become visible even if they are not listed in DNS or indexed by search engines.

This allows analysts to discover hostnames that might otherwise remain hidden.

## ADVANTAGES OVER BRUTE FORCE

Traditional subdomain enumeration methods rely on guessing names from wordlists.

Certificate transparency logs instead provide actual recorded hostnames that were included in certificates.

This means the results are often more accurate and may include unusual or organization-specific naming conventions that wordlists would miss.

It also reduces the need for high-volume brute-force DNS queries.

## HISTORICAL DISCOVERY VALUE

Transparency logs also maintain historical certificate data.

This means analysts may discover subdomains associated with older certificates that are no longer actively used.

These legacy systems can sometimes remain accessible and may run outdated software, making them particularly interesting targets during reconnaissance.

## HOW THIS FITS INTO RECON WORKFLOWS

Certificate transparency searches are typically performed early in reconnaissance as part of passive enumeration.

A typical workflow might include:

domain ownership investigation  
DNS enumeration  
certificate transparency searches  
subdomain brute forcing  
virtual host discovery

Using CT logs early can significantly expand the list of potential targets before active scanning begins.

## ATTACKER MINDSET

From an attacker perspective, certificate transparency logs provide a reliable record of infrastructure that an organization has exposed through TLS.

Every certificate issued for a subdomain effectively becomes a clue about internal architecture, development environments, or service naming conventions.

These clues can then guide further reconnaissance and targeted enumeration efforts.

# FINGERPRINTING 

Fingerprinting is the phase where you identify the technologies behind a target so later testing becomes more focused and efficient. Instead of treating the website as a black box, you build a technical profile of the server, framework, middleware, and defensive controls.

The value of this phase is that it narrows the attack surface. Once you know the web server, the operating environment, the application platform, and whether a protective layer is present, you can prioritize techniques that actually fit the target instead of probing blindly.

The **TOOLS** section shows both passive and active fingerprinting options. Some tools are broad technology profilers, some are better for web stack identification, and some focus on defensive controls. The important idea is that fingerprinting is not a single action. It is usually a layered process where you confirm one clue with another.

The first three entries in **COMMANDS** represent manual header inspection. This works because HTTP responses often reveal implementation details directly. The initial request exposes the front-facing server behavior, while following the redirects reveals more about the application logic behind the site. In this example, the progression is important because each response leaks a little more than the previous one. The first step identifies the web server family. The next step exposes the redirect logic. The final step reveals application-specific endpoints that strongly indicate the CMS in use.

The **HEADERS** section contains the main artifacts used for manual fingerprinting. The server header is the classic banner source. Framework or platform hints often show up in redirect-related or powered-by style headers. Link-related headers may expose API endpoints or framework-specific structures. Security headers also matter because their absence or weakness can reveal configuration issues, maturity level, or hardening gaps.

The **PATHS** section is especially useful because application frameworks often leave recognizable routes behind. A JSON API path with a well-known naming convention is a strong platform indicator. Login routes and license files are also high-value because they can confirm application type, expose versioning clues, or reveal administrative entry points.

The installation step for the firewall fingerprinting tool in **COMMANDS** matters because defensive controls can distort later recon results. If a site is behind a web application firewall, later requests may be filtered, blocked, normalized, or responded to artificially. Recognizing that early changes how you interpret behavior from every later step.

The firewall detection command in **COMMANDS** is important because it identifies not just that protection exists, but what kind of protection is present. That helps explain why some later enumeration attempts may behave inconsistently. It also helps you understand whether unusual HTTP behavior comes from the application itself or from an intermediary security product.

The software installation steps for the web scanner in **COMMANDS** show preparation for broader fingerprinting. This stage is not just about finding vulnerabilities. In this module, the scanner is being used to extract technology and configuration details. That is why the tuning option matters: it narrows execution toward software identification rather than a broader assessment.

The scanner command in **COMMANDS** is valuable because it consolidates many fingerprinting clues into one pass. In practice, it confirms what the manual checks suggested and then adds more context. It can identify the server family, probable application platform, visible files, weak header posture, and administrative interfaces. The point is not just automation for speed, but automation for correlation.

The **TECHNOLOGIES** section reflects the kind of conclusions fingerprinting is meant to produce. A web server version suggests operating environment and patch age. A CMS fingerprint suggests likely administrative paths, plugin ecosystems, and common weaknesses. A named WAF suggests filtering behavior and defensive middleware. Certificate and TLS details give clues about transport security and issuance practices.

The **URLS** section matters because recon often pivots from headers into discovered endpoints and from discovered endpoints into platform-specific testing. Public documentation links shown in the material are part of explanation, but the target URLs themselves are the real recon pivots because they expose where the application logic and APIs live.

The **TARGETS** section includes both the public domain and the internal-style virtual hosts relevant to the lab. That teaches an important methodology point: fingerprinting is not always limited to the main public hostname. Once additional hosts are identified through vhost discovery or DNS work, each one may need separate fingerprinting because different hosts can run different stacks on the same infrastructure.

Overall, this section fits into the workflow after DNS and host discovery. First you identify what hosts exist. Then you fingerprint each one to understand what it runs. That information drives the next steps: targeted content discovery, CMS-specific checks, login surface analysis, plugin and theme investigation, WAF-aware probing, and version-based vulnerability research.


# CRAWLING 

## Crawling Overview

Crawling, also called spidering, is the automated process of systematically navigating through a website by following links between pages. The crawler begins with a starting point and continuously discovers new pages by extracting links from the content it visits.

In web reconnaissance, crawling is used to map a website’s structure and identify pages, endpoints, files, and resources that might not be directly visible from the homepage.

Unlike fuzzing, which guesses possible paths, crawling follows links that already exist within the application.

## How Crawlers Operate

The crawling process begins with a **seed URL**, which is the starting page of the website. The crawler downloads the page and analyzes its content to identify hyperlinks.

Every discovered link is placed into a queue. The crawler then visits each queued link, repeating the process by extracting additional links from those pages.

Over time, this iterative process allows the crawler to build a comprehensive map of the website.

This method allows the discovery of:

hidden pages  
deeply nested resources  
API endpoints  
document repositories

## Crawling Strategies

There are two primary traversal strategies used by web crawlers.

### Breadth-First Crawling

Breadth-first crawling prioritizes exploring pages at the same level before diving deeper into the structure.

The crawler starts at the homepage, gathers all immediate links, then visits those pages before exploring deeper links.

This strategy is useful for quickly mapping the overall structure of a site.

### Depth-First Crawling

Depth-first crawling follows a single path of links as far as possible before backtracking.

This strategy explores the deepest parts of a site more quickly and can help reach deeply nested content that may not be obvious from the main navigation.

The strategy selected depends on the reconnaissance goal.

## Information Extracted During Crawling

Web crawlers can collect many types of information beyond just page URLs.

### Links

Links are the core output of crawling. These include both internal links within the same site and external links pointing to other domains.

Collecting links helps build a structural map of the application.

### Comments

Comments embedded in HTML or posted by users on forums and blogs may reveal internal information.

Developers sometimes leave debugging notes, version information, or references to internal systems within comments.

### Metadata

Metadata contains descriptive information about the page.

Examples include page titles, descriptions, keywords, and author data.

These details can reveal the purpose of pages or technologies used by the site.

### Sensitive Files

Crawlers can discover files that should not be publicly accessible.

Examples include backup files, configuration files, and server logs.

These files may expose:

credentials  
API keys  
database connections  
internal system paths

## Importance of Context in Reconnaissance

Individual pieces of information rarely reveal vulnerabilities on their own.

The real value of crawling comes from **correlating multiple findings**.

For example, discovering several URLs referencing the same directory might suggest that the directory exists publicly.

Investigating that directory manually could reveal exposed files or directory listing vulnerabilities.

Similarly, comments mentioning internal systems may become significant when combined with discovered directories or configuration files.

Reconnaissance therefore requires interpreting results collectively rather than examining them in isolation.

## Role of Crawling in Web Recon

Crawling is typically performed after initial reconnaissance stages such as DNS discovery, host enumeration, and fingerprinting.

Once a target host is identified, crawling helps uncover the internal structure of the web application.

This information feeds directly into later phases such as:

content discovery  
endpoint enumeration  
parameter analysis  
vulnerability testing

By mapping the entire accessible structure of a site, crawlers reveal parts of the attack surface that might otherwise remain hidden.

# ROBOTS.TXT 

## What robots.txt Is

The robots.txt file is a plain text configuration file placed in the root directory of a website. It follows the Robots Exclusion Standard and provides instructions for automated crawlers about which parts of a website they are allowed to access.

It functions as a guidance document for bots rather than a security mechanism. Legitimate crawlers typically follow its rules, while malicious bots may ignore them.

## Location and Structure

The file is always located in the root of a website. Crawlers check this file before performing large-scale indexing operations.

The file is composed of records separated by blank lines. Each record usually contains two main parts: a user-agent identifier and a set of directives that apply to that crawler.

A user-agent defines which crawler the rules apply to. The wildcard character represents all crawlers.

Directives provide the actual instructions that define which paths should be allowed or restricted.

## Common Directives

Several directives are commonly used within robots.txt files.

The disallow directive specifies directories or files that crawlers should avoid accessing. This is often used to prevent indexing of administrative areas or internal resources.

The allow directive explicitly permits access to certain paths even if they fall within a disallowed directory.

The crawl delay directive controls how frequently a crawler can send requests. This helps prevent excessive load on the server.

The sitemap directive provides the location of an XML sitemap so crawlers can index the website more efficiently.

## Why Websites Use robots.txt

Website owners use robots.txt primarily for operational reasons rather than security.

Limiting crawler access helps prevent unnecessary indexing of private or duplicate content. It also reduces server load by preventing bots from crawling large or irrelevant sections of the site.

In some situations, it helps ensure compliance with policies or legal restrictions related to sensitive or copyrighted material.

## robots.txt in Reconnaissance

For reconnaissance, robots.txt can reveal valuable information about the internal structure of a website.

Disallowed paths often reference directories that the site owner does not want indexed by search engines. These directories may contain administrative interfaces, backup files, development environments, or other sensitive content.

Even though these paths are hidden from search engines, they remain accessible to anyone who knows the URL.

## Discovering Hidden Resources

Security professionals frequently inspect robots.txt to identify hidden directories or endpoints.

For example, if a directory is explicitly disallowed, that directory may contain administrative tools or internal content.

This information can guide further exploration and help locate resources that are not linked in the website navigation.

## Mapping Website Structure

The robots.txt file can also reveal structural information about the application.

By reviewing allowed and disallowed paths, analysts can infer which sections of the site exist and how the application is organized.

This information can assist with planning deeper crawling or targeted testing.

## Detecting Defensive Measures

Some websites intentionally place fake or trap directories inside robots.txt to detect malicious crawling behavior.

These directories function as honeypots. If a bot attempts to access them, it may indicate automated scanning activity.

Finding such traps can signal that the site has defensive monitoring mechanisms in place.

## Reconnaissance Context

robots.txt analysis typically occurs during the crawling stage of reconnaissance.

It complements automated spidering by revealing areas that may not be reachable through normal link traversal.

Combining crawler results with robots.txt findings can provide a more complete view of the website’s structure and potential attack surface.

# WELL-KNOWN URIS 

## Overview of .well-known

The `.well-known` directory is a standardized location on web servers used to store machine-readable metadata and configuration files. It was defined by RFC 8615 to provide a predictable place where clients and services can retrieve important configuration information.

Instead of guessing where certain configuration files may exist, software can simply request a standardized path under `.well-known`.

This convention allows browsers, authentication systems, applications, and security tools to automatically discover configuration information for services running on a domain.

## Purpose of the Standard

The goal of `.well-known` is to simplify service discovery. Many internet protocols require specific metadata to function correctly, such as authentication configuration or security contact information.

By storing this metadata in a predictable location, systems can automatically discover configuration details without manual configuration.

This improves interoperability and reduces configuration complexity.

## Examples of Well-Known URIs

Several standardized URIs exist under `.well-known`.

A security reporting file may provide instructions for responsible vulnerability disclosure and contain contact information for reporting security issues.

A password change URI provides a standardized location where browsers or password managers can redirect users when they want to update credentials.

Some URIs are used to support authentication frameworks, while others help verify domain ownership for applications.

Email security protocols can also store their configuration within this directory.

Each of these URIs serves a specific protocol or operational purpose and follows documented specifications.

## OpenID Configuration Endpoint

One of the most useful endpoints for reconnaissance is the OpenID configuration endpoint.

OpenID Connect is an authentication layer built on top of OAuth 2.0. Applications using this system expose configuration metadata through the `.well-known/openid-configuration` path.

When accessed, this endpoint returns a structured JSON document describing how authentication is implemented.

This document contains information about authorization endpoints, token issuance endpoints, and user information endpoints.

It also lists supported authentication response types, scopes, and cryptographic signing algorithms.

## Reconnaissance Value

For security professionals, these configuration endpoints can reveal important details about authentication infrastructure.

The returned metadata can expose the locations of authorization servers, token endpoints, and identity APIs.

These endpoints may later become targets for testing authentication flows, authorization logic, or token handling.

Understanding the authentication architecture also helps map the overall application ecosystem.

## Endpoint Discovery

Configuration data retrieved from these endpoints can reveal several new attack surface components.

The authorization endpoint handles authentication requests.

The token endpoint is responsible for issuing tokens during OAuth flows.

The user information endpoint provides identity information about authenticated users.

A JSON Web Key Set endpoint exposes the cryptographic keys used to validate tokens.

Each of these endpoints represents an important piece of the identity infrastructure.

## Security Analysis Opportunities

The metadata returned by these endpoints also reveals supported authentication methods and token signing algorithms.

Understanding the allowed algorithms can help analysts determine whether secure cryptographic practices are being used.

Supported scopes indicate what types of access permissions exist within the authentication system.

Response types help analysts understand how tokens and authorization codes are issued during authentication flows.

This information is useful when designing tests for authentication weaknesses.

## Reconnaissance Methodology

During web reconnaissance, analysts often inspect `.well-known` directories to identify available service metadata.

Because these locations are standardized, checking them is quick and often reveals configuration details that would otherwise require deeper analysis.

These endpoints may expose authentication mechanisms, security contact information, asset verification data, or mail security policies.

Reviewing them can significantly improve understanding of how a web application’s supporting infrastructure operates.

## Role in Web Recon

The `.well-known` directory is typically inspected during the crawling and endpoint discovery stages of reconnaissance.

It complements techniques like directory enumeration, subdomain discovery, and fingerprinting.

By combining the information retrieved from these endpoints with other reconnaissance findings, analysts can build a clearer map of the target’s authentication systems, services, and security configurations.

# CREEPY CRAWLIES 

## TOOLS

This section introduces both general-purpose and specialized crawling tools.

The proxy-based crawlers are useful for interactive application mapping and are common in web testing workflows.

The Python framework is highlighted because it supports custom crawling logic, which makes it useful for tailored reconnaissance rather than generic browsing alone.

The scalable Java crawler is included to show that crawling can range from focused application mapping to large-scale collection.

The command-line helpers are not recon tools by themselves. They are used to install dependencies, retrieve the custom spider, unpack it, and execute it.

## COMMANDS

The package installation command prepares the Python crawling framework required for the custom spider.

The file download command retrieves the provided reconnaissance spider archive from the academy storage location.

The archive extraction command unpacks the spider files into the current working directory so they can be executed locally.

The Python execution command launches the custom spider against the target domain. This is the key operational step because it performs the crawl and collects structured recon data.

Together, these commands form a complete mini workflow:  
prepare the framework,  
retrieve the custom spider,  
extract it,  
run it against the target.

## FILES

The archive file contains the provided spider package.

The Python script is the actual crawler used for reconnaissance.

The JSON file is the structured output artifact. It is the main result of the crawl and becomes the basis for later analysis.

These files matter because this section is not only about crawling the site, but also about preserving extracted intelligence in a reusable format.

## URLS

The archive URL is the source of the custom spider package.

The target URL is the domain being crawled.

The remaining URLs are examples of the kinds of artifacts the spider can extract from a site:  
external links,  
internal pages,  
documents,  
JavaScript resources,  
and media files.

This matters because crawling is valuable not just for page discovery, but for collecting all linked assets that may reveal technology, business logic, or sensitive content.

## JSON KEYS

These keys define the categories of information the spider extracts.

Email addresses can reveal contacts, naming patterns, and potential phishing targets.

Links help map site structure and reachable content.

External files can expose documents such as PDFs that may contain metadata or internal information.

JavaScript files are high-value because they often reveal endpoints, client-side logic, and technology choices.

Form field discovery helps identify input surfaces, workflows, and possible authentication or data entry points.

Images, videos, and audio can reveal media assets and sometimes metadata.

Comments are often especially interesting in recon because developers may leave hints, paths, disabled features, or operational notes in HTML.

## JSON STRUCTURE

The example output shows how the spider organizes findings into a machine-readable format.

That structure matters because it turns raw crawl output into categories that can be analyzed systematically. Instead of manually reading every page, the operator can quickly inspect the extracted buckets of intelligence and decide where to focus next.

A results file like this is useful because it supports correlation. For example:  
emails can be tied to organizational naming conventions,  
links can be reviewed for hidden paths,  
external files can be checked for sensitive documents,  
JavaScript files can be inspected for endpoints,  
comments can be reviewed for clues about the application.

## HOW THIS FITS INTO RECON

This section moves crawling from theory into execution.

Earlier crawling material explained what spidering is and why it matters. This section demonstrates how to operationalize it with a custom tool that gathers structured data rather than just a simple page list.

In methodology terms, this usually comes after identifying the host and fingerprinting the stack. Once you know what site you are dealing with, crawling helps uncover its content, relationships, and artifacts at scale.

## ATTACKER MINDSET

The attacker is not crawling just to collect pages.

The attacker is looking for useful byproducts:  
email addresses for social engineering,  
documents for data leakage,  
JavaScript for hidden endpoints,  
comments for developer clues,  
forms for input attack surface,  
and links that expose forgotten sections of the application.

The real value is in what those extracted artifacts reveal when reviewed together.

# SEARCH ENGINE DISCOVERY 

## Overview

Search engine discovery is an Open Source Intelligence (OSINT) technique that uses search engines to gather information about a target. Instead of directly interacting with the target infrastructure, analysts leverage indexed web data collected by search engines.

Because the data comes from public sources, this technique is passive and generally safe to perform during early reconnaissance.

Search engines can reveal information that may not be easily visible on a website itself. They may index documents, archived pages, hidden endpoints, or references to systems that are not directly linked in site navigation.

## Why It Matters

Search engine discovery is powerful because search engines index enormous amounts of internet data.

This allows analysts to identify:

public documents  
login portals  
employee information  
misconfigured systems  
exposed backups or configuration files

Since the information is already indexed publicly, this method provides insights without generating traffic toward the target.

This makes it an effective starting point for reconnaissance.

## Search Operators

Search operators are modifiers that refine search queries and allow analysts to locate specific types of content.

Instead of returning broad results, operators narrow the scope of a query to reveal highly targeted information.

For example, restricting results to a specific domain helps analysts enumerate publicly indexed pages belonging to an organization.

Other operators allow filtering results based on URL structure, file type, page title, or page content.

Using these operators together allows analysts to construct highly precise queries capable of revealing sensitive or interesting information.

## File Discovery

One common use of search operators is locating publicly accessible files.

Documents such as PDFs, spreadsheets, and presentations are often indexed by search engines.

These files may contain:

internal reports  
employee information  
technical documentation  
system configuration details

Discovering these files can provide valuable intelligence during reconnaissance.

## Finding Login Pages

Search operators can also reveal login portals.

Login interfaces sometimes exist on paths that are not linked publicly but still indexed by search engines.

Identifying these interfaces early can highlight authentication entry points that may later become targets for further testing.

## Configuration Files and Backups

Another valuable use of search queries is identifying configuration files or database backups.

These files may be exposed due to misconfiguration or accidental upload to public directories.

Search queries targeting configuration extensions or backup-related keywords can reveal these files if they have been indexed.

Such discoveries can expose credentials, internal infrastructure details, or system architecture information.

## Advanced Query Techniques

Search operators can be combined with logical operators to produce more powerful queries.

Logical operators allow analysts to include multiple terms, exclude specific terms, or broaden search results to include multiple variations.

Wildcards and range searches allow analysts to locate information even when the exact structure or wording is unknown.

Using these techniques effectively requires experimentation and refinement of queries.

## Google Dorking

Google Dorking refers to the practice of using advanced search queries to identify sensitive information or security weaknesses.

These queries combine multiple operators to locate specific types of content on a domain.

For example, analysts may search for login interfaces, exposed documents, configuration files, or backup databases.

Large collections of such queries exist and are commonly used by security researchers to identify potential security exposures.

## Reconnaissance Workflow

Search engine discovery typically occurs early in the reconnaissance phase.

It complements other techniques such as DNS enumeration and subdomain discovery.

The workflow often begins with identifying the domain and gathering publicly indexed pages.

Analysts then refine queries to locate specific types of content such as login pages, documents, or configuration files.

These findings can guide deeper reconnaissance activities such as crawling, directory discovery, or vulnerability testing.

## Limitations

Despite its usefulness, search engine discovery has limitations.

Search engines do not index every page on the internet. Some content may be hidden behind authentication or excluded through crawling restrictions.

Organizations may also remove sensitive content or prevent indexing through configuration mechanisms.

Therefore, search engine discovery should be combined with other reconnaissance methods to build a more complete picture of the target environment.

## Attacker Perspective

From an attacker’s perspective, search engines act as a powerful intelligence source.

They effectively perform large-scale crawling and indexing on behalf of the attacker.

Instead of discovering resources manually, attackers can query search engines to reveal data that has already been collected and categorized.

This can dramatically accelerate the reconnaissance phase and expose valuable information about a target’s digital presence.

# WEB ARCHIVES 
The Wayback Machine is a historical archive of web content. It stores snapshots of websites across time, allowing you to inspect how a target looked in the past rather than only its current state.

In reconnaissance, its value comes from historical visibility. Current websites often remove pages, rename directories, retire applications, or hide old content. Archived captures can still preserve those older states and expose information that is no longer visible live.

A key benefit is discovery of old assets. Previous versions of a site may reveal directories, pages, files, subdomains, forms, or technologies that no longer appear on the current version. Even if those resources are gone from navigation, they may still indicate patterns, naming conventions, or infrastructure history worth investigating.

Another important use is change tracking. Comparing older captures with newer ones helps identify how the site evolved. That can reveal migrations, redesigns, framework changes, removed login portals, deprecated applications, or shifts in exposed functionality.

This also supports intelligence gathering. Archived pages may contain employee names, old documents, past service offerings, contact details, technology references, or business information that is useful for profiling the target.

From an operational perspective, it is also a passive source. You are interacting with the archive, not directly probing the target’s infrastructure. That makes it a low-noise recon source.

The Hack The Box example illustrates this historical-view concept. The significance is not the specific site alone, but the fact that an early capture can reveal an earlier version of the platform, its branding, structure, and exposed content at that time. That is exactly the kind of perspective web archives provide during recon.

In methodology terms, web archives fit well after basic host discovery and search engine discovery. Once you know the target and some of its assets, archive review helps answer questions like:  
what used to exist,  
what changed,  
what disappeared,  
and what legacy clues remain useful.

The attacker mindset here is simple: defenders usually focus on what is exposed now, but older versions can still reveal patterns, forgotten resources, and historical weaknesses. Web archives let you mine that older attack surface without touching the target directly.

# AUTOMATING RECON 

## Overview

Automating reconnaissance involves using tools and frameworks to perform information-gathering tasks automatically rather than manually.

Reconnaissance often involves repetitive activities such as DNS enumeration, crawling websites, discovering subdomains, scanning ports, and gathering metadata. Performing these tasks manually can be slow and inefficient.

Automation allows these processes to run quickly and consistently while freeing the analyst to focus on interpreting results.

---

## Why Automation Is Important

Automation improves reconnaissance in several ways.

Efficiency increases because tools can execute tasks much faster than a human performing the same actions manually.

Scalability becomes possible because automation can gather information across many domains or targets simultaneously.

Consistency improves because automated tools follow the same predefined logic each time they run, reducing the chance of mistakes or missed steps.

Coverage becomes more complete because automated frameworks often combine many reconnaissance techniques into a single workflow.

Integration is also a major advantage. Many automation frameworks connect easily with vulnerability scanners, exploitation tools, or OSINT sources, creating a seamless pipeline from reconnaissance to testing.

---

## Reconnaissance Frameworks

Reconnaissance frameworks combine multiple reconnaissance techniques into a single tool or platform.

These frameworks typically support modules or plugins that allow analysts to run specific recon tasks such as:

DNS enumeration  
subdomain discovery  
web crawling  
email harvesting  
certificate analysis  
port scanning

Some frameworks focus primarily on OSINT, while others combine network scanning and web reconnaissance capabilities.

Examples include FinalRecon, Recon-ng, SpiderFoot, and theHarvester.

---

## FinalRecon

FinalRecon is a Python-based web reconnaissance tool designed to gather a large amount of information about a target website using modular commands.

It supports several types of reconnaissance modules including:

HTTP header analysis  
SSL certificate inspection  
WHOIS lookups  
DNS enumeration  
subdomain discovery  
web crawling  
directory discovery  
Wayback Machine data retrieval  
port scanning

These modules can be executed individually or combined to run a comprehensive reconnaissance scan.

---

## Installation Workflow

Installing FinalRecon involves cloning the repository, installing the required dependencies, and enabling execution permissions for the main script.

After installation, the help command provides a full list of modules and command options available within the tool.

This modular design allows users to run only the reconnaissance tasks they need rather than executing everything at once.

---

## Module Usage

Each module focuses on a specific reconnaissance function.

Header analysis reveals information about the web server and technologies used by the site.

SSL inspection gathers certificate data including the issuer and encryption configuration.

WHOIS queries provide domain registration details such as registrar information, domain creation dates, and name servers.

Crawling modules extract links, scripts, images, and other resources from the website.

DNS enumeration retrieves records related to the domain.

Subdomain enumeration searches external data sources for additional domain assets.

Directory discovery tests common directory names to find hidden paths on the server.

Wayback retrieval collects archived URLs from the Wayback Machine.

Port scanning checks which services are exposed on the target host.

---

## Reconnaissance Workflow with Automation

In practice, automation tools are often used early in the reconnaissance phase.

A typical workflow might involve:

identifying the target domain  
running automated reconnaissance tools  
collecting DNS and infrastructure data  
discovering subdomains and endpoints  
mapping website structure  
identifying potential entry points

The automated results are then analyzed manually to determine which findings are relevant or potentially exploitable.

---

## Attacker Perspective

From an attacker’s perspective, automation drastically speeds up the reconnaissance phase.

Instead of running multiple tools manually and collecting results separately, frameworks combine these processes into a single workflow.

This allows attackers to map a target's digital footprint quickly and identify possible weaknesses or exposed assets.

Automation therefore acts as a force multiplier, allowing a single operator to perform reconnaissance across many targets efficiently.