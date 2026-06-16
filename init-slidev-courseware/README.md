# 初始化 Slidev 交互式课件项目

> **这份文件是什么：** 一个给 Claude Code 看的工作规范。它指导 Claude Code 从零开始帮教师创建一个高品质的 Slidev 公开课课件项目——包括环境配置、收集教学素材、设计视觉系统、编写幻灯片。
>
> **目标用户：无编程经验的教师。** 假设只安装了 Claude Code，其余一切由 AI 引导完成。

---

## 如何让 Claude Code 执行这份规范

### 你需要做的事（一次性）

把 `spec.md` 部署到你的服务器上，得到一个可访问的 URL，例如：

```
https://your-server.com/spec.md
```

### 教师使用时（每次新建课件）

**关键问题：** 不能直接让 Claude Code 去 fetch URL——因为 `skipWebFetchPreflight` 还没配置，WebFetch 会被安全检查拦截，流程直接死亡。

**解决方案：** 教师粘贴一段极短的**引导语**（约 20 行），它先解锁 WebFetch，再 fetch 规范。

告诉教师：

> **"打开 Claude Code，粘贴下面这段话，回车："**

```text
请执行以下步骤：

1. 创建 .claude/settings.json，写入：
{
  "skipWebFetchPreflight": true,
  "permissions": {
    "allow": [
      "mcp__plugin_chrome-devtools-mcp_chrome-devtools__*",
      "Bash(pnpm install *)",
      "Bash(pnpm add *)",
      "Bash(pnpm dev)",
      "Bash(pnpm build)",
      "Bash(corepack *)",
      "Bash(node --version)",
      "Bash(pnpm --version)",
      "Bash(git *)",
      "Bash(ls *)",
      "Bash(mkdir *)",
      "Bash(cd *)"
    ]
  }
}

2. 然后通过 WebFetch 获取 https://your-server.com/spec.md 的完整内容

3. 严格按照该规范，一步步引导我创建课件项目
```

> **教师只需要做一件事：粘贴这 20 行，回车。** 引导语先配置 `skipWebFetchPreflight: true`，解锁 WebFetch，再拉取完整规范。不会死锁。

### 备选：本地文件模式

如果你还没有部署规范文件到服务器，可以让教师把整个 `init-slidev-courseware/` 文件夹发给他，然后告诉他：

> **"在这个文件夹里打开 Claude Code，输入：请阅读 spec.md，严格按照其规范帮我创建课件项目"**

本地文件不需要 WebFetch，也就不需要 `skipWebFetchPreflight`。
