# TechBlog CMS - 0 元构建的赛博堡垒（架构展示版）

🌐 **Language / 语言:** [English](README.md) | [简体中文](README.zh-CN.md)

> ⚠️ **重要声明**：本项目为个人私有项目（Proprietary），**未开源**。
> 此仓库仅作为**架构设计与技术理念的展示白皮书**，不包含完整的运行源码、部署脚本与敏感配置。
> 未经许可，禁止复制、修改、分发或用于任何商业用途。

![Node.js](https://img.shields.io/badge/Node.js-v22_LTS-339933?logo=node.js&logoColor=white)
![Express](https://img.shields.io/badge/Express-4.x-000000?logo=express&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-3-003B57?logo=sqlite&logoColor=white)
![License](https://img.shields.io/badge/license-Proprietary-red.svg)
![Architecture](https://img.shields.io/badge/Architecture-Home%20Cloud-blueviolet)

---

## 🌟 核心理念：极致白嫖与数字主权
在一个动辄需要“上云”、“买高防”、“付费CDN”的时代，这套系统证明了：**只要架构足够优雅，0 元也能构筑企业级的个人数字堡垒。**

整个系统运行在一台名为“家里云”的轻量级服务器上（4C4G80GB，127.0.0.1），通过 Cloudflare Tunnel（赛博大善人）穿透外网，享受免费的高防与全球加速。按需开机（通常不超过6小时/次），用完即走，物理级别的“断网防御”。

## 🏗️ 架构全景概览

```text
[ 外网访问 ]
     │
     ▼
[ Cloudflare (免费高防 + Edge Cache + Tunnel) ]
     │
     ▼
[ 家里云 (127.0.0.1) ]
 ├── Node.js + Express + SQLite (WAL模式)
 ├── EasyMDE (全本地化加载)
 └── Nginx 反代 File Browser (私人云盘)
     │
     ├─(5分钟净量备份)─> [ GitCode (30GB 雷霆容量主仓库) ] ──(单向强制覆盖)──> [ GitHub / Gitee (只读镜像) ]
     │
     └─(钉钉 Webhook) ─> [ 实时告警与自愈 ]
```

## 🛡️ 极客级的安全防线（Showcase）

这是本项目最引以为傲的设计，在保障极致体验的同时，将安全边界拉满：

1. **赛博灯泡防护罩（CSP + SHA-256）**
   - 全站严格同源 JS（`script-src 'self'`），禁止一切内联 JS。
   - 唯一的内联防闪烁脚本（深色模式预加载），使用 **SHA-256 哈希指纹放行**。多一个空格、少一个引号都无法执行，从根源掐断 XSS。
   - 图片仅允许 HTTPS，样式表优先同源+Cloudflare，适度保留内联 CSS 以成全 EasyMDE 的丝滑体验。
   - B站视频 iframe 白名单（`player.bilibili.com`），无缝嵌入知识库。
2. **闭源私有 + 单向镜像**
   - 主干代码与数据由 GitCode 私有仓库承载。
   - GitHub 与 Gitee 仅作为只读镜像供朋友观摩，任何越权修改都会被下一次同步**强制覆盖**，彻底杜绝外部污染。
3. **核心应用安全**
   - `helmet` + `csurf` + `express-rate-limit` + `DOMPurify` 全家桶。
   - 密码采用 `scrypt` 哈希，多用户三级权限隔离（admin / editor / viewer）。

## 💾 神仙级数据备份与自愈（Showcase）

基于 SQLite (WAL 模式) 的独特机制，配合自动化脚本，实现了“无感化”的异地灾备：

*   **5分钟级净量备份**：
    *   拒绝直接 `cp`（防止丢 WAL 写入），采用原子化 `.dump`。
    *   **数据净化手术**：自动过滤 `logs` 日志表与 `sqlite_sequence`，剥离 `users.last_login` 字段，防止无意义的 commit 污染仓库。
*   **半年期自动换仓（Git Repo 轮转）**：
    *   每半年（1月/7月）自动切换至全新的 GitCode 仓库归档旧数据，防止单仓库体积膨胀。
    *   带自动回滚机制：换仓失败即刻还原旧仓库，绝不丢数据。
*   **故障自愈与超时哨兵**：
    *   `retry-push.sh`：网络抖动导致 push 失败？每分钟重试，1 分钟内自动恢复，并推送钉钉“已恢复”通知。
    *   `check-backup-stale.sh`：15 分钟未备份即钉钉告警，开机 10 分钟内智能跳过误报。
*   **钉钉 Webhook 全链路监控**：
    *   admin 登录、性能异常切换、崩溃拉起、疑似爆破攻击、备份超时/恢复、代码自动更新结果……全部实时推送到手机。手动重启不发通知（拒绝打扰）。

## 🚀 项目核心功能

- **Markdown 写作 + B站视频嵌入**（EasyMDE 本地化，支持图片本地上传/外链）。
- **首页 Banner + 推荐文章管理**，动态短文发布（按权限删除）。
- **笔记云盘**（Nginx 反代 File Browser）。
- **自定义页面**：支持 MC / 胡桃专属 HTML 上传。
- **软删除 + 回收站 + 孤儿图片清理**。
- **操作日志**：谁、何时、做了什么，一清二楚。
- **服务器状态页**：CPU / 内存 / 磁盘 / 网络 / 异常检测面板。
- **深色模式**（拒绝赛博灯泡闪烁）。

## 🛠️ 技术栈一览

| 层级 | 技术 |
| --- | --- |
| 运行时 | Node.js v22 LTS |
| 后端框架 | Express |
| 模板引擎 | EJS + express-ejs-layouts |
| 数据库 | SQLite3（WAL 模式） |
| Markdown | markdown-it + EasyMDE (本地化) |
| 安全 | helmet + csurf + express-rate-limit + DOMPurify |
| 进程管理 | systemd |
| 反向代理 | Nginx |
| 边缘网络 | Cloudflare Tunnel |
| 代码托管 | GitCode (主) + GitHub/Gitee (只读镜像) |

## 🤝 赛博鸣谢

这套系统的诞生，离不开互联网“赛博大善人”们的无私奉献：

- 鸣谢 **Cloudflare** 提供的外网访问、免费 SSL 与无限高防。
- 鸣谢 **GitCode** 提供的 30GB 雷霆容量私有仓库，承载了数据命脉。
- 鸣谢 **哔哩哔哩** 提供了海量优质的学习视频（与 iframe 白名单）。
- 鸣谢 **钉钉 Webhook** 充当了最忠诚的赛博门卫。
- 鸣谢 **Node.js** 提供的高效异步运行时。
- 鸣谢 **DeepSeek V4.1 Flash** 提供的技术支持。

## 📄 许可与协议

**Copyright (c) 2026. All rights reserved.**

**本项目为个人私有项目（Proprietary），未开源。**
本仓库仅展示系统架构与设计思路。未经作者明确书面许可，**禁止复制、修改、分发或用于任何商业用途。** 作者保留一切追究法律责任的权利。

---
*“最高级的折腾，往往采取最朴素的防御。全私有，是对自己数字资产最大的尊重。”*
