# TrustIssues
Intentionally vulnerable college-club web portal (Flask) paired with a fully patched version — 15 reproduced vulnerabilities (SQLi, IDOR, broken auth, XSS, CSRF, path traversal, AI prompt injection &amp; more) with PoCs, root-cause writeups, and verified fixes. Built for CC-VIT Cybersecurity Club recruitment.

# Trust Issues


A deliberately vulnerable club portal, paired one-to-one with a patched
version, built to show that *"you have to log in"* is not the same thing
as *"this application is secure."*

> ⚠ **Authorized local testing only.** Everything here runs on
> `localhost` / an isolated Docker network. Do not deploy, expose, or point
> any of these tools at infrastructure you don't own.

---

## What's in here


Vulnerable and secured code are two separate, independently runnable Flask
apps (not a branch toggle), so they can be run side by side and diffed
directly. Every vulnerable route is tagged `# VULN-N` in `vulnerable/app.py`;
every fix is tagged `# FIX-N` in `secure/app.py` at the matching location.

## Quick start (Docker — recommended)

```bash
docker compose up --build
```

- Landing page: **http://localhost:8080**
- Vulnerable build: **http://localhost:5000**
- Secured build: **http://localhost:5001**

## Quick start (without Docker)

```bash
# vulnerable build
cd vulnerable
pip install -r requirements.txt
python3 app.py        # seeds db/vulnerable.db on first run, serves :5000

# secured build (separate terminal)
cd secure
pip install -r requirements.txt
python3 app.py        # seeds db/app.db on first run, serves :5001

# landing page (optional, separate terminal)
cd landing
python3 -m http.server 8080
```

## Demo accounts (seeded automatically, identical on both builds)

| Role | Username | Password |
|---|---|---|
| admin | `admin1` | `admin123` |
| admin | `admin2` | `admin456` |
| admin | `root` | `toor` |
| member | `alice` | `alice123` |
| member | `bob` | `bob123` |
| member | `carol` | `carol123` |
| member | `dave` | `dave123` |
| member | `erin` | `erin123` |
| member | `frank` | `frank123` |
| member | `grace` | `grace123` |

(3 admins, 7 members, as specified in the task brief.)

## Vulnerabilities covered

1. SQL Injection
2. Brute-Force Attack
3. Insecure Session Management
4. Insecure Direct Object Reference (IDOR)
5. Broken Function-Level Authorization
6. Information Disclosure via robots.txt
7. Unrestricted File Upload
8. Stored Cross-Site Scripting (XSS)
9. Cross-Site Request Forgery (CSRF)
10. Insecure Direct File Access / Path Traversal
11. Sensitive Information Exposure
12. Direct Prompt Injection (AI chatbot)
13. AI Sensitive Information Disclosure (AI chatbot)
14. Indirect Prompt Injection (AI chatbot)
15. AI Hallucination / Unverified Output (AI chatbot)

Full root cause, impact, and fix for each is in `docs/vulnerabilities.md`.

## Running the proof-of-concepts

```bash
cd poc
bash 02_bruteforce.sh                  # run against vulnerable (port 5000)
bash 02_bruteforce_secure_check.sh     # run against secured (port 5001), shows lockout
# open 09_csrf_attack.html directly in a browser while logged in as an
# admin on the VULNERABLE build in the same browser session
```

All other PoCs (SQLi, IDOR, broken auth, robots.txt, upload, XSS, path
traversal, info exposure, and all 4 chatbot vulnerabilities) are plain
`curl` one-liners — the exact commands and real output from running them
are documented step-by-step in **`docs/poc-walkthrough.md`**, organized
vulnerable-vs-secured so every fix can be seen failing the identical
attack it used to allow.

## Reading order for reviewers

1. `docs/security-assessment-report.md` — the short summary
2. `docs/vulnerabilities.md` — the technical detail, root cause, impact, fix
3. `docs/poc-walkthrough.md` — the receipts: real commands, real output
4. `docs/assumptions-and-limitations.md` — what this build does and doesn't claim
5. `vulnerable/app.py` vs `secure/app.py` — the actual code diff, `VULN-N`/`FIX-N` tags

## Tech stack

- **Backend:** Python 3 / Flask (raw `sqlite3`, no ORM — kept deliberately
  simple and explicit so the SQL injection and its fix are visible in plain
  text)
- **Database:** SQLite
- **Containerization:** Docker + docker-compose, one container per
  component (landing, vulnerable, secure)
- **Frontend:** vanilla HTML/CSS/JS — scroll-scrubbed canvas hero sequence
  and a "digital rain" background on the landing page

## Chatbot note

The AI chatbot is in scope specifically to demonstrate prompt-injection-style
vulnerabilities, TryHackMe-lab style. It is intentionally **not** backed by
a real LLM API (no key required, no external calls) — it's a small
rule-based responder built to reproduce the *architectural* failure modes
(treating user/document text as instructions, leaking data with no
authorization check, inventing unverified answers) cheaply and
reproducibly. See `docs/assumptions-and-limitations.md` for exactly what
this does and doesn't prove about real LLM integrations.

## Scope & disclaimer

All testing shown in this repository was performed exclusively against
the author's own local instances of these applications. No third-party
services, campus infrastructure, or systems outside the author's control
were scanned or exploited at any point.
