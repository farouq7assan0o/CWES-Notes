# Introduction 

**What This Section Covers**

This introductory section establishes the conceptual framework for web reconnaissance as the first phase of a penetration test. It defines the goals of recon and distinguishes between the two primary methodologies.

**Goals of Web Reconnaissance**

The section outlines four core objectives: asset identification (mapping the visible attack surface), discovery of hidden or inadvertently exposed information, attack surface analysis (finding weaknesses in technologies and configurations), and intelligence gathering for exploitation or social engineering. These goals apply equally to offensive and defensive security work.

**Active vs Passive Reconnaissance**

The fundamental distinction is whether the attacker directly interacts with the target.

Active reconnaissance involves direct contact - port scanning, vulnerability scanning, banner grabbing, OS fingerprinting, service enumeration, and web spidering. Because these techniques send traffic to the target, they carry a medium-to-high risk of detection by IDS, firewalls, or log review. The advantage is comprehensiveness and directness.

Passive reconnaissance relies entirely on publicly available data - search engine queries, WHOIS lookups, DNS record analysis, web archive review, social media analysis, and code repository inspection. Detection risk is very low since the attacker never touches the target directly. The tradeoff is that passive methods are limited to whatever has already been made public.

**Attacker Mindset**

Reconnaissance determines the quality of everything that follows. Skipping or rushing this phase leads to missed entry points and wasted effort during exploitation. The module frames recon as methodical and systematic, not opportunistic.

**Module Direction**

The section signals that the module begins with WHOIS as a foundational passive technique, then builds toward more advanced methods. Each technique taught will fit into either the active or passive column of this framework.




# WHOIS

**What WHOIS Is**

WHOIS is a query-response protocol for looking up registration data about internet resources - primarily domain names, but also IP address blocks and autonomous systems. It functions like a public registry, returning structured records about who owns or manages a given resource.

**What a WHOIS Record Contains**

A standard record includes the domain name, registrar, registrant contact, administrative contact, technical contact, creation and expiration dates, and name servers. Each of these fields has recon value depending on what the attacker is looking for.

**Why It Matters for Recon**

From an attacker's perspective, WHOIS is a low-risk passive technique that can immediately surface actionable intelligence. Key personnel fields expose names, email addresses, and phone numbers useful for social engineering and phishing target selection. Name server and IP address fields reveal network infrastructure and potential entry points. Registrar and date fields can indicate domain age, transfer history, or upcoming expiration - all potentially useful for domain hijacking research.

**Historical WHOIS Data**

Services like WhoisFreaks allow lookup of historical WHOIS records, which can show how ownership, contacts, or infrastructure changed over time. This is useful for tracking acquisition patterns, identifying previously exposed contacts, or spotting infrastructure reuse.

**Privacy Limitations**

GDPR compliance and privacy masking services mean that many modern WHOIS records will show proxy or redacted contact data rather than real registrant information, particularly for European targets. The Registration Data Access Protocol (RDAP) is the emerging successor to WHOIS and offers more granular, privacy-aware access. Attackers must account for this when assessing how much WHOIS data will actually be available for a given target.

**Placement in the Methodology**

WHOIS is a purely passive technique - no traffic reaches the target. It is typically one of the first steps in an engagement, providing a structural overview of the target's domain before moving to active techniques or deeper DNS enumeration.

# Utilising WHOIS 

**How This Section Is Structured**

The section uses three real-world scenarios to illustrate how WHOIS data is interpreted in practice, then demonstrates the command-line tool with a live example against `facebook.com`.

**Scenario 1 - Phishing Investigation**

A recently registered domain with hidden registrant information and name servers tied to a bulletproof hosting provider is a strong phishing indicator. None of these signals alone is conclusive, but their combination raises immediate suspicion. WHOIS is used here as a rapid triage tool before deeper investigation of the hosting infrastructure.

**Scenario 2 - Malware Analysis**

When a C2 domain is registered via an anonymizing email, routed through a lax registrar, and geolocated to a high-risk region, WHOIS helps build a picture of the threat actor's operational infrastructure. The hosting provider identified through WHOIS becomes a notification target and a pivot point for further investigation.

**Scenario 3 - Threat Intelligence**

Across multiple domains attributed to one threat actor, WHOIS data reveals patterns: clustered registration dates prior to attacks, shared name servers across campaigns, recurring aliases, and takedown history. These patterns form TTPs and produce indicators of compromise (IOCs) usable by other defenders.

**Reading the facebook.com Output**

The live example demonstrates how to interpret a real WHOIS record. Key observations include: the domain's age (registered 1997) establishes legitimacy; Meta Platforms, Inc. as registrant confirms ownership; multiple `clientX` and `serverX` prohibited statuses indicate the domain is locked against unauthorized transfer or modification; and the use of internal name servers (`A/B/C/D.NS.FACEBOOK.COM`) shows that Meta manages its own DNS infrastructure rather than relying on a third-party provider.

**Limitation to Internalize**

WHOIS alone is insufficient. Contact data may be redacted, proxied, or generic (e.g., "Domain Admin"). It provides structural and ownership context but must be combined with DNS enumeration, passive DNS, certificate transparency, and other techniques to build a complete picture of the target.

# DNS 

**What DNS Is**

DNS translates human-readable domain names into IP addresses. It is a hierarchical, distributed system - queries travel from a local resolver up through root name servers, then TLD name servers, then authoritative name servers, with each level narrowing toward the final answer. Results are cached at each layer to reduce repeat queries.

**The Hosts File**

Before DNS is consulted, the operating system checks the local hosts file. This file allows static, manual hostname-to-IP mappings that override DNS entirely. For recon and testing, this is frequently used to point a domain to a local or test server without changing actual DNS records. Editing requires root or administrator privileges but takes effect immediately.

**DNS Zones and Zone Files**

A DNS zone is an administrative boundary managed by a specific entity. The zone file is the authoritative text record of all DNS entries within that zone. Understanding zone file structure is important because zone transfer attacks (AXFR) attempt to retrieve the entire zone file from a misconfigured server, exposing all subdomains, mail servers, and infrastructure in one query.

**Record Types and Their Recon Value**

Each record type leaks different information. A and AAAA records map hostnames to IP addresses and reveal live hosts. CNAME records reveal aliasing relationships and can point to outdated or third-party infrastructure. MX records identify mail servers, which are often separately administered and may be less hardened. NS records reveal the hosting provider or DNS management platform. TXT records frequently contain verification strings for third-party services (SPF, DKIM, 1Password, Google Workspace, etc.), which reveal what SaaS tools the organization uses. SOA records identify the primary name server and administrative email. SRV records expose internal services and their ports. PTR records enable reverse lookups from IP to hostname.

**Why DNS Matters for Recon**

DNS data passively reveals the full shape of a target's infrastructure. Subdomains discovered via DNS may expose development environments, VPNs, internal tools, or forgotten servers that are less patched than the main application. Changes in DNS over time (new subdomains, new TXT records) signal infrastructure changes worth investigating. DNS enumeration is a passive technique when using public resolvers, making it low-risk and high-yield early in an engagement.

# Digging DNS 

**Tool Selection**

`dig` is the primary tool for DNS recon due to its flexibility and detailed output across all record types. `nslookup` and `host` are simpler alternatives suited for quick lookups. `dnsenum`, `fierce`, and `dnsrecon` automate enumeration tasks including subdomain brute-forcing and zone transfer attempts. `theHarvester` is an OSINT aggregator that pulls DNS and email data from multiple public sources simultaneously.

**Reading dig Output**

The dig response has four sections. The header shows query type, status (NOERROR means the query succeeded), and flag values. The question section confirms what was asked. The answer section contains the actual record data along with the TTL, which indicates how long the result will be cached. The footer shows query time, the responding server's IP and port, protocol (UDP or TCP), and message size. An optional `opt pseudosection` appears when EDNS is in use, enabling features like DNSSEC and larger response sizes.

**Key Flag Meanings**

`qr` confirms this is a response. `rd` means the client requested recursion. `ad` means the resolver considers the data authentic. Seeing the warning "recursion requested but not available" means the queried server is authoritative-only and will not perform recursive lookups on behalf of the client.

**Practical Query Patterns**

Specifying a resolver with `@` allows querying a specific DNS server rather than the system default, useful for testing authoritative servers directly or using a trusted public resolver. `+trace` follows the full resolution chain from root to authoritative server, which is valuable for understanding delegation and spotting misconfigurations. `-x` performs reverse lookups to map an IP back to a hostname. `+short` suppresses all metadata and returns only the answer value, useful for scripting. `+noall +answer` similarly trims output to just the answer section.

**Caution**

Excessive or automated DNS queries can be detected and rate-limited or blocked. The `ANY` query type is frequently ignored by modern resolvers per RFC 8482. Always operate within scope and obtain proper authorization before running automated DNS enumeration against a target.



# Subdomains 

**Why Subdomains Matter**

The main domain is rarely the only attack surface. Subdomains frequently host infrastructure that is less hardened, less monitored, or forgotten entirely. Development and staging environments are particularly valuable targets because they often run with weaker security controls and may expose features or data not yet hardened for production. Administrative login portals on subdomains may be intended as internal-only but are still publicly reachable. Legacy applications on abandoned subdomains may run outdated software with known CVEs. Configuration files or internal documents may also be inadvertently accessible.

**Active Subdomain Enumeration**

Active enumeration queries the target's DNS infrastructure directly. A DNS zone transfer (AXFR request) is the highest-yield technique when it succeeds, as it retrieves the entire zone file and exposes every DNS record in one response. However, properly configured servers reject unauthorized zone transfer requests, making this rarely viable. Brute-force enumeration is the fallback: tools like `dnsenum`, `ffuf`, and `gobuster` iterate through wordlists of common subdomain names and test each against the target domain. This is detectable because it generates high query volumes against the target's DNS servers.

**Passive Subdomain Enumeration**

Passive enumeration avoids direct contact with the target. Certificate Transparency logs are a particularly powerful source: every publicly trusted TLS certificate is logged, and the Subject Alternative Name (SAN) field often lists all subdomains the certificate covers. This can reveal subdomains without sending a single query to the target. Search engine operators like `site:` filter results to a specific domain and its subdomains, surfacing indexed pages that may not be linked from the main site. Online DNS aggregation services compile historical DNS data from multiple sources, enabling subdomain discovery without active interaction.

**Combined Approach**

Active enumeration is more comprehensive but noisier. Passive enumeration is stealthy but may miss subdomains that have never appeared in public sources. Using both in sequence - starting passive to avoid early detection, then active for completeness - produces the most thorough results.

# Subdomain Bruteforcing 

**How Brute-Force Enumeration Works**

The process has four stages. A wordlist is selected based on how much is known about the target. Each word in the list is appended to the target domain to form candidate subdomains. Each candidate is queried via DNS for an A or AAAA record. Those that resolve successfully are recorded as valid subdomains and may be validated further by checking whether they serve live web content.

**Wordlist Strategy**

General-purpose wordlists like SecLists' `subdomains-top1million-20000.txt` cover the most statistically common subdomain names and are the standard starting point. Targeted or custom wordlists are more efficient when the target's naming conventions are known from prior recon - for example, if internal documentation or job postings reveal environment names or product names used as subdomain prefixes.

**dnsenum Capabilities**

Beyond brute-forcing, `dnsenum` also attempts zone transfers from discovered name servers, performs reverse lookups, scrapes Google for additional subdomains, and performs WHOIS lookups. This makes it a multi-function tool rather than a pure brute-forcer. The `--enum` flag enables a set of default enumeration options as a shortcut. The `-r` flag enables recursive brute-forcing, meaning discovered subdomains are themselves used as new targets for further enumeration - useful for deeply nested subdomain structures.

**Tool Differentiation**

`fierce` is designed for ease of use and includes wildcard detection, which prevents false positives when a DNS server resolves every queried name regardless of whether the subdomain exists. `dnsrecon` combines multiple techniques in one run. `amass` is the most feature-rich option with integrations into external data sources. `puredns` is optimized for high-speed resolution and filtering at scale.

**Detection Risk**

Brute-force enumeration generates a high volume of DNS queries in a short period, which is detectable by DNS logging or rate-limiting on the target's name servers. It is an active technique and should only be used within authorized scope.

# DNS Zone Transfers 

**What a Zone Transfer Is**

A DNS zone transfer (AXFR) is a mechanism for replicating all DNS records in a zone from a primary name server to a secondary one. It is a legitimate administrative function used to keep DNS servers synchronized. The problem arises when access controls are misconfigured and any client - not just trusted secondary servers - is permitted to request the full zone file.

**The Transfer Process**

The secondary server sends an AXFR request to the primary. The primary responds first with the SOA record, which contains the zone's serial number used to determine whether the secondary's data is current. It then transmits all records in the zone sequentially (A, AAAA, MX, CNAME, NS, TXT, SRV, PTR, etc.). Once complete, the primary signals end-of-transfer and the secondary acknowledges.

**Why It Is a Vulnerability**

A successful unauthorized zone transfer hands the attacker a complete map of the target's DNS infrastructure in a single request. This includes every subdomain, every associated IP address, mail server configurations, name server details, and any TXT records containing verification tokens or other metadata. Subdomains that are never linked from the main website - staging environments, admin panels, internal tools - all appear in the zone file.

**Current State**

Most modern DNS servers are configured to restrict zone transfers to explicitly trusted secondary server IP addresses. However, misconfigurations still occur, particularly on older or poorly maintained infrastructure. Attempting an AXFR is a low-cost, high-reward check that should always be performed early in DNS recon. Even a rejected transfer reveals something about the server's configuration posture.

**The zonetransfer.me Example**

`zonetransfer.me` is a deliberately misconfigured demo domain maintained specifically to illustrate the risk. The `dig axfr` command against its name server returns all 50 records in the zone, demonstrating exactly what an attacker would receive from a vulnerable production server. The command structure - `dig axfr @<nameserver> <domain>` - is the standard pattern for all zone transfer attempts.

# Virtual Hosts 

**What Virtual Hosting Is**

Virtual hosting allows a single web server and IP address to serve multiple distinct websites or applications. The web server uses the `Host` header in every HTTP request to determine which virtual host configuration to apply, then serves content from the corresponding document root.

**VHosts vs Subdomains**

A subdomain is a DNS concept - it has its own DNS record pointing to an IP. A virtual host is a web server configuration concept - it tells the server what to do when a request arrives with a particular `Host` header value. The two often overlap, but a virtual host can exist without a public DNS record. This is the core reason VHost fuzzing is necessary: DNS enumeration will not reveal virtual hosts that have no DNS entries. They are only discoverable by directly testing `Host` header values against the server's IP.

**Types of Virtual Hosting**

Name-based virtual hosting is the most common and uses only the `Host` header to differentiate sites - one IP, many domains. IP-based hosting assigns a unique IP per site, avoiding `Host` header dependency but requiring more IP addresses. Port-based hosting differentiates sites by port number on the same IP, which is functional but uncommon in public-facing deployments.

**How gobuster VHost Fuzzing Works**

gobuster iterates through a wordlist, constructs a `Host` header value from each entry, sends an HTTP request to the target IP, and records which responses differ from the baseline (typically by status code or response size). The `--append-domain` flag is required in newer versions to correctly form full hostnames (e.g., `forum.inlanefreight.htb`) rather than sending bare wordlist words as the `Host` value. Without it, the constructed hostnames would be malformed and results would be unreliable.

**Detection Risk**

VHost fuzzing generates high request volume against a single IP and is detectable by IDS and WAF systems. It is an active technique and requires authorization. The `-t` flag increases thread count for speed but also increases the likelihood of triggering rate limiting or alerting.

# Certificate Transparency Logs 

**What CT Logs Are**

Certificate Transparency logs are public, append-only ledgers maintained by independent organizations. Every time a Certificate Authority issues an SSL/TLS certificate, it must submit that certificate to multiple CT logs. Because the logs are public and permanent, anyone can query them. This makes CT logs a passive, highly reliable source of subdomain intelligence.

**Why CT Logs Are Valuable for Recon**

Unlike brute-forcing, which is bounded by the quality of a wordlist, CT logs provide a factual historical record of every certificate ever issued for a domain. This means subdomains that were created and later decommissioned, subdomains with unusual or non-guessable names, and subdomains associated with expired certificates are all discoverable. Expired certificate subdomains are particularly interesting because they may still be live and running outdated, unpatched software.

**How the Merkle Tree Ensures Integrity**

CT logs use a Merkle tree structure to make tampering detectable. Each certificate is a leaf node; parent nodes are hashes of their children. The root hash represents the entire log state. Altering any certificate changes its hash, which cascades up and changes the root hash, immediately revealing tampering. This makes CT logs cryptographically trustworthy as a data source.

**Using the crt.sh API**

The `curl` command queries the crt.sh JSON API for all certificates matching a domain. The `jq` filter then selects only records where the `name_value` field (which holds the domain or subdomain covered by the certificate) contains a specific string - in the example, "dev". The `-r` flag outputs plain strings rather than JSON-quoted values. `sort -u` deduplicates and sorts the results. This pattern can be adapted to filter for any substring relevant to the target (e.g., "staging", "admin", "api", "vpn").

**crt.sh vs Censys**

crt.sh is free, requires no registration, and is sufficient for most subdomain discovery tasks. Censys offers deeper filtering, broader data including non-web assets, and API access, but requires account registration. For targeted certificate analysis or correlating certificates with IP addresses and hosting infrastructure, Censys is the stronger tool.



# Fingerprinting 

**What Fingerprinting Is**

Fingerprinting extracts technical details about the software stack powering a target - web server type and version, operating system, CMS, frameworks, and security controls. This information enables targeted attacks against known CVEs, reveals misconfigurations, and helps prioritize which targets to pursue.

**Banner Grabbing with curl**

The `-I` flag sends a HEAD request and returns only HTTP headers without downloading the page body. Following the redirect chain manually reveals more: the initial HTTP response shows an Apache server redirecting to HTTPS; the HTTPS response reveals `X-Redirect-By: WordPress`, exposing the CMS; the final destination returns `wp-json` path references in `Link` headers, further confirming WordPress. Each redirect is a separate fingerprinting opportunity.

**WAF Detection with wafw00f**

Before further probing, detecting a WAF is critical. A WAF can block, alter, or log fingerprinting attempts and may cause false negatives or trigger alerts. `wafw00f` sends crafted requests and analyzes responses to identify the WAF vendor and product. In the example, Wordfence (a WordPress-specific WAF by Defiant) is identified. Knowing this informs which evasion techniques may be needed in subsequent testing.

**Nikto with -Tuning b**

The `-Tuning b` flag restricts Nikto to Software Identification modules only, avoiding noisy vulnerability probes while still extracting technology information. The scan reveals the Apache version (2.4.41, which is outdated), confirms WordPress, finds the login page at `/wp-login.php`, identifies a `license.txt` file that may disclose software versions, and flags missing security headers (`Strict-Transport-Security`, `X-Content-Type-Options`). Each finding is a potential attack vector or entry point.

**Chaining the Findings**

The section demonstrates how fingerprinting results compound: curl reveals Apache and WordPress; wafw00f reveals Wordfence protecting the WordPress install; Nikto confirms both, adds the WordPress login URL, flags the outdated Apache version, and surfaces missing headers. This layered approach builds a complete technology profile before any exploitation is attempted.


# Crawling 

**What Crawling Is**

Web crawling (spidering) is the automated process of following links from page to page to systematically map and collect content from a website. A crawler starts at a seed URL, fetches the page, extracts all links, adds them to a queue, and repeats. This is distinct from fuzzing, which guesses paths that may not be linked anywhere.

**Breadth-First vs Depth-First**

Breadth-first crawling explores all links at the current depth before going deeper, producing a broad overview of site structure quickly. Depth-first follows a single link chain as deep as possible before backtracking, which is better suited for reaching deeply nested content. The choice depends on the goal: broad mapping favors breadth-first; targeted deep discovery favors depth-first.

**What Crawlers Extract**

Internal and external links map the site structure and reveal relationships to third-party infrastructure. Comments in page source or interactive sections can inadvertently expose software versions, internal process details, or developer notes. Metadata (titles, descriptions, author names, dates) provides contextual intelligence. Sensitive files are the highest-value finds: backup files (`.bak`, `.old`), configuration files (`web.config`, `settings.php`), and log files (`error_log`, `access_log`) may contain credentials, API keys, database connection strings, or source code.

**The Importance of Context**

Individual findings gain significance through correlation. A single comment mentioning a "file server" is low value in isolation. Combined with a crawled link pointing to `/files/` and the discovery that directory browsing is enabled on that path, the same comment becomes a strong signal of exposed sensitive data. Effective recon requires analyzing findings relationally, not individually. The section emphasizes this as the core analytical skill that separates surface-level data collection from actionable intelligence.


# robots.txt 

**What robots.txt Is**

A plain text file placed in the root directory of a website that instructs web crawlers which paths they may and may not access. It follows the Robots Exclusion Standard. Directives are organized into records separated by blank lines, each targeting a specific user-agent or all user-agents via the wildcard `*`.

**How It Works**

Each record specifies a user-agent and one or more directives. `Disallow` blocks access to a path or pattern. `Allow` explicitly permits access even under a broader `Disallow` rule. `Crawl-delay` throttles request frequency to protect server load. `Sitemap` points crawlers to an XML sitemap for efficient indexing.

**Why It Matters for Recon**

`robots.txt` is a passive, publicly accessible intelligence source that website owners inadvertently use to document their own sensitive paths. `Disallow` entries frequently reveal admin panels, private directories, backup locations, and internal tools that are intentionally kept out of search engine indexes but are still live and potentially reachable. Mapping all disallowed and allowed paths provides a structural outline of the site's hidden areas without sending a single request to those paths. Some sites also plant honeypot directories in `robots.txt` to detect and log bots that ignore the directives, so awareness of this tactic is important. The file is not enforceable - a crawler can freely ignore it - but reading it first is a zero-noise, high-value recon step.

# Well-Known URIs 

**What .well-known Is**

Defined in RFC 8615, `.well-known` is a standardized directory at the root of a web server used to centralize metadata, configuration files, and service information. IANA maintains a registry of all recognized URI suffixes under this path. Clients and tools can programmatically construct URLs to retrieve specific configuration data without needing to discover paths manually.

**Recon Value of Each URI**

`security.txt` exposes contact information for vulnerability disclosure, which also reveals the security team's structure and reporting process. `change-password` reveals the location of the password change page, useful for understanding authentication flows. `openid-configuration` is the highest-value endpoint for recon: it exposes the full OpenID Connect provider configuration, including every OAuth2 endpoint, supported scopes and response types, and the JWKS URI containing the server's public cryptographic keys. `assetlinks.json` reveals associated digital assets such as Android apps linked to the domain. `mta-sts.txt` exposes email transport security policy details.

**openid-configuration in Depth**

Accessing this endpoint maps the entire authentication infrastructure in a single request. The authorization endpoint is where OAuth2 flows begin. The token endpoint is where tokens are exchanged. The userinfo endpoint retrieves authenticated user data. The `jwks_uri` exposes the server's public keys, which can be used to understand or test JWT signature verification. Supported scopes reveal what user data the application can request. Supported algorithms reveal what signing mechanisms are in use, which is relevant for JWT attacks.

**Methodology Note**

These endpoints are passive - querying them sends requests to the target but only to well-known public paths. They are low-noise, high-yield recon steps that should be checked early. The IANA registry lists all registered suffixes and should be consulted to identify any additional endpoints relevant to the target's technology stack.

# Creepy Crawlies 

**Tool Overview**

Burp Suite Spider is integrated into a full web application testing platform and is suited for interactive, targeted crawling during an assessment. OWASP ZAP is a free alternative with both automated and manual modes. Scrapy is a Python framework for building custom crawlers, offering flexibility for tailored recon tasks. Apache Nutch is designed for large-scale crawls and requires more setup but handles massive scope.

**ReconSpider**

ReconSpider is a custom Scrapy-based spider provided by HTB. It automates the collection of multiple data categories from a target domain in a single run and outputs everything to `results.json` for offline analysis.

**What results.json Contains**

Each key maps to a distinct data category. `emails` directly feeds social engineering and phishing target lists. `links` maps the site structure and may reveal unlinked internal paths. `external_files` surfaces PDFs and documents that may contain metadata, internal information, or credentials. `js_files` are high-value targets: JavaScript files often contain API endpoints, hardcoded tokens, internal logic, and third-party service integrations. `form_fields` reveal input surfaces for injection testing. `images` can contain EXIF metadata. `comments` in HTML source code frequently expose developer notes, disabled code, internal path references, version strings, and other unintentional disclosures.

**Analytical Approach**

No single key in `results.json` is conclusive on its own. The value comes from cross-referencing: a comment referencing an internal path combined with a JS file revealing an API endpoint combined with an external PDF leaking employee names creates a composite picture. The JSON format makes this data easy to parse, grep, or feed into downstream tools.

# Search Engine Discovery

**What Search Engine Discovery Is**

Search engine discovery (Google Dorking / Google Hacking) uses specialized search operators to extract targeted information from what search engines have already indexed. It is entirely passive - no requests go to the target. All queries go to the search engine, which returns pages it has already crawled and cached.

**Operator Categories and Their Recon Use**

`site:` scopes all results to a specific domain, making it the foundation of most dorks. `inurl:` finds pages where a specific string appears in the URL path, useful for locating login portals, admin panels, and specific endpoints. `filetype:` / `ext:` surfaces documents of a specific type - PDFs, spreadsheets, SQL dumps, and config files are all high-value targets. `intitle:` searches page titles, which often reflect the page's true purpose more accurately than body content. `intext:` / `inbody:` searches body content for specific strings like "password reset" or "internal use only." `cache:` retrieves the search engine's stored copy of a page, which may reflect older content no longer live on the server.

**Combining Operators**

The real power comes from chaining operators. `AND`, `OR`, `NOT`, `-`, and `" "` allow precise filtering. Parentheses group logical conditions. The wildcard `*` substitutes unknown words. The `..` operator searches numerical ranges, useful for price-based or date-based filtering on target sites.

**Google Hacking Database**

The GHDB at exploit-db.com is a curated library of proven dorks organized by category (sensitive files, login portals, error messages, etc.). It should be consulted during any engagement to quickly apply community-validated queries to the target domain.

**Key Limitation**

Search engines do not index everything. Content behind authentication, content blocked by `robots.txt`, recently published content, and content on servers that have never been crawled will not appear. Search engine discovery complements but does not replace active enumeration.




# Web Archives 

**What the Wayback Machine Is**

The Wayback Machine is a public digital archive operated by the Internet Archive (non-profit) that has been capturing website snapshots since 1996. Each snapshot is a full copy of the page including HTML, CSS, JavaScript, images, and other resources, timestamped to the exact date and time of capture.

**How It Works**

Automated crawlers browse the web, follow links, and download copies of pages at regular intervals - daily, weekly, or monthly depending on the site's popularity and change frequency. Snapshots are stored with timestamps, allowing any historical version to be retrieved by URL and date. Not every page on the internet is archived; the Wayback Machine prioritizes culturally, historically, or research-relevant content. Site owners can request exclusion, though it is not guaranteed.

**Why It Matters for Recon**

The Wayback Machine surfaces information that no longer exists on the live site. Old pages, directories, subdomains, and files that have been removed or restructured may still be accessible in archived snapshots. Comparing snapshots across time reveals structural changes, technology migrations, and content that was briefly exposed then removed. Archived versions of `robots.txt`, configuration files, or admin paths from earlier periods of the site's life can reveal paths and endpoints that are still live but no longer linked. Employee names, email formats, internal tool references, and past technologies may appear in older content and remain relevant to the current engagement.

**Detection Profile**

Accessing the Wayback Machine is entirely passive - all requests go to archive.org, not to the target. It is one of the stealthiest recon techniques available and should be used early and throughout an engagement whenever the current state of the site leaves gaps in the intelligence picture.

# Automating Recon 

**Why Automate**

Manual recon is slow, inconsistent, and does not scale. Automation handles repetitive DNS queries, subdomain brute-forcing, header analysis, and crawling simultaneously and consistently across many targets. The analyst's time is then spent on interpreting results rather than executing individual commands.

**Framework Overview**

FinalRecon is a modular all-in-one tool covering headers, WHOIS, SSL, DNS, subdomains, directories, crawling, port scanning, and Wayback Machine queries in a single Python script. Recon-ng is a more powerful framework modeled after Metasploit's console, with individual modules for each technique and workspace management for organizing findings. theHarvester is specialized for OSINT aggregation from public sources (search engines, Shodan, PGP servers), primarily surfacing emails, subdomains, and employee names. SpiderFoot automates a wide range of OSINT collection across IP addresses, domains, emails, and social media. OSINT Framework is not a tool but a curated directory of resources organized by data type.

**FinalRecon in Practice**

Flags are combined freely to run only the modules needed. The `--full` flag runs everything. API keys can be added with `-k` to unlock data from Shodan, VirusTotal, Facebook, and BeVigil for subdomain enumeration. Output is exported automatically to the default directory or a custom path specified with `-cd`. The default wordlist for directory enumeration can be swapped with `-w` for a larger or more targeted list. Thread counts for directory enum (`-dt`) and port scan (`-pt`) can be tuned for speed vs. stealth tradeoffs.

**Chaining with Manual Techniques**

Automation frameworks like FinalRecon are best used as a first pass to surface the broad landscape. Findings are then triaged manually - interesting subdomains get further DNS analysis, interesting headers get fingerprinting, interesting directories get crawled or fuzzed. Automation accelerates discovery; manual analysis determines what matters.

