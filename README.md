# VAPT — Vulnerability Assessment and Penetration Testing

[![Awesome](https://awesome.re/badge.svg)](https://awesome.re)
[![License: CC0-1.0](https://img.shields.io/badge/license-CC0--1.0-lightgrey.svg)](LICENSE)

A curated, practitioner-oriented guide to **Vulnerability Assessment and Penetration Testing (VAPT)** — methodology, free and open-source tools, how to build your own authorized practice lab, and the frameworks (MITRE ATT&CK, the Cyber Kill Chain, STRIDE) used to structure a test. Written for someone learning VAPT from scratch, not just someone who already knows the tool names.

**Scope:** the VAPT method and tooling — scoping and rules of engagement, reconnaissance, vulnerability scanning, exploitation, reporting, and remediation retesting — plus free/open-source tools for each phase and how to set up a legal, isolated environment to practice in. Compliance-driven penetration testing *requirements* (PCI-DSS Requirement 11.4, FedRAMP's annual pentest mandate) are covered by the companion [Security Frameworks](https://github.com/garynair/security-frameworks) and [Federal Compliance](https://github.com/garynair/federal-compliance) lists; this list is the practical how-to those requirements point to.

**A note on authorization before anything else:** every tool and technique in this list is dual-use. Running any of them against a system you do not own and do not have explicit, written authorization to test is illegal in most jurisdictions (in the US, under the Computer Fraud and Abuse Act) regardless of intent. This list is written for authorized engagements, CTF competitions, your own lab, and platforms built for legal practice (TryHackMe, HackTheBox, PortSwigger Web Security Academy). See [Legal and Authorization](#legal-and-authorization-scope-and-rules-of-engagement) below before running anything else in this list.

Contributions welcome.

---

## Contents

- [What VAPT Is](#what-vapt-is)
- [How to Approach a VAPT Engagement](#how-to-approach-a-vapt-engagement)
- [Legal and Authorization, Scope, and Rules of Engagement](#legal-and-authorization-scope-and-rules-of-engagement)
- [Methodology Standards (PTES, OSSTMM, NIST SP 800-115, OWASP)](#methodology-standards-ptes-osstmm-nist-sp-800-115-owasp)
- [MITRE ATT&CK](#mitre-attck)
- [Cyber Kill Chain and the Unified Kill Chain](#cyber-kill-chain-and-the-unified-kill-chain)
- [Threat Modeling for Scoping (STRIDE, PASTA, DREAD)](#threat-modeling-for-scoping-stride-pasta-dread)
- [Reconnaissance and OSINT Tools](#reconnaissance-and-osint-tools)
- [Network Scanning Tools (Nmap and Alternatives)](#network-scanning-tools-nmap-and-alternatives)
- [Vulnerability Scanners: Nessus and Free Alternatives](#vulnerability-scanners-nessus-and-free-alternatives)
- [Web Application Testing Tools](#web-application-testing-tools)
- [API Security Testing with Postman](#api-security-testing-with-postman)
- [Exploitation Frameworks](#exploitation-frameworks)
- [Password and Credential Testing](#password-and-credential-testing)
- [Wireless Testing](#wireless-testing)
- [Building Your Own Practice Lab](#building-your-own-practice-lab)
- [Free Legal Practice Platforms](#free-legal-practice-platforms)
- [Severity Scoring and Reporting (CVSS, CVE)](#severity-scoring-and-reporting-cvss-cve)
- [Templates in This Repo](#templates-in-this-repo)
- [Cross-Framework Mapping and Cloud Security Tooling](#cross-framework-mapping-and-cloud-security-tooling)
- [Certifications and Training](#certifications-and-training)
- [Government and Standards Bodies](#government-and-standards-bodies)
- [Learning Resources](#learning-resources)
- [Related Lists](#related-lists)

---

## What VAPT Is

**Vulnerability Assessment (VA)** is a broad, largely automated scan of a system or network to identify and list known vulnerabilities — outdated software, missing patches, misconfigurations — ranked by severity. It answers "what's wrong here?" and is typically run frequently (monthly or continuously) because it's cheap to repeat.

**Penetration Testing (PT)** is a manual, goal-driven simulation of an attacker actually trying to exploit those vulnerabilities (and others a scanner cannot find — logic flaws, chained weaknesses, social engineering) to determine real-world impact. It answers "so what — what could an attacker actually do with this?" and is typically run less often (annually, or after a major change) because it takes a skilled human days to weeks.

The two are complementary, not interchangeable: a vulnerability assessment tells you a server is missing a patch; a penetration test tells you whether that missing patch lets an attacker reach the customer database three hops away. Most mature programs run continuous vulnerability scanning feeding into a less frequent, deeper penetration test — which is exactly why this list covers both together.

---

## How to Approach a VAPT Engagement

1. **Define scope and get written authorization first.** See Legal and Authorization below — this is not optional and comes before any tool is opened.
2. **Choose a methodology.** PTES, OSSTMM, NIST SP 800-115, or the OWASP Testing Guide (for web apps specifically) — pick one so your phases and reporting are consistent and repeatable across engagements.
3. **Reconnaissance (passive, then active).** Gather what's publicly discoverable (OSINT) before touching the target directly, then move to active scanning once scope confirms it's in bounds.
4. **Vulnerability scanning.** Run an automated scan (Nessus, OpenVAS, Nuclei) to build the initial list of known weaknesses across the in-scope estate.
5. **Manual verification and exploitation.** Confirm scanner findings are not false positives, then attempt controlled exploitation against agreed rules of engagement — this is where a penetration test earns the name.
6. **Post-exploitation and lateral movement (if in scope).** Determine what an attacker could reach *from* the initial foothold — this is usually where the real business impact is found, not at the point of initial compromise.
7. **Document everything as you go.** Screenshots, commands run, timestamps — reconstructing evidence after the fact is how reports lose credibility with a client or auditor.
8. **Score and report findings.** Use CVSS for consistent severity scoring (see below), and write for two audiences in one report: an executive summary in business terms, and technical findings with reproduction steps.
9. **Remediate and retest.** A pentest that ends at the report, without a retest of fixed findings, is incomplete — most frameworks (PCI-DSS included) expect evidence that findings were actually closed.
10. **Feed results into the risk register.** Every unresolved finding becomes a risk-register entry with an owner and a treatment plan — see the companion [Risk Management](https://github.com/garynair/risk-management) list for that structure.

---

## Legal and Authorization, Scope, and Rules of Engagement

**Before running any tool in this list against anything other than your own isolated lab or a platform built for legal practice:**

1. Get **written** authorization from someone with the actual authority to grant it — a verbal "sure, go ahead" from the wrong person is not a legal defense.
2. Define scope precisely: specific IP ranges, domains, applications, and explicitly what is *out* of scope (production payment systems, third-party-hosted infrastructure you don't control, physical premises unless separately authorized).
3. Agree rules of engagement: permitted testing windows, whether denial-of-service-style testing is allowed, escalation contacts if something breaks, and how findings involving live customer data will be handled.
4. Get a signed **Letter of Authorization / Statement of Work** before the first scan — this is the document that turns otherwise-illegal access into an authorized engagement.
5. For a third party's cloud infrastructure (AWS, Azure, GCP), check the provider's own penetration-testing policy — most require notification even with the customer's own authorization, and some activities remain prohibited regardless of consent.
6. Retain authorization documentation for as long as your legal/compliance function requires — an auditor or a court will ask for it, not just take your word for it.

See [`templates/rules-of-engagement-template.md`](templates/rules-of-engagement-template.md) in this repo for a starting scope-and-authorization document.

- [Computer Fraud and Abuse Act (CFAA) Overview (DOJ)](https://www.justice.gov/jm/jm-9-48000-computer-fraud) - The US Department of Justice's manual entry on the CFAA, the primary federal statute criminalizing unauthorized computer access — the reason written authorization is not optional.
- [SANS: Legal Issues in Penetration Testing](https://www.sans.org/white-papers/legal-issues-pen-testing/) - A widely referenced SANS paper on the legal and contractual issues specific to structuring a penetration-testing engagement.
- [AWS Penetration Testing Policy](https://aws.amazon.com/security/penetration-testing/) - AWS's official policy on which activities customers may test without prior approval and which still require notification.

---

## Methodology Standards (PTES, OSSTMM, NIST SP 800-115, OWASP)

**Path to adoption:** all voluntary, free, and non-certifiable as standards (certifications built on top of them, like OSCP, are separate — see Certifications below).

- [Penetration Testing Execution Standard (PTES)](http://www.pentest-standard.org/) - A widely adopted seven-phase methodology (pre-engagement, intelligence gathering, threat modeling, vulnerability analysis, exploitation, post-exploitation, reporting) with detailed technical guidelines for each phase.
- [OSSTMM (Open Source Security Testing Methodology Manual)](https://www.isecom.org/OSSTMM.3.pdf) - ISECOM's rigorous, metrics-based methodology covering not just cyber but physical, wireless, and human/social-engineering testing channels.
- [NIST SP 800-115](https://csrc.nist.gov/pubs/sp/800/115/final) - "Technical Guide to Information Security Testing and Assessment," NIST's free federal methodology covering the full assessment lifecycle, widely used as a baseline outside government too.
- [OWASP Web Security Testing Guide (WSTG)](https://owasp.org/www-project-web-security-testing-guide/) - The definitive, free, community-maintained methodology for web application penetration testing, organized by vulnerability category with step-by-step test cases.
- [OWASP Mobile Application Security Testing Guide (MASTG)](https://mas.owasp.org/MASTG/) - The mobile-specific companion to WSTG, covering iOS and Android application testing methodology.
- [OWASP Top 10](https://owasp.org/www-project-top-ten/) - The most cited list of critical web application security risks, useful as a scoping checklist even outside a full WSTG-driven engagement.

---

## MITRE ATT&CK

**What it's for:** a free, continuously updated knowledge base of real-world adversary tactics and techniques, organized as a matrix (Tactics as columns — Initial Access, Execution, Persistence, Privilege Escalation, and more — Techniques as entries under each). Penetration testers use it two ways: to structure test cases around techniques real attackers actually use (rather than a generic checklist), and to map findings and post-exploitation activity back to a common, industry-standard vocabulary a client's blue team already recognizes.

- [MITRE ATT&CK](https://attack.mitre.org/) - The official matrix, browsable by Tactic, Technique, and platform (Enterprise, Mobile, ICS), with detection and mitigation guidance attached to each technique.
- [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) - Free browser tool for annotating and color-coding the matrix — commonly used to show which techniques a specific engagement covered, or which a target's defenses already detect.
- [Atomic Red Team](https://atomicredteam.io/) - Free, open-source library of small, executable tests mapped directly to individual ATT&CK techniques, useful for validating whether a specific technique is actually detected in your lab or client environment.

---

## Cyber Kill Chain and the Unified Kill Chain

**What it's for:** a phase model of how an attack actually unfolds end to end, used to structure both an attacker's plan and a defender's detection strategy — "where in the chain can we break this?" The Lockheed Martin Cyber Kill Chain is the original, widely known seven-stage model; MITRE ATT&CK is more granular (dozens of techniques per stage) but doesn't impose a strict linear order the way the Kill Chain does. Many practitioners use the Kill Chain for high-level engagement narrative and ATT&CK for the technical detail underneath each stage.

- [Lockheed Martin Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html) - The original seven-stage model: Reconnaissance, Weaponization, Delivery, Exploitation, Installation, Command and Control, Actions on Objectives.
- [Unified Kill Chain](https://www.unifiedkillchain.com/) - Paul Pols' 18-phase model explicitly designed to combine the Cyber Kill Chain's end-to-end narrative with ATT&CK's technical granularity, addressing gaps each model has on its own (e.g., the original Kill Chain's weak coverage of lateral movement).
- [MITRE ATT&CK Mapping to the Kill Chain](https://attack.mitre.org/resources/) - MITRE's own resource discussing how ATT&CK tactics relate to Kill Chain-style phase models.

---

## Threat Modeling for Scoping (STRIDE, PASTA, DREAD)

**What it's for:** before you scan or exploit anything, threat modeling identifies *what kinds* of attacks a system is realistically exposed to, so a test plan targets the right threat categories instead of running every available tool at everything. This is where STRIDE fits: it's not a scanning tool, it's a structured way to walk an architecture diagram and generate the specific test cases a scanner or manual tester then goes and validates.

- **STRIDE** (Microsoft) — a mnemonic for six threat categories: **S**poofing identity, **T**ampering with data, **R**epudiation, **I**nformation disclosure, **D**enial of service, **E**levation of privilege. Walk each component and data flow in an architecture diagram and ask where each category could apply; each "yes" becomes a specific test case for the engagement (e.g., "can an unauthenticated user spoof the session token on this endpoint?").
- **PASTA** (Process for Attack Simulation and Threat Analysis) — a seven-stage, risk-centric methodology that explicitly ties threat modeling to business impact and simulated attacker behavior, more heavyweight than STRIDE and often used for higher-value applications.
- **DREAD** — a scoring model (**D**amage, **R**eproducibility, **E**xploitability, **A**ffected users, **D**iscoverability) sometimes used to rank threats identified via STRIDE before testing, or to rank findings after testing, though many practitioners now prefer CVSS (below) for the latter because DREAD's scoring is more subjective.

- [Microsoft Threat Modeling (STRIDE) and Threat Modeling Tool](https://learn.microsoft.com/en-us/azure/security/develop/threat-modeling-tool-threats) - Microsoft's own reference for STRIDE and its free Threat Modeling Tool for building the architecture diagrams STRIDE is applied to.
- [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling) - OWASP's overview comparing STRIDE, PASTA, DREAD, and other threat-modeling methodologies with guidance on when each applies.
- [PASTA Threat Modeling Overview](https://versprite.com/blog/what-is-pasta-threat-modeling/) - A detailed walkthrough of PASTA's seven stages from one of the methodology's originating contributors.

---

## Reconnaissance and OSINT Tools

**Path to adoption:** free and open-source; passive OSINT collection is generally lower legal risk than active scanning (it doesn't touch the target directly) but should still be within agreed scope.

- [theHarvester](https://github.com/laramies/theHarvester) - Free, open-source tool for gathering emails, subdomains, hosts, and employee names from public sources (search engines, PGP key servers, Shodan) during the passive-recon phase.
- [Recon-ng](https://github.com/lanmaster53/recon-ng) - A free, modular web-reconnaissance framework with a Metasploit-like console interface, automating a large share of OSINT collection through installable modules.
- [Shodan](https://www.shodan.io/) - A free-tier-available search engine for internet-connected devices and exposed services, widely used to identify a target's external attack surface before active scanning begins.
- [Maltego Community Edition](https://www.maltego.com/community/) - Free-tier link-analysis tool for visually mapping relationships between domains, IPs, people, and infrastructure gathered during OSINT.

---

## Network Scanning Tools (Nmap and Alternatives)

**Path to adoption:** free and open-source. Active scanning — even simple port scanning — should be explicitly within your agreed scope and rules of engagement before you run it.

- [Nmap](https://nmap.org/) - The essential, free, open-source network scanner: host discovery, port scanning, service/version detection, and OS fingerprinting, extensible through the Nmap Scripting Engine (NSE) for basic vulnerability checks.
- [Nmap Scripting Engine (NSE) Documentation](https://nmap.org/book/nse.html) - The official guide to Nmap's scripting engine, including the `vuln` and `safe` script categories most relevant to a VAPT engagement.
- [Masscan](https://github.com/robertdavidgraham/masscan) - A free, extremely high-speed port scanner capable of scanning the entire IPv4 address space in minutes, typically used for broad initial discovery before a more detailed Nmap pass on the hosts found.
- [Netcat](https://nc110.sourceforge.io/) - The free, classic "TCP/IP Swiss army knife" — banner grabbing, simple port checks, and setting up listeners during later exploitation phases.

---

## Vulnerability Scanners: Nessus and Free Alternatives

**Path to adoption:** Nessus itself is commercial, but Tenable publishes a genuinely free tier; several fully open-source alternatives exist with no host-count limit.

- [Nessus Essentials](https://www.tenable.com/products/nessus/nessus-essentials) - Tenable's free tier of the industry-standard vulnerability scanner, licensed for up to 16 IP addresses — free registration required, no cost. The best starting point for learning the workflow the paid product (Nessus Professional) also uses.
- [Greenbone Community Edition (OpenVAS)](https://www.greenbone.net/en/community-edition/) - The fully free, open-source vulnerability-scanning platform (formerly branded standalone as OpenVAS), with no host-count limit, maintaining its own regularly updated vulnerability-test feed.
- [Qualys Community Edition](https://www.qualys.com/community-edition/) - Tenable's competitor's free tier, covering a limited number of assets across VMDR (vulnerability management) and a handful of other Qualys modules.
- [Nuclei](https://github.com/projectdiscovery/nuclei) - A free, open-source, template-driven vulnerability scanner from ProjectDiscovery, fast-growing in popularity for its large community template library covering CVEs, misconfigurations, and exposed panels.
- [Nikto](https://github.com/sullo/nikto) - A free, open-source web-server scanner checking for outdated software, dangerous files, and common misconfigurations — lighter-weight than a full web-app scanner but a fast first pass.

---

## Web Application Testing Tools

**Path to adoption:** free and open-source, or a genuinely usable free tier for the two dominant commercial-grade proxies.

- [OWASP ZAP (Zed Attack Proxy)](https://www.zaproxy.org/) - Free, open-source web-app security scanner and intercepting proxy, maintained by OWASP, with both automated scanning and manual testing (via the intercepting proxy) in one tool — the most widely used free alternative to Burp Suite.
- [Burp Suite Community Edition](https://portswigger.net/burp/communitydownload) - PortSwigger's free tier of the industry-standard intercepting proxy: manual request interception and modification, Repeater, and Intruder (rate-limited in the free tier); the professional tier adds the automated scanner.
- [sqlmap](https://sqlmap.org/) - Free, open-source, automated SQL-injection detection and exploitation tool, one of the most widely used specialized web-app testing utilities.
- [DIRB / Gobuster](https://github.com/OJ/gobuster) - Free, open-source directory and file brute-forcing tools for discovering unlinked content on a web server, a routine early step in web-app testing.

---

## API Security Testing with Postman

**What it's for:** Postman is not purpose-built as a security tool, but it's the most widely used API client for manually testing REST/GraphQL API endpoints — authentication bypass, broken object-level authorization (BOLA/IDOR), mass assignment, and rate-limiting gaps are usually found through exactly the kind of manual request crafting and replay Postman is built for.

**How to use it for VAPT:**
1. Import or build a collection covering every documented endpoint, then deliberately test undocumented ones discovered via recon (parameter fuzzing, common path guessing).
2. Test authorization, not just authentication: swap a valid token/ID for another user's and confirm the API correctly denies the request (this is exactly how most Broken Object-Level Authorization findings — OWASP API Security Top 10's #1 risk — are found).
3. Use environment variables to switch between test-user tokens quickly, so cross-user authorization tests are fast to repeat across every endpoint.
4. Test input validation deliberately: oversized payloads, unexpected types, and injection payloads in every parameter, not just the ones the documentation calls out.
5. Export findings as a shareable collection so a reproduction step in your report is literally "import this collection and run request #4" rather than a prose description.

- [Postman](https://www.postman.com/) - The free tier covers everything needed for manual API security testing: request building, environments, collections, and scripted pre-request/test assertions.
- [OWASP API Security Top 10](https://owasp.org/www-project-api-security/) - The API-specific companion to the OWASP Top 10, the checklist most Postman-driven API testing is structured against.

---

## Exploitation Frameworks

**Path to adoption:** Metasploit Framework itself is free and open-source; commercial tiers (Metasploit Pro) add reporting and workflow automation on top of the same engine.

- [Metasploit Framework](https://github.com/rapid7/metasploit-framework) - The free, open-source exploitation framework: a large, actively maintained library of exploits, payloads, and post-exploitation modules, and the closest thing the industry has to a standard exploitation platform.
- [Metasploitable 2/3](https://docs.rapid7.com/metasploit/metasploitable-2/) - Rapid7's deliberately vulnerable virtual machines, purpose-built as a safe, legal target for practicing Metasploit and general exploitation technique in your own lab.

---

## Password and Credential Testing

**Path to adoption:** free and open-source; only ever run against credentials or hashes you are authorized to test (your own lab, an authorized engagement's extracted hashes, or a CTF).

- [Hashcat](https://hashcat.net/hashcat/) - The free, GPU-accelerated password-recovery tool, widely regarded as the fastest hash-cracking tool available, supporting hundreds of hash types.
- [John the Ripper](https://www.openwall.com/john/) - The free, long-established password-cracking tool, with the "Jumbo" community edition adding broad hash-format support beyond the base distribution.
- [SecLists](https://github.com/danielmiessler/SecLists) - A free, community-maintained collection of wordlists (passwords, usernames, fuzzing payloads) used across nearly every tool in this list, not just password cracking.

---

## Wireless Testing

**Path to adoption:** free and open-source; wireless testing has an especially narrow legal boundary since radio signals cannot be scoped to a single organization's property line the way a network range can — confirm authorization covers the physical premises, not just the SSID.

- [Aircrack-ng](https://www.aircrack-ng.org/) - The free, open-source suite for assessing Wi-Fi network security: packet capture, WEP/WPA/WPA2 key testing, and deauthentication testing, requiring a compatible wireless adapter in monitor mode.

---

## Building Your Own Practice Lab

**How to set it up:**
1. Install a Type-2 hypervisor: [VirtualBox](https://www.virtualbox.org/) (free) or VMware Workstation Player (free for personal use) — this is where every lab machine lives.
2. Install an attacker VM: [Kali Linux](https://www.kali.org/) or [Parrot Security OS](https://www.parrotsec.org/), both free, purpose-built Linux distributions with most tools in this list pre-installed.
3. Install one or more deliberately vulnerable target VMs — see Free Legal Practice Platforms below for specific images (Metasploitable, OWASP Juice Shop, DVWA).
4. **Isolate the network.** Configure your hypervisor's network mode as Host-Only or an internal/NAT network — never bridged to your home or work network — so lab traffic never reaches, or is reachable from, anything outside the lab. This single step is what keeps a home lab unambiguously legal: you're only ever testing machines you own, on a network segment that can't touch anyone else's.
5. Snapshot target VMs before testing so you can roll back after intentionally breaking something.
6. Keep the attacker VM's tools updated (`apt update && apt full-upgrade` on Kali) — vulnerability signatures and exploit modules go stale quickly.
7. Once comfortable in your own lab, move to a legal cloud-hosted range (TryHackMe, HackTheBox) before ever touching a real, third-party-owned target.

- [VirtualBox](https://www.virtualbox.org/) - Free, open-source hypervisor, the most common choice for a home VAPT lab.
- [Kali Linux](https://www.kali.org/) - The most widely used penetration-testing Linux distribution, free, with most tools in this list pre-installed and maintained by Offensive Security.
- [Parrot Security OS](https://www.parrotsec.org/) - A free, Debian-based alternative to Kali with a similar tool set and a lighter default footprint.

---

## Free Legal Practice Platforms

**Path to adoption:** free (with paid tiers for more content) and explicitly built for legal, unrestricted practice — no separate authorization letter needed for these specifically, because the platform itself is the authorizing party for its own infrastructure.

- [TryHackMe](https://tryhackme.com/) - Free-tier, browser-accessible, guided rooms ranging from complete-beginner to advanced, widely recommended as the first stop for structured, legal hands-on practice.
- [HackTheBox](https://www.hackthebox.com/) - Free-tier access to a large library of standalone vulnerable machines and challenges, less guided than TryHackMe and generally considered the next step up in difficulty.
- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - Completely free, extensive web-application security curriculum built by the makers of Burp Suite, pairing theory with live, legal practice labs for nearly every OWASP Top 10 category.
- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/) - A free, open-source, deliberately vulnerable modern web application, runnable locally via Docker, widely used for web-app testing practice and OWASP Top 10 training.
- [DVWA (Damn Vulnerable Web Application)](https://github.com/digininja/DVWA) - A free, open-source PHP/MySQL web application with adjustable difficulty levels, one of the longest-standing deliberately vulnerable practice targets.
- [VulnHub](https://www.vulnhub.com/) - A free repository of downloadable, deliberately vulnerable VM images for offline, self-hosted practice in your own isolated lab.

---

## Severity Scoring and Reporting (CVSS, CVE)

**How to score and report a finding:**
1. Score every finding with CVSS so severity ratings are consistent across testers, engagements, and time — don't invent an ad hoc High/Medium/Low without a documented scoring rationale.
2. Reference the relevant CVE ID(s) where a finding matches a known, published vulnerability, so the client (and their other tools) can cross-reference it.
3. Write every finding with the same structure: title, CVSS score and vector string, affected asset(s), description, reproduction steps, evidence (screenshots/output), business impact in plain language, and remediation guidance.
4. Separate the executive summary (business risk, in plain language, ideally one page) from the technical findings (full detail for the team that will actually fix it) — the same report should serve both audiences without forcing either to wade through the other's section.
5. Include a remediation-retest section (or a separate retest report) confirming which findings were verified as fixed, and when.

See [`templates/vulnerability-finding-template.md`](templates/vulnerability-finding-template.md) and [`templates/pentest-report-outline.md`](templates/pentest-report-outline.md) in this repo.

- [CVSS (Common Vulnerability Scoring System)](https://www.first.org/cvss/) - FIRST.org's free, industry-standard severity-scoring framework (current version 4.0, with 3.1 still widely used), including the official calculator.
- [CVSS Calculator](https://www.first.org/cvss/calculator/4-0) - The official interactive calculator for generating a CVSS score and vector string from a finding's characteristics.
- [CVE Program](https://www.cve.org/) - The free, publicly available dictionary of standardized vulnerability identifiers referenced throughout the industry, including by every scanner in this list.
- [National Vulnerability Database (NVD)](https://nvd.nist.gov/) - NIST's free, enriched version of the CVE list, adding CVSS scores, CWE mappings, and affected-product data to each entry.

---

## Templates in This Repo

- [`templates/rules-of-engagement-template.md`](templates/rules-of-engagement-template.md) - A starting scope, authorization, and rules-of-engagement document to complete before any engagement begins.
- [`templates/vulnerability-finding-template.md`](templates/vulnerability-finding-template.md) - A single-finding template built on the CVSS-scoring structure described above.
- [`templates/pentest-report-outline.md`](templates/pentest-report-outline.md) - A full report outline separating the executive summary from technical findings, plus a remediation-retest section.

---

## Cross-Framework Mapping and Cloud Security Tooling

- [Prowler](https://github.com/prowler-cloud/prowler) - Open-source cloud security and compliance scanner for AWS, Azure, GCP, and Kubernetes; the cloud-configuration equivalent of a network vulnerability scanner. Also referenced in the companion [Security Frameworks](https://github.com/garynair/security-frameworks) list.
- [ScoutSuite](https://github.com/nccgroup/ScoutSuite) - Open-source multi-cloud security auditing tool from NCC Group, producing a single report across major cloud providers.
- [Dradis](https://dradisframework.com/) - Free (community edition) and commercial reporting and collaboration platform purpose-built for penetration-testing teams to consolidate findings from multiple tools into one report.
- [Faraday](https://github.com/infobyte/faraday) - Open-source vulnerability-management and collaboration platform for consolidating tool output (Nmap, Nessus, Burp, and more) across a pentest team.

---

## Certifications and Training

- [OSCP (OffSec Certified Professional)](https://www.offsec.com/courses/pen-200/) - Offensive Security's hands-on, exam-based certification, widely regarded as the industry benchmark for demonstrating practical exploitation skill rather than multiple-choice knowledge.
- [CEH (Certified Ethical Hacker)](https://www.eccouncil.org/train-certify/certified-ethical-hacker-ceh/) - EC-Council's broad, knowledge-based introductory certification covering VAPT methodology and tool familiarity.
- [GPEN (GIAC Penetration Tester)](https://www.giac.org/certifications/penetration-tester-gpen/) - SANS/GIAC's certification covering the process and legal/methodological rigor of a penetration-testing engagement.
- [CompTIA PenTest+](https://www.comptia.org/certifications/pentest) - A vendor-neutral certification covering planning, scoping, and the full VAPT lifecycle, positioned as an intermediate step below OSCP.
- [eJPT (eLearnSecurity Junior Penetration Tester)](https://ine.com/learning/certifications/ejpt) - INE's affordable, practical, entry-level penetration-testing certification, commonly recommended as a first hands-on credential before OSCP.
- [OSWE (OffSec Web Expert)](https://www.offsec.com/courses/web-300/) - Offensive Security's advanced, source-code-review-driven web application exploitation certification.

---

## Government and Standards Bodies

- [CISA](https://www.cisa.gov/) - The Cybersecurity and Infrastructure Security Agency, which publishes its own free penetration-testing and vulnerability-scanning services for eligible US critical-infrastructure organizations.
- [FIRST.org](https://www.first.org/) - The Forum of Incident Response and Security Teams, which owns and maintains the CVSS scoring standard.
- [MITRE](https://www.mitre.org/) - The not-for-profit R&D organization maintaining both ATT&CK and the CVE Program.
- [NIST Computer Security Resource Center (CSRC)](https://csrc.nist.gov/) - Publisher of SP 800-115 and host of the National Vulnerability Database.
- [OWASP Foundation](https://owasp.org/) - The nonprofit maintaining the Top 10, WSTG, MASTG, API Security Top 10, and Juice Shop referenced throughout this list.

---

## Learning Resources

- [PortSwigger Web Security Academy](https://portswigger.net/web-security) - Free, comprehensive, hands-on web-app security curriculum — the single best free starting point for web-focused VAPT learning.
- [TryHackMe](https://tryhackme.com/) - Free-tier, structured, guided learning paths for complete beginners through to advanced topics.
- [HackTricks](https://book.hacktricks.xyz/) - A free, extensively cross-referenced, community-maintained knowledge base of practical techniques across nearly every VAPT topic and tool.
- [Offensive Security's Free Content](https://www.offsec.com/free-cybersecurity-education/) - Free introductory material from the organization behind OSCP, including the free "PEN-103: Kali Linux Basics" course.

---

## Related Lists

- [Security Frameworks](https://github.com/garynair/security-frameworks) - A companion curated list covering NIST CSF, ISO/IEC 27001, PCI-DSS (which mandates annual penetration testing under Requirement 11.4), CIS Controls, and DISA STIG.
- [Risk Management](https://github.com/garynair/risk-management) - A companion curated list covering the risk register, heat maps, and treatment planning that unresolved VAPT findings feed into.
- [Federal Compliance](https://github.com/garynair/federal-compliance) - A companion curated list covering FedRAMP's own annual penetration-testing requirement and the POA&M process open findings feed into for federal systems.
- [IT Audit & Controls](https://github.com/garynair/it-audit-controls) - A companion curated list covering ITGC and control-testing methodology adjacent to, but distinct from, adversarial VAPT.

---

## Contributing

PRs welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the criteria a new entry must meet.

## License

This list is published under [CC0 1.0 Universal](LICENSE). The linked resources retain their own licenses. Templates in the `templates/` directory are original works released under the same CC0 license — use, modify, and redistribute them freely.
