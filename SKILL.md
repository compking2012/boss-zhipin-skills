---
name: boss-zhipin-cli
description: >
  Configures and runs OpenCLI `opencli boss` against BOSS Zhipin (www.zhipin.com) as a
  recruiter: recommended candidates, batch or single greetings, chat list and messages,
  resumes, interview invites, phone or WeChat exchange, candidate labels, posted jobs, public
  job search and job detail, and per-job statistics. Documents prerequisite setup—global
  `@jackwener/opencli`, Chrome with the OpenCLI Browser Bridge unpacked extension, and an
  authenticated recruiter session—and allows running `npm install -g @jackwener/opencli` when
  the CLI is absent. Use for BOSS直聘招聘端, HR or hiring-owner tasks, 候选人/新招呼/推荐牛人,
  聊天列表与消息, 简历与约面, 交换联系方式, 职位与数据, installing or troubleshooting opencli and
  Browser Bridge, or English phrases such as BOSS Zhipin recruiter, batch greet, and job stats.
---

# BOSS直聘招聘端（OpenCLI）

本技能说明如何通过 **OpenCLI** 的 `opencli boss` 子命令，在 **招聘方账号**（HR、用人经理、业务负责人等）下处理 **推荐/投递候选人**、沟通、简历、状态与数据。**域名**：`www.zhipin.com`；**策略**：cookie，需浏览器侧登录。

## 安装与环境（前置）

`boss` 属于 **浏览器策略**（Strategy: cookie | Browser: yes），必须同时满足：**OpenCLI CLI**、**Chrome + Browser Bridge 扩展**、**已在 Chrome 中登录 BOSS 直聘招聘端**。执行任何 `opencli boss` 前，Agent 应先检查环境；**缺什么补什么**（CLI 可用命令自动安装；扩展与登录需用户在本机 Chrome 完成一次）。

### 1. 检测是否已安装 OpenCLI

在终端执行：

```bash
command -v opencli && opencli --version
```

若 `command` 无输出或报错，视为 **未安装**。

### 2. 自动安装 OpenCLI（Agent 可直接执行）

当用户环境 **尚未安装** `opencli` 或版本过旧需要升级时，Agent 可在征得用户同意（或用户已明确要求配置环境）后执行：

```bash
npm install -g @jackwener/opencli
```

升级：

```bash
npm update -g @jackwener/opencli
```

**说明**：需本机已安装 **Node.js** 与 **npm**（随 Node 自带）。若 `npm` 不可用，应先安装 Node LTS，再重试上述命令。安装完成后再次验证：`opencli --version`、`opencli boss --help`。

### 3. Browser Bridge（Chrome 扩展 + 微守护进程）

**重要**（与 [Browser Bridge 官方说明](https://opencli.info/docs/guide/browser-bridge.html) 一致）：浏览器类命令会 **复用你在 Chrome 里已登录的网站会话**。运行命令前，请先在 Chrome 中登录目标网站（此处即 BOSS 直聘招聘端）。

OpenCLI 通过 **Browser Bridge Chrome 扩展** 与 **微守护进程（micro-daemon）** 连接浏览器：**零配置**，首次执行浏览器类命令时会 **自动启动** 守护进程。

#### 安装扩展

**方式一：下载预构建包（推荐）**

1. 打开 [jackwener/opencli Releases](https://github.com/jackwener/opencli/releases)，下载最新的 `opencli-extension.zip`。
2. 解压后打开 `chrome://extensions`，右上角开启 **开发者模式**。
3. 点击 **加载已解压的扩展程序**，选择解压后的文件夹。

**方式二：从源码目录加载（开发者）**

1. 打开 `chrome://extensions`，开启 **开发者模式**。
2. **加载已解压的扩展程序**，选择 OpenCLI 仓库中的 `extension/` 目录。

#### 验证与排错

安装扩展后，执行任意浏览器类命令时守护进程会自动启动，**无需 token、无需手工配置**。连通性自检：

```bash
opencli doctor   # 检查扩展与守护进程连接
```

若需查看守护进程状态：`opencli daemon status`；停止：`opencli daemon stop`；重启：`opencli daemon restart`。默认约 4 小时无请求且无扩展连接时退出；可用环境变量 `OPENCLI_DAEMON_TIMEOUT`（毫秒）覆盖，`0` 表示常驻（详见官方文档）。

**Agent 行为**：无法替用户在图形界面完成「加载扩展」。若 `opencli` 已安装但 `boss` 报 Bridge/连接错误，应 **提示用户** 按 **方式一** 安装扩展并确认扩展已启用；开发者可指引 **方式二**。可附带官方文档链接：<https://opencli.info/docs/guide/browser-bridge.html>。

### 4. 登录 BOSS 直聘（招聘端）

在 **与扩展相同的 Chrome 用户配置** 中打开 `https://www.zhipin.com`（或 `www.zhipin.com`），使用 **招聘方账号** 登录并保持会话有效。未登录或会话过期时，`opencli boss` 可能失败或数据为空。

### 5. 快速自检（可选）

```bash
opencli boss recommend --limit 1 -f json
```

成功则 CLI、Bridge、登录大致正常（仍应以实际业务输出为准）。

---

## 其他约定

1. **输出格式**：脚本化解析优先使用 `-f json`（或 `yaml`）；人类阅读用默认 `table`。
2. **合规**：涉及打招呼、发消息、要联系方式、约面等操作前，应取得 **用户明确确认**；不得自动化批量骚扰或违反平台规则。

---

## 命令速查（招聘端）

| 场景 | 命令 | 说明 |
|------|------|------|
| 推荐/新招呼列表 | `opencli boss recommend` | 查看系统推荐的候选人（新招呼等） |
| 批量打招呼 | `opencli boss batchgreet` | 按职位批量向推荐候选人发招呼 |
| 单人打招呼 | `opencli boss greet <uid>` | 向指定候选人发招呼并开始聊天 |
| 聊天列表 | `opencli boss chatlist` | 招聘端会话列表 |
| 聊天记录 | `opencli boss chatmsg <uid>` | 与某候选人的消息记录 |
| 发消息 | `opencli boss send <uid> "<text>"` | 主动发送聊天内容 |
| 简历 | `opencli boss resume <uid>` | 查看候选人简历（招聘端） |
| 交换联系方式 | `opencli boss exchange <uid>` | 请求手机或微信（`--type phone\|wechat`） |
| 面试邀请 | `opencli boss invite <uid>` | 发送面试邀请（时间、地址、联系人等） |
| 标签 | `opencli boss mark <uid>` | 添加或移除候选人标签 |
| 我的职位 | `opencli boss joblist` | 已发布职位列表 |
| 职位数据 | `opencli boss stats` | 按职位维度的数据统计 |
| 搜职位（公开） | `opencli boss search <query>` | 关键词搜职位（偏求职者侧检索；招聘方偶尔用于对标 JD） |
| 职位详情 | `opencli boss detail <security-id>` | 由 `search` 结果中的 `securityId` 取 JD 详情 |

**通用参数**：`-f/--format`（table | plain | json | yaml | md | csv）、`-v/--verbose`。具体默认值与列名以 `opencli boss <cmd> --help` 为准。

---

## 典型工作流（Agent 指引）

### 1. 处理「新招呼 / 推荐牛人」

从推荐列表获取候选人标识（如 `encrypt_uid`、`security_id`、`encrypt_job_id`），再决定是否打招呼或批量处理：

```bash
opencli boss recommend --limit 20 -f json
# 可选：按职位筛选批量打招呼（见 batchgreet --help）
opencli boss batchgreet --job-id "<encrypt_job_id>" --limit 5 -f json
# 或对单个候选人打招呼
opencli boss greet "<uid>" --job-id "<encrypt_job_id>" --text "..." -f json
```

### 2. 跟进沟通与状态

拉会话列表 → 按需拉取单聊记录或回复：

```bash
opencli boss chatlist --page 1 --limit 20 -f json
opencli boss chatmsg "<uid>" --page 1 -f json
opencli boss send "<uid>" "您好，想跟您确认一下方便沟通的时间。" -f json
```

结合 **`mark`** 维护内部阶段（标签名或 ID 以 `--help` 为准，如：沟通中、已约面、不合适等）：

```bash
opencli boss mark "<uid>" --label "沟通中" -f json
# 移除标签：--remove true
```

### 3. 简历与意向评估

在取得授权的前提下查看简历以支持筛选与记录：

```bash
opencli boss resume "<uid>" -f json
```

### 4. 约面与联系方式

```bash
opencli boss invite "<uid>" --time "2026-04-10 14:00" --address "..." --contact "..." -f json
opencli boss exchange "<uid>" --type phone -f json
```

### 5. 职位与数据看板

```bash
opencli boss joblist -f json
opencli boss stats --job-id "<encrypt_job_id>" -f json
```

---

## 字段与 ID 约定

- **`uid`**：候选人侧加密 ID（多数字段来自 `recommend` / `chatlist` 等输出的 `encrypt_uid` 或帮助中所述 uid 列）。
- **`security-id`**：职位搜索等接口中的 **职位** 安全 ID，用于 `detail`，勿与候选人 uid 混淆。
- **`encrypt_job_id`**：职位维度筛选时使用（见 `batchgreet`、`stats` 等）。

若命令失败，可加 `-v` 查看调试信息，并核对当前浏览器会话是否为 **招聘方** 且未过期。

---

## 与自动化表格/CRM 的衔接（可选）

若用户用飞书/表格做候选人管道，Agent 可：**用 `opencli boss … -f json` 拉取结构化结果 → 写入用户自有表格**，字段映射由用户表头决定；本技能不依赖任何仓库内脚本，**不要将含隐私的简历或聊天记录提交到公共仓库**。

---

## 禁止与注意

1. 不伪造身份、不绕过平台授权、不批量滥用接口。
2. 候选人个人信息仅用于用户明示的招聘目的，**不外传、不入公共代码库**。
3. 具体列名、枚举值以当前 CLI `--help` 为准；OpenCLI 升级后请以用户本机 `opencli boss <子命令> --help` 为最终依据。
