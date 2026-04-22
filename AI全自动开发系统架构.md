# AI 全自动开发系统架构

## 🏭 Dark Factory 开发模式

### 核心理念：「三不原则」

- ❌ No human writes code — 人不写代码
- ❌ No human reviews code — 人不审代码
- ❌ No human tests code — 人不测代码

完全依靠模型能力和使用者对 AI 的掌控力。

### 🎯 这不是为了好玩，而是未来主流的开发方式

当模型生产高质量代码的速率越来越高，如果我们还坚持手写代码、阅读代码，只会成为 AI 的瓶颈。让模型有效地工作得更久，才是未来工程师的核心竞争力。

---

## ⚙️ AI 全自动开发系统架构

这套系统的本质就是几个文件，但设计精巧：

### 1️⃣ task.json —— 任务列表

AI 的「待办清单」，包含所有开发任务：

```json
{
  "tasks": [
    {
      "description": "实现用户登录功能",
      "passes": false
    }
  ]
}
```

**工作方式：**

- AI 每次从中领取一个 `passes: false` 的任务
- 开发完成后标记为 `passes: true`
- 其他 AI 同事知道项目进展

### 2️⃣ progress.txt —— 工作日志

AI 的「工作周报」，记录：

- 每次完成的任务详情
- 测试过程和结果
- 遇到的问题和解决方案

```text
## 2024-03-20 - Task: 实现登录功能

### What was done:
- 创建了登录页面
- 接入了 Supabase Auth

### Testing:
- 浏览器测试通过
- 构建成功

### Notes:
- 需要配置真实的环境变量
```

### 3️⃣ claude.md —— AI 员工手册

定义开发流程规范，强制 AI 按标准工作。

**6 步标准流程：**

1. 在 `task.json` 里领任务
2. 开发实现
3. 必须测试和验证（build/lint/浏览器实际操作）
4. 更新 `progress.txt`
5. 更新 `task.json` 标记完成
6. git 提交代码，写清楚提交信息

**为什么有效：**

- AI 通过 `task.json` 和 `progress.txt` 知道该做什么
- 每次任务后上下文清空，模型保持最佳状态
- 严格按规范执行，每次都会 commit，有问题也无所谓，可以 revert

---

## 🎯 Harness Engineering 方法论

### 核心理念

> 让模型有效地工作得更久，才是未来工程师的核心竞争力。

**为什么？**

- 模型生产高质量代码的速率越来越高
- 手写代码、阅读代码只会成为 AI 的瓶颈
- 设计良好的流程和提示词，让 AI 持续高效产出

### 实践要点

1. **清晰的任务拆分** — 每个任务足够小，可独立完成
2. **完善的测试反馈** — 让 AI 能自我验证
3. **规范的工作流程** — 通过 `claude.md` 强制执行
4. **上下文管理** — 及时清空，保持模型最佳状态

---

## 📎 附录：Harness Engineering Rules 提示词案例

````markdown
# Spring FES Video - Project Instructions

## Project Context

A video processing application with Next.js frontend.

> Note: Detailed project requirements will be added to task.json as they are defined.

---

## MANDATORY: Agent Workflow

Every new agent session MUST follow this workflow:

### Step 1: Initialize Environment

```bash
./init.sh
```

This will:
- Install all dependencies
- Start the development server at http://localhost:3000

**DO NOT skip this step.** Ensure the server is running before proceeding.

### Step 2: Select Next Task

Read `task.json` and select ONE task to work on.

Selection criteria (in order of priority):

1. Choose a task where `passes: false`
2. Consider dependencies - fundamental features should be done first
3. Pick the highest-priority incomplete task

### Step 3: Implement the Task

- Read the task description and steps carefully
- Implement the functionality to satisfy all steps
- Follow existing code patterns and conventions

### Step 4: Test Thoroughly

After implementation, verify ALL steps in the task:

**强制测试要求（Testing Requirements - MANDATORY）：**

1. **大幅度页面修改**（新建页面、重写组件、修改核心交互）：
   - **必须在浏览器中测试！** 使用 MCP Playwright 工具
   - 验证页面能正确加载和渲染
   - 验证表单提交、按钮点击等交互功能
   - 截图确认 UI 正确显示

2. **小幅度代码修改**（修复 bug、调整样式、添加辅助函数）：
   - 可以使用单元测试或 lint/build 验证
   - 如有疑虑，仍建议浏览器测试

3. **所有修改必须通过**：
   - `npm run lint` 无错误
   - `npm run build` 构建成功
   - 浏览器/单元测试验证功能正常

**测试清单：**

- [ ] 代码没有 TypeScript 错误
- [ ] lint 通过
- [ ] build 成功
- [ ] 功能在浏览器中正常工作（对于 UI 相关修改）

### Step 5: Update Progress

Write your work to `progress.txt`:

```
## [Date] - Task: [task description]

### What was done:
- [specific changes made]

### Testing:
- [how it was tested]

### Notes:
- [any relevant notes for future agents]
```

### Step 6: Commit Changes (包含 task.json 更新)

**IMPORTANT: 所有更改必须在同一个 commit 中提交，包括 task.json 的更新！**

流程：

1. 更新 `task.json`，将任务的 `passes` 从 `false` 改为 `true`
2. 更新 `progress.txt` 记录工作内容
3. 一次性提交所有更改：

```bash
git add .
git commit -m "[task description] - completed"
```

**规则：**

- 只有在所有步骤都验证通过后才标记 `passes: true`
- 永远不要删除或修改任务描述
- 永远不要从列表中移除任务
- **一个 task 的所有内容（代码、progress.txt、task.json）必须在同一个 commit 中提交**

---

## ⚠️ 阻塞处理（Blocking Issues）

**如果任务无法完成测试或需要人工介入，必须遵循以下规则：**

### 需要停止任务并请求人工帮助的情况：

1. **缺少环境配置**：
   - .env.local 需要填写真实的 API 密钥
   - Supabase 项目需要创建和配置
   - 外部服务需要开通账号

2. **外部依赖不可用**：
   - 第三方 API 服务宕机
   - 需要人工授权的 OAuth 流程
   - 需要付费升级的服务

3. **测试无法进行**：
   - 登录/注册功能需要真实用户账号
   - 功能依赖外部系统尚未部署
   - 需要特定硬件环境

### 阻塞时的正确操作：

**DO NOT（禁止）：**

- ❌ 提交 git commit
- ❌ 将 task.json 的 passes 设为 true
- ❌ 假装任务已完成

**DO（必须）：**

- ✅ 在 progress.txt 中记录当前进度和阻塞原因
- ✅ 输出清晰的阻塞信息，说明需要人工做什么
- ✅ 停止任务，等待人工介入

### 阻塞信息格式：

```
🚫 任务阻塞 - 需要人工介入

**当前任务**: [任务名称]

**已完成的工作**:
- [已完成的代码/配置]

**阻塞原因**:
- [具体说明为什么无法继续]

**需要人工帮助**:
1. [具体的步骤 1]
2. [具体的步骤 2]
...

**解除阻塞后**:
- 运行 [命令] 继续任务
```

---

## Project Structure

```
/
├── CLAUDE.md          # This file - workflow instructions
├── task.json          # Task definitions (source of truth)
├── progress.txt       # Progress log from each session
├── init.sh            # Initialization script
└── hello-nextjs/      # Next.js application
    ├── src/app/       # App Router pages
    ├── src/components/
    └── ...
```

## Commands

```bash
# In hello-nextjs/
npm run dev      # Start dev server
npm run build    # Production build
npm run lint     # Run linter
```

## Coding Conventions

- TypeScript strict mode
- Functional components with hooks
- Tailwind CSS for styling
- Write tests for new features

---

## Key Rules

1. **One task per session** — Focus on completing one task well
2. **Test before marking complete** — All steps must pass
3. **Browser testing for UI changes** — 新建或大幅修改页面必须在浏览器测试
4. **Document in progress.txt** — Help future agents understand your work
5. **One commit per task** — 所有更改（代码、progress.txt、task.json）必须在同一个 commit 中提交
6. **Never remove tasks** — Only flip `passes: false` to `true`
7. **Stop if blocked** — 需要人工介入时，不要提交，输出阻塞信息并停止
````

---

## 相关参考

- 开源 Demo：<https://github.com/SamuelQZQ/auto-coding-agent-demo>
- 主视频：<https://www.bilibili.com/video/BV15gDLBbE1p/>
- 配套视频：<https://www.bilibili.com/video/BV1zZcYz1EMy/>
- 推荐阅读：Anthropic 长程应用开发中的驾驭框架设计

> 核心经验：为项目设计出一个 feedback loop，让 AI 可以通过 playwright 访问浏览器、写单元测试等方式自我验证。只要形成 feedback loop，当前 SOTA 模型的 debug 能力比人更强。
