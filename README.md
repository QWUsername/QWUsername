# QWedge SYSTEM 1.0

一个用 Python 编写的、运行在终端里的「桌面系统」——带多用户登录、ASCII 图形界面、
开/关机动画，以及一整套内置小程序和一个可在浏览器中使用、支持外网分享的
**Launchpad 启动台**。

> 作者：QWUsername　|　版本：1.0（QW1.0）　|　平台：Windows（其他平台可运行，部分功能受限）

---

## 特性一览

- **终端桌面系统**：ASCII 字符图标 + 边框菜单，纯 `print/flush` 渲染，稳定不花屏。
- **多用户账户系统**
  - 密码使用「随机盐 + SHA-256」哈希存储，支持恒定时间校验（防时序攻击）。
  - 记录 `salt`、`account_type`、`created_at`、`last_login` 等信息。
  - 支持注册、注销、删除用户、**更改用户名**、**更改密码**。
  - 更改用户名/密码、删除用户都必须先验证该用户的原密码。
  - 密码输入时显示圆点掩码（`•••`），支持退格。
  - 「上次登录用户」下次启动可自动登录，无需重复输入。
- **内置程序**：Program、This Computer、Notebook、Terminal、Calculator、Clock、Game、About、Settings。
- **Launchpad 启动台**
  - 本地 HTTP 服务（默认端口 `18765`），浏览器访问。
  - 展示页（朋友可看可下载）、协作页（朋友可提交代码）、开发者工作台。
  - 代码库 / 已部署 / 待审核 / 回收站，完整的提交—审核—发布工作流。
  - SSE 实时推送，数据变化页面自动刷新，无需手动刷新。
  - 文件上传：分块上传（768KB/块）、断点续传、类型与大小校验、在线预览、一键导入编辑器。
- **外网访问隧道**：内置 Cloudflare Tunnel、ngrok、OpenFrp 三种引擎的统一管理菜单，
  可让不同网络下的朋友访问你的 Launchpad。
- **开机动画 / 关机动画**：Q​Wedge 图标的启动与关闭动画。
- **中英文双语**：终端与网页全部界面可在 Settings 中切换中文 / English，设置自动持久化。

---

## 快速开始

### 环境要求

- Python **3.8 或更高版本**（开发环境为 Python 3.14）。
- 核心系统**仅使用 Python 标准库**，开箱即用，无需安装任何第三方包。
- （可选）只有使用 **OpenFrp** 隧道的凭据加密功能时需要 PyNaCl：

  ```bash
  pip install pynacl
  ```

### 方式一：单文件版（推荐用于分发）

单文件版 `qwedge1.0.py` 已把所有模块内联，**单独一个文件即可运行**，
不需要 `Models/` 目录：

```bash
python qwedge1.0.py
```

直接把这一个文件发给朋友，对方有 Python 就能运行；数据会写在文件所在目录。

### 方式二：完整版（开发 / 多文件）

需要保留 `Models/` 目录，运行主程序：

```bash
python qwedge_system_main_1.0.py
```

---

## 默认账号

首次运行会自动初始化以下账号，**登录后请尽快修改密码**：

| 入口 | 用户名 | 密码 |
| --- | --- | --- |
| 终端系统 | `QWedge` | `QWedge` |
| Launchpad 网页 | `admin` | `admin123` |

---

## 主菜单

```
 [1] Login          登录
 [2] Program        程序运行器
 [3] This Computer  本机信息
 [4] Notebook       记事本
 [5] Terminal       终端（Cmd）
 [6] Calculator     计算器
 [7] Clock          时钟
 [8] Game           游戏
 [9] About          关于
 [10] Settings      设置（语言切换等）
 [11] Launchpad     启动台
 [12] Poweroff      关机
```

除登录外，其余程序需要先登录才能使用。

---

## Launchpad 启动台

进入主菜单 `[11] Launchpad`，或在系统启动时服务会自动在后台运行。

- 本机访问：<http://localhost:18765>
- 页面：
  - `/`　　　公开**展示页**：浏览、下载作品（只读）。
  - `/collab`　公开**协作页**：朋友可在线写代码并提交到「待审核」。
  - `/dev`　　**开发者工作台**：编辑、部署、删除、审核、上传文件。
- 朋友提交的作品会进入「待审核」队列，开发者在终端或工作台**批准 / 拒绝**后，
  才会进入代码库或对外展示。
- 所有数据变化通过 SSE（`/api/events`）实时推送到所有打开的页面。

### 让外网的朋友访问

在 Launchpad 菜单中选择「隧道 / Tunnel」，先选择引擎，再按提示操作：

- **Cloudflare Tunnel**：免费、无需账号即可获得临时网址；适合快速分享。
- **ngrok**：需要 authtoken，可配置固定域名。
- **OpenFrp**：国内节点，可自选较短域名（需要 PyNaCl 处理凭据）。

---

## 项目结构

```
QWedgeSYSTEM 2.5 main/
├── qwedge_system_main_2.5.py      # 主程序入口（完整版）
├── qwedge1.0.py                   # 单文件版（自包含，可独立分发）
├── Models/                        # 各功能模块（完整版使用）
│   ├── qlog.py                    # 统一日志（控制台 + 日志文件）
│   ├── i18n.py                    # 中英文国际化
│   ├── login_1_2.py               # 登录 / 注册 / 账户管理
│   ├── launchpad_server.py        # Launchpad HTTP 服务与网页
│   ├── tunnel_manager.py          # Cloudflare / ngrok 隧道管理
│   ├── openfrp_manager.py         # OpenFrp 隧道管理
│   ├── boot_animation.py          # 开机动画
│   ├── shutdown_animation.py      # 关机动画
│   └── *_1.0.py / *_1.1.py        # 各内置小程序
├── Launchpad/                     # 已部署程序
├── Library/                       # 代码库
├── Pending/                       # 待审核作品
├── .uploads/                      # 上传文件（含分块缓存）
├── .app_trash/                    # 应用回收站
├── logs/                          # 运行日志
└── Saved/                         # 记事本等保存的数据
```

### 运行时生成的数据文件

| 文件 / 目录 | 说明 |
| --- | --- |
| `users.json` | 终端系统的用户数据 |
| `launchpad_users.json` | Launchpad 网页用户数据 |
| `language.txt` | 当前语言（zh / en） |
| `last_user.txt` | 上次登录用户（用于自动登录） |
| `dev_token.txt` | Launchpad 开发者令牌 |
| `launchpad_audit.log` | Launchpad 操作审计日志 |
| `ngrok_authtoken.txt`、`ngrok_domain.txt` | ngrok 配置 |
| `openfrp_cred.json` | OpenFrp 凭据（加密） |
| `cloudflared.exe`、`ngrok.exe`、`frpc.exe` | 隧道引擎程序 |

这些文件会在首次运行 / 首次使用对应功能时自动创建，无需手动准备；
删除后重新运行会重新初始化。

---

## 打包成可执行文件（exe）

单文件版可直接用 PyInstaller 打包，对方无需安装 Python：

```bash
pip install pyinstaller
pyinstaller -F -c qwedge1.0.py
```

- `-F`：打包成单个 exe；`-c`：保留控制台窗口（本系统需要，不要用 `-w`）。
- 可加 `-i 图标.ico` 指定程序图标。
- 打包后的数据文件写在 **exe 所在目录**。
- 生成的 exe 位于 `dist/` 目录。

---

## 常见问题

**Q：在 IDE（Trae / VSCode / PyCharm）的运行面板里，屏幕一层层叠加、密码不显示圆点？**
A：这些面板不是真正的控制台，无法清屏 / 关闭回显。请在系统终端
（cmd、PowerShell、Windows Terminal）中运行，即可正常清屏和显示掩码。

**Q：朋友换设备 / 不在同一网络下访问不了？**
A：本机地址（localhost / 局域网 IP）只能在同一网络访问。跨网络请使用
Launchpad 内置的 Cloudflare / ngrok / OpenFrp 隧道获取公网网址。

**Q：Launchpad 端口被占用？**
A：服务启动失败时不影响系统其他功能；关闭占用 `18765` 端口的程序后重试即可。

---

## 技术栈

- Python 标准库：`hashlib`、`secrets`、`http.server`、`threading`、`ctypes`、`msvcrt`、`json` 等。
- 可选第三方：[PyNaCl](https://github.com/pyca/pynacl)（OpenFrp 凭据加密）。
- 打包：[PyInstaller](https://pyinstaller.org/)。

---

## 许可证

本项目由 QWUsername 制作，仅供学习与个人使用。
