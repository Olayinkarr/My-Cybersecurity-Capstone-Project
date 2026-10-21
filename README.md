# From Vulnerability to Compliance: Securing Legacy Systems at Apex Healthcare Solutions

A cybersecurity capstone project that turns technical findings from two intentionally vulnerable machines, **Kioptrix** and **Metasploitable 2**, into a business-focused security report: what is vulnerable, why it matters, what risk it creates, and which controls and policies would fix it.

**Author:** Joseph Olayinka Olaoluwa (Group 18)

## Scenario

Apex Healthcare Solutions is a fictional medium-sized healthcare technology company that provides digital patient record services to clinics and hospitals. Two legacy systems were found running in its environment, and management wanted a report that explains the risks in business terms rather than a focus on exploitation.

## Objectives

- Identify discovered vulnerabilities on both systems
- Explain the security impact of each one
- Assess the business risk
- Recommend technical security controls
- Recommend organizational (GRC) policies
- Present everything in a professional report with an executive summary

## Findings at a Glance

### Kioptrix

| Vulnerability | Affected Service | Risk |
|---|---|---|
| Outdated Apache HTTP Server (1.3.20) | Apache, ports 80/443 | High |
| Outdated OpenSSH (2.9p2) | OpenSSH, port 22 | High |
| Exposed Samba service | Samba, port 139 | High |

### Metasploitable 2

| Vulnerability | Affected Service | Risk |
|---|---|---|
| UnrealIRCd backdoor | IRC, port 6667 | High |
| VSFTPD 2.3.4 backdoor | FTP, port 21 | High |
| Exposed Telnet service | Telnet, port 23 | High |

Each vulnerability is rated on likelihood and impact, with an explanation, in the full report.

## Recommended Controls

| Vulnerability | Recommended Control |
|---|---|
| Outdated Apache | Patch management process; update to a supported version |
| Outdated OpenSSH | Update OpenSSH and disable legacy protocols |
| Exposed Samba | Restrict access, disable unnecessary shares, keep updated |
| UnrealIRCd backdoor | Remove the backdoored version and install a secure release |
| VSFTPD 2.3.4 backdoor | Upgrade or replace the FTP service |
| Exposed Telnet | Disable Telnet and use SSH instead |

## GRC Policy Recommendations

1. **Access Control Policy**: limits system and patient data access to authorized users
2. **Patch Management Policy**: keeps operating systems and services updated
3. **Vulnerability Management Policy**: regular identification, prioritization and remediation of weaknesses
4. **Acceptable Use Policy**: defines responsible use of company systems and networks

## Report Contents

1. Background and project objective
2. Task 1: Vulnerability identification (3 per system)
3. Task 2: Risk assessment (likelihood, impact, rating)
4. Task 3: Security control recommendations
5. Task 4: GRC policy recommendations
6. Task 5: Executive summary

## Read the Full Report

[My Capstone Project Result 1.0.pdf](./My%20Capstone%20Project%20Result%201.0.pdf)

## Disclaimer

This project was carried out for educational purposes only, using intentionally vulnerable machines in a controlled lab environment. No real organization or patient data was involved.
