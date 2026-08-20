# 接入 GitHub：用 Git 同步与备份 Obsidian 仓库

> Obsidian 的数据都是本地 Markdown 文件，非常适合用 Git + GitHub 做**免费同步、版本控制和云端备份**。这篇指南从零讲清楚：怎么把 Obsidian 仓库接到 GitHub 上，怎么日常同步，怎么让它自动完成。

---

## 目录

1. [为什么要接入 GitHub](#为什么要接入-github)
2. [GitHub 网页版 vs 桌面版](#github-网页版-vs-桌面版)
3. [前置准备](#前置准备)
4. [方式一：用 GitHub Desktop 图形界面（推荐新手）](#方式一用-github-desktop-图形界面推荐新手)
5. [方式二：用命令行（进阶，可完全掌控）](#方式二用命令行进阶可完全掌控)
6. [配置 .gitignore（重要）](#配置-gitignore重要)
7. [用 Obsidian Git 插件自动同步](#用-obsidian-git-插件自动同步)
8. [多设备同步与冲突处理](#多设备同步与冲突处理)
9. [常见问题](#常见问题)
10. [小结](#小结)

---

## 为什么要接入 GitHub

| 好处 | 说明 |
| ---- | ---- |
| **版本控制** | 每次修改都有历史记录，写坏了随时回滚到任意时间点 |
| **云端备份** | 电脑硬盘坏了也不怕，GitHub 上多一份完整副本 |
| **多设备免费同步** | 替代付费的 Obsidian Sync，在电脑/手机间同步笔记 |
| **数据始终是纯文本** | 笔记依然是 `.md` 文件，不被任何平台锁定 |
| **免费** | 个人私有仓库免费（GitHub 对个人免费提供无限私有仓库） |

> 注意：笔记通常涉及隐私，**强烈建议使用 Private（私有）仓库**，不要误建为 Public 公开仓库。

---

## GitHub 网页版 vs 桌面版

GitHub 有两套常用入口，一个负责「云端」，一个负责「本地操作」：

| 入口 | 作用 |
| ---- | ---- |
| **GitHub 网页版**（github.com） | 在云端创建/管理仓库，查看历史记录，浏览文件 |
| **GitHub 桌面版**（GitHub Desktop） | 装在电脑上的图形客户端，负责把云端仓库「克隆」到本地、提交改动、上传更新，全程点按钮，不用敲命令 |

> 简单理解：**网页版管云端，桌面版管你电脑上的文件夹与云端之间的同步**。桌面版本质上是 Git 命令的图形化外壳，适合不想记命令的新手。

---

## 前置准备

### 1. 注册 GitHub 账号

前往 <https://github.com> 注册账号（免费即可）。

### 2. 安装 Git（命令行方案需要，桌面版会自动带上）

- 前往 <https://git-scm.com/downloads> 下载安装。
- 安装后打开终端验证：

```bash
git --version
# 应输出类似：git version 2.43.0
```

### 3. 安装 GitHub Desktop（方式一需要）

- 前往 <https://desktop.github.com> 下载安装，并用 GitHub 账号登录。

### 4. 配置 Git 身份（命令行方案需要，只需一次）

```bash
git config --global user.name "你的名字"
git config --global user.email "你的邮箱@example.com"
```

> 这个身份会记录在每次提交里，邮箱建议和 GitHub 账号邮箱一致。

---

## 方式一：用 GitHub Desktop 图形界面（推荐新手）

这是最省心的方式，全程鼠标点击，适合不想记命令的用户。

### 第 1 步：在网页版创建云端项目

1. 登录 GitHub 网页版，点击右上角 `+` → **New repository**。
2. 填写项目名，例如 `Study_Example`。
3. **选择 Private（私有）**。
4. 不要勾选「Add a README」「.gitignore」「license」，保持完全空白。
5. 点击 **Create repository**。

### 第 2 步：用桌面版克隆到电脑

1. 打开 GitHub Desktop，点击 **File → Clone repository**（克隆仓库）。
2. 在列表里找到刚创建的 `Study_Example`（或粘贴仓库地址）。
3. 选择要保存到电脑的本地路径（**建议选一个空文件夹**，例如 `D:\Study_Example`）。
4. 点击 **Clone**，云端仓库就被「拉」到本地，变成一个普通文件夹。

### 第 3 步：把这个文件夹变成 Obsidian 仓库

1. 打开 Obsidian → **打开文件夹作为仓库（Open folder as vault）**。
2. 选择刚才克隆下来的本地文件夹。
3. 现在你在这个文件夹里写的所有笔记，都已经是「接在 GitHub 上」的了。

### 第 4 步：日常上传更新

每次在 Obsidian 里写完笔记后，回到 GitHub Desktop：

1. 左侧会列出所有**有改动的文件**（勾选确认）。
2. 左下角填写一句「改动说明」，例如 `新增 2 篇学习笔记`。
3. 点击 **Commit to main**（提交）。
4. 再点击顶部 **Push origin**（推送/上传）。
5. 回到网页版刷新，就能看到更新已经上传到云端。

> 一句话流程：**在 Obsidian 里写 → 打开桌面版 → Commit → Push**，完成同步。

---

## 方式二：用命令行（进阶，可完全掌控）

如果你习惯命令行，或想在没有图形界面的环境（服务器、自动化脚本）里操作，用命令更直接。

### 第 1 步：在网页版创建云端仓库

同上：新建 `Study_Example`，选 Private，保持空白。

### 第 2 步：本地初始化 Git 仓库

打开终端，进入你的 Obsidian 仓库文件夹（以 `C:\Projects\2026\Obsidian_tutorial` 为例）：

```bash
cd C:\Projects\2026\Obsidian_tutorial
git init
```

执行后该文件夹会多出一个隐藏的 `.git` 文件夹，表示它已变成一个 Git 仓库。

### 第 3 步：首次提交并推送

```bash
# 1. 把所有文件加入暂存区
git add .

# 2. 提交（生成第一个版本快照）
git commit -m "初始化 Obsidian 仓库"

# 3. 把本地分支命名为 main（GitHub 默认分支名）
git branch -M main

# 4. 关联远程仓库（地址换成你自己的）
git remote add origin https://github.com/你的用户名/Study_Example.git

# 5. 推送到 GitHub
git push -u origin main
```

### 第 4 步：日常手动同步

```bash
git pull                                   # 拉取远程最新内容
git add .                                  # 暂存改动
git commit -m "描述这次改了什么"            # 提交
git push                                   # 推送
```

---

## 配置 .gitignore（重要）

不是所有文件都需要同步。有些文件（界面状态、缓存）每台设备不同，同步了反而容易冲突。在仓库根目录新建一个 `.gitignore` 文件，内容如下：

```gitignore
# —— Obsidian 界面状态与缓存（每台设备不同，忽略可避免冲突）——
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache/

# —— 回收站 ——
.trash/

# —— 操作系统产生的垃圾文件 ——
.DS_Store
Thumbs.db
desktop.ini
```

**说明：**

- `.obsidian/workspace.json`：记录「当前打开了哪些面板/文件」的界面状态，同步它没有意义，还极易引发冲突。
- `.obsidian/` 文件夹里**其他配置**（插件设置、主题、快捷键）默认**跟随同步**，这样多台设备的设置也能保持一致。
- 如果希望每台设备**用不同的设置**，就把整行 `.obsidian/` 也加进 `.gitignore`，二选一即可。

> 小技巧：文件列表里看不到以 `.` 开头的隐藏文件？在 Obsidian 设置 → 文件与链接 → 打开「显示隐藏文件」。

---

## 用 Obsidian Git 插件自动同步

如果你不想每次手动点按钮或敲命令，可以用社区插件 **Obsidian Git** 自动完成提交、推送和拉取。

### 安装步骤

1. Obsidian → 设置 → **第三方插件（社区插件）**。
2. 首次使用需点击「关闭安全模式」。
3. 点「浏览」，搜索 **Obsidian Git**，安装并启用。

### 推荐配置

打开插件设置，建议这样配：

| 设置项 | 建议值 | 说明 |
| ------ | ------ | ---- |
| **Auto backup interval**（自动备份间隔） | 10～30 分钟 | 每隔一段时间自动提交一次 |
| **Auto pull interval**（自动拉取间隔） | 10～30 分钟 | 自动拉取其他设备的改动 |
| **Auto pull on startup** | 开启 | 打开 Obsidian 时先拉取一次，避免冲突 |
| **Commit message** | 保留默认（含时间戳） | 提交信息自动生成 |
| **Pull before push** | 开启 | 推送前先拉取，减少冲突 |

### 手动触发

也可以用命令面板（`Ctrl/Cmd + P`）输入：

- `Obsidian Git: Commit` —— 提交
- `Obsidian Git: Push` —— 推送
- `Obsidian Git: Pull` —— 拉取
- `Obsidian Git: Create backup` —— 一次性备份

> 插件首次推送仍需完成一次身份认证（见下方「常见问题」），之后即可自动推送。

---

## 多设备同步与冲突处理

### 在第二台设备上拉取仓库

- **桌面版**：File → Clone repository，把云端仓库克隆到这台电脑，再用 Obsidian 打开即可。
- **命令行**：

```bash
git clone https://github.com/你的用户名/Study_Example.git
```

### 为什么会冲突？怎么避免？

- **冲突**发生在两台设备同时改了**同一个文件**的**同一处**时，Git 无法自动合并。
- **避免方法**：
  1. 打开设备前先 **Pull** 一次（或开启「Auto pull on startup」）。
  2. 尽量一台设备写完、推送完，再在另一台设备上继续。
  3. 把 `.obsidian/workspace.json` 加入 `.gitignore`（前面已做），能减少一大半无意义冲突。
- **遇到冲突怎么办**：打开冲突文件，会看到 `<<<<<<<`、`=======`、`>>>>>>>` 标记，手动保留想要的内容、删掉标记后重新提交即可。

---

## 常见问题

### 1. 推送时提示认证失败 / 要求输入密码

GitHub 从 2021 年起不再支持 HTTPS 密码登录，需要二选一：

- **个人访问令牌（PAT，推荐）**：
  1. GitHub → Settings → Developer settings → Personal access tokens → Generate new token。
  2. 勾选 `repo` 权限，生成后复制令牌。
  3. 推送时密码一栏**粘贴令牌**（不是你的登录密码）。
- **SSH 密钥**：生成密钥并把公钥添加到 GitHub，然后用 `git@github.com:...` 形式的地址（更省心，推荐进阶用户）。
- 用 **GitHub Desktop** 的话，登录账号后基本不会遇到这个问题（它已帮你处理好认证）。

### 2. 中文文件名/内容乱码

```bash
git config --global core.quotepath false
git config --global i18n.commitencoding utf-8
```

### 3. 换行符警告（CRLF / LF）

Windows 和 Mac/Linux 换行符不同，可能产生无意义差异。Windows 用户建议配置：

```bash
git config --global core.autocrlf true
```

### 4. 附件 / 大文件很多，仓库越来越大

- 图片、PDF 等二进制文件会占空间。可考虑：
  - 把大附件放进单独文件夹并加入 `.gitignore`，改用网盘同步附件。
  - 或使用 Git LFS（大文件存储），但对个人笔记通常没必要。
- GitHub 单个仓库建议控制在 1GB 以内，个人笔记一般不会超。

### 5. 忘记自己做过哪些改动

```bash
git log --oneline   # 查看提交历史
git status          # 查看当前未提交的改动
git diff            # 查看具体改了什么内容
```

---

## 小结

| 方案 | 适合谁 | 特点 |
| ---- | ------ | ---- |
| GitHub Desktop 桌面版 | 不想记命令的新手 | 全程鼠标点击，直观 |
| 纯命令行 Git | 想完全掌控、懂命令行的用户 | 灵活、可控，稍麻烦 |
| Obsidian Git 插件 | 想省心的普通用户 | 自动提交/推送/拉取，配置一次长期生效 |
| 官方 Obsidian Sync | 愿意付费、要极致体验的用户 | 端到端加密、官方维护，最省心 |

无论选哪种，**核心思路都一样**：把 Obsidian 仓库当作一个普通 Git 项目来管理，笔记从此有了历史、备份和跨设备能力。

---

*相关阅读：回到 [[Obsidian使用教程]] 查看完整入门指南。*
