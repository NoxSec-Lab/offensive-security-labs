# Cohort — Hack The Box Write-Up

- **Target:** `10.129.215.166` (`cohort.htb`)
- **Focus:** Web enumeration, initial access, privilege escalation

## 1. Enumeration & Reconnaissance
Ran a service and default script scan (`nmap -sCV 10.129.215.166 -oA /home/kali/cohort/cohort`):
![Enumeration and Reconnaissance](assets/01-Enumeration-Reconnaissance.png)
- **Open Ports:** `22/tcp` (OpenSSH 9.6p1), `80/tcp` (Nginx 1.24.0 -> redirects to `https://cohort.htb/`), and `443/tcp` (Nginx 1.24.0, TLS cert for `cohort.htb` and `*.cohort.htb`).
- **Next Step:** Add `cohort.htb` to `/etc/hosts`, check the web app, and watch for subdomains (`*.cohort.htb`).

## 2. Web Enumeration & SSRF Discovery
Exploring `https://cohort.htb` surfaced two primary leads:
![Client Insights URL Validator](assets/02-client-insights.png)

![Cohort Analytics Team](assets/03-team-members.png)
- **Potential Usernames:** Identified two team members on the main site — **Mara Quinteros** and **Devin Oyelaran** — giving us candidate usernames for later authentication or vhost enumeration.
- **Suspected SSRF Surface:** Under **Client Insights** ("Register a report source URL"), the form takes a URL and states it fetches and previews the feed while blocking internal and loopback addresses.

Testing `http://10.10.16.229/test.csv` against a local Python HTTP server (`python3 -m http.server 80`) confirmed outbound server-side fetching:
![SSRF Callback Verification](assets/04-ssrf-callback.png)
- **SSRF Confirmed:** The target requested `/test.csv` and returned `Reachable. HTTP 200 (text/csv)` with the file preview.
- **Filter Bypass (`0.0.0.0`):** Submitting `http://0.0.0.0/status` bypassed the internal/loopback check and returned JSON from `cohort-edge` (`nginx`).

![Status Endpoint Leak](assets/05-status-bypass.png)
- **Leaked Upstreams:** The response exposed internal targets including `cohort.htb` (`/var/www/cohort`), `/api/` (`127.0.0.1:5000`), and `nb-1be3782a8afd3ad5.cohort.htb` (`127.0.0.1:8888`, an internal analyst workspace).[cite: 5]
- **Next Step:** Check `nb-1be3782a8afd3ad5.cohort.htb` (and test internal ports `5000` / `8888` or `/api/` endpoints) to see what is exposed and how to pivot.[cite: 5]
