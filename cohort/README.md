# Cohort — Hack The Box Write-Up

- **Target:** `10.129.215.166` (`cohort.htb`)[cite: 1]
- **Focus:** Web enumeration, SSRF / data feed testing, initial access, privilege escalation

## 1. Enumeration & Reconnaissance
Ran a service and default script scan (`nmap -sCV 10.129.215.166 -oA /home/kali/cohort/cohort`)[cite: 1]:
- **Open Ports:** `22/tcp` (OpenSSH 9.6p1), `80/tcp` (Nginx 1.24.0 -> redirects to `https://cohort.htb/`), and `443/tcp` (Nginx 1.24.0, TLS cert for `cohort.htb` and `*.cohort.htb`)[cite: 1].
- **Next Step:** Add `cohort.htb` to `/etc/hosts`, check the web app, and watch for subdomains (`*.cohort.htb`)[cite: 1].

![Enumeration and Reconnaissance](assets/01-Enumeration-Reconnaissance.png)
