# Identity and Access Management — Hands-on Labs

A collection of step-by-step Linux security labs for **CYBE-6217: Identity and Access Management**. Each lab walks through a real administrative task—creating users, hardening authentication, auditing permissions, and configuring centralized identity services—with commands, expected results, and screenshot checkpoints suitable for lab reports.

These materials complement the course case studies (AetherScale, MedData, SwiftPay, EduCloud) and the lab report generator in the parent repository.

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [How to Use These Labs](#how-to-use-these-labs)
- [Lab Index](#lab-index)
  - [Lab 1 — Assigning Limited sudo Privileges](#lab-1--assigning-limited-sudo-privileges)
  - [Lab 2 — Disabling the sudo Timer](#lab-2--disabling-the-sudo-timer)
  - [Lab 3 — Encrypted Home Directory with adduser](#lab-3--encrypted-home-directory-with-adduser)
  - [Lab 4 — Password Complexity Criteria](#lab-4--password-complexity-criteria)
  - [Lab 5 — Account and Password Expiry](#lab-5--account-and-password-expiry)
  - [Lab 6 — Detecting Compromised Passwords](#lab-6--detecting-compromised-passwords)
  - [Lab 7 — Configuring pam_tally2](#lab-7--configuring-pam_tally2)
  - [Lab 8 — FreeIPA and ADSys on AlmaLinux](#lab-8--freeipa-and-adsys-on-almalinux)
  - [Lab 9 — Searching for SUID and SGID Files](#lab-9--searching-for-suid-and-sgid-files)
  - [Lab 10 — Extended File Attributes](#lab-10--extended-file-attributes)
  - [Lab 11 — Shared Group Directory](#lab-11--shared-group-directory)
- [Suggested Learning Path](#suggested-learning-path)
- [Operating System Summary](#operating-system-summary)
- [Related Resources](#related-resources)
- [License](#license)

---

## Overview

The labs are organized around core IAM themes:

| Theme | Labs | Focus |
|-------|------|-------|
| **Privilege management** | 1, 2 | `sudo`, `visudo`, least privilege, authentication timeout |
| **User & credential security** | 3, 4, 5, 6 | Encrypted homes, password quality, aging/expiry, breach detection |
| **Authentication hardening** | 7 | PAM, account lockout, brute-force mitigation |
| **Centralized identity** | 8 | FreeIPA, Kerberos, LDAP, ADSys |
| **File permissions & auditing** | 9, 10, 11 | SUID/SGID, extended attributes, groups, ACLs, sticky bit |

Each lab file (`01.md`–`11.md`) follows a consistent structure:

1. **Objective** — what you will learn and why it matters
2. **Environment** — target OS and tools
3. **Steps** — numbered procedures with commands
4. **Expected Result** — what success looks like (often with screenshot placeholders)
5. **Results** — summary of outcomes
6. **Conclusion** — key takeaways

---

## Prerequisites

- Root or `sudo` access on a Linux VM (virtual or physical)
- Basic command-line familiarity (`cd`, `ls`, text editors, package managers)
- Network access where noted (Lab 6 uses the Have I Been Pwned API)
- For Lab 8: a dedicated AlmaLinux 9 host with a resolvable hostname and sufficient RAM for FreeIPA (~2 GB minimum recommended)

**Recommended setup:** Use separate VMs or snapshots per lab when possible, especially for Labs 7 and 8, which modify PAM and install identity services.

---

## How to Use These Labs

1. Open the lab markdown file for the exercise you are working on (e.g. [`01.md`](01.md)).
2. Provision the VM listed under **Environment** (see [Operating System Summary](#operating-system-summary)).
3. Execute each step in order; capture screenshots at the marked checkpoints for your report.
4. Compare your terminal output with the **Expected Result** sections before moving on.
5. Complete the **Results** and **Conclusion** sections in your own words for submission.

To generate a formatted DOCX/PDF lab report from the parent project case studies, see the [main README](../README.md) and run `build_report.py`.

---

## Lab Index

### Lab 1 — Assigning Limited sudo Privileges

**File:** [`01.md`](01.md) · **OS:** AlmaLinux 9 · **Tools:** `useradd`, `passwd`, `visudo`, `sudo`

Create three users (`lionel`, `katelyn`, `maggie`) and grant differentiated sudo access via the `sudoers` file: full admin, a single command (`systemctl status sshd`), and a command alias (`STORAGE`). Verify each user's effective privileges by switching accounts and running allowed and denied commands.

**Key concepts:** least privilege, `sudoers` syntax, command aliases, privilege verification.

---

### Lab 2 — Disabling the sudo Timer

**File:** [`02.md`](02.md) · **OS:** AlmaLinux 9 · **Tools:** `sudo`, `visudo`, `sudo -k`

Explore the default sudo authentication cache (`timestamp_timeout`), invalidate it with `sudo -k`, then set `Defaults timestamp_timeout=0` globally. Restrict the zero-timeout rule to a single user (`lionel`) and compare behavior across accounts.

**Key concepts:** sudo timestamp cache, global vs. per-user `Defaults`, `sudo -k`.

---

### Lab 3 — Encrypted Home Directory with adduser

**File:** [`03.md`](03.md) · **OS:** Ubuntu 22.04 · **Tools:** `ecryptfs-utils`, `adduser`, `ecryptfs-unwrap-passphrase`

Install eCryptfs utilities, create user `cleopatra` with `--encrypt-home`, log in, and confirm that files are encrypted at rest on disk while transparently decrypted in the active session.

**Key concepts:** eCryptfs, encrypted home directories, passphrase recovery, data-at-rest protection.

---

### Lab 4 — Password Complexity Criteria

**File:** [`04.md`](04.md) · **OS:** Ubuntu 22.04 · **Tools:** `libpam-pwquality`, `/etc/security/pwquality.conf`

Install the pwquality PAM module, enforce a minimum password length (19 characters), then add complexity rules (character classes, dictionary checks). Test enforcement by attempting weak passwords on a test user (`goldie`).

**Key concepts:** PAM, password policy, `minlen`, `dcredit`/`ucredit`/`lcredit`/`ocredit`, `dictcheck`.

---

### Lab 5 — Account and Password Expiry

**File:** [`05.md`](05.md) · **OS:** Ubuntu 22.04 · **Tools:** `useradd`, `usermod`, `passwd`, `chage`

Create user `samson` with an account expiration date, force a password change on first login, and configure password aging (maximum/minimum age, warning period, inactivity lockout). Verify policies with `chage -l`.

**Key concepts:** account expiration, password aging, `-e`/`--expiredate`, `chage`, inactive account management.

---

### Lab 6 — Detecting Compromised Passwords

**File:** [`06.md`](06.md) · **OS:** Ubuntu 22.04 · **Tools:** `curl`, Bash, [HIBP Pwned Passwords API](https://haveibeenpwned.com/Passwords)

Query the Have I Been Pwned API using k-anonymity (SHA-1 prefix only), build a shell script that checks whether a password appears in known breaches, and test with known-compromised and safe passwords.

**Key concepts:** credential stuffing, k-anonymity, breach-aware password policy, API integration.

---

### Lab 7 — Configuring pam_tally2

**File:** [`07.md`](07.md) · **OS:** CentOS 7 · **Tools:** PAM, `pam_tally2`, `/etc/pam.d/`

Configure `pam_tally2` on console and system-auth stacks to lock accounts after repeated failed logins. Trigger lockout, inspect failure counters, reset a locked account, and adjust the failure threshold.

**Key concepts:** brute-force mitigation, PAM stack order, `deny=`/`unlock_time=`, `pam_tally2 --user`.

> **Note:** `pam_tally2` is deprecated on newer distributions in favor of `pam_faillock`. This lab targets CentOS 7 as specified in the course materials.

---

### Lab 8 — FreeIPA and ADSys on AlmaLinux

**File:** [`08.md`](08.md) · **OS:** AlmaLinux 9 · **Tools:** FreeIPA, ADSys, Kerberos, LDAP

**Part A:** Update the system, set hostname and `/etc/hosts`, install and configure a FreeIPA server, verify the installation, and create a test user.

**Part B:** Install ADSys, enable and verify the service for centralized policy integration.

**Key concepts:** centralized IAM, DNS/hostname requirements, `ipa-server-install`, Kerberos realm, ADSys policy agent.

---

### Lab 9 — Searching for SUID and SGID Files

**File:** [`09.md`](09.md) · **OS:** Ubuntu 22.04 · **Tools:** `find`, `chmod`, `diff`, `ls`

Baseline SUID/SGID files with `find`, create a test file under another user and set the SUID bit, re-run the search, and compare results. Verify that the SUID permission executes the program with the file owner's privileges.

**Key concepts:** SUID, SGID, privilege escalation risk, permission auditing, `find -perm`.

---

### Lab 10 — Extended File Attributes

**File:** [`10.md`](10.md) · **OS:** Ubuntu 22.04 · **Tools:** `lsattr`, `chattr`, `rm`, `mv`, `ln`

Create a test file, apply the append-only (`+a`) attribute and observe that overwrites and deletes fail while appends succeed. Switch to immutable (`+i`), repeat tests, and explore effects on rename, hard links, and symbolic links.

**Key concepts:** `chattr`/`lsattr`, append-only, immutable files, tamper resistance.

---

### Lab 11 — Shared Group Directory

**File:** [`11.md`](11.md) · **OS:** Ubuntu 22.04 · **Tools:** `groupadd`, `useradd`, `chmod`, `chown`, `setfacl`, `getfacl`

Create the `sales` group and users (`mimi`, `mrgray`, `mommy`), build a shared directory with SGID and sticky bit, test collaborative file creation, and apply ACLs so specific users gain or lose access beyond standard Unix permissions.

**Key concepts:** collaborative directories, SGID (`g+s`), sticky bit (`+t`), POSIX ACLs, `setfacl`/`getfacl`.

---

## Suggested Learning Path

Labs are numbered in a logical progression. Follow them in order when possible:

```
Labs 1–2  →  sudo & privilege control
Labs 3–6  →  user accounts, passwords & credentials
Lab 7     →  authentication lockout (PAM)
Lab 8     →  centralized identity (FreeIPA)
Labs 9–11 →  file permissions, auditing & shared access
```

Within the password/credential block (3–6), the recommended sequence is: encryption → complexity → expiry → breach checking.

---

## Operating System Summary

| Lab | Distribution | Version |
|-----|--------------|---------|
| 1, 2, 8 | AlmaLinux | 9 |
| 3, 4, 5, 6, 9, 10, 11 | Ubuntu | 22.04 |
| 7 | CentOS | 7 |

Use snapshots or separate VMs when switching between distributions.

---

## Related Resources

- [Parent project README](../README.md) — lab report generator (`build_report.py`, `convert_to_pdf.py`)
- Course slides and additional materials: `2026_Identity and Access Management/` (parent directory)
- Case study scenarios: `case studies Tchakounte (2).pdf`

---

## License

This lab collection is released under the [MIT License](LICENSE). Copyright (c) 2026 Yunweneric.
