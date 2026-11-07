# Hidden Deep Into My Heart — TryHackMe (Easy)

**Date:** 2026-10-31 · **Platform:** TryHackMe · **Category:** Web enumeration / reconnaissance

**Skills practiced:** Directory enumeration with Gobuster, `robots.txt` analysis, page source review, hidden resource discovery, authentication testing

## 1. Summary
The target was a web application whose main page showed no useful content or links. Directory enumeration revealed a `robots.txt` file that exposed a hidden directory and a comment that looked like a credential. Following that lead, and reviewing the page source, led to a hidden administrator login. After gaining access with valid credentials, I obtained the flag.

## 2. Reconnaissance
The main page had no visible links or functionality, so I moved on to content discovery.

```bash
gobuster dir -u http://<TARGET_IP>:5000 -w /usr/share/wordlists/dirb/common.txt
```

Gobuster identified `/robots.txt`.

## 3. Enumeration
**robots.txt analysis.** The file disallowed a hidden directory (`/cupids_secret_vault/`) and contained a comment that looked like a possible password or hint. *(The comment's value is intentionally omitted.)*

**Hidden directory.** Visiting `/cupids_secret_vault/` returned a message saying there was more to discover, which suggested further hidden content.

**Source code review.** Inspecting the HTML source of that page revealed a reference to another resource: `/cupids_secret_vault/administrator`.

**Administrator panel.** That path contained a login form for administrators.

*(Add screenshots here with sensitive values hidden.)*

## 4. Exploitation (initial access)
[Complete with your own details, because the draft does not say how this step was done: how did you identify the valid username, and how did you decide which password to try (for example, the clue found in robots.txt)? How many attempts did it take?]

After authenticating, the administrator area contained the flag. *(Flag intentionally omitted.)*

## 5. Findings and severity
| Finding | Severity (CVSS) | Impact |
|---|---|---|
| Sensitive path disclosed in `robots.txt` | Low | Reveals the location of hidden resources to anyone |
| Possible credential left in a `robots.txt` comment | High | Credentials exposed in a public file allow unauthorized access |
| Admin panel referenced in client-visible HTML | Low | Makes a sensitive interface easy to find |
| Reliance on obscurity instead of access control | Medium | Hidden paths are not a security control |

## 6. Remediation recommendations
- Do not use `robots.txt` to hide sensitive content. It is public and tells attackers where to look.
- Never leave credentials, hints or secrets in comments or public files.
- Protect administrative areas with real access control: server-side authentication, strong unique passwords, multi-factor authentication, and network restrictions (for example, VPN or IP allow-listing).
- Remove references to internal paths from client-side code.
- Add rate limiting and account lockout to login forms.

## 7. Lessons learned
- `robots.txt` is one of the first files to check during web reconnaissance.
- Comments and page source can contain useful information.
- Systematic enumeration often turns a small finding into full access.
- [Add one lesson of your own, for example what slowed you down.]

## Tools used
Web browser, Gobuster, browser developer tools (F12).
