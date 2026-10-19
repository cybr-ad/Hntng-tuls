# 🎯 Bug Bounty Toolkit (`bug-tools`)

[![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)](https://www.kali.org/)
[![Debian](https://img.shields.io/badge/Debian-Supported-A81D33?style=for-the-badge&logo=debian&logoColor=white)](https://www.debian.org/)
[![Bash](https://img.shields.io/badge/Bash-5.x-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)](https://www.gnu.org/software/bash/)
[![Go](https://img.shields.io/badge/Go-1.21+-00ADD8?style=for-the-badge&logo=go&logoColor=white)](https://go.dev/)
[![Python](https://img.shields.io/badge/Python-3.11+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](https://opensource.org/licenses/MIT)

> **A self-healing, battle-tested automated installer for Kali Linux and Debian systems.**  
> Installs 70+ offensive security and reconnaissance tools, configures wordlists and resolvers, builds standalone CLI wrappers, and auto-provisions your API keys across native tool configurations.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Key Features](#-key-features)
- [Installation Stages](#-installation-stages-at-a-glance)
- [Quick Start](#-quick-start)
  - [Prerequisites](#prerequisites)
  - [Step-by-Step Setup](#step-by-step-setup)
- [Centralized API Key Configuration](#-centralized-api-key-configuration)
- [Included Tools & Categorized Reference](#-included-tools--categorized-reference)
  - [1. Subdomain Enumeration & DNS Recon](#1--subdomain-enumeration--dns-recon)
  - [2. Web Crawling, Spidering & JS Analysis](#2--web-crawling-spidering--js-analysis)
  - [3. Web Fuzzing & Content Discovery](#3--web-fuzzing--content-discovery)
  - [4. Vulnerability Scanning & Parameter Fuzzing](#4--vulnerability-scanning--parameter-fuzzing)
  - [5. CMS & Framework Auditing](#5--cms--framework-auditing)
  - [6. Cloud, CORS & Secrets Extraction](#6--cloud-cors--secrets-extraction)
  - [7. Network & Port Scanning](#7--network--port-scanning)
  - [8. Dictionaries, Wordlists & Payloads](#8--dictionaries-wordlists--payloads)
- [Filesystem Hierarchy](#-filesystem-hierarchy)
- [Troubleshooting & Auto-Fixes](#-troubleshooting--auto-fixes)
- [Legal Disclaimer](#%EF%B8%8F-legal-disclaimer)
- [License](#-license)

---

## 💡 Overview

Setting up a complete bug bounty environment on a fresh machine typically requires hours of compiling Go binaries, resolving Debian/Kali PEP 668 package conflicts, configuring MassDNS resolvers, cloning payloads, and manually hunting down configuration files for API keys.

`install_tools.sh` automates the entire process end-to-end. It features **built-in self-healing intelligence** to anticipate, detect, and resolve common installation blockers automatically before they can interrupt your deployment.

---

## ✨ Key Features

- 🩺 **Self-Healing Preflight**: Detects and clears stale `apt` locks, repairs interrupted `dpkg` configurations, monitors disk space, and tests/restores DNS with fallback public resolvers (`1.1.1.1`, `8.8.8.8`, `9.9.9.9`).
- 🔄 **Idempotent Execution**: Completely safe to rerun multiple times. It detects already-installed tools and skips them, only compiling or downloading missing packages.
- ⚡ **Auto Go Toolchain Check**: Verifies your Go installation and automatically installs or upgrades to official Go `>= 1.21` from `go.dev` (with multi-architecture support for `x86_64` and `aarch64/ARM64`).
- 🔑 **Automated API Key Distribution**: Write your API keys once in `api_keys.txt`. The script parses and injects them directly into the native configuration paths for Subfinder, Amass, theHarvester, Shodan CLI, Nuclei/PDCP, Chaos, and Metabigor.
- 🛡️ **Python PEP 668 & Pipx Handling**: Bypasses Debian's `EXTERNALLY-MANAGED` restrictions safely using isolated `pipx` environments with fallbacks to system packages.
- 🚀 **CLI Wrappers for Git Repos**: Generates global executable wrappers in `/usr/local/bin` for Python git-only repositories (`corsy`, `paramspider`, `ssrfmap`, `tplmap`), allowing you to call them directly from anywhere.
- 📦 **Automated Post-Setup**: Updates Nuclei templates, downloads MassDNS resolver lists, symlinks SecLists to `/usr/share/wordlists/seclists`, and registers global environment profiles.
- 📝 **Live Logging**: Detailed, color-coded terminal output (`✓`, `⚠`, `✗`, `🔧`) with persistent logs written to `/var/log/tool_install.log`.

---

## 🔄 Installation Stages at a Glance

The installer executes through 9 structured milestones:

```
[0/8] Preflight checks & auto-repair   (apt locks, dpkg state, disk, DNS, git tuning)
[1/8] Base dependencies               (compilers, development headers, runtimes)
[2/8] Go toolchain check & upgrade    (ensures Go ≥ 1.21 with persistent environment)
[3/8] APT security tools              (nmap, sqlmap, gobuster, ffuf, nikto, amass, etc.)
[4/8] Go tools                        (ProjectDiscovery suite, TomNomNom tools, web spiders)
[5/8] Python tools                    (pipx-isolated CLI tools and core pentest libraries)
[6/8] GitHub repositories             (wordlists, CMS tools, git-only scanners + CLI wrappers)
[7/8] API key distribution            (auto-provisions configs + live timeout-protected test)
[8/8] Post-install fixes              (template updates, MassDNS resolvers, wordlist symlinks)
```

---

## 🚀 Quick Start

### Prerequisites

- **Operating System:** Kali Linux (2023.x+ recommended), Debian 12+, or Ubuntu LTS.
- **Privileges:** Root / `sudo` access.
- **Disk Space:** Minimum 10 GB free recommended (to clone complete SecLists, Go cache, and dependencies).
- **Network:** Active internet connection.

### Step-by-Step Setup

#### 1. Clone the Repository
```bash
git clone https://github.com/cybr-ad/bug-tools.git
cd bug-tools
```

#### 2. Configure Your API Keys (Optional but Recommended)
Open `api_keys.txt` in your text editor and add whatever API keys you have. Unused keys can be left blank:
```bash
nano api_keys.txt
```

> [!TIP]
> You do **not** need all keys! Fill in only the ones you own. The script will automatically configure tools to run in free mode for any omitted keys.

#### 3. Make Scripts Executable & Run the Installer
```bash
chmod +x install_tools.sh api_setup.sh
sudo ./install_tools.sh
```

#### 4. Refresh Your Environment
After installation completes, reload your shell profile or open a new terminal tab to load Go binaries and tool environment variables:
```bash
source ~/.bashrc
# Or source the system-wide profile created by the installer:
source /etc/profile.d/bugbounty.sh
```

---

## 🔑 Centralized API Key Configuration

The repository includes a dedicated key engine ([`api_setup.sh`](api_setup.sh)) that ingests [`api_keys.txt`](api_keys.txt) and automatically formats and deploys your credentials into the native configuration files of each tool:

| Target Tool | Configuration Destination | Supported Providers |
| :--- | :--- | :--- |
| **Subfinder** | `~/.config/subfinder/provider-config.yaml` | Shodan, Censys, VirusTotal, SecurityTrails, Chaos, BinaryEdge, GitHub, FullHunt, IntelX, WhoisXML, Fofa, CertSpotter, DNSDB, BuiltWith, ZoomEye, Quake, ThreatBook, C99, BeVigil, PassiveTotal, BufferOver, etc. |
| **OWASP Amass** | `~/.config/amass/config.ini` | Shodan, Censys, VirusTotal, SecurityTrails, Chaos, BinaryEdge, GitHub, FullHunt, IntelX, WhoisXML, Fofa, CertSpotter, DNSDB, ZoomEye, Quake, ThreatBook, PassiveTotal, BufferOver |
| **theHarvester** | `~/.theHarvester/api-keys.yaml` & `/etc/theHarvester/api-keys.yaml` | Shodan, Censys, VirusTotal, SecurityTrails, BinaryEdge, GitHub, Hunter.io, IntelX, WhoisXML, FullHunt, HIBP, Tomba, CertSpotter, BuiltWith, RouteViews, BeVigil, PassiveTotal |
| **Shodan CLI** | `~/.shodan/api_key` & `shodan init <KEY>` | Official Shodan API key integration |
| **ProjectDiscovery Chaos** | `export CHAOS_KEY=...` & `/etc/profile.d/chaos.sh` | ProjectDiscovery Cloud Platform (Chaos DNS dataset) |
| **Nuclei / PDCP** | `~/.pdcp/credentials.yaml` & `export PDCP_API_KEY=...` | ProjectDiscovery Cloud Platform token |
| **GitHub Search** | `export GITHUB_TOKEN=...` & `/etc/profile.d/github.sh` | GitHub personal access token for OSINT queries |
| **Metabigor** | `~/.config/metabigor/config.yaml` | Shodan, Censys |

> [!NOTE]
> During Stage `[7/8]`, the installer automatically runs non-blocking, live verification queries against Subfinder, Chaos, and Shodan to confirm your keys are working properly.

---

## 🛠️ Included Tools & Categorized Reference

### 1. 🔍 Subdomain Enumeration & DNS Recon

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[Subfinder](https://github.com/projectdiscovery/subfinder)** | Go | Fast passive subdomain enumeration utilizing multiple passive APIs. | `subfinder` |
| **[Amass](https://github.com/owasp-amass/amass)** | APT | In-depth attack surface mapping and external asset discovery. | `amass` |
| **[PureDNS](https://github.com/d3mondev/puredns)** | Go | Fast domain resolver & bruteforcer leveraging MassDNS. | `puredns` |
| **[MassDNS](https://github.com/blechschmidt/massdns)** | APT | High-performance DNS stub resolver (millions of queries). | `massdns` |
| **[DNSX](https://github.com/projectdiscovery/dnsx)** | Go | Multi-purpose DNS toolkit with wildcard filtering. | `dnsx` |
| **[ShuffleDNS](https://github.com/projectdiscovery/shuffledns)** | Go | MassDNS wrapper written in Go for subdomain bruteforcing. | `shuffledns` |
| **[AlterX](https://github.com/projectdiscovery/alterx)** | Go | Fast and customizable subdomain wordlist generator via permutations. | `alterx` |
| **[AltDNS](https://github.com/infosec-au/altdns)** | Go | Subdomain alteration and mutation discovery engine. | `altdns` |
| **[Assetfinder](https://github.com/tomnomnom/assetfinder)** | Go | Find domains and subdomains related to a given target. | `assetfinder` |
| **[Sublist3r](https://github.com/aboul3la/Sublist3r)** | APT | Fast OSINT search engine scraper for subdomains. | `sublist3r` |
| **[Subzy](https://github.com/LukaSikic/subzy)** | Go | Multi-threaded subdomain takeover detection scanner. | `subzy` |
| **[Chaos Client](https://github.com/projectdiscovery/chaos-client)** | Go | Client for ProjectDiscovery's internet-wide DNS dataset. | `chaos` |
| **[DNSRecon](https://github.com/darkoperator/dnsrecon)** | APT | Advanced DNS enumeration, zone transfer, and cache snooping. | `dnsrecon` |
| **[Fierce](https://github.com/mschwager/fierce)** | APT | DNS reconnaissance tool for locating non-contiguous IP space. | `fierce` |
| **[sub.sh](https://github.com/cihanmehmet/sub.sh)** | Git (`/opt/tools`) | Recon wrapper script for subdomain enumeration and takeovers. | `/opt/tools/sub.sh` |

---

### 2. 🕷️ Web Crawling, Spidering & JS Analysis

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[Katana](https://github.com/projectdiscovery/katana)** | Go | Next-generation crawling and spidering framework. | `katana` |
| **[GoSpider](https://github.com/jaeles-project/gospider)** | Go | Fast web spider written in Go with JavaScript parsing. | `gospider` |
| **[Hakrawler](https://github.com/hakluke/hakrawler)** | Go | Fast crawler for gathering endpoints, assets, and JS files. | `hakrawler` |
| **[Waybackurls](https://github.com/tomnomnom/waybackurls)** | Go | Fetch URLs from the Wayback Machine for target domains. | `waybackurls` |
| **[GAU (GetAllUrls)](https://github.com/lc/gau)** | Go | Fetch known URLs from AlienVault, Wayback, Common Crawl, URLScan. | `gau` |
| **[Waymore](https://github.com/xnl-h4ck3r/waymore)** | pipx | Archive downloader and URL finder from Wayback, Common Crawl, AlienVault. | `waymore` |
| **[getJS](https://github.com/003random/getJS)** | Go | Extracts JavaScript links and sources from target domains. | `getJS` |
| **[JSLeak](https://github.com/channyein1337/jsleak)** | pipx | Secrets, sensitive endpoints, and API key scanner for JS files. | `jsleak` |
| **[broken-link-checker](https://github.com/stevenvachon/broken-link-checker)** | Git (`/opt/tools`) | High-speed crawler identifying broken links & takeover points. | `/opt/tools/broken-link-checker` |

---

### 3. ⚡ Web Fuzzing & Content Discovery

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[ffuf](https://github.com/ffuf/ffuf)** | APT / Go | Extremely fast web fuzzer written in Go for directories, vhosts, and parameters. | `ffuf` |
| **[Gobuster](https://github.com/OJ/gobuster)** | APT / Go | Multi-threaded directory, DNS subdomain, vhost, and S3 brute-forcer. | `gobuster` |
| **[Feroxbuster](https://github.com/feroxbuster/feroxbuster)** | Go | Fast, simple, recursive content discovery tool written in Rust/Go. | `feroxbuster` |
| **[Dirsearch](https://github.com/maurosoria/dirsearch)** | pipx | Advanced command-line tool designed to brute force web directories. | `dirsearch` |
| **[Wfuzz](https://github.com/xmendez/wfuzz)** | APT | Versatile web application fuzzer and bruteforcer for HTTP parameters. | `wfuzz` |
| **[DIRB](https://dirb.sourceforge.net/)** | APT | Classic dictionary-based web content scanner. | `dirb` |
| **[httpx](https://github.com/projectdiscovery/httpx)** | Go | Fast and multi-purpose HTTP toolkit for probing technologies and endpoints. | `httpx` |
| **[httprobe](https://github.com/tomnomnom/httprobe)** | Go | Takes a list of domains and probes for working HTTP/HTTPS servers. | `httprobe` |

---

### 4. 🎯 Vulnerability Scanning & Parameter Fuzzing

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[Nuclei](https://github.com/projectdiscovery/nuclei)** | Go | Fast and customizable vulnerability scanner driven by community templates. | `nuclei` |
| **[Nuclei Templates](https://github.com/projectdiscovery/nuclei-templates)** | Git (`/opt/tools`) | Official repository of community-curated vulnerability templates. | `/opt/tools/nuclei-templates` |
| **[SQLmap](https://github.com/sqlmapproject/sqlmap)** | APT | Automated SQL injection detection and database takeover engine. | `sqlmap` |
| **[Dalfox](https://github.com/hahwul/dalfox)** | Go | Parameter analysis and high-speed XSS vulnerability scanner. | `dalfox` |
| **[XSStrike](https://github.com/s0md3v/XSStrike)** | pipx | Advanced XSS suite with multi-parser, intelligent payload generation. | `xsstrike` |
| **[Gxss](https://github.com/KathanP19/Gxss)** | Go | Identify reflected URL parameters for Cross-Site Scripting. | `Gxss` |
| **[kxss](https://github.com/Emoe/kxss)** | Go | Fast check for unescaped characters in parameters. | `kxss` |
| **[Arjun](https://github.com/s0md3v/Arjun)** | pipx | HTTP parameter discovery suite (GET, POST, JSON, XML). | `arjun` |
| **[ParamSpider](https://github.com/devanshbatham/ParamSpider)** | Git (`/opt/tools`) + Wrapper | Mines parameters from dark corners of web archives. | `paramspider` |
| **[Commix](https://github.com/commixproject/commix)** | pipx | Automated command injection and exploitation engine. | `commix` |
| **[SSRFmap](https://github.com/swisskyrepo/SSRFmap)** | Git (`/opt/tools`) + Wrapper | Automatic SSRF fuzzer and exploitation tool. | `ssrfmap` |
| **[Tplmap](https://github.com/epinna/tplmap)** | Git (`/opt/tools`) + Wrapper | Server-Side Template Injection (SSTI) scanner and exploitation. | `tplmap` |
| **[Nikto](https://github.com/sullo/nikto)** | APT | Comprehensive web server assessment for misconfigurations and flaws. | `nikto` |
| **[WafW00f](https://github.com/EnableSecurity/wafw00f)** | APT / pipx | Web Application Firewall fingerprinting and identification. | `wafw00f` |
| **[Interactsh Client](https://github.com/projectdiscovery/interactsh)** | Go | Out-of-band (OAST) interaction gathering and validation. | `interactsh-client` |

---

### 5. 🧩 CMS & Framework Auditing

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[WPScan](https://github.com/wpscanteam/wpscan)** | APT | Black-box WordPress vulnerability scanner for themes & plugins. | `wpscan` |
| **[CMSeeK](https://github.com/Tuhinshubhra/CMSeeK)** | Git (`/opt/tools`) | Intelligent CMS detection and auditing for 170+ CMS architectures. | `/opt/tools/CMSeeK/cmseek.py` |
| **[Droopescan](https://github.com/droope/droopescan)** | pipx / Git | Security scanner targeting Drupal, SilverStripe, WordPress, and Joomla. | `droopescan` |
| **[JoomScan](https://github.com/rezasp/joomscan)** | Git (`/opt/tools`) | OWASP Joomla security assessment scanner. | `/opt/tools/joomscan/joomscan.pl` |
| **[aem-hacker](https://github.com/0ang3el/aem-hacker)** | Git (`/opt/tools`) | Reconnaissance and exploit toolkit for Adobe Experience Manager (AEM). | `/opt/tools/aem-hacker` |

---

### 6. ☁️ Cloud, CORS & Secrets Extraction

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[Corsy](https://github.com/s0md3v/Corsy)** | Git (`/opt/tools`) + Wrapper | CORS misconfiguration scanner capable of detecting exploitable headers. | `corsy` |
| **[CORScanner](https://github.com/chenjj/CORScanner)** | Git (`/opt/tools`) | Fast multi-origin CORS misconfiguration auditing engine. | `/opt/tools/CORScanner` |
| **[s3-buckets-finder](https://github.com/gwen001/s3-buckets-finder)** | Git (`/opt/tools`) | Discovers and inspects publicly accessible and misconfigured AWS S3 buckets. | `/opt/tools/s3-buckets-finder` |
| **[CloudUnflare](https://github.com/greycatz/CloudUnflare)** | Git (`/opt/tools`) | Cloudflare reverse-proxy bypass tool to locate origin server IPs. | `/opt/tools/CloudUnflare` |
| **[DumpsterDiver](https://github.com/securing/DumpsterDiver)** | Git (`/opt/tools`) | Analyzes files and directories to uncover hardcoded keys and tokens using entropy. | `/opt/tools/dumpsterdiver` |
| **[Git-Dumper](https://github.com/arthaud/git-dumper)** | pipx | Dumps exposed `.git` directories and recreates repositories. | `git-dumper` |
| **[AWS CLI](https://aws.amazon.com/cli/)** | APT | Official Amazon Web Services command line utility. | `aws` |
| **[Shodan CLI](https://github.com/achillean/shodan-python)** | pipx | Official Shodan command-line search engine client. | `shodan` |
| **[theHarvester](https://github.com/laramies/theHarvester)** | APT | OSINT gatherer for emails, subdomains, employee names, and open ports. | `theHarvester` |
| **[Metabigor](https://github.com/j3ssie/metabigor)** | Go | Intelligence tool performing OSINT (IP/ASN/CIDR/org) without API keys. | `metabigor` |

---

### 7. 🌐 Network & Port Scanning

| Tool | Engine / Source | Description | Command |
| :--- | :--- | :--- | :--- |
| **[Nmap](https://nmap.org/)** | APT | Industry-standard network exploration and port scanner. | `nmap` |
| **[Naabu](https://github.com/projectdiscovery/naabu)** | Go | Fast, reliable port scanner written in Go focusing on stealth and speed. | `naabu` |
| **[Impacket](https://github.com/fortra/impacket)** | APT | Suite of Python networking protocol libraries and audit tools. | `impacket-*` |
| **[Whois](https://packages.debian.org/sid/whois)** | APT | Client for querying RFC 3912 domain registration & WHOIS data. | `whois` |
| **[Dnsutils](https://packages.debian.org/sid/dnsutils)** | APT | Core DNS utilities including `dig`, `host`, and `nslookup`. | `dig`, `host` |

---

### 8. 📚 Dictionaries, Wordlists & Payloads

The installer clones world-class security dictionaries into `/opt/tools/` and creates convenient system symlinks:

| Repository / Wordlist | Path | Highlights |
| :--- | :--- | :--- |
| **[SecLists](https://github.com/danielmiessler/SecLists)** | `/opt/tools/SecLists` & `/usr/share/wordlists/seclists` | Master collection of usernames, passwords, discovery directories, fuzz payloads, and webshells. |
| **[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)** | `/opt/tools/PayloadsAllTheThings` | Complete attack guides and payloads for SQLi, XSS, SSRF, SSTI, XXE, CSRF, and LFI. |
| **[IntruderPayloads](https://github.com/1N3/IntruderPayloads)** | `/opt/tools/IntruderPayloads` | Attack payloads, lists, and patterns designed for fuzzers and Burp Intruder. |
| **[FuzzDB](https://github.com/fuzzdb-project/fuzzdb)** | `/opt/tools/fuzzdb` | Comprehensive pattern library for fault injection, predictable resource hunting, and regexes. |
| **[Commonspeak2](https://github.com/assetnote/commonspeak2-wordlists)** | `/opt/tools/commonspeak2-wordlists` | Contextual, high-confidence wordlists generated by Assetnote via internet-scale datasets. |
| **MassDNS Public Resolvers** | `/etc/massdns/resolvers.txt` | Curated, active public DNS resolvers automatically validated and downloaded. |

---

## 🗂️ Filesystem Hierarchy

```
/
├── root/
│   ├── go/
│   │   ├── bin/                 <-- All Go compiled binaries (httpx, nuclei, subfinder, ...)
│   │   └── pkg/
│   ├── .config/
│   │   ├── subfinder/           <-- Subfinder provider-config.yaml (auto-generated)
│   │   ├── amass/               <-- Amass config.ini (auto-generated)
│   │   └── metabigor/           <-- Metabigor config.yaml (auto-generated)
│   ├── .pdcp/                   <-- PDCP / Nuclei credentials
│   └── .shodan/                 <-- Shodan CLI credentials
│
├── opt/
│   ├── tools/                   <-- Cloned GitHub repos & Wordlists
│   │   ├── SecLists/
│   │   ├── PayloadsAllTheThings/
│   │   ├── CMSeeK/
│   │   ├── corsy/
│   │   ├── ParamSpider/
│   │   ├── SSRFmap/
│   │   └── ...
│   └── SecLists                 <-- Symlink to /opt/tools/SecLists
│
├── usr/
│   ├── local/
│   │   ├── bin/                 <-- Python CLI wrappers (corsy, paramspider, ssrfmap, tplmap)
│   │   └── go/                  <-- Clean Go toolchain installation (≥ 1.21)
│   └── share/
│       └── wordlists/
│           └── seclists         <-- Symlink to /opt/tools/SecLists
│
├── etc/
│   ├── massdns/
│   │   └── resolvers.txt        <-- Fresh public DNS resolvers
│   └── profile.d/
│       ├── bugbounty.sh         <-- Global PATH: Go, local bin, MassDNS environment
│       ├── golang.sh            <-- Go GOPATH and GOBIN definitions
│       ├── chaos.sh             <-- CHAOS_KEY export (if provided)
│       ├── pdcp.sh              <-- PDCP_API_KEY export (if provided)
│       └── github.sh            <-- GITHUB_TOKEN export (if provided)
│
└── var/
    └── log/
        └── tool_install.log     <-- Complete execution and diagnostic log
```

---

## 🔧 Troubleshooting & Auto-Fixes

`install_tools.sh` automatically performs checks and repairs, but if you run into any manual issues:

### 1. `Command not found` after installation
The script appends paths to `/etc/profile.d/bugbounty.sh` and `/root/.bashrc`. Simply reload your environment:
```bash
source ~/.bashrc
# Or manually export for your current session:
export PATH=$PATH:/usr/local/go/bin:/root/go/bin:/root/.local/bin:/usr/local/bin
```

### 2. Missing Critical Tools
At the end of the installation, a **Critical Tool Check** verifies that essential tools are present:
```
Critical tool check:
  gobuster           ✓  /usr/bin/gobuster
  ffuf               ✓  /usr/bin/ffuf
  nikto              ✓  /usr/bin/nikto
  sqlmap             ✓  /usr/bin/sqlmap
  nmap               ✓  /usr/bin/nmap
  httpx              ✓  /root/go/bin/httpx
  katana             ✓  /root/go/bin/katana
  nuclei             ✓  /root/go/bin/nuclei
  subfinder          ✓  /root/go/bin/subfinder
  naabu              ✓  /root/go/bin/naabu
```
If any tool shows as missing, follow the auto-fix suggestion displayed in the summary, or re-run the installer. The installer is idempotent and will quickly resume from where it left off.

### 3. Reviewing the Installation Logs
Every command output, warning, and auto-repair action is preserved in the persistent log file:
```bash
tail -n 100 /var/log/tool_install.log
# Or grep for failures:
grep -E "✗|⚠" /var/log/tool_install.log
```

---

## ⚖️ Legal Disclaimer

> **IMPORTANT NOTICE:**  
> The scripts, tools, and wordlists referenced in this repository are intended for **authorized penetration testing, bug bounty hunting, and educational security research only**.  
> Testing targets without prior mutual written consent is illegal. It is the end user's responsibility to obey all applicable local, state, and federal laws. The developers and contributors assume no liability and are not responsible for any misuse, damage, or legal consequences caused by this toolkit.

---

## 📄 License

Distributed under the [MIT License](https://opensource.org/licenses/MIT). Individual tools, libraries, and wordlists installed by this script belong to their respective authors and retain their individual open-source licenses.

---

<p align="center">
  <b>Happy Hunting! 🎯</b>
</p>
