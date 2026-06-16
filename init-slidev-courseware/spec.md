# Slidev 交互式课件 — 创建规范

> **给 Claude Code 看的工作规范。** 严格按照本文档的步骤执行，不要跳过任何一步。

---

## 文件约定

**本文档中的 `> **AI 宣告：**` 引用块，是给 AI 的话术模板，AI 应该用自然语言说出来，而不是照念。** 话术模板保证 AI 始终向用户说明当前在做什么、为什么做、还要多久。

---

## 第 0 步：理解你的用户 & 检查操作系统

**用户是教师，很可能没有任何开发经验。** 这意味着：

- 不要假设用户理解 `node`、`pnpm`、`端口`、`localhost`、`终端` 等术语
- 每个操作之前，用一句话用人话解释"这一步在做什么"
- 遇到报错时，不要慌张地让用户自己排查 —— 你先分析错误日志，再给用户明确的指令
- 用户不知道怎么授权，你需要明确告诉用户"接下来 Claude Code 会弹出一个授权提示，请点击 Allow/允许"
- **你可以帮用户修复问题**，比如 KaTeX 不渲染是因为 HTML 块内缺少空行 —— 你知道这个坑，主动修好
- **绝对不要根据一个标题就开始写幻灯片** —— 必须先收集教学思路。用户说"帮我生成一个XX的PPT"时，你的第一反应不是开始写代码，而是说"好的！在开始之前，请先花几分钟讲一下您这节课的思路……"

### 0.1 检测操作系统

**先搞清楚用户用的是什么操作系统**，这决定了后续所有命令的写法。

```bash
# macOS
uname -s   # 输出 "Darwin"

# Windows（在 PowerShell 中）
$env:OS    # 输出 "Windows_NT"
```

直接问用户："您的电脑是 Windows 还是 Mac？"（Linux 教师在概率上极低，但也支持）

### 0.2 环境兼容性检查与预处理

| 检查项       | macOS                          | Windows                                         | 说明                                                              |
| ------------ | ------------------------------ | ----------------------------------------------- | ----------------------------------------------------------------- |
| Shell 环境   | 自带 zsh/bash ✅               | **需检查** — 需要 PowerShell 7+ 或 Git Bash     | Windows 用户必须有 PowerShell（Win+R 输入 `powershell` 回车测试） |
| `ln` 命令    | 自带 ✅                        | **不适用** — 用 `cp -r` 替代                    | Windows 下创建符号链接需要管理员权限，改用复制                    |
| `mv` 命令    | 自带 ✅                        | PowerShell: 用 `Move-Item`；Git Bash: `mv` 可用 | 优先使用 `Move-Item` 或引导用户安装 Git Bash                      |
| Node.js 安装 | `brew install node` 或官网下载 | 官网 `.msi` 安装包                              | 见第 1 步                                                         |
| pandoc 安装  | `brew install pandoc`          | 官网 `.msi` 安装包                              | 见第 3.4 步                                                       |

**如果用户在 Windows 上且没有 PowerShell：**

> "您的 Windows 系统需要 PowerShell 来执行后续命令。请打开 Microsoft Store，搜索 'PowerShell'，安装最新版本（免费）。安装完成后告诉我。"

**如果用户在 Windows 上且没有 Git Bash（推荐安装）：**

> "为了获得最佳的兼容性，建议您再安装一个叫 'Git for Windows' 的工具。请打开 <https://gitforwindows.org/> ，下载并安装。安装时一路点 Next 保持默认设置即可。这个工具会提供一套在 Windows 上通用的命令环境。"

### 0.3 通用排障口诀（AI 在遇到环境问题时主动告知用户）

| 症状                                         | 解决方案                                                                       |
| -------------------------------------------- | ------------------------------------------------------------------------------ |
| 刚装完 Node.js，但 `node --version` 说找不到 | **注销系统重新登录**（或重启电脑）。这是因为新安装的程序还没加入 PATH 环境变量 |
| 提示权限不足 / Permission denied             | macOS: 在前面加 `sudo`；Windows: 以管理员身份打开 PowerShell                   |
| `pnpm` 命令不被识别                          | 先确认 corepack enable 执行成功。如果还不行，重启终端窗口                      |
| 安装过程中网络很慢                           | 中国大陆用户可能访问 npm 仓库慢。告诉 AI："帮我配置淘宝镜像"                   |
| 终端窗口不知道在哪打开                       | macOS: `Cmd+空格` 搜 "Terminal"；Windows: `Win+R` 输入 `powershell`            |

> **核心原则：** 小白用户遇到任何报错，你应该先安抚"这很正常，环境配置是最麻烦的一步，我帮您解决"，然后自己分析错误日志。不要反问用户"你是不是没有XXX？"—— 用户不知道答案。

### 0.4 UX 沟通原则 — 让用户始终知道你在做什么

**教师不是程序员。** 如果你闷头执行 10 步操作不发一言，用户会焦虑："它在干嘛？怎么还没好？是不是卡住了？"

**每次切换阶段时，必须先向用户宣告你要做什么、为什么要做、大概需要多久。**

#### 全程分阶段概述（首次接触用户时告知）

> "我们来做一套完整的交互式课件。整个过程分两大步：
>
> **第一步：环境配置（约 10 分钟，只做一次）** — 安装 Node.js、配置工具链，把项目骨架搭起来。这一步我可能需要指导您手动安装几个工具，我会一步步告诉您怎么操作。
>
> **第二步：课件制作（约 30-60 分钟）** — 您告诉我这节课的思路、提供教案和教材，我来设计幻灯片。这一步您可以语音输入、发文件、随时提修改意见。
>
> 那我们先从第一步开始——"

#### 阶段切换时必须宣告

每进入一个新的"阶段"，AI 应该用一句简短的话说明：

| 阶段         | AI 宣告示例                                                                      |
| ------------ | -------------------------------------------------------------------------------- |
| 开始环境检查 | "我先检查一下您的电脑环境，几秒钟就好。"                                         |
| 安装 Node.js | "接下来需要安装 Node.js，这是运行课件的基础。请打开浏览器访问……"                 |
| 安装工具链   | "现在安装 pnpm 和 pandoc，这些是辅助工具，安装一次以后就不用管了。"              |
| 创建项目     | "环境就绪！现在我来创建课件项目的骨架，大约 1 分钟。"                            |
| 写设计系统   | "现在写入课件的视觉设计——配色、字体、卡片样式。这些决定了课件'长什么样'。"       |
| 收集教学内容 | "🛑 项目骨架搭好了。**在开始写具体内容之前**，我需要先了解您的教学设计……"        |
| 写幻灯片     | "好的，现在我根据您的思路开始写第一版幻灯片。写完后您可以预览，我们再逐页修改。" |
| 遇到报错     | "别担心，这是一个常见的小问题，我来修一下。原因是……"                             |

#### 关键原则

- **先说"要做什么"，再做。** 不要先执行再解释。
- **区分"一次性"和"每次都要"。** 环境配置是一次性的，让用户有耐心。修改课件是反复的，让用户知道可以随时提意见。
- **用"我们"，不是"你"。** "我们需要安装 Node.js" > "你去安装 Node.js"
- **给时间预期。** "这一步大约需要 3 分钟"让用户安心

---

## 第 0.5 步：收集用户信息（使用 AskUserQuestion）

**在开始任何技术操作之前，先用 AskUserQuestion 工具收集以下信息。** 这些信息会直接填入模板，教师不需要手动编辑任何代码。

一次性询问（合并成一个 AskUserQuestion 调用，包含所有问题）：

**问题 1：课题信息**

- header: `课题名称`
- question: `请告诉我这节课的课题名称（如"电容器的电容"）`
- 用户输入后会填入：`slides.md` 的 `title`、`cover-title`、`global-top.vue` 内页文字、CLAUDE.md 的项目名称

**问题 2：章节编号**

- header: `章节`
- question: `这节课属于哪个章节？（如"第十章 第4节" → 格式：10.4）`
- 用户输入后会填入：封面 `cover-chapter`（`§ X.X`）

**问题 3：教师姓名**

- header: `授课教师`
- question: `授课教师的姓名？`
- 用户输入后会填入：封面 `cover-subtitle`

**问题 4：学校名称**

- header: `学校`
- question: `学校或机构名称？`
- 用户输入后会填入：封面 `cover-school`、`global-top.vue` 右侧文字

**问题 5：Logo（可选）**

- header: `Logo`
- question: `您有学校 logo 图片吗？`
- 选项：
  - `有，我会提供图片文件` → AI 后续引导放入 `public/logo.png`，`hasLogo = true`
  - `没有，用文字就好`（推荐）→ `hasLogo = false`，顶栏右侧显示学校名称文字

> **收集完毕后，AI 直接将信息填入对应位置，教师不需要手动编辑任何文件。**

---

## 第 1 步：检查 Node.js 环境

> **AI 宣告：** "现在开始环境配置（只做这一次）。我先检查您的电脑上是否安装了 Node.js，这是运行课件的基础……"

**这是在做什么：** Slidev 是一个基于 Node.js 的工具，所以需要先确认电脑上有 Node.js。

```bash
node --version
```

- 如果显示版本号 >= `v22.0.0`（当前 LTS）→ 验证 `npm --version` 也可用，然后继续下一步
- 如果显示 `command not found` 或版本过低 → 引导用户：

**macOS 用户：**

> "您的电脑还没有安装 Node.js。请打开 https://nodejs.org/ ，点击左边绿色的 LTS 按钮下载安装包。双击 `.pkg` 文件，一路点 **继续** 保持默认设置即可（安装包体积很小，不用改安装位置）。安装完成后，**请注销系统重新登录**（或重启电脑），然后告诉我。"

**Windows 用户：**

> "您的电脑还没有安装 Node.js。请打开 https://nodejs.org/ ，点击左边绿色的 LTS 按钮下载 `.msi` 安装包。双击运行，一路点 **Next** 保持默认设置即可（安装包很小，不用改盘符）。安装完成后，**请注销系统重新登录**（或重启电脑），然后告诉我。"

**为什么安装完要重新登录？** 新安装的程序需要加入系统的 PATH 环境变量，注销/重启后才会生效。如果用户跳过了这一步，命令行会仍然提示 `node: command not found`。

安装完成后再次运行：

```bash
node --version
npm --version
```

两个命令都有输出 → 继续下一步。

### 1.5 检查 Git（版本管理工具）

**这是在做什么：** Git 是一个"存档"工具——每次你修改课件到一个阶段，Git 可以帮你保存一个快照。这样如果后续改坏了，可以随时回到之前的版本。类比游戏里的"存档点"。

```bash
git --version
```

- **如果已安装** → 继续下一步。AI 应该：
  - 在项目创建后立即 `git init`
  - **在每个逻辑阶段完成后自动提交**（比如：环境配置完成、设计系统写入完成、幻灯片初稿完成……）
  - 提交信息用中文，让教师能看懂：`git commit -m "环境配置完成"`、`git commit -m "写入设计系统和样式"`
  - 每次提交前告诉教师一声："我把目前的进度保存一下"

- **如果未安装** → 引导用户：

**macOS：**

> "您的电脑还没有 Git。请打开终端，输入 `xcode-select --install`，会弹出安装提示，点击'安装'即可。大约需要 5 分钟。安装完成后输入 `git --version` 确认。"

**Windows：**

> "您的电脑还没有 Git。请打开 https://gitforwindows.org/ ，下载安装包。双击运行，一路点 Next 保持默认设置即可。安装完成后，**需要重新打开 PowerShell**，然后输入 `git --version` 确认。"

> **注意：** Git 不是必须的。如果教师不想安装，跳过即可。但安装了 Git 后，AI 必须主动定期提交，这是防止误操作丢工作的安全网。

---

## 第 2 步：启用 pnpm 包管理器

**这是在做什么：** `pnpm` 是一个更快的 Node.js 包管理工具，用来下载项目的依赖。

Node.js 16.9+ 自带 corepack，执行：

```bash
corepack enable
corepack prepare pnpm@latest --activate
```

验证：

```bash
pnpm --version
```

---

## 第 3 步：配置 Claude Code 环境（MCP + 权限）

**这是在做什么：** 让 Claude Code 能够：

1. 通过 Chrome DevTools MCP 预览你的课件网页
2. 获得必要的命令执行权限，减少反复授权

### 3.1 安装 Chrome DevTools MCP

```bash
claude mcp add chrome-devtools-mcp -- npx @anthropic-ai/mcp-server-chrome-devtools-mcp
```

> **告诉用户：** 这会安装一个浏览器调试工具，让 Claude Code 能帮你预览课件效果。

### 3.2 配置 settings.json 权限

在项目根目录创建 `.claude/settings.json`（如果不存在），写入：

```json
{
  "skipWebFetchPreflight": true,
  "permissions": {
    "allow": [
      "mcp__plugin_chrome-devtools-mcp_chrome-devtools__*",
      "Bash(pnpm install *)",
      "Bash(pnpm add *)",
      "Bash(pnpm dev)",
      "Bash(pnpm dev -- *)",
      "Bash(pnpm build)",
      "Bash(corepack *)",
      "Bash(node --version)",
      "Bash(pnpm --version)",
      "Bash(ls *)",
      "Bash(mkdir *)",
      "Bash(cd *)"
    ]
  }
}
```

**关键解释（告诉用户）：**

- `skipWebFetchPreflight: true` — 允许 Claude Code 直接访问网页。在中国大陆，很多网站（如 GitHub、Google Fonts）无法直接访问，这个设置避免 Claude Code 在尝试访问时卡住。
- `allow` 中的命令 — 预先授权常用操作（安装依赖、启动开发服务器、构建项目），这样用户不需要每次都点"允许"。

> **注意：** `.claude/settings.json` 在项目目录内，只对这个项目生效。如果想对所有项目生效，放在用户目录 `~/.claude/settings.json`。

### 3.3 安装 Slidev 技能

Slidev 技能需要通过 `npx skills add` 安装，不是内置的。

```bash
npx skills add slidevjs/slidev
```

**这会在 `.agent/` 目录下生成技能文件。** 为了让 Claude Code 识别到，需要将其复制到 `.claude/` 目录：

**macOS / Git Bash (Windows)：**

```bash
mkdir -p .claude/skills
cp -r .agent/skills/slidev .claude/skills/slidev
```

**Windows PowerShell（无 Git Bash）：**

```powershell
New-Item -ItemType Directory -Force -Path .claude/skills
Copy-Item -Recurse .agent/skills/slidev .claude/skills/slidev
```

> **注意：** 不使用 `ln -s`（符号链接）—— Windows 下需要管理员权限。改用 `cp -r`（复制）更可靠。

**这些技能文件可以提交到 Git。** 为了让其他教师 clone 项目后自动安装技能，在 `package.json` 中添加 `postinstall` 脚本：

```json
{
  "scripts": {
    "postinstall": "npx skills add slidevjs/slidev && mkdir -p .claude/skills && cp -r .agent/skills/slidev .claude/skills/slidev 2>/dev/null || true",
    "dev": "slidev",
    "build": "slidev build"
  }
}
```

这样其他教师 clone 项目后只需运行 `pnpm install`，技能就会自动安装到位。

> **验证：** 安装完成后，运行 `ls .claude/skills/slidev/`（macOS/Git Bash）或 `dir .claude\skills\slidev\`（Windows PowerShell）应该能看到技能文件。Claude Code 重启后即可使用 Slidev 技能。

### 3.4 安装 pandoc（PDF/Word 文档读取）

**这是在做什么：** 教师提供的教案、学案、教材通常是 Word（`.docx`）或 PDF 格式。pandoc 是一个文档格式转换工具，Claude Code 可以用它来读取这些文件的内容。

**检查是否已安装：**

```bash
pandoc --version
```

**如果未安装：**

**macOS：**

```bash
brew install pandoc
```

> 如果提示 `brew: command not found`，先安装 Homebrew：打开 https://brew.sh/ ，复制页面上的安装命令到终端运行，然后再 `brew install pandoc`。

**Windows：**

> "请打开 https://pandoc.org/installing.html ，点击 'Download the latest installer for Windows' 下载 `.msi` 安装包。双击运行，一路点 Next 保持默认设置即可。安装完成后告诉我。"

> **验证：** 安装完成后，运行 `pandoc --version` 确认有输出。

安装 pandoc 后，AI 可以：

- 读取 `.docx` 教案/学案 → `pandoc 文件.docx -t markdown --wrap=none`
- 读取 `.pdf` 教材扫描件 → 结合其他工具提取文字
- 将 Markdown 教案转换为 Word 供打印 → `pandoc 教案.md -o 教案.docx`

> **告诉教师：** "pandoc 是一个文档转换小工具，安装一次即可。有了它，我可以直接读取您的 Word 教案和 PDF 教材，您不需要手动复制粘贴。"

---

## 第 4 步：创建 Slidev 项目

```bash
pnpm create slidev@latest
```

AI 应替用户回答交互式选项：

- **Project name**: 输入项目目录名（如 `physics-courseware`）
- **Project title**: 课件标题
- **Theme**: 选择 `default`（后续用自定义 CSS 覆盖）
- **Install**: Yes
- **Agent**: 选择 `pnpm`

等待安装完成，然后 `cd` 进入项目目录。

---

## 第 5 步：安装额外依赖

```bash
pnpm add -D @iconify-json/mdi
```

- `@iconify-json/mdi` — Material Design Icons 图标集，Slidev 原生支持 `<mdi-xxx />` 语法
- 图标预览：https://icones.js.org/collection/mdi

（如果需要格式化工具，可选 `pnpm add -D oxfmt`）

---

## 第 6 步：删除默认生成的无用文件

Slidev 模板会生成示例幻灯片和组件，删除它们：

```bash
rm -f slides.md pages/*.md components/*.vue
```

---

## 第 7 步：编写 `slides.md` frontmatter

创建 `slides.md`，写入以下 frontmatter：

```yaml
---
theme: default
title: <课件标题>
titleTemplate: "%s"
highlighter: shiki
transition: fade
mdc: true
layout: cover
colorSchema: dark
clickAnimation: card
fonts:
  sans: Nunito Sans
  mono: Fira Code
  provider: none
defaults:
  layout: default
  transition: fade
---
```

**关键解释：**

- `transition: fade` — 页面切换使用淡入淡出（0.5s），平滑不突兀
- `mdc: true` — 启用 Markdown Components（支持 `<v-click>` 等 Slidev 扩展语法）
- `colorSchema: dark` — 深色模式（与自定义深蓝背景匹配）
- `clickAnimation: card` — v-click 元素使用卡片入场动效
- `fonts.provider: none` — **不使用 Google Fonts**（中国大陆不可访问），改用系统字体
- `defaults.layout: default` — 除封面外所有页面默认使用 default 布局
- `defaults.transition: fade` — 所有页面默认使用 fade 过渡

---

## 第 8 步：编写 `style.css` — 完整设计系统

这是整个项目的视觉灵魂。将以下内容写入 `style.css`：

### 7.1 CSS 变量（配色系统）

```css
:root {
  /* 配色系统 */
  --c-bg: #090d1a;
  --c-bg-soft: #0f1425;
  --c-surface: rgba(30, 41, 59, 0.65);
  --c-surface-solid: #1e293b;
  --c-border: rgba(148, 163, 184, 0.1);
  --c-border-glow: rgba(226, 168, 70, 0.2);
  --c-text: #f1f5f9;
  --c-text-dim: #94a3b8;
  --c-accent: #e2a846; /* 暖金 — 强调色 */
  --c-accent-glow: #f59e0b;
  --c-accent-2: #3b82f6; /* 深蓝 — 第二强调色 */
  --c-accent-2-glow: #2563eb;
  --c-physics: #2dd4bf;
  --c-danger: #f87171; /* 红色 — 正电/危险 */
  --radius-xl: 1.75rem;
  --radius-lg: 1.25rem;
  --radius-md: 0.75rem;
  --radius-sm: 0.5rem;
}
```

### 7.2 基础排版（保证教室后排清晰可见）

```css
/* 基础字号 22px — 公开课大教室后排清晰可读 */
html {
  font-size: 22px;
  color: var(--c-text);
  background: var(--c-bg);
}

body {
  font-family:
    "PingFang SC",
    "Microsoft YaHei",
    "Hiragino Sans GB",
    "WenQuanYi Micro Hei",
    system-ui,
    -apple-system,
    sans-serif;
  background: linear-gradient(160deg, var(--c-bg) 0%, #0c1122 40%, var(--c-bg-soft) 100%);
}

/* 调整 Slidev 默认布局的内边距 */
.slidev-layout {
  padding: 3rem 2.5rem 1.5rem 2.5rem !important;
  background: transparent !important;
}

/* 标题层级 */
h1 {
  font-size: 1.8rem !important;
  font-weight: 700 !important;
  letter-spacing: -0.02em;
  color: var(--c-text);
  margin-bottom: 0.5rem;
}
h2 {
  font-size: 1.25rem !important;
  font-weight: 600 !important;
  color: var(--c-text);
  margin-bottom: 0.3rem;
}
h3 {
  font-size: 1rem !important;
  font-weight: 500 !important;
  color: var(--c-text-dim);
}

/* 正文行高 */
p,
li {
  font-size: 1rem;
  line-height: 1.55;
}
```

### 7.3 SVG 保护（关键！防止 UnoCSS 破坏 SVG 字体）

```css
/* SVG 内部不受 html 字号影响 */
svg {
  font-size: 16px;
}

/* 撤销 UnoCSS 对 SVG font-size 属性的错误匹配
   UnoCSS 把 font-size="11" → [font-size~="11"] { font-size: 2.75rem }
   这里用属性选择器 + !important 覆盖回去 */
svg [font-size] {
  font-size: unset !important;
}
```

> **为什么需要这个？** Slidev 使用 UnoCSS，它的属性选择器 `[font-size~="11"]` 会匹配 SVG 元素上的 `font-size="11"` 属性，将其覆盖为 `2.75rem`（因为 UnoCSS 认为这是 HTML class 而非 SVG 属性）。这会导致 SVG 内文字巨大化。必须用 `!important` 覆盖回去。

### 7.4 玻璃态卡片系统

```css
.card {
  background: var(--c-surface);
  backdrop-filter: blur(16px);
  -webkit-backdrop-filter: blur(16px);
  border: 1px solid var(--c-border);
  border-radius: var(--radius-lg);
  padding: 0.85rem 1.25rem;
  transition: all 0.3s ease;
}
.card:hover {
  background: rgba(30, 41, 59, 0.8);
  border-color: rgba(148, 163, 184, 0.18);
  transform: translateY(-1px);
}
.card-highlight {
  border-color: var(--c-border-glow);
  box-shadow:
    0 0 30px rgba(226, 168, 70, 0.06),
    inset 0 1px 0 rgba(255, 255, 255, 0.02);
}
.card-highlight:hover {
  border-color: rgba(226, 168, 70, 0.35);
  box-shadow:
    0 0 40px rgba(226, 168, 70, 0.1),
    0 0 80px rgba(226, 168, 70, 0.04),
    inset 0 1px 0 rgba(255, 255, 255, 0.03);
}
```

### 7.5 聊天气泡系统

用于师生问答场景，左侧蓝色（教师提问）、右侧金色（学生回答）：

```css
.chat-msg {
  max-width: 75%;
  padding: 0.65rem 1.1rem;
  font-size: 1rem;
  line-height: 1.55;
  position: relative;
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
}

/* 左侧气泡（教师/提问）— 蓝色调，左下角小圆角 */
.chat-left {
  background: rgba(59, 130, 246, 0.1);
  border: 1px solid rgba(59, 130, 246, 0.2);
  border-radius: 1.25rem 1.25rem 1.25rem 0.35rem;
  margin-right: auto;
  color: #e2e8f0;
}

/* 右侧气泡（学生/回答）— 暖金色调，右下角小圆角 */
.chat-right {
  background: rgba(226, 168, 70, 0.1);
  border: 1px solid rgba(226, 168, 70, 0.2);
  border-radius: 1.25rem 1.25rem 0.35rem 1.25rem;
  margin-left: auto;
  color: #f1f5f9;
}

/* 气泡内强调文字 */
.chat-left .chat-em {
  color: #60a5fa;
  font-weight: 600;
}
.chat-right .chat-em {
  color: #f59e0b;
  font-weight: 600;
}
```

**在 slides.md 中使用：**

```html
<div v-click.left class="chat-msg chat-left">电容器上标的电压是什么？</div>
<div v-click.right class="chat-msg chat-right">
  <span class="chat-em">额定工作电压</span>——电容器正常工作的最大电压
</div>
```

注意 `.left` / `.right` 配合 `v-click.left` / `v-click.right` 实现左右方向入场。

### 7.6 v-click 动效预设

```css
.slidev-vclick-target {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}
.slidev-vclick-hidden {
  opacity: 0;
  transform: translateY(16px);
}

/* 预设：左侧滑入（聊天气泡-左） */
.slidev-vclick-anim-left.slidev-vclick-hidden {
  opacity: 0;
  transform: translateX(-40px);
}

/* 预设：右侧滑入（聊天气泡-右） */
.slidev-vclick-anim-right.slidev-vclick-hidden {
  opacity: 0;
  transform: translateX(40px);
}
```

### 7.7 封面样式

```css
/* 章节号 */
.cover-chapter {
  font-family: "Times New Roman", "STIX Two Text", "Noto Serif CJK SC", Georgia, serif;
  font-size: 1.2rem;
  font-weight: 600;
  color: var(--c-text-dim);
  letter-spacing: 0.04em;
  margin-bottom: 0.3rem;
}

/* 封面标题 — 渐变文字 + 微弱的亮度呼吸动画 */
.cover-title {
  font-size: 3.5rem !important;
  font-weight: 900 !important;
  letter-spacing: 0.04em !important;
  background: linear-gradient(
    135deg,
    #f59e0b 0%,
    #e2a846 25%,
    #fbbf24 50%,
    #60a5fa 75%,
    #3b82f6 100%
  );
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
  margin-bottom: 0.8rem;
  animation: title-shimmer 4s ease-in-out infinite alternate;
}

@keyframes title-shimmer {
  0% {
    filter: brightness(1);
  }
  100% {
    filter: brightness(1.15);
  }
}

.cover-subtitle {
  display: flex;
  align-items: center;
  gap: 0.6rem;
  font-size: 1.1rem;
  color: var(--c-text-dim);
  letter-spacing: 0.04em;
}
.cover-school {
  font-size: 0.85rem;
  color: var(--c-text-dim);
  opacity: 0.55;
  letter-spacing: 0.08em;
  margin-top: 0.3rem;
}
```

### 7.8 辅助工具类

```css
/* 标签（药丸形） */
.tag-icon {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  background: rgba(59, 130, 246, 0.12);
  border: 1px solid rgba(59, 130, 246, 0.2);
  border-radius: 2rem;
  padding: 0.45rem 1.2rem;
  font-size: 0.8rem;
  color: var(--c-accent-2);
  font-weight: 500;
  backdrop-filter: blur(8px);
}

/* 文字强调色 */
.text-accent {
  color: var(--c-accent);
  font-weight: 600;
}
.text-accent-2 {
  color: var(--c-accent-2);
  font-weight: 600;
}

/* 缩放强调（尺寸阶梯） */
.text-lg {
  font-size: 1.1rem !important;
}
.text-xl {
  font-size: 1.25rem !important;
}
.text-2xl {
  font-size: 1.4rem !important;
}
.text-3xl {
  font-size: 1.7rem !important;
}
.text-4xl {
  font-size: 2.1rem !important;
}

/* LaTeX 公式 */
.katex {
  font-size: 1.15rem !important;
}
.katex-display {
  font-size: 1.35rem !important;
  margin: 1rem 0 !important;
}

/* 表格 */
table {
  font-size: 0.9rem;
  border-collapse: separate;
  border-spacing: 0;
  border-radius: var(--radius-md);
  overflow: hidden;
}
th {
  background: var(--c-surface-solid);
  font-weight: 600;
  padding: 0.6rem 1.2rem;
}
td {
  padding: 0.5rem 1.2rem;
  border-top: 1px solid var(--c-border);
  background: rgba(15, 20, 37, 0.4);
}
```

### 7.9 图片占位样式

```css
.placeholder-img {
  width: 100%;
  height: 160px;
  object-fit: cover;
  border-radius: var(--radius-md);
  background: var(--c-surface-solid);
  border: 1px dashed rgba(148, 163, 184, 0.15);
  transition: all 0.3s ease;
}
.placeholder-img:hover {
  border-color: rgba(148, 163, 184, 0.35);
}
```

---

## 第 9 步：覆盖全局底栏 — 去掉页码

Slidev 默认会在每页底部显示页码。公开课不需要页码，只需一条装饰线。

创建 `global-bottom.vue`：

```vue
<template>
  <div class="global-bottom">
    <div class="global-bottom-line"></div>
  </div>
</template>

<style scoped>
.global-bottom {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 100;
  pointer-events: none;
}

.global-bottom-line {
  height: 2px;
  background: linear-gradient(
    90deg,
    rgba(148, 163, 184, 0) 0%,
    rgba(148, 163, 184, 0.15) 15%,
    rgba(148, 163, 184, 0.15) 85%,
    rgba(148, 163, 184, 0) 100%
  );
}
</style>
```

**原理：** Slidev 会自动加载 `global-bottom.vue`（如果存在），替换默认的底栏（页码+导航）。这里用两侧渐隐的装饰线替代页码，专业且不分散学生注意力。

---

## 第 10 步：创建全局顶栏

创建 `global-top.vue`：

```vue
<template>
  <div class="global-top">
    <Transition name="top-text" mode="out-in">
      <span class="global-top-title" :key="isCover ? 'chapter' : 'topic'">
        {{ isCover ? "章节/单元名称" : "当前课题/小节标题" }}
      </span>
    </Transition>
    <img v-if="hasLogo" src="/logo.png" alt="学校名" class="global-top-logo" />
    <span v-else class="global-top-school-text">XX学校</span>
  </div>
</template>

<script setup lang="ts">
import { computed } from "vue";

const nav = ($slidev as any).nav;
const isCover = computed(() => nav.currentPage === 1);
</script>

<style scoped>
.global-top {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 2.5rem;
  z-index: 100;
  pointer-events: none;
}

.global-top-title {
  font-size: 0.65rem;
  font-weight: 600;
  color: rgba(241, 245, 249, 0.45);
  letter-spacing: 0.06em;
  white-space: nowrap;
  line-height: 1;
}

.global-top-logo {
  height: 1.4rem;
  width: auto;
  opacity: 0.7;
}

/* 文字切换动画：上下平移 + 轻微缩放 */
.top-text-enter-active,
.top-text-leave-active {
  transition: all 0.45s cubic-bezier(0.4, 0, 0.2, 1);
}
.top-text-enter-from {
  opacity: 0;
  transform: translateY(-12px) scale(0.95);
}
.top-text-leave-to {
  opacity: 0;
  transform: translateY(12px) scale(1.05);
}
</style>
```

**关键点：**

- 通过 `nav.currentPage === 1` 判断是否在封面页
- Vue `<Transition>` 实现文字切换的平滑动效
- `pointer-events: none` 确保不干扰 Slidev 的点击导航
- Logo 图片放在 `public/` 或项目根目录下

---

## 第 11 步：学校/机构标识（Logo 或文字）

**默认方案：** 顶栏右侧显示学校名称文字（无需任何图片文件）。

`global-top.vue` 中的 logo 区域默认使用文字：

```html
<img v-if="hasLogo" src="/logo.png" alt="学校名" class="global-top-logo" />
<span v-else class="global-top-school-text">XX学校</span>
```

对应在 `<script setup>` 中：

```typescript
const hasLogo = ref(false); // 用户提供 logo.png 后改为 true
```

**如果用户有 Logo 图片：**

1. 创建 `public/` 文件夹：`mkdir -p public`
2. 让用户将 logo.png 放入 `public/` 目录
3. 将 `hasLogo` 改为 `true`

**AI 操作流程：**

1. 在收集用户信息时（见第 0.5 步），询问学校名称并直接填入 `global-top-school-text`
2. 告知用户："顶栏右侧默认显示学校名称。如果您有学校 logo 图片，可以发给我，我会帮您替换。"

> **注意：** CLAUDE.md 和所有文档中的"logo"描述统一改为"学校名称（或 logo）"，避免让无编程经验的教师误以为必须提供图片文件。

---

## 第 12 步：编写 `CLAUDE.md`

创建 `CLAUDE.md`，写入以下项目说明：

```markdown
# <项目名称> — 公开课交互式课件

## 项目概述

<一句话描述：什么课题、面向什么场景、基于什么技术栈>

## 核心原则：必须令人惊艳

**每一页都应该是设计作品。** 这是用 Slidev 而非 PowerPoint 的关键原因。

- 精致的玻璃态卡片、微妙的发光阴影、平滑的动效
- 页面切换必须有连续性（文字形变、平移），不能干闪
- 所有交互有意义，不花哨，但也不沉闷

## 设计系统

### 视觉规范

- **玻璃态**：卡片使用 `backdrop-filter: blur(16px)` + 半透明背景
- **大圆角**：`border-radius: 1.25rem ~ 1.75rem`
- **字号**：基础 22px，封面标题 ~57px，正文 22px，公开课大教室后排清晰可读
- **配色**：
  - 背景：深蓝渐变 `#090d1a → #0c1122 → #0f1425`
  - 卡片：半透明 `rgba(30, 41, 59, 0.65)` + backdrop-blur
  - 强调色：暖金 `#e2a846`，深蓝 `#3b82f6`
  - 警示/对比：红色 `#f87171`，蓝色 `#3b82f6`
- **字体**：仅使用系统字体（PingFang SC, Microsoft YaHei 等），**禁止 Google Fonts**（中国大陆不可用）
- **封面标题**：渐变文字 + 微弱的亮度呼吸动画

### 动画系统

- **页面过渡**：`transition: fade`（0.5s）
- **顶栏文字切换**：使用 Vue `<Transition>` 上下平移 + 缩放
- **内容逐条呈现**：`v-click` / `v-clicks`
- **卡片 hover**：微上浮 + 边框增亮

### 顶栏规则

- **封面（第1页）**：顶栏左显示章节/单元名称（如"第十章 静电场"）
- **内页（第2页起）**：顶栏左显示当前课题/小节标题（如"电容器的电容"）
- **所有页**：顶栏右显示学校名称（或 logo，如果用户提供了图片）

> 封面与内页顶栏文字不同，通过 Vue `<Transition>` 实现平滑切换动画。

### 页脚

- 仅保留一条两侧渐隐的装饰线，**不显示页码**（公开课不需要）

## 图标

- 使用 `@iconify-json/mdi`（Material Design Icons），Slidev 原生支持 `<mdi-xxx />`
- **不要用 emoji** 替代图标，emoji 不专业且跨平台渲染不一致

## 技术约定

- 包管理：`pnpm`
- **LaTeX**：Slidev 内置 KaTeX。在 Markdown 段落中直接用 `$...$`。
  - **HTML block 内**需要加空行让 Markdown 解析器处理 `$...$`
- **导航**：使用键盘（→ ←）或翻页笔，**不要试图 hack 点击翻页**
- 组件：Vue 3 组件放 `components/`，Slidev 自动注册
- 样式：全局 `style.css`

## 开发注意事项

### 1. HTML 内 LaTeX 需要空行

`$...$` 在 HTML `<div>` 内部不会被 KaTeX 处理，除非在 `$...$` 所在段落前后加空行。

### 2. SVG font-size 被 UnoCSS 劫持

`html { font-size: 22px }` 会级联到 SVG。UnoCSS 的属性选择器 `[font-size~="11"]` 会匹配 SVG 的 `font-size="11"` 属性并覆盖。已在 `style.css` 中加入修复。

### 3. 学科表述必须准确

（根据具体学科填入专业规范，如物理术语、数学符号、化学方程式等）

### 4. 课件结构：先问后答

每页幻灯片应该**先抛问题，再给答案**，而不是一开始就把结论亮出来。这是授课视角，不是知识罗列。

### 5. 导航：不要 hack 点击翻页

Slidev 的默认行为：**点击页面中央不会翻页**，只有点击留黑区域才行。这是有意设计——内容区留给交互式组件。
**绝对不要修改这个行为。** 告诉教师使用：键盘 ← →、翻页笔、或鼠标移到左下角工具栏。触摸屏在两侧左右滑动可翻页。

### 6. 不要引入 Google Fonts

`fonts.provider: none`，仅使用系统字体。中国大陆无法访问 Google Fonts，引入会导致页面加载失败。

### 7. 不要用 emoji 代替图标

Emoji 不专业且跨平台渲染不一致。使用 `<mdi-xxx />` 图标（需 `@iconify-json/mdi`）。图标搜索：https://icones.js.org/collection/mdi

## 项目结构
```

slides.md ─ 主课件
global-top.vue ─ 全局顶栏
global-bottom.vue ─ 全局底栏（装饰线）
style.css ─ 设计系统 + SVG保护
components/ ─ 交互式 Vue 组件（自动注册）
public/ ─ 静态资源（学校logo、图片等，可选）
content/
思路.md ─ 课程设计思路（用户口述/提供）
教材.md ─ 教材原文（用户提供）
教案.md ─ 公开课教案（用户提供或参考）
逐字稿.md ─ 逐句台词（AI 生成）
学案.md ─ 学案/学习任务单（用户提供，可选）

```

```

---

## 第 13 步：🛑 收集教学内容素材 — 开始工作前必须完成

> **AI 宣告：** "环境配置完成，项目骨架已搭建好了！🎉 现在进入第二步——制作课件内容。但在开始写幻灯片之前，我需要先了解您的教学设计。请您花几分钟讲一下这节课的思路……"

**这是整个流程中最重要的一步。在用户提供足够的教学内容之前，绝对不要开始写幻灯片。**

### 为什么这一步至关重要

教师不是来让你"根据标题猜内容"的。没有教师的教学思路，你生成的课件：

- 知识点顺序可能与教材不符
- 举例可能与教师习惯不同
- 缺少教师独特的授课技巧和过渡语
- 学科表述可能不准确

**素材越丰富，AI 写的课件越贴合教师的真实课堂。**

### AI 必须做的事

项目初始化完成后，**暂停代码工作**，用以下话术引导用户提供素材：

> "项目环境已经搭建好了。在我开始写课件之前，我需要先了解您这节课的教学设计。请您通过语音或文字，花 5-10 分钟讲一下：
>
> 1. **这节课的思路** — 从引入到总结，您打算怎么讲？每个环节要强调什么？
> 2. **教材原文** — 如果方便，请把教材对应章节拍照或复制给我
> 3. **参考资料** — 您有现成的教案、学案、或其他老师的公开课资料吗？
>
> 您不用写得很正式，想到哪说到哪就好。我会根据您的思路来设计每一页幻灯片。"

### 如何引导用户提供文件（对小白友好）

教师通常不熟悉"把文件放到项目目录"这种操作。AI 需要提供多种方式：

**方式 A：拖拽到 content 文件夹（最推荐，0 技术门槛）**

> "在您的项目文件夹里，有一个叫 `content` 的文件夹。您可以直接把 Word 教案、PDF 教材等文件**拖拽进去**，就像往 U 盘里拖文件一样。拖进去后告诉我，我来读取。"

**方式 B：复制粘贴文本**

> "如果您觉得拖文件麻烦，也可以直接把教材内容、教案文字**复制粘贴发给我**。Word 里的文字直接 Ctrl+C / Cmd+C 复制过来就行。"

**方式 C：拍照**

> "如果手头只有纸质教材，拍照发给我也可以。请拍得清楚一些，一页一张。"

### 关键追问：区分自编资料 vs 参考资料

当用户提供教案/学案等文件时，**必须追问来源**：

> "您提供的这份教案——
>
> - 是**您自己写的**（针对这节课的）吗？
> - 还是**网上下载的参考资料**（人教版标准教案等）？
>
> 这两类我会区别对待：您自己写的我会严格遵循，参考资料的我会提取有用部分但不盲从。"

**为什么这很重要：**

- 自编教案 = 教师的教学意图，AI 应严格遵循其结构和重点
- 参考教案 = 普适性资料，AI 可以借鉴但不必须逐条照搬

### 素材收集清单

| 文件              | 必要性       | 说明                                                                |
| ----------------- | ------------ | ------------------------------------------------------------------- |
| `content/思路.md` | **必须**     | 用户口述或文字描述的教学思路。AI 听后整理成结构化文档               |
| `content/教材.md` | **强烈建议** | 教材对应章节的原文。确保知识点表述、公式符号与教材一致              |
| `content/教案.md` | 推荐         | 用户提供的教案。**追问来源**：自编（严格遵循）vs 参考（借鉴不盲从） |
| `content/学案.md` | 可选         | 学生的学习任务单，帮助 AI 理解学生的认知起点和课堂活动设计          |
| 其他参考文件      | 可选         | 其他教师的公开课视频笔记、历年课件、优质课评比资料等                |

### 用户只有标题、没有教案怎么办？

如果教师是即兴发挥型（没有书面教案），不强求。但 AI 应该：

1. **先让教师口述思路**，AI 整理成 `content/思路.md`
2. **自己去搜教材原文**（如人教版教材公开内容），整理到 `content/教材.md`
3. **将整理好的思路.md 和教材.md 给教师确认**："您看这是我根据您的口述和教材整理的思路，对吗？"

### 收集完毕后的确认

素材收集完毕后，AI 应该向用户确认：

> "我已经理解了您的教学思路，总结如下：
>
> - **引入**：……（用户说的引入方式）
> - **核心知识点**：……（按顺序列出）
> - **实验/互动环节**：……
> - **总结方式**：……
>
> 我计划设计约 XX 张幻灯片。如果没问题，我就开始写代码了。"

**用户确认后，才能进入第 14 步。**

### ❌ 错误示范

```
用户："帮我生成一个带电粒子在磁场中运动的PPT"
AI：（直接开始写 slides.md）
```

**这是绝对禁止的。** 正确做法：

```
用户："帮我生成一个带电粒子在磁场中运动的PPT"
AI："好的！在开始之前，我需要先了解您的教学设计。请先花几分钟讲一下您这节课的思路……"
```

---

## 第 14 步：编写 `slides.md` 课件内容

### 12.1 封面页示例

```markdown
---
layout: cover
---

<div class="cover-chapter"><span class="cover-section">§</span> X.X</div>

<h1 class="cover-title">课题标题</h1>

<div class="cover-subtitle">
  <span>授课教师：XXX</span>
</div>

<div class="cover-school">XX学校 / XX机构</div>
```

### 12.2 内页示例（v-click 逐条呈现）

```markdown
---
layout: default
---

## 标题

<div class="grid grid-cols-2 gap-4 mt-4">

<v-clicks>

<div class="card">
  <div class="font-semibold text-lg">要点一</div>
  <div class="text-sm opacity-60 mt-1">描述文字</div>
</div>

<div class="card">
  <div class="font-semibold text-lg">要点二</div>
  <div class="text-sm opacity-60 mt-1">描述文字</div>
</div>

</v-clicks>

</div>

<v-click>

<div class="card card-highlight mt-5">
  总结文字 — 在最后一步点击出现
</div>

</v-click>
```

### 12.3 LaTeX 公式示例

**行内公式：**

```markdown
电容的定义式：$C = \dfrac{Q}{U}$
```

**块级公式：**

```markdown
$$C = \frac{\varepsilon_r S}{4\pi k d}$$
```

**HTML 块内的 LaTeX（需要空行！）：**

```html
<div class="card">当一个极板带电量为 $+Q$ 时，另一个极板必然带电 $-Q$</div>
```

### 12.4 聊天气泡示例

```html
<div class="flex flex-col gap-4">
  <div v-click.left class="chat-msg chat-left">（教师提问）……？</div>

  <div v-click.right class="chat-msg chat-right">
    <span class="chat-em">（关键词）</span>——（学生回答/解释）
  </div>
</div>
```

### 12.5 交互式组件示例

```markdown
---
layout: default
---

# 标题

<MyComponent />
```

---

## 第 15 步：编写交互式 Vue 组件

组件放在 `components/` 目录下，Slidev 会自动注册（文件名即组件名）。

### 13.1 组件模板

```vue
<script setup lang="ts">
import { ref, computed, onUnmounted } from "vue";

// 状态定义
const value = ref<number>(0);
let timer: any = null;

// 计算属性
const displayValue = computed(() => value.value.toFixed(1));

// 交互逻辑
function handleAction() {
  // ...
}

// 清理
onUnmounted(() => {
  if (timer) clearInterval(timer);
});
</script>

<template>
  <div class="component-root">
    <svg viewBox="0 0 600 300" class="component-svg">
      <!-- SVG 内容 -->
    </svg>
    <!-- 控件 -->
  </div>
</template>

<style scoped>
.component-root {
  width: 100%;
}
.component-svg {
  display: block;
  width: 100%;
  height: auto;
}
.component-svg text {
  user-select: none !important;
  pointer-events: none !important;
}
</style>
```

### 13.2 组件设计规范

- **全宽 SVG**：使用 `viewBox` 而非固定宽高，配合 `width: 100%` 自适应
- **深色主题**：SVG 内颜色使用 `#475569`（线）、`#1e293b`（暗面）、`#334155`（边框）
- **交互反馈**：鼠标悬浮时颜色变亮（如 `#475569` → `#64748b`）
- **颜色渐变**：使用 `interpolateColor()` 工具函数实现物理量到颜色的映射
- **电荷表示**：正电荷用 `#f87171`（红色 `+`），负电荷用 `#60a5fa`（蓝色 `−`）
- **使用实际学科公式**：在 computed 中实现真实计算，让学生看到数据随参数变化

### 13.3 颜色插值工具函数

```typescript
function interpolateColor(color1: string, color2: string, factor: number): string {
  const parseHex = (hex: string) => {
    const match = hex.replace("#", "");
    return {
      r: parseInt(match.substring(0, 2), 16),
      g: parseInt(match.substring(2, 4), 16),
      b: parseInt(match.substring(4, 6), 16),
    };
  };
  const c1 = parseHex(color1);
  const c2 = parseHex(color2);
  const r = Math.round(c1.r + factor * (c2.r - c1.r));
  const g = Math.round(c1.g + factor * (c2.g - c1.g));
  const b = Math.round(c1.b + factor * (c2.b - c1.b));
  return `rgb(${r}, ${g}, ${b})`;
}
```

---

## 第 16 步：启动开发服务器

```bash
pnpm dev
```

Slidev 会在 `http://localhost:3030` 启动。打开浏览器即可看到课件。

---

## 第 17 步：构建生产版本

```bash
pnpm build
```

输出在 `dist/` 目录，可部署到任意静态服务器。

导出为 PDF（可选）：

```bash
pnpm export
```

---

## 第 18 步：🔒 验证知识持久化 — 防止新对话"变蠢"

**这是整个流程中第二重要的一步（仅次于第 13 步收集素材）。**

### 问题

当教师关闭 Claude Code 后再次打开，新会话会读取 `CLAUDE.md` 来理解项目。如果 `CLAUDE.md` 里缺少关键知识——

- 不知道 KaTeX 在 HTML 块内需要空行
- 不知道 SVG font-size 被 UnoCSS 劫持的坑
- 不知道点击中央不翻页是预期行为
- 不知道不能用 Google Fonts 和 emoji

——那么新会话的 AI 会反复踩同样的坑，教师会觉得"这 AI 怎么越来越蠢"。

### AI 必须做的事

项目创建完成后，**打开 `CLAUDE.md`，逐条确认以下内容已写入**：

| #   | 必须包含的知识                              | 为什么重要                             |
| --- | ------------------------------------------- | -------------------------------------- |
| 1   | **HTML 内 LaTeX 需要空行**                  | 否则公式在卡片里不渲染，教师反复报修   |
| 2   | **SVG font-size 被 UnoCSS 劫持** + 修复代码 | 否则 SVG 文字巨大化，教师看不懂        |
| 3   | **导航：键盘/翻页笔，点击中央不翻页**       | 否则教师以为 bug，新 AI 可能试图 hack  |
| 4   | **不要 Google Fonts**（`provider: none`）   | 否则新 AI 可能引入，中国大陆加载失败   |
| 5   | **不要 emoji，用 `<mdi-xxx />` 图标**       | 否则新 AI 为了省事用 emoji，课件不专业 |
| 6   | **先问后答的授课视角**                      | 否则新 AI 写成知识罗列，失去教学节奏   |
| 7   | **配色/字号/玻璃态的设计规范**              | 否则新 AI 改样式时破坏视觉一致性       |
| 8   | **项目中交互式组件的名称和用途**            | 否则新 AI 不知道有这些组件可用         |

### 验证方法

逐条检查后，告诉教师：

> "我已经确认 CLAUDE.md 中包含了所有关键知识。以后任何时候重新打开 Claude Code，它都会记得这些规则，不会变蠢。"

### 如果有 Git

```bash
git add CLAUDE.md && git commit -m "固化关键知识到 CLAUDE.md，防止新会话踩坑"
```

---

## 致教师：你不需要懂编程 — AI 会搞定这些

**本章节的目的是让教师（零开发经验）知道：遇到问题时不要慌，告诉 AI，AI 能修。**

### 你可能会遇到的情况

| 现象                                      | 这是什么                             | 你该怎么做                                  |
| ----------------------------------------- | ------------------------------------ | ------------------------------------------- |
| 课件上公式显示为 `$C=Q/U$` 而不是漂亮公式 | HTML 块内的 KaTeX 需要空行才能渲染   | 截图或描述给 AI："第 X 页的公式没渲染出来"  |
| SVG 图形里的文字大得离谱                  | UnoCSS 样式污染了 SVG                | 告诉 AI："SVG 里的字变大了"                 |
| 某个图标只显示方块或空白                  | 图标名拼写错误或未安装               | 告诉 AI 哪一页的哪个图标不显示              |
| 点击页面中间没反应，不会翻页              | **这不是 bug**，是 Slidev 的预期行为 | 用键盘 ← → 或翻页笔翻页（见下方"导航指南"） |
| 运行 `pnpm dev` 后弹出一堆红字报错        | 可能是依赖没装好或语法错误           | 把错误信息截图发给 AI                       |
| Claude Code 反复弹出授权提示              | 权限设置不完整                       | 告诉 AI："帮我把常用命令加到授权白名单"     |

### 核心心态

**你不是程序员，你是指挥官。** 你只负责告诉 AI"哪里不对"和"我想要什么效果"，AI 负责修。本项目已经过验证，所有已知的坑（KaTeX 空行、SVG 字号、UnoCSS 污染）AI 都认识，能快速修复。

---

## 导航指南（AI 必须向教师解释）

**这是最重要的 UX 说明，请确保教师理解。**

### Slidev 的翻页机制（不是 bug！）

Slidev 的设计理念是：**内容区域留给交互组件（滑块、按钮），不响应点击翻页。** 因此：

| 翻页方式           | 说明                                                 |
| ------------------ | ---------------------------------------------------- |
| **键盘 ← →**       | **主要方式。** 左箭头 = 上一页，右箭头 = 下一页      |
| **翻页笔**         | 插入电脑 USB 即可，翻页笔的上/下页按钮映射到键盘 ← → |
| **鼠标移到左下角** | 会出现 Slidev 工具栏，点击箭头翻页                   |
| **触摸屏**         | 在屏幕两侧区域左右滑动可翻页（中间留给交互式组件）   |
| **点击页面中央**   | ❌ 不会翻页！这是预期行为，不是 bug                  |

> **AI 注意：如果教师反馈"点击页面中间翻不了页"，不要试图 hack Slidev 的点击行为。** 这是 Slidev 的核心设计决策。引导教师使用键盘、翻页笔、或左下角工具栏。

### 进入全屏 / 演示模式

- 按 `F` 键进入全屏
- 按 `Esc` 退出全屏
- 按 `O` 键进入演讲者概览模式（可以看到下一页预览和备注）

---

## 常见问题排查（AI 参考）

### Q1: SVG 内文字巨大

**原因：** UnoCSS 的属性选择器覆盖了 SVG 的 `font-size` 属性。`html { font-size: 22px }` 级联到 SVG，且 UnoCSS 的 `[font-size~="11"]` 会匹配 SVG 的 `font-size="11"` 属性并覆盖。
**解决：** 确认 `style.css` 中包含 SVG 保护代码：

```css
svg {
  font-size: 16px;
}
svg [font-size] {
  font-size: unset !important;
}
```

### Q2: HTML `<div>` 内的 LaTeX 不渲染

**原因：** KaTeX 只处理 Markdown 段落，不处理 HTML 块内的内容。`$...$` 在 `<div>` 内部不会被 KaTeX 识别。
**解决：** 在 `$...$` 所在段落前后加空行，使其被识别为 Markdown 段落：

```html
<div class="card">$C = \frac{Q}{U}$</div>
```

> **告诉教师：** 这是个已知的格式要求，如果在 HTML 卡片里写公式没渲染出来，告诉 AI 即可，AI 知道怎么修。

### Q3: 图标不显示

**原因：** 未安装 `@iconify-json/mdi` 或使用了不存在的图标名。
**解决：** 执行 `pnpm add -D @iconify-json/mdi`，在 https://icones.js.org/collection/mdi 确认图标名是否存在。

### Q4: 封面/内页顶栏文字不切换

**原因：** `global-top.vue` 中的 `nav.currentPage` 判断逻辑问题。如果封面不是第 1 页，或者使用了非 `cover` 布局。
**解决：** 确认封面页是 slides.md 中的第 1 页（`currentPage === 1`），且封面使用 `layout: cover`。

### Q5: 点击页面空白处无法翻页 ← 最常见的问题

**原因：** Slidev 的默认行为：只响应留黑区域（非内容区）的点击。页面中央的内容区不响应点击翻页。
**解决：** **绝对不要试图修改这个行为。**

- 这是 Slidev 的有意设计：内容区留给交互式组件的点击，翻页由键盘/翻页笔/左下角工具栏负责
- 告诉教师使用键盘 ← →、翻页笔、或鼠标移到左下角点击箭头
- 如果教师非要点击翻页 → 引导他们习惯键盘，这是 Slidev 的标准用法

### Q6: `pnpm dev` 启动后浏览器一片空白

**原因：** 通常是 slides.md 语法错误导致页面无法渲染。
**解决：** 检查终端输出（运行 `pnpm dev` 的那个窗口），Slidev 会打印具体错误位置。常见原因：

- frontmatter 格式错误（YAML 缩进问题）
- HTML 标签未闭合
- Vue 组件导入路径错误

### Q7: 运行时修改 `style.css` 不生效

**原因：** Slidev 的 HMR 对 `style.css` 的某些更改可能需要手动刷新。
**解决：** 在浏览器中按 `Cmd+Shift+R`（Mac）或 `Ctrl+Shift+R`（Windows）强制刷新。

### Q8: 构建后的静态网页部署到服务器，图片/资源 404

**原因：** Slidev 默认以根路径 `/` 构建。如果部署在子目录（如 `/courseware/`），需要配置 base path。
**解决：** 在 `slides.md` 的 frontmatter 中添加 `base: /courseware/`（替换为实际子目录路径）。

### Q9: Claude Code 反复弹出授权提示

**原因：** 未在 `.claude/settings.json` 中预授权常用命令。
**解决：** 回到第 3 步，确认 `.claude/settings.json` 的 `permissions.allow` 列表中包含了所有常用命令。如果遇到新的授权请求，告诉用户"接下来 Claude Code 会弹出授权提示，请点击 Allow，然后勾选 'Always allow'"。

---

## 关键设计原则总结

| 原则                  | 做法                                               |
| --------------------- | -------------------------------------------------- |
| **教室后排可见**      | `html { font-size: 22px }`，最小字号不低于 0.65rem |
| **大屏幕适配**        | 全宽 SVG + viewBox，不要用固定 px 尺寸             |
| **先问后答**          | 每页先用 v-click 隐藏答案，点击再出现              |
| **玻璃态**            | `backdrop-filter: blur(16px)` + 半透明背景         |
| **不要 emoji**        | 用 `<mdi-xxx />` MDI 图标                          |
| **不要 Google Fonts** | `fonts.provider: none`，使用系统字体栈             |
| **不要页码**          | `global-bottom.vue` 覆盖默认底栏                   |
| **顶栏过渡**          | Vue `<Transition>` 实现封面→内页文字切换           |
| **SVG 保护**          | `svg [font-size] { font-size: unset !important }`  |
| **内容准确性**        | 组件内用真实学科公式计算，不是拍脑袋的数值         |
| **翻页 ≠ 点击中央**   | 这是 Slidev 的预期设计，用键盘/翻页笔/左下角工具栏 |
| **教师是用户**        | 用户无编程经验，AI 负责修 bug，不要让用户自己排查  |
