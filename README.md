# Footprinting & Network Scanning
Practical cybersecurity project exploring footprinting, reconnaissance, DNS enumeration, web technology discovery, and network scanning.

## Overview

This project covers Week 2 of my NetworkWalks internship, combining two practicals:

- **W2-PM1 — Footprinting & Reconnaissance:** using six Kali Linux tools to build a public profile of `networkwalks.com` without any active exploitation.
- **W2-PM5 — Network Scanning:** using Zenmap to discover live hosts on my own local network.

Full write-up (with risk analysis and recommendations) is in [`wk2_Blessing_Augustine_Report.pdf`](wk2_Blessing_Augustine_Report.pdf).

> **Disclaimer:** All activity here was performed only against `networkwalks.com` (authorized through the internship program) or my own local network. This repository is for educational purposes only.

## Tools Used

| Tool | Purpose |
|---|---|
| WHOIS | Domain registration details (registrar, dates, name servers) |
| WhatWeb | Fingerprint web technologies (CMS, plugins, server, IP) |
| Nslookup | Resolve domain name to IP address |
| curl -I | Inspect HTTP response headers |
| Wafw00f | Detect Web Application Firewall |
| DNSRecon | Enumerate DNS records (SOA, NS, MX, TXT, SRV) |
| ipconfig | Identify local IP, subnet mask, gateway |
| Zenmap (Nmap GUI) | Ping scan to discover live hosts and MAC addresses |

## Part 1 — Footprinting `networkwalks.com`

### How to Reproduce

Run each of the following in a Kali Linux terminal:

```bash
whois networkwalks.com
whatweb networkwalks.com
nslookup networkwalks.com
curl -I https://networkwalks.com
wafw00f networkwalks.com
dnsrecon -d networkwalks.com
```

No special setup is required beyond a standard Kali install — all six tools ship with it by default.

### Findings

| Command | Key Finding |
|---|---|
| `whois networkwalks.com` | Registered via GoDaddy, created 2019, expires 2027, name servers on Hostgator, registrant masked via Domains By Proxy |
| `whatweb networkwalks.com` | WordPress 7.1, WP Download Manager 3.3.58, Bootstrap 7.1, jQuery 3.7.1, IP `192.232.216.135` |
| `nslookup networkwalks.com` | Resolves to `192.232.216.135` |
| `curl -I https://networkwalks.com` | HTTP/2 200, REST API endpoints exposed via `link` header (`/wp-json/`), session cookie `__wpdm_client` |
| `wafw00f networkwalks.com` | Protected by ModSecurity (SpiderLabs) |
| `dnsrecon -d networkwalks.com` | SOA/NS on Hostgator, MX to `mail.networkwalks.com`, SPF TXT, Google site-verification TXT, 8 SRV autodiscover records (cPanel) |

## Part 2 — Local Network Scan with Zenmap

### How to Reproduce

1. On the Windows host, open **Command Prompt** and run `ipconfig` to find your local IPv4 address and subnet mask.
2. Open **Zenmap**.
3. In the **Target** field, enter your subnet in CIDR notation (e.g. `10.0.0.0/24`).
4. In the **Profile** dropdown, select **Ping scan**. This runs `nmap -sn <target>` under the hood.
5. Click **Scan** and wait for it to complete — it will list every host that responded, with IP and MAC address, under the **Hosts** tab.
6. Click the **Topology** tab, then **Legend** to enable the key, to view a visual map of discovered hosts.
7. Optionally, click **Save Graphic** to export the topology as an image/PDF for evidence.

### Findings

**Local configuration (`ipconfig`):**
- IPv4: `10.0.0.10`
- Subnet mask: `255.255.255.0`
- Default gateway: `10.0.0.1`

**Ping scan (`nmap -sn 10.0.0.0/24`):** 3 hosts up out of 256 addresses, in 8.07 seconds.

| Host | Status | MAC Address | Notes |
|---|---|---|---|
| 10.0.0.1 | Up | 52:54:00:12:35:00 (QEMU virtual NIC) | Default gateway |
| 10.0.0.2 | Up | 08:00:27:8A:35:D2 (Oracle VirtualBox virtual NIC) | VM on the network |
| 10.0.0.10 | Up | — | My own host |

A topology diagram was generated in Zenmap's Topology tab with the legend enabled, showing all three hosts as green nodes (fewer than 3 open ports each).

## Risk Summary

Full detail is in the report, but at a glance: findings ranged from **Low** to **Medium** risk. Everything gathered here was public information or a basic host discovery scan — no exploitation or vulnerability validation was performed, so none of these findings confirm an actual vulnerability on their own.

## Recommendations

- Regularly review what WhatWeb/HTTP headers expose about the tech stack
- Keep CMS and plugins patched and versions out of easy public view where possible
- Periodically audit DNS records for anything no longer needed publicly
- Keep the WAF tuned and monitored
- Run periodic internal network scans to confirm only expected devices are present

## Key Takeaways

This week's practicals helped me understand the relationship between reconnaissance, network visibility, and security assessment.

I learned how to:

- Perform basic domain reconnaissance
- Collect WHOIS information
- Identify website technologies
- Resolve domains to IP addresses
- Inspect HTTP response headers
- Identify WAF technologies
- Enumerate DNS records
- Determine my local network range
- Discover active hosts using Zenmap
- Interpret reconnaissance findings from a security perspective
  
Footprinting and network scanning can provide valuable information that can later support vulnerability assessment, defensive monitoring, and penetration testing when performed within an authorized environment.

## Repository Contents
├── README.md
├── wk2_Blessing_Augustine_Report.pdf   # Full report
└── evidence/                                    # Terminal output & Zenmap screenshots


**Cybersecurity Internship Program — NetworkWalks Academy**

**Author:** Blessing Princess Augustine

**Cybersecurity Intern | Aspiring Penetration Tester**

Focused on building practical skills in ethical hacking and penetration testing through hands-on labs, reconnaissance, network scanning, vulnerability assessment, and security testing.



---
*Part of my ongoing cybersecurity learning journey at NetworkWalks Academy, working toward penetration testing.*
