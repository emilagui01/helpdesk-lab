# Help Desk Ticketing System Lab

*Home Lab 2 of 3 · **Complete** · [view all labs](https://emilagui01.github.io/#labs)*

A self-hosted osTicket help desk on Ubuntu Server 24.04, connected to the Active Directory domain from [Lab 1](https://emilagui01.github.io/ad-homelab/) and run like a small IT support team: tickets route themselves by help topic, Tier 1 escalates to Tier 2, and recurring fixes become knowledge base articles and canned responses.

**Live writeup:** https://emilagui01.github.io/helpdesk-lab/
**Portfolio:** https://emilagui01.github.io/#labs

## Environment

| Component | Details |
|---|---|
| Server | HELPDESK1, Ubuntu Server 24.04.5 LTS (Hyper-V), 192.168.50.20 static |
| Stack | Apache 2.4, MariaDB 10.11, PHP 8.3.6, osTicket v1.18.4 |
| Directory | emillab.local (DC01, 192.168.50.10), LDAP via `svc-osticket` service account |
| Client | CLIENT01, Windows 11, domain-joined |
| Management | SSH from the Hyper-V host |

## What's built

- [x] Ubuntu Server VM with static IP, AD DNS, expanded LVM volume, SSH management
- [x] LAMP stack and osTicket, with a least-privilege database account and a locked-down install
- [x] Four SLA tiers (P1 4h, P2 8h, P3 24h, P4 72h)
- [x] Tier 1 (IT Help Desk) and Tier 2 (Systems Administration) departments
- [x] Seven help topics that set department, priority and SLA automatically
- [x] Tier 1 and Tier 2 agents with role-based access
- [x] LDAP / Active Directory plugin with a read-only service account, verified with `ldapsearch`
- [x] Five tickets worked end to end, including a Tier 1 to Tier 2 escalation
- [x] Public knowledge base articles, an internal Tier 1 procedure, and four canned responses
- [x] SLA tracking: a P1 outage left past its 4-hour deadline lands in the Overdue queue

## Troubleshooting highlights

| Problem | Root cause | Fix |
|---|---|---|
| Installer couldn't resolve `archive.ubuntu.com` | DC01, the only DNS server, was off | Start DC01 first; set a boot order for the lab |
| Package lists said `resolute` | Ubuntu 26.04 ISO downloaded by mistake | Rebuilt on 24.04 LTS (PHP 8.3) |
| VM booted to PXE | Linux guest boot settings | UEFI CA Secure Boot template, ISO attached, DVD first |
| Hyper-V "VMMS not available" | Management service stopped by accident | `Start-Service vmms` after StopPending cleared |
| Root volume 10.7 GB of 25 GB | Default LVM layout | `lvextend -r -l +100%FREE` |
| MariaDB ERROR 1396, `!': event not found` | User already created with placeholder password; bash `!` expansion | `ALTER USER` inside the MariaDB shell |
| Portal sign-in refused | Auth modes off, account stuck Pending Activation, wrong password | Direct `ldapsearch` bind showed `data 52e`; reset password, enabled auth, re-registered user |
| KB commands missing usernames | Editor stripped `<user>` as an HTML tag | Used `USERNAME` placeholder |

## Known issues

- Portal users are local osTicket accounts, not AD-bound (next step: register against the AD backend)
- LDAP on 389 without TLS (production: LDAPS on 636)
- No mail server, so email alerts and activation links are disabled
- HTTP only (production: TLS on Apache)

## Repo structure

```
index.html     Lab writeup (GitHub Pages)
README.md      This file
b-*.jpg        Phase 1: build
c-*.jpg        Phase 2: configure
o-*.jpg        Phase 3: operate
t-*.jpg        Troubleshooting evidence
overdue.jpg    SLA overdue screenshot
```

---
Emil Aguirre Herrera · CompTIA Network+ · Microsoft Azure Fundamentals (AZ-900)
