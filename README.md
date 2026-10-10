# TechBlog CMS - A Zero-Cost Cyber Fortress (Architecture Showcase)

**Language / 语言:** [English](README.md) | [简体中文](README.zh-CN.md)

> **IMPORTANT NOTICE**: This is a personal, private project (Proprietary) and is **NOT open source**.
> This repository serves purely as an **architecture design whitepaper**, demonstrating technical philosophies. It does NOT contain the complete source code, deployment scripts, or sensitive configurations.
> Unauthorized copying, modification, distribution, or commercial use is strictly prohibited.

> **Open-Source Core (MIT)**: The independently runnable blog engine extracted from this complete version is open-sourced separately:
> **https://github.com/wng409/myblog-core**
> If you want to actually run it, see the core repo. This repository only showcases the architecture of the complete version.
> Note: `myblog-core` is released under the MIT License. Its use, modification, and distribution are governed by that license, not by this repository's Proprietary notice.

![Node.js](https://img.shields.io/badge/Node.js-v22_LTS-339933?logo=node.js&logoColor=white)
![Express](https://img.shields.io/badge/Express-4.x-000000?logo=express&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-3-003B57?logo=sqlite&logoColor=white)
![License](https://img.shields.io/badge/license-Proprietary-red.svg)
![Architecture](https://img.shields.io/badge/Architecture-Home%20Cloud-blueviolet)

---

## Core Philosophy: Extreme Frugality & Digital Sovereignty

In an era where deploying a blog often requires "going to the cloud," buying "DDoS protection," or paying for CDNs, this system proves that **with elegant architecture, you can build an enterprise-grade personal digital fortress for $0.**

The entire system runs on a lightweight server dubbed "Home Cloud" (4C4G80GB, 127.0.0.1), penetrated through a Cloudflare Tunnel for free DDoS protection and global acceleration. It runs on-demand (usually less than 6 hours per session) and goes offline when done, a physical-level "disconnected defense."

## Open Source vs Closed Source

This repository showcases the complete version (Proprietary, closed-source). The core is the independently runnable engine extracted from it, released under MIT.

| | This Repo (Showcase) | myblog-core |
| --- | :---: | :---: |
| Full source | No | Yes (MIT) |
| Independently runnable | No | Yes |
| License | Proprietary | MIT |
| Purpose | Architecture whitepaper | Runnable blog engine |

- Want the architecture: read this repo.
- Want to deploy it: see [myblog-core](https://github.com/wng409/myblog-core).
- The core is a subset snapshot. The complete version is still being updated; critical security fixes may be synced to core.

## Architecture Overview

```text
[ Public Internet ]
     |
     v
[ Cloudflare (Free DDoS Protection + Edge Cache + Tunnel) ]
     |
     v
[ Home Cloud (127.0.0.1) ]
 |-- Node.js + Express + SQLite (WAL Mode)
 |-- EasyMDE (Fully localized assets)
 |-- Nginx Reverse Proxy to File Browser (Private Cloud Drive)
     |
     |--(5-min clean backup)--> [ GitCode (30GB Main Repo) ] --(One-way force overwrite)--> [ GitHub / Gitee (Read-only Mirrors) ]
     |
     |--(DingTalk Webhook)--> [ Real-time Alerts & Self-healing ]
```

## Geek-Level Security Defense (Showcase)

This is the most proud design of this project. It maximizes the security boundary while ensuring an extreme user experience:

1. **"Cyber Flashbang" Protection (CSP + SHA-256)**
   - Strict same-origin JS (`script-src 'self'`), blocking all inline JS.
   - The ONLY inline script (dark mode pre-loader to prevent FOUC) is whitelisted using a **SHA-256 hash fingerprint**. Not a single space or quote can be altered, rooting out XSS from the source.
   - Images only allowed via HTTPS. Styles prioritized for same-origin + Cloudflare, with a pragmatic allowance for inline CSS to keep EasyMDE buttery smooth.
   - Bilibili video iframe whitelist (`player.bilibili.com`) for seamless knowledge embedding.

2. **Closed-Source Private + One-Way Mirrors**
   - Main code and data are hosted on GitCode (Private).
   - GitHub and Gitee serve ONLY as read-only mirrors for friends. Any unauthorized modification is immediately **force-overwritten** by the next sync, completely eliminating external pollution.

3. **Core Application Security**
   - `helmet` + `csurf` + `express-rate-limit` + `DOMPurify`.
   - Passwords hashed with `scrypt`, multi-user three-tier permission isolation (admin / editor / viewer).

## God-Tier Data Backup & Self-Healing (Showcase)

Leveraging the unique mechanics of SQLite (WAL Mode) and automated scripts, it achieves "frictionless" off-site disaster recovery:

- **5-Minute Clean Backup**:
  - Rejects direct `cp` (to prevent WAL write loss), uses atomic `.dump` instead.
  - **Data Purification Surgery**: Automatically filters out the `logs` table and `sqlite_sequence`, and strips the `users.last_login` field to prevent meaningless commits from polluting the repository.

- **Semi-Annual Repo Rotation (Archiving)**:
  - Every 6 months (Jan/Jul), automatically switches to a brand-new GitCode repository to archive old data, preventing single-repo bloat.
  - Auto-rollback mechanism: if rotation fails, it instantly restores the old repo. Zero data loss.

- **Self-Healing & Timeout Sentinel**:
  - `retry-push.sh`: Network jitter causing push failure? Retries every minute, auto-recovers within 1 minute, and sends a DingTalk "Recovered" notification.
  - `check-backup-stale.sh`: Alerts via DingTalk if no backup for 15 minutes. Intelligently skips false alarms within 10 minutes of boot-up.

- **DingTalk Webhook Full-Link Monitoring**:
  - Admin login, performance anomaly switches, crash recovery, brute-force attacks, backup timeouts/recovery, auto-update results, all pushed to the phone in real-time. Manual restarts do not trigger notifications (no spam).

## Core Features

- **Markdown Writing + Bilibili Video Embed** (Localized EasyMDE, supports local image upload/external links).
- **Homepage Banner + Featured Posts**, dynamic short posts (with role-based deletion).
- **Notes Cloud Drive** (Nginx reverse proxy to File Browser).
- **Custom Pages**: Supports MC / Hutao exclusive HTML uploads.
- **Soft Delete + Recycle Bin + Orphaned Image Cleanup**.
- **Operation Logs**: Who, when, and what, crystal clear.
- **Server Status Page**: CPU / RAM / Disk / Network / Anomaly detection dashboard.
- **Dark Mode** (Say no to Cyber Flashbangs).

## Tech Stack

| Layer | Technology |
| --- | --- |
| Runtime | Node.js v22 LTS |
| Backend | Express |
| Template Engine | EJS + express-ejs-layouts |
| Database | SQLite3 (WAL Mode) |
| Markdown | markdown-it + EasyMDE (Localized) |
| Security | helmet + csurf + express-rate-limit + DOMPurify |
| Process Manager | systemd |
| Reverse Proxy | Nginx |
| Edge Network | Cloudflare Tunnel |
| Code Hosting | GitCode (Main) + GitHub/Gitee (Read-only Mirrors) |

## Cyber Acknowledgements

The birth of this system is inseparable from the selfless dedication of the internet's "Cyber Benefactors":

- Thanks to **Cloudflare** for external access, free SSL, and infinite DDoS protection.
- Thanks to **GitCode** for the 30GB mega-capacity private repo, holding the lifeline of data.
- Thanks to **Bilibili** for providing massive high-quality learning videos (and the iframe whitelist).
- Thanks to **DingTalk Webhook** for acting as the most loyal cyber guard.
- Thanks to **Node.js** for providing an efficient asynchronous runtime.
- Thanks to **DeepSeek V4.1 Flash Agent** for assisting with the extraction and cleanup of the core.

## License & Terms

**Copyright (c) 2026. All rights reserved.**

**This repository (Showcase) is a personal, private project (Proprietary) and is NOT open source.**
It ONLY showcases the system architecture and design philosophy. Unauthorized copying, modification, distribution, or any commercial use is strictly prohibited without explicit written permission.

**The open-source core `myblog-core` is licensed differently:**
- Repo: https://github.com/wng409/myblog-core
- Released under the **MIT** License. Free to use, modify, and distribute.
- This repository's Proprietary notice does **NOT** apply to `myblog-core`.

The author reserves all rights to the complete version.

---
*"The most advanced tinkering often adopts the simplest defense. Keeping everything private is the greatest respect for one's digital assets."*
