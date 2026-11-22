# Cohort — Hack The Box Write-Up

- **Target:** `10.129.215.166` (`cohort.htb`)
- **Focus:** Web enumeration, initial access, privilege escalation

## 1. Enumeration & Reconnaissance
Ran a service and default script scan (`nmap -sCV 10.129.215.166 -oA /home/kali/cohort/cohort`):
![Enumeration and Reconnaissance](assets/01-Enumeration-Reconnaissance.png)
- **Open Ports:** `22/tcp` (OpenSSH 9.6p1), `80/tcp` (Nginx 1.24.0 -> redirects to `https://cohort.htb/`), and `443/tcp` (Nginx 1.24.0, TLS cert for `cohort.htb` and `*.cohort.htb`).
- **Next Step:** Add `cohort.htb` to `/etc/hosts`, check the web app, and watch for subdomains (`*.cohort.htb`).

## 2. Web Enumeration & SSRF Surface
Exploring `https://cohort.htb` surfaced two primary leads:
![Client Insights URL Validator](assets/02-client-insights.png)
![Cohort Analytics Team](assets/03-team-members.png)
- **Potential Usernames:** Identified two team members on the main site — **Mara Quinteros** and **Devin Oyelaran** — giving us candidate usernames for later authentication or vhost enumeration[cite: 3].
- **SSRF Entry Point:** Under **Client Insights** ("Register a report source URL"), the app accepts a remote URL, fetches it, validates the response, and returns a short preview[cite: 2].
- **Filter Constraint & Next Step:** The page notes that internal and loopback addresses are blocked[cite: 2]. Next, enumerate subdomains (`*.cohort.htb`) and test how the URL validator handles redirects, alternate IP representations, or internal hostnames.
