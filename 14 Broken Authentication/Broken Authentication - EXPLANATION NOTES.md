
# What is Authentication 

## What Authentication Is

Authentication is the process of confirming an entity's identity — verifying that someone is who they claim to be. It is distinct from authorization, which determines what an authenticated entity is permitted to access. The distinction matters in pentesting: bypassing authentication means getting in as someone else; bypassing authorization means accessing resources you are not permitted to reach once inside.

## Authentication Categories

There are three factor categories: knowledge (something you know, like a password or PIN), ownership (something you have, like a hardware token or authenticator app), and inherence (something you are, like a fingerprint or face). These map directly to what an attacker must steal, clone, or spoof in order to bypass each factor type.

## Single-Factor vs Multi-Factor

Single-factor authentication relies on one category alone. Password-only login is the most common example and also the most commonly exploited. Multi-factor authentication (MFA) chains two or more categories together — for example, a password plus a TOTP code — requiring an attacker to compromise multiple factors simultaneously, which significantly raises the difficulty of a successful attack.

## Relevance to This Module

The module focuses on login form exploitation. The login endpoint noted in the Paths section represents the type of target under analysis throughout the course. The attacker mindset here is: identify the authentication method in use, determine how many factors are required, and then attack the weakest link — most often the knowledge factor, since credentials can be guessed, brute-forced, or leaked.


# Attacks on Authentication 

## Why This Section Matters

This section establishes the threat model for each authentication factor type before the module dives into hands-on exploitation. Understanding which factor is being attacked informs the technique selection throughout all later sections.

## Attacking Knowledge-based Authentication

This is the primary focus of the module because it is the most exploitable category. Static credentials — passwords, PINs, security question answers — can be obtained through phishing, leaked in data breaches, guessed from weak choices, or brute-forced systematically. The attacker does not need physical access or biometric spoofing capabilities; network access and a wordlist may be sufficient. This low barrier to entry makes knowledge-based attacks the dominant category in real-world web application testing.

## Attacking Ownership-based Authentication

Physical possession factors resist phishing and credential stuffing, but they introduce a different attack surface: physical proximity and cryptographic weaknesses. NFC badge cloning in public spaces is highlighted as a realistic vector — an attacker in a café or on public transit can silently clone an access card. Hardware tokens can also be targeted through weaknesses in the underlying algorithm. The logistical overhead of managing physical tokens often limits their deployment, which means knowledge-based systems remain the more common target in web app engagements.

## Attacking Inherence-based Authentication

Biometric systems are notable because a successful breach is permanent. Unlike a compromised password, a leaked fingerprint or facial template cannot be revoked and reissued — the user has no replacement biometric. The 2019 breach cited in the source demonstrates this: all biometric data was exposed with no recovery path for affected users. From an attacker perspective, the value of obtaining biometric data is therefore disproportionately high. Privacy concerns and algorithmic bias in recognition systems are additional structural weaknesses that affect trust and adoption.

## Chain of Reasoning Into the Rest of the Module

The module proceeds to focus almost entirely on knowledge-based authentication because it is the most prevalent in web applications and the most tractable to attack remotely. The subsequent sections on brute-force, password attacks, authentication bypasses, and session attacks all build on the weaknesses of knowledge-based systems introduced here.

# Enumerating Users 

## What User Enumeration Is

User enumeration exploits the fact that a web application responds differently depending on whether a submitted username exists in its database. This difference can be in the error message text, HTTP response size, status code, or timing. Even a subtle distinction is sufficient to build a valid user list.

## Why It Matters

Valid usernames reduce the search space for brute-force attacks from a two-dimensional problem (username + password) to a one-dimensional one (password only). They also enable targeted password spraying — testing one common password across all confirmed accounts — and facilitate OSINT correlation, since users reuse usernames across services such as SSH, RDP, and FTP.

## The Error Message Difference (This Lab)

The lab presents two distinct error messages. An unrecognized username returns "Unknown user", while a valid username with a wrong password returns "Invalid credentials". This asymmetry is all that is needed. The ffuf command fuzzes the username field while holding the password constant at an invalid value, then filters out all responses containing "Unknown user" — leaving only responses that indicate the username was recognized, which correspond to valid accounts.

## How the ffuf Command Works

The `-w` flag supplies the username wordlist. The `-X POST` and `-H` flags set the method and content type to match a standard form submission. The `-d` flag carries the POST body with `FUZZ` as the placeholder for each wordlist entry. The `-fr` flag filters responses by string — any response body matching "Unknown user" is discarded, and only hits that pass the filter are reported. The identified username `consuelo` is then carried forward into the password brute-force phase.

## Side-Channel Enumeration

Even when error messages are identical, timing differences can leak username validity if the application only performs a database lookup for known users. A valid username causes a database hit and potentially a hash comparison, which takes longer than an immediate rejection of an unknown username. This method requires more careful measurement but works against applications that appear to return uniform responses.

## Common Pitfalls

Filtering on the wrong string will either produce no results or flood output with false positives. Before running ffuf at scale, manually test one known-invalid and one known-valid username to confirm which string uniquely identifies the invalid case, then use that as the `-fr` value.




# Brute-Forcing Passwords 

## Attack Flow Overview

This section continues directly from the user enumeration phase. Once a valid username (`admin`) is confirmed, the attack pivots to brute-forcing the password field while keeping the username fixed. The entire strategy depends on having a wordlist that matches the target's password policy — otherwise the majority of attempts are wasted on passwords the application would never accept in the first place.

## Why Policy-Aware Wordlist Filtering Matters

The lab application displays its password policy on the login page: at least one uppercase letter, one lowercase letter, one digit, and a minimum of ten characters. Rockyou.txt contains over 14 million entries, but only around 150,000 of them satisfy all four constraints simultaneously. Filtering first reduces the attack space by roughly 99%, which directly translates to a faster attack and fewer requests sent to the target.

## The grep Chain vs awk

Both approaches in the Commands section produce the same `custom_wordlist.txt`. The chained `grep` approach is readable and easy to adjust per constraint. The `awk` one-liner is more efficient as a single pass and is easier to extend with additional conditions. Either is valid; the choice depends on preference and environment.

## How the ffuf Command Works Here

The pattern is identical to the user enumeration phase but inverted: the username is now fixed as `admin` and `FUZZ` moves to the password field. The `-fr` flag filters out any response containing "Invalid username", meaning only responses that do not match that string are reported — which in practice means only the successful login response survives the filter. A successful login returns an HTTP 302 redirect rather than a 200 with an error message, which is the distinguishing signal.

## Credential and Endpoint Reuse

The discovered credential (`admin` / `Buttercup1`) grants access to `/admin.php`, a privileged endpoint that would not be reachable without authentication. This demonstrates the full chain: enumerate username → filter wordlist to policy → brute-force password → authenticate to admin panel.

## Common Pitfalls

Skipping the wordlist filtering step is the most common mistake. Using the full rockyou.txt against a policy-enforced application wastes time and increases detection risk without improving success probability. Always read the password policy — if it is displayed on the login page, it is telling you exactly how to prune your wordlist before starting.

# Brute-Forcing Password Reset Tokens 

## What Makes a Reset Token Weak

A password reset token is a temporary secret that substitutes for the user's password. If an attacker can obtain or guess a valid token, they can reset the account's password and take it over entirely — without ever knowing the original password. The lab token (`7351` in the example email) is a 4-digit numeric value, which means there are only 10,000 possible values (0000–9999). This is a trivially small search space for a brute-force attack.

## Token Generation and Wordlist Preparation

The `seq -w` command generates every integer from 0 to 9999 with zero-padding so that all values are four characters wide (e.g., `0001`, `0042`, `0999`). Zero-padding is critical: if the application stores and compares tokens as zero-padded strings, submitting `182` instead of `0182` would fail to match. Always match the observed token format exactly.

## How the ffuf Attack Works

Unlike the login brute-force examples in earlier sections, this attack targets a GET parameter in the URL rather than a POST body. The `FUZZ` keyword is placed directly in the query string. The `-fr` flag filters out all responses containing the invalid-token error message, leaving only the response that corresponds to a valid, active token. A match at token `6182` reveals that this token belongs to an active reset session — in the lab, it resolves to the admin account.

## Why Active Tokens Must Exist

The attack only works if there is a currently active reset token in the system for the targeted account. If targeting a specific user rather than waiting for opportunistic hits, the attacker should trigger a password reset request for that user first, which creates a fresh token, and then immediately brute-force before it expires. The lab states tokens expire in 24 hours, but real-world tokens often expire much sooner.

## Attack Chain

The full chain is: request a password reset for the target account → generate a token wordlist matching the observed format and length → brute-force the token endpoint filtering on the error response → land on the reset page authenticated as the target user → set a new password and take over the account.

## Common Pitfalls

Failing to match the token format (padding, length, character set) is the most common error. If the observed token is `7351` and the application expects exactly four digits with leading zeros, a wordlist generated without `-w` would produce `0` through `9999` without padding and miss matches for values like `0001`. Always inspect a sample token first and replicate its exact format in the wordlist.


# Brute-Forcing 2FA Codes 

## Precondition: Valid Session Required

Unlike the reset token attack, this attack requires the attacker to already hold valid credentials. The lab assumes `admin:admin` was obtained via phishing. The attacker logs in with those credentials, which creates an authenticated session that is stopped at the 2FA checkpoint. The session cookie (`PHPSESSID`) represents a partially authenticated state — the password check has passed but the OTP check has not.

## Why the Session Cookie is Critical

The `-b` flag in the ffuf command passes the `PHPSESSID` cookie with every request. Without it, each OTP submission would not be associated with the admin's partially authenticated session, and the server would have no context to validate the TOTP against. The session cookie is the binding element that ties the brute-force attempts to the correct account and authentication state.

## Token Space and Wordlist

A 4-digit numeric TOTP has exactly 10,000 possible values (0000–9999). The `seq -w` command generates all of them with consistent zero-padding, matching the format the application expects. This is identical in structure to the reset token wordlist from the previous section.

## Interpreting Multiple Hits

The ffuf output returns many hits after the correct code is found. This happens because once the correct TOTP (`6513`) is submitted and accepted, the session transitions to fully authenticated. Every subsequent request using that same session cookie is then redirected (HTTP 302) to `/admin.php` rather than returning the "Invalid 2FA Code" error — so all remaining wordlist entries also produce 302 responses. The first hit in the output is the actual valid TOTP; all subsequent hits are artifacts of the now-authenticated session.

## Accessing the Protected Endpoint

After the brute-force completes, the attacker does not need to replay the winning OTP. The session is already marked as fully authenticated server-side. Navigating directly to `/admin.php` in a browser with the same session cookie is sufficient to confirm access.

## Attack Chain

The full chain is: obtain credentials (phishing or other means) → log in to advance past the password check and capture the `PHPSESSID` → generate a 4-digit token wordlist → brute-force the OTP endpoint while passing the session cookie → session becomes fully authenticated on the first valid hit → access the admin panel directly.

## Common Pitfalls

Omitting the `-b` cookie flag is the critical failure mode here. Without the session cookie the requests are unauthenticated and the server will never process them as OTP submissions for the target account. Always intercept the 2FA POST request first to identify both the parameter name (`otp` in this lab) and the exact cookie name and value before constructing the ffuf command.

# Weak Brute-Force Protection

## Purpose of This Section

This section is purely conceptual — no hands-on lab commands are introduced. It covers the two most common defenses against brute-force attacks (rate limiting and CAPTCHAs) and explains how each can be weakened or bypassed under certain implementation conditions.

## Rate Limiting and Its Weakness

Rate limiting restricts how many requests a source can make within a time window. When implemented correctly it makes brute-force attacks infeasible by either introducing escalating delays or locking out the source. The critical implementation detail is how the "source" is identified. If the application reads the client's IP from a trusted network header like `X-Forwarded-For` rather than the actual TCP connection source — which is necessary when sitting behind load balancers or reverse proxies — an attacker can forge that header with a different value on every request. Since the application trusts the header unconditionally, each request appears to come from a different IP and the rate limit counter never accumulates. This is a straightforward bypass requiring only the addition of a randomized `X-Forwarded-For` value to each request in the brute-force tool. CVE-2020-35590 is a real-world example of this class of vulnerability.

## CAPTCHA and Its Weaknesses

CAPTCHAs are intended to require human interaction, making automated submission loops impractical. The two failure modes highlighted in the source are: first, leaking the CAPTCHA solution in the HTTP response itself (visible in the screenshot showing the solution in the DOM or response body), which allows a script to read and replay the answer without any image recognition; second, the increasing availability of automated CAPTCHA-solving tools, including AI-driven image and voice recognition models, which can solve standard CAPTCHA challenges programmatically. Either condition degrades the CAPTCHA from a human-verification control to a trivially bypassed one.

## Attacker Mindset

When encountering brute-force protection during an engagement, the first question is how the protection identifies the requester. If it is IP-based and the application trusts forwarded headers, test whether spoofing `X-Forwarded-For` bypasses the counter. If a CAPTCHA is present, inspect the response source for the solution before investing effort in external solvers. Both are low-effort checks that frequently succeed before more complex bypass techniques are needed.



# Default Credentials 

## What Default Credentials Are

Many web applications ship with hardcoded credentials intended for initial setup and access after installation. If an administrator fails to change these credentials after deployment, the application remains accessible to anyone who knows — or looks up — the defaults. This is a trivially exploitable condition requiring no technical attack tools.

## Reconnaissance Approach

The methodology here is purely OSINT-driven. There are three practical lookup paths: dedicated default credential databases like CIRT.net which index credentials by vendor and product, SecLists' Default-Credentials collection which provides offline wordlists, and a simple targeted web search combining the application name with the phrase "default credentials". The BookStack example demonstrates that installation documentation is often publicly indexed and directly reveals the default admin account.

## Why This Works

Open-source and commercial applications publish setup guides that include default login details by design. Those guides are indexed by search engines and remain accessible indefinitely. An attacker does not need to reverse-engineer anything — the information is in the product's own documentation.

## Where This Fits in the Methodology

Default credential testing should occur early in any authentication assessment, before investing time in brute-force or more complex attacks. If the application is newly deployed, misconfigured, or maintained by a non-technical administrator, default credentials are frequently still active. The cost of the check is minimal — one or two login attempts — and the payoff is immediate access if successful.

## Common Pitfalls

Assuming default credentials only apply to network devices or industrial systems is a mistake. Web applications across all categories — CMS platforms, wikis, dashboards, and admin panels — ship with defaults. Any identified application should be queried against at least one default credential source before moving to more resource-intensive attack techniques.

# Vulnerable Password Reset 

## Two Distinct Attack Paths

This section covers two separate vulnerabilities in password reset flows. The first is brute-forcing a weak security question answer. The second is a business logic flaw where a hidden `username` parameter in the reset flow can be manipulated to target a different account than the one that passed the security question.

## Security Question Brute-Force

Security questions based on geography — birth city, home city, city of education — have a finite and publicly enumerable answer space. A world cities CSV file provides roughly 26,000 candidate values, which is small enough for ffuf to exhaust quickly. The session cookie must be included in the ffuf command because the application uses the session to track which account the reset flow is targeting. Without it, the server has no context for the submitted security response. The filter string "Incorrect response." removes all failed attempts, leaving only the redirect (HTTP 302) that signals the correct answer. OSINT can reduce the wordlist significantly — filtering to a single country narrows the city list from 26,000 to around 1,000.

## Hidden Parameter Manipulation (Business Logic Flaw)

The more severe vulnerability requires no brute-forcing at all. The application passes the `username` as a POST parameter alongside the security response and again in the final password reset request. If the application does not verify that the username in the final step matches the username that passed the security question, the attacker can answer their own security question (or the target's) and then swap the `username` value in the final `POST /reset_password.php` request to `admin`. The server then resets the admin's password rather than the authenticated user's. This is a state management failure — the application should bind the reset flow to the session server-side and never trust a client-supplied username at the final step.

## Attack Chain - Security Question Brute-Force

Trigger a reset for the target account at `/reset.php` → capture the `PHPSESSID` → generate a city wordlist from the CSV → brute-force `/security_question.php` with the session cookie → obtain the correct response → proceed to `/reset_password.php` to set a new password.

## Attack Chain - Parameter Manipulation

Log in or trigger reset with a known account (`htb-stdnt`) → answer the security question correctly using known credentials → intercept the final `POST /reset_password.php` request → change `username=htb-stdnt` to `username=admin` → submit → admin password is now set to the attacker's chosen value.

## Common Pitfalls

For the brute-force path, omitting the session cookie is the primary failure. For the parameter manipulation path, the key is carefully reading every POST parameter in each step of the reset flow — hidden fields are easy to miss without intercepting requests in a proxy. Any parameter that identifies the target account and is controlled by the client is a potential manipulation point.

# Authentication Bypass via Direct Access

## The Core Vulnerability

The PHP code issues a `Location` redirect header to send unauthenticated users to the login page, but it does not call `exit` afterward. This means PHP continues executing the rest of the script after the redirect header is set. The full page content — including protected admin data — is generated and appended to the HTTP response body. The response is therefore a 302 with the entire admin page in its body.

## Why Browsers Hide the Vulnerability

A browser automatically follows a 302 redirect and renders the destination page (`index.php`), discarding the body of the redirect response. This makes the flaw invisible to casual browsing but fully exploitable with any tool that does not automatically follow redirects, or with an intercepting proxy that allows response modification.

## The Burp Technique

Burp's response interception feature allows the attacker to see and edit the raw HTTP response before the browser processes it. By changing the status code from `302 Found` to `200 OK`, the browser is instructed to render the response body as a normal page rather than redirecting. Since the body already contains the fully rendered admin page, the protected content is displayed without any authentication having occurred.

## The Fix

Adding `exit` immediately after the `header()` call stops PHP execution at that point. The response body remains empty, and no protected content is ever transmitted to the client. The redirect still fires, but there is nothing in the body to intercept.

## Attacker Mindset

Whenever a web application redirects to a login page after accessing a protected endpoint, the first check should be whether the redirect response contains content in its body. This can be done with curl using `--max-redirs 0` or by disabling automatic redirect following in Burp. Even a partial page in the response body can leak sensitive data or confirm that a direct-access bypass is possible.

## Common Pitfalls

This vulnerability is easy to overlook because the browser presents it as a working authentication gate. Always inspect redirect responses directly — do not rely on browser behavior to assess whether authentication is properly enforced.



# Authentication Bypass via Parameter Modification 

## What the Vulnerability Is

The application uses a client-supplied GET parameter (`user_id`) as part of its authentication and authorization logic. Rather than determining a user's identity and privileges exclusively from the server-side session, it reads `user_id` from the URL and uses that value to control what data is returned. This means the client — and therefore the attacker — has direct influence over which account context the server processes requests under.

## Observed Behavior Chain

Logging in with `htb-stdnt` issues a redirect to `/admin.php?user_id=183`. The page loads but shows limited data, indicating this account has low privileges. Removing the `user_id` parameter entirely from the URL causes the server to redirect back to the login page even though the session cookie is still valid — confirming that `user_id` is part of the authentication check, not just an optional hint. Restoring the parameter returns a 200 directly without requiring re-authentication, confirming it is trusted unconditionally by the server.

## Privilege Escalation via Parameter Brute-Force

Since `user_id` is a numeric identifier, the admin account likely has a different (often lower-numbered) ID such as `1`. Substituting alternative values into the parameter while holding a valid session allows the attacker to access the page under a different user's context. If the targeted `user_id` belongs to an administrator, the full admin data is returned. This is effectively an IDOR — the parameter is an insecure direct reference to a user record, and the server does not verify that the requesting session is authorized to access that record.

## Relationship to IDOR

This vulnerability sits at the intersection of authentication and authorization bypass. The parameter modification allows impersonating another user (authentication dimension) and accessing data the current session is not permitted to see (authorization dimension). The IDOR module covers the authorization aspect in more depth.

## Attacker Mindset

Any numeric or enumerable parameter in a post-login URL — especially one named `user_id`, `uid`, `account`, `id`, or similar — is a candidate for modification. The test is simple: change the value and observe whether the server enforces that the session owner matches the requested resource. If no mismatch error is returned, the parameter is being trusted without proper server-side validation.

## Common Pitfalls

Assuming a valid session cookie alone is sufficient proof that authentication is enforced. In this lab the session cookie is necessary but not sufficient — the `user_id` parameter is also required, and it is the parameter, not the session, that actually controls access. Always test what happens when URL parameters are removed, modified, or replaced with other users' identifiers.

# Attacking Session Tokens 

## Why Session Tokens Matter

A valid session token is equivalent to authenticated access. If an attacker can obtain or forge a token belonging to another user, they impersonate that user to the application without ever knowing their password. This section covers three categories of weak session tokens: insufficiently random tokens that can be brute-forced, tokens with predictable structure that can be enumerated, and tokens that contain encoded plaintext data that can be tampered with directly.

## Brute-Forceable Tokens

A four-character hexadecimal session token has at most 65,536 possible values — trivially small for ffuf using the same techniques from earlier sections. The more subtle variant is a 32-character token where 28 characters are static and only 4 are random. The visual length of the token is misleading; the actual entropy is identical to a 4-character token. Capturing multiple session tokens from repeated logins and comparing them side by side is the detection method. Any characters that remain constant across all samples are static and contribute zero entropy.

## Incrementing Tokens

Sequential numeric session identifiers are the most exploitable pattern. An attacker who knows their own token value can enumerate both past (decrement) and future (increment) tokens to hijack any active session. No brute-force tooling is even necessary — simple arithmetic suffices.

## Encoded Plaintext Tokens (Base64 and Hex)

These are the most directly exploitable. The token is not random at all — it is structured data that the application encodes and passes to the client, trusting it on return. The attack is: decode the token, identify the fields (username, role), modify the role or username value, re-encode, and submit the forged token. The application reads the role from the token itself rather than from a server-side session store, so the attacker fully controls their privilege level.

The base64 workflow: decode with `base64 -d` to read the plaintext, modify the target field, re-encode with `base64` to produce the forged token.

The hex workflow: identical logic, using `xxd -p` to produce the hex-encoded output of the modified plaintext.

## Detection Methodology

Always capture multiple session tokens across separate logins and compare them. Look for: repeated substrings (static segments), sequential or near-sequential numeric patterns, and tokens that decode cleanly under base64 or hex. Any of these signals indicates exploitable weakness. A well-implemented session token should be a cryptographically random value of sufficient length with no embedded structured data.

## Common Pitfalls

Assuming a long token is a strong token. Length alone does not provide security — the four-character entropy example demonstrates this clearly. Always analyze token content and variability, not just token length.


# Further Session Attacks

## Session Fixation

Session fixation exploits a web application that does not generate a new session token after the user successfully authenticates. The attack requires that the application also accept a session token value supplied externally — in this example through the `sid` GET parameter — and set the `session` cookie to that value.

The attack chain has four steps. The attacker authenticates, records their session token, then logs out to invalidate it. The attacker then sends the victim a crafted URL containing the known token as the `sid` parameter. When the victim visits the URL, the application obediently sets the `session` cookie to the attacker-supplied value. The victim then logs in, but because the application does not issue a new token on authentication, the victim's authenticated session is now bound to the token the attacker already knows. The attacker replays that token and takes over the session.

The fix is straightforward: always regenerate the session token immediately after a successful login. The pre-authentication token must never carry over into the post-authentication state.

## Improper Session Timeout

A session token with no expiry remains permanently valid. If an attacker captures a session token through any means — fixation, theft, brute-force — a missing or overly long timeout allows that token to be used indefinitely, long after the legitimate user has stopped interacting with the application. The appropriate timeout duration is application-specific: sensitive applications (banking, healthcare) warrant timeouts measured in minutes; lower-sensitivity applications may tolerate hours. The key requirement is that a finite timeout exists and is enforced server-side, not merely on the client.

## Attacker Mindset

For session fixation, the prerequisite is that the application accepts an externally supplied session identifier before authentication. This is testable by checking whether appending a custom value to a session-related GET or POST parameter results in that value being set as the session cookie in the response. For timeout issues, the test is to capture a valid token, wait beyond the expected session duration, and check whether it is still accepted. Both vulnerabilities are often overlooked during development because they only manifest in multi-step interaction scenarios.


# Skills Assessment 
## Attack Chain Overview

The assessment chains three techniques covered across the module: user enumeration via differing error messages, policy-aware password brute-forcing, and 2FA bypass via direct access with a partially authenticated session.

## Phase 1 - Reconnaissance

Directory fuzzing with ffuf against the root reveals `login.php` and `index.php` as the primary entry points. The discovered directories (`assets`, `css`, `js`) are not directly useful but confirm the application structure.

## Phase 2 - User Enumeration

Two error messages are present: "Unknown username or password." for unrecognized usernames and "Invalid credentials." for valid usernames with wrong passwords. Fuzzing the username field while filtering on the unknown-user message identifies `gladys` as a valid account. Multiple filter approaches are tested — string-based (`-fr`) and size-based (`-fs`) — both converge on the same result.

## Phase 3 - Policy-Aware Password Brute-Force

The application enforces a password policy. The `awk` command filters rockyou.txt to only entries that are exactly 12 characters long, contain lowercase, uppercase, and digits, and are alphanumeric only — matching the observed policy. This reduces the wordlist to a manageable size. Filtering on response size (`-fs 4344`) rather than string content is used as the detection method. The correct password `dWinaldasD13` is identified with a 302 redirect response.

## Phase 4 - 2FA Bypass via Direct Access

After logging in with valid credentials, the server issues a 302 to `/2fa.php` and sets a `PHPSESSID` cookie. The session is now in a partially authenticated state — past the password check but not yet past the 2FA gate. Instead of brute-forcing the OTP, the direct access technique from Section 10 is applied: the session cookie is replayed against protected endpoints directly, bypassing the 2FA checkpoint entirely. The endpoint `/profile.php` returns a 302 redirect to `/2fa.php` but with the protected page content in the response body — a classic missing-exit vulnerability. The flag is embedded in the rendered page body.

## Key Insight

The 2FA bypass here is not a brute-force of the OTP but a direct access bypass: the server redirects unauthenticated or 2FA-incomplete sessions back to `/2fa.php` but still renders and transmits the protected page content in the response body. curl does not follow redirects by default, so the body of the 302 response — including the flag — is visible directly in the terminal output without any Burp response interception needed.