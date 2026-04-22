# Login Brute Forcing 

**Introduction**  
This section frames brute forcing as the systematic guessing of secrets that protect systems, comparing digital credentials to physical locks. The goal is to set context for why authentication mechanisms are high-value targets and why repeated guessing is still relevant despite modern defenses.

**What is Brute Forcing**  
Brute forcing is defined as a trial-and-error technique where an attacker repeatedly attempts credentials or keys until a valid one is found. Its feasibility depends on password entropy, available computational power, and defensive controls such as lockouts or CAPTCHAs. The concept emphasizes exponential growth in complexity as password length and character diversity increase.

**How Brute Forcing Works**  
The attack follows a looped process: initiate attempts, generate candidate values, submit them to the target, and evaluate success or failure. This loop continues until access is achieved or attempts are stopped. Understanding this flow is important for recognizing where defenses can interrupt or slow the process.

**Types of Brute Forcing**  
Different techniques optimize the guessing process based on available information and constraints. Simple brute force exhausts the full keyspace, while dictionary and hybrid attacks reduce effort by leveraging human password habits. Credential stuffing, password spraying, and reverse brute force exploit password reuse patterns. Rainbow tables target hashed credentials, and distributed attacks scale attempts across multiple systems to overcome complexity.

**The Role of Brute Forcing in Penetration Testing**  
Within penetration testing, brute forcing is used selectively rather than blindly. Testers rely on it when other attack paths fail, when password policies appear weak, or when specific high-value accounts are in scope. The purpose is not just access, but to demonstrate risk and validate the effectiveness of authentication controls and defensive mechanisms.
# Password Security Fundamentals 

This section is purely conceptual and establishes why brute-force attacks succeed or fail based on password quality rather than tooling alone.

**Core idea**  
Brute forcing is a math problem. Password strength directly determines the size of the search space an attacker must exhaust. Every additional character and character set multiplies the effort required.

**Length over everything**  
NIST guidance prioritizes length because it increases entropy exponentially. Even with a limited character set, longer passwords rapidly become infeasible to brute force within realistic time and resource constraints.

**Role of complexity**  
While modern guidance favors passphrases, complexity still matters in brute-force scenarios. Expanding the character set increases per-character possibilities, slowing exhaustive attacks and hybrid wordlist mutations.

**Uniqueness as damage control**  
Password reuse does not make brute forcing easier, but it massively increases impact. One cracked password can unlock multiple services, turning a single success into systemic compromise.

**Randomness vs. wordlists**  
Attackers rarely start with pure brute force. They optimize using dictionary, hybrid, and rule-based attacks. Non-random passwords collapse the effective search space and negate theoretical strength.

**Common weaknesses attackers rely on**  
Short length, dictionary words, personal data, predictable patterns, and simple substitutions are all aggressively targeted because they dramatically outperform raw brute force in real environments.

**Password policies trade-offs**  
Minimum length and complexity improve security, but forced rotation and over-strict rules often backfire by encouraging predictable variations. Pentesters look for policy friction that leads to weak user behavior.

**Default credentials as the fastest win**  
Default usernames and passwords eliminate the need for brute forcing entirely. Known credential pairs reduce the attack to a handful of attempts and are commonly the first check in any engagement.

**Default usernames as an overlooked risk**  
Even when passwords are changed, predictable usernames narrow the attack surface. Knowing the username turns credential attacks into a single-variable problem.

**Pentester mindset takeaway**  
Password security analysis informs:

- Whether brute force is viable at all
    
- Which attack type is most efficient
    
- How much time and compute to allocate
    
- Where “low-hanging fruit” like defaults may exist
    

This section sets the foundation for later tool-based attacks by teaching when brute forcing is worth attempting and when it is mathematically or operationally impractical.
# Brute Force Attacks
This section grounds brute forcing in mathematics by showing how the total search space grows exponentially as password length or character variety increases. The formula illustrates why defenders prioritize longer passwords and broader character sets: each incremental change massively increases attacker workload.

The comparison table demonstrates how small changes, such as adding two characters or including uppercase letters, cause the number of combinations to explode. This reinforces the concept that entropy matters more than superficial complexity.

The discussion on computational power highlights that cracking time is a function of both search space and guess rate. Faster hardware shortens attack time, but exponential growth in combinations eventually outpaces even extreme computing resources, making certain passwords infeasible to crack in practice.

The PIN-cracking example shifts from theory to practice. A fixed-length numeric PIN has a very small and predictable keyspace, making brute force trivial when no protections are in place. The script demonstrates a sequential online brute-force attack by iterating through every possible value and testing it against an exposed endpoint.

From an attacker’s mindset, this showcases why rate limiting, lockouts, and monitoring are critical. From a defender’s perspective, it illustrates how even simple authentication mechanisms become vulnerable when exposed without controls. The example fits into the exploitation methodology as a proof-of-concept attack used to validate weak authentication rather than a scalable real-world strategy.
# Dictionary Attacks 
This section introduces dictionary attacks as an optimization over pure brute force by reducing the search space to likely human-chosen passwords. Instead of testing every possible combination, the attacker leverages predictable password behavior, dramatically increasing efficiency.

The Python script demonstrates an online dictionary attack against a POST-based authentication endpoint. Rather than generating guesses algorithmically, it consumes a curated wordlist of known weak passwords sourced from real-world credential leaks. This reflects an attacker mindset focused on probability and speed rather than completeness.

The attack flow is simple but effective: fetch a wordlist, iterate through each candidate, submit it to the target endpoint, and inspect responses for indicators of success. The presence of a valid HTTP response and a specific JSON key acts as the success condition, allowing the attack to terminate early once the correct password is identified.

From a methodology perspective, this mirrors real penetration testing practice. Dictionary attacks are commonly attempted before brute force because they are faster, quieter, and more likely to succeed against systems with poor password hygiene. Defensively, the example reinforces why rate limiting, monitoring, and strong password policies are critical to preventing low-effort account compromise.
# Hybrid Attacks
Hybrid attacks exploit predictable human behavior, particularly around password reuse and mandatory password rotation policies. Instead of choosing entirely new passwords, users often make minimal changes—such as appending symbols or incrementing years—which creates highly guessable patterns.

The attack strategy begins with a dictionary attack using common or context-aware passwords. If this fails, it evolves into a controlled brute-force phase that mutates those same dictionary entries. By appending numbers, symbols, or date-based variations, attackers achieve wide coverage without resorting to an impractical full keyspace brute force.

The filtering example demonstrates how attackers refine large leaked-password datasets to match known password policies. By chaining regular-expression filters, the attacker eliminates non-compliant entries step by step, drastically shrinking the candidate set. This turns a generic wordlist into a policy-compliant, high-probability attack list.

From an attacker’s perspective, this maximizes efficiency and success rates while minimizing noise and time. From a defensive standpoint, it highlights why password complexity rules alone are insufficient. Without user education, rate limiting, and multi-factor authentication, predictable compliance behavior becomes an exploitable weakness.

The credential stuffing section extends the hybrid mindset further, showing how leaked credentials from one breach can be reused at scale against other services. The root cause remains password reuse, reinforcing the importance of unique passwords and layered authentication defenses across all systems.
# Hydra

This section introduces Hydra as a high-performance online authentication attack tool designed to brute-force credentials across many network services. Its core strength lies in parallelization, allowing multiple authentication attempts to run simultaneously, drastically reducing attack time compared to sequential approaches.

The installation and help commands establish tool availability and familiarize the attacker with Hydra’s modular design. Hydra’s syntax reflects its flexibility: usernames, passwords, targets, services, and protocol-specific options are all independently configurable, enabling precise control over attack behavior.

The service examples demonstrate how Hydra adapts to different authentication mechanisms. For HTTP basic authentication, it cycles through username-password combinations against protected endpoints. For SSH and FTP, it targets remote login services directly, scaling efficiently across multiple hosts or non-standard ports.

The web form example highlights Hydra’s ability to brute-force custom login logic by matching request parameters and defining explicit success conditions. This is critical in real-world applications where success is not always indicated by a simple status code.

The RDP example illustrates Hydra’s hybrid capability, blending brute-force generation with protocol awareness. Instead of relying on wordlists, it dynamically generates passwords within a defined length and character set, aligning with suspected password policies.

From an attacker’s mindset, Hydra is a force multiplier: it automates credential attacks across protocols while remaining adaptable to context and constraints. From a defensive perspective, this section underscores why exposed authentication services must be protected with strong passwords, rate limiting, monitoring, and multi-factor authentication.
# Login Brute Forcing 

**Basic HTTP Authentication**  
This section explains Basic HTTP Authentication as a simple challenge-response mechanism where credentials are transmitted in every request using Base64 encoding. While Base64 is often misunderstood as encryption, it is merely an encoding scheme, meaning credentials can be trivially decoded if intercepted.

The authentication flow begins with a 401 Unauthorized response and a `WWW-Authenticate` header. Browsers respond by prompting the user for credentials, concatenating them in the `username:password` format, encoding the result, and attaching it to the `Authorization` header in subsequent requests.

Because Basic Auth does not implement account lockouts, CAPTCHA challenges, or request throttling by default, it is particularly susceptible to brute-force attacks. Every request is stateless and independent, making automated guessing highly effective.

The Hydra example demonstrates a focused brute-force attack where the username is already known. By eliminating the username variable, the attack reduces complexity and accelerates success. Hydra’s `http-get` module handles the authentication exchange automatically, submitting credentials via the Authorization header and interpreting server responses.

From an attacker’s perspective, Basic Auth is low-hanging fruit when exposed without additional controls. From a defensive standpoint, this section reinforces why Basic Auth should only be used in conjunction with HTTPS, strong credentials, rate limiting, and preferably replaced with more robust authentication mechanisms whenever possible.
# Login Forms 

This section demonstrates how custom HTML login forms operate and how they can be brute-forced using Hydra’s `http-post-form` module.

The HTML form examples show how credentials are collected via named input fields and submitted to a server endpoint using the POST method. These field names directly dictate the parameter names required for automation.

The HTTP POST request illustrates how browsers transmit credentials as URL-encoded key-value pairs, which Hydra later replicates programmatically.

The `http-post-form` syntax defines three critical components: the request path, the POST parameters, and the condition string. Hydra substitutes the placeholders in the parameters with wordlist values and evaluates responses using the condition string.

Failure conditions (`F=`) are the most reliable method, as many applications explicitly return an error message on invalid login attempts. Success conditions (`S=`) are used when distinctive indicators like redirects or post-login keywords exist.

Manual HTML inspection confirms the parameter names (`username`, `password`) and request method, ensuring the payload matches the application’s expectations. Network inspection and proxy interception validate the exact request structure and endpoint.

The constructed params string mirrors a legitimate login attempt while embedding Hydra placeholders, allowing automated credential cycling. Wordlists are supplied externally and paired systematically.

The final Hydra command chains all identified elements together: username list, password list, target service, port, request structure, and failure condition. When the failure condition is absent in a response, Hydra identifies a valid credential pair, enabling authenticated access to retrieve the flag.

This workflow reflects standard web brute-force methodology: reconnaissance, parameter identification, condition tuning, automation, and post-authentication verification.

# Medusa 

**Installation**

This section verifies whether Medusa is available on the system and shows the standard package manager method to install it on Linux-based penetration testing environments.

**Command Syntax**

The generic syntax defines how targets, credentials, modules, and module-specific options are combined. Medusa separates target selection, credential input, and service logic, making it modular and scalable.

**Parameter Usage Examples**

Target parameters control whether Medusa attacks a single host or multiple hosts from a file. Credential parameters determine whether usernames and passwords are supplied individually or via lists. Module parameters define which authentication protocol is attacked, while auxiliary options control speed, verbosity, ports, and stopping behavior.

**Module Usage Examples**

Each module is purpose-built to communicate with a specific authentication mechanism. The examples demonstrate how Medusa adapts to protocols such as FTP, SSH, databases, remote desktop services, and web applications. Web-related modules rely on correctly defining request structure and form parameters to emulate real authentication attempts.

**Attack Examples**

The SSH example represents a classic service brute-force scenario using username and password lists. The multi-host HTTP example shows Medusa’s parallelism when attacking several targets at once. The empty and default password example highlights attacker techniques for quickly identifying weak configurations without full brute-force cycles.

Overall, Medusa fits into brute-force methodology as a high-performance, protocol-aware tool used after credential patterns, services, and authentication mechanisms are identified.
# Web Services 

This section demonstrates chaining brute-force attacks across multiple services to expand access on a target system.

The initial Medusa command targets an SSH service using a known username and a high-probability password wordlist. Limiting parallel tasks balances speed with stealth while systematically testing credentials until authentication succeeds.

Once valid SSH credentials are obtained, direct login enables internal reconnaissance that would otherwise be inaccessible externally. Enumerating listening services reveals additional exposed attack surfaces, in this case an FTP service bound to the local interface.

Local enumeration confirms the presence of FTP, allowing a pivot from SSH access to credential attacks against another service. The discovery of a home directory matching the FTP username informs the attacker’s choice of target account, reducing guesswork.

The FTP brute-force attack uses Medusa’s FTP module with increased parallelism, reflecting lower protocol resistance compared to SSH. Successful authentication enables direct file system access through the FTP service.

Retrieving the flag file demonstrates post-exploitation objectives: leveraging cracked credentials to access sensitive data. This workflow highlights how weak passwords across multiple services can be chained to fully compromise a system, even when individual services appear correctly configured.

The attack path follows standard methodology: external brute-force, authenticated access, internal enumeration, lateral service targeting, and data extraction.

# Custom Wordlists 

**Username Anarchy**

This section focuses on generating realistic username permutations based on a person’s name. Username Anarchy automates the creation of common corporate and personal username formats by combining first names, last names, initials, casing variations, truncations, and ordering patterns. This significantly increases the likelihood of matching real-world username conventions compared to generic username lists.

**CUPP**

CUPP is used to generate a highly targeted password list by profiling the victim. It leverages personal data such as names, nicknames, dates, relationships, pets, interests, and organizations to produce thousands of password mutations. The strength of CUPP lies in transforming OSINT into password candidates that reflect human password creation habits.

**Password Policy Filtering**

After generation, the password list is refined to comply with a known organizational password policy. The filtering chain enforces minimum length, uppercase letters, lowercase letters, numbers, and multiple special characters. This step eliminates invalid candidates and focuses the attack on passwords that the authentication system will actually accept, improving efficiency and reducing noise.

**Hydra Web Login Brute Force**

The final step chains the custom username list with the filtered password list in a Hydra web form attack. The placeholders dynamically substitute usernames and passwords while Hydra evaluates server responses against the failure condition. This completes the attack flow by combining tailored enumeration with automated credential testing against the login form.
# 
#
#
#
#