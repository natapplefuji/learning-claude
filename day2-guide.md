# Day 2 — Frontend + Integration + Polish

> 🎯 **เป้าหมายวันนี้**: สร้าง frontend + เชื่อม MCP เพิ่ม + setup hooks + capstone

**เวลา**: 8 ชั่วโมง (รวมพัก) | **Modules**: 5

---

## ☀️ Warmup (15 min)

📋 **Prompt #24 — Recap วันแรก**

```
We're starting Day 2. Read the current state of the project (CLAUDE.md files, .claude/ folder, what's in backend/). Tell me back:

1. What's working
2. What's incomplete from Day 1
3. What you'd suggest we tackle first today before moving to frontend

Be honest about gaps.
```

---

## Module 6 — Frontend with Skills + Subagents (2 hr)

> **Learning goal**: ใช้ skill กับงาน frontend, multi-agent workflow

### Step 6.1 — Scaffold frontend

📋 **Prompt #25**

```
/model sonnet

In the frontend/ folder, scaffold a Vite + React + TypeScript project.

Setup:
- Vite, React 18, TypeScript strict
- Tailwind CSS
- shadcn/ui (initialize with default theme, neutral base color)
- TanStack Query
- React Router v6
- Vitest + RTL

Don't run create-vite — generate the files yourself based on what each tool needs. Then `npm install` and verify `npm run dev` works.
```

> 💡 **เกร็ด**: บังคับ generate เองเพื่อให้ Claude เข้าใจโครงสร้างจริงๆ ไม่ใช่ pattern จาก template

---

### Step 6.2 — สร้าง skill: react-component

📋 **Prompt #26**

```
Create .claude/skills/react-component/SKILL.md.

This skill activates when I ask to create a new React component.

Process must be:
1. Determine if it's a presentation component or container
2. Create file: src/components/{ComponentName}/{ComponentName}.tsx
3. Define Props interface with explicit types (no `any`)
4. Implement with hooks, no class components
5. If data-fetching: use TanStack Query, never fetch in component
6. Add Storybook-style file: {ComponentName}.stories.tsx (basic stories: Default, Loading, Error)
7. Add test file: {ComponentName}.test.tsx with at least: renders without crashing, key interactions
8. Export from src/components/index.ts

Conventions:
- PascalCase file names
- Tailwind only, no inline styles
- forwardRef when component might be used with refs
- Always handle loading and error states for data components
- Use shadcn/ui primitives when possible (Button, Input, Card, etc.)

Add a checklist at the end: what files should exist after, what should pass.
```

---

### Step 6.3 — สร้าง subagent: api-client-generator

📋 **Prompt #27**

```
Create .claude/agents/api-client-generator.md.

This subagent generates TypeScript types and TanStack Query hooks from a backend endpoint description.

Input: HTTP method + path + request/response shape

Output:
1. Types in src/lib/api/types.ts (or feature-specific file)
2. Fetch function in src/lib/api/{feature}.ts
3. TanStack Query hook in src/hooks/use{Feature}.ts

It must:
- Match types exactly with backend DTOs (ask user to share or grep backend/src for the DTO)
- Use the apiClient (with auth token) from src/lib/api/client.ts
- Include error handling matching our error response shape

Frontmatter:
- name: api-client-generator
- description: generate frontend API clients from backend endpoints
- model: claude-haiku-4-5 (this is mostly mechanical translation)
- tools: Read, Write, Edit, Grep
```

> 💡 **Model choice**: Haiku ตรงนี้ — เป็น mechanical translation ไม่ต้องคิดมาก ประหยัด

---

### Step 6.4 — Build pages ใช้ทุกอย่างพร้อมกัน

📋 **Prompt #28**

```
We're going to build the Dashboard page now. This is a complex page so we'll coordinate multiple agents.

Plan first (use plan mode):

1. Auth context + login page (use react-component skill)
2. API client for tasks (dispatch api-client-generator)
3. Kanban board component with 3 columns (use react-component skill)
4. Drag-drop using @dnd-kit
5. Task card component (use react-component skill)

For each step, decide: do it in main session, or dispatch a subagent? Justify.

Then execute the plan.
```

> 💬 **อภิปราย**: ดูว่าเลือก dispatch subagent ตรงไหน ทำไมไม่ทำใน main เลย

---

### ✅ Module 6 Checkpoint

- [ ] Frontend dev server รัน
- [ ] Login page + Dashboard ใช้งานได้กับ backend จริง
- [ ] react-component skill ใช้งานได้ผ่านการสั่ง "create a new component for X"
- [ ] api-client-generator subagent generate types ตรงกับ backend จริง

---

## Module 7 — MCP Playwright (1.5 hr)

> **Learning goal**: ติดตั้ง MCP เพิ่ม, ใช้ E2E จริง

### Step 7.1 — ติดตั้ง Playwright MCP

ออกจาก Claude Code:

```bash
claude mcp add playwright -- npx -y @playwright/mcp@latest
```

เปิดใหม่ ตรวจสอบ:

📋 **Prompt #29**

```
/mcp
```

ควรเห็นทั้ง postgres และ playwright

---

### Step 7.2 — ให้ Claude run E2E เอง

📋 **Prompt #30**

```
Use the playwright MCP to verify our app works end-to-end. Steps:

1. Open http://localhost:5173
2. Click "Register" 
3. Fill in: email, password, name
4. Submit
5. Verify redirect to dashboard
6. Click "+ New Task"
7. Fill task title "Try Claude Code MCP"
8. Submit
9. Verify task appears in "Todo" column
10. Drag it to "Done" column
11. Verify it's now in Done

Take screenshots at each major step. If anything fails, debug and fix.
```

> 💡 **Wow moment**: Claude จะเปิด browser, click จริง, เห็น UI จริง ผู้เรียนจะตื่นเต้น และจะเข้าใจ MCP คืออะไรชัดเจน

---

### Step 7.3 — สร้าง persistent E2E tests

📋 **Prompt #31**

```
Now generate persistent Playwright tests in e2e/tests/. Cover the 3 happy paths from requirements.md:

1. register → login → create task → mark done
2. login → filter tasks by priority
3. login → comment on task → see in activity feed

Use page object pattern. Tests should be independent (each starts fresh).

After writing, run them via `npx playwright test` and ensure all pass.
```

---

### ✅ Module 7 Checkpoint

- [ ] Playwright MCP ทำงาน
- [ ] เห็น Claude คลิกผ่าน UI จริงๆ ครั้งหนึ่ง
- [ ] E2E tests 3 paths ผ่านทั้งหมด

---

## Module 8 — MCP GitHub Workflow (1 hr)

> **Learning goal**: ใช้ MCP สำหรับ git/GitHub flow

### Step 8.1 — ติดตั้ง GitHub MCP

ต้องมี GitHub Personal Access Token (PAT) — สร้างจาก github.com/settings/tokens

```bash
claude mcp add github -e GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx -- npx -y @modelcontextprotocol/server-github
```

---

### Step 8.2 — Push project ขึ้น GitHub

📋 **Prompt #32**

```
1. Initialize git in the project root if not already
2. Create a sensible .gitignore for Java + Node + Docker
3. Use the github MCP to create a new repo named "taskflow-workshop" (private)
4. Add it as remote and push current code

Show me the new repo URL.
```

---

### Step 8.3 — Issue-driven workflow

📋 **Prompt #33**

```
We need a "Comments" feature. Use github MCP to create 3 issues:

1. "feat: add POST /api/tasks/{id}/comments endpoint"
2. "feat: add comment list UI in TaskDetail page"
3. "test: add E2E test for comment flow"

Each issue must have:
- Clear acceptance criteria
- Technical notes pointing to relevant files
- "good first task" label on issue 3

Then show me the issue URLs.
```

---

### Step 8.4 — Implement → PR ผ่าน MCP

📋 **Prompt #34**

```
Pick issue #1. Implement it on a new branch `feat/comments-api`. After it works:

1. Commit with conventional commit message
2. Push branch
3. Use github MCP to open a PR to main
4. PR description should: link the issue, list changes, list how to test
5. Self-review: read the diff, leave at least 2 review comments on your own PR (things to improve)

Show me PR URL.
```

> 💡 **เกร็ด**: การ self-review ฝึกผู้เรียนคิดเหมือน reviewer ก่อน push

---

### ✅ Module 8 Checkpoint

- [ ] GitHub MCP ทำงาน
- [ ] มี repo + issues + PR ที่ Claude สร้าง
- [ ] PR description มีคุณภาพ (ไม่ใช่ placeholder)

---

## Module 9 — Hooks, Settings, Plugins (2 hr)

> **Learning goal**: lock down workflow ด้วย hooks, customize settings, ใช้ plugin

### Step 9.1 — settings.json ของ project

📋 **Prompt #35**

```
Create .claude/settings.json with these:

1. permissions.allow:
   - "Bash(mvn:*)"
   - "Bash(npm:*)"
   - "Bash(docker:*)"
   - "Bash(git:*)"
   - "Read", "Write", "Edit"

2. permissions.deny:
   - "Bash(rm -rf:*)"
   - "Bash(sudo:*)"

3. env:
   - DATABASE_URL pointing to local Postgres

4. model: claude-sonnet-4-6 (default — Opus only when explicitly asked)

Show me the file and explain each section. Also show me how to override at user level vs project level.
```

---

### Step 9.2 — Pre-tool-use hook (safety)

📋 **Prompt #36**

```
Create .claude/hooks/pre-tool-use.sh that:

- Triggers on Bash tool calls
- Blocks any command containing: "rm -rf /", "DROP DATABASE", "FORCE PUSH", "git push --force", "kubectl delete"
- Exits with non-zero on block, prints clear message

Make it executable. Then test it: try to run a forbidden command via Claude, see it get blocked.

Update settings.json to register this hook for PreToolUse event.
```

---

### Step 9.3 — Pre-commit hook (quality)

📋 **Prompt #37**

```
Create .claude/hooks/before-commit.sh:

Backend (if backend files changed):
- Run `mvn spotless:apply` (or google-java-format)
- Run `mvn test -DfailIfNoTests=false` 
- Block commit if tests fail

Frontend (if frontend files changed):
- Run `npm run lint --fix`
- Run `npm test -- --run`
- Block commit if tests fail

Make it smart: detect what changed via `git diff --cached --name-only`, only run relevant checks.

Register as Stop hook.
```

> 💬 **อภิปราย**: ทำไม Stop hook ดีกว่า PostToolUse สำหรับงาน commit?

---

### Step 9.4 — Custom slash command

📋 **Prompt #38**

```
Create .claude/commands/review.md — a custom slash command /review.

When I run `/review`, Claude should:
1. Run `git diff main` to see changes on current branch
2. Check against backend/CLAUDE.md and frontend/CLAUDE.md conventions
3. Look for: missing tests, hardcoded values, TODO without ticket, security issues
4. Output a markdown review with PASS/CONCERN/BLOCK for each finding

After creating, test by running `/review`.
```

---

### Step 9.5 — Plugin

> 💬 **อภิปราย**: plugin คือ collection ของ skills + agents + commands ที่แชร์กันได้ ลองดู community plugin

📋 **Prompt #39**

```
Search the Claude Code marketplace or docs for available plugins. Find one that fits our stack (e.g., a Java/Spring plugin or testing plugin). Install it via:

claude plugin install <plugin-name>

Then demonstrate one feature it adds. If you can't find a good one, create our own mini-plugin: bundle our java-endpoint skill + test-writer agent into a plugin folder we could share with another team.
```

---

### ✅ Module 9 Checkpoint

- [ ] settings.json ครบ permissions, env, model
- [ ] Hook block dangerous command ได้จริง
- [ ] Pre-commit hook ทำงาน — เคยถูก block อย่างน้อย 1 ครั้ง
- [ ] Custom command `/review` ใช้ได้
- [ ] เข้าใจ plugin คืออะไร

---

## Module 10 — Capstone Challenge (1.5 hr)

> **Learning goal**: ใช้ทุกอย่างที่เรียนมา ภายใต้ time pressure

### The challenge

> เพิ่ม feature **"Task Labels"** ให้สมบูรณ์ ภายใน 60 นาที

### Spec

- Task มี labels ได้หลาย label (many-to-many)
- Label มี name + color
- เพิ่ม endpoint:
  - `GET /api/labels` — list labels
  - `POST /api/labels` — create label
  - `POST /api/tasks/{id}/labels/{labelId}` — attach label
  - `DELETE /api/tasks/{id}/labels/{labelId}` — detach label
- Frontend:
  - Filter dashboard by label
  - แสดง label badges บน task card
  - dropdown ใน task detail สำหรับ add/remove label
- Test: 1 backend integration test + 1 E2E test

### Rules

ผู้เรียนต้อง:

1. ใช้ **plan mode** ก่อนเขียน code
2. ใช้ skills (java-endpoint, react-component) ที่สร้างเอง
3. ใช้ **subagents** อย่างน้อย 2 ครั้ง
4. ใช้ **MCP Postgres** ตรวจสอบ schema หลัง migration
5. ใช้ **MCP Playwright** verify feature
6. **Commit ผ่าน hook** ทั้งหมด (lint pass, tests pass)
7. **Open PR** ผ่าน GitHub MCP
8. เปลี่ยน **model** ตาม phase: Opus (plan) → Sonnet (code) → Haiku (mechanical work)

### Time budget

| Phase | Time | Activity |
|---|---|---|
| 0:00 - 0:10 | 10m | Plan mode (Opus) |
| 0:10 - 0:25 | 15m | Backend (Sonnet + skill) |
| 0:25 - 0:35 | 10m | Frontend types/hooks (Haiku, dispatched subagent) |
| 0:35 - 0:50 | 15m | Frontend UI (Sonnet + skill) |
| 0:50 - 0:55 | 5m | E2E + Playwright MCP verify |
| 0:55 - 1:00 | 5m | Commit + PR + review |

---

## 🏆 Workshop Wrap-up (30 min)

### Show & Tell

แต่ละคน present:
1. Capstone feature ที่ทำเสร็จ
2. ขั้นตอนที่ใช้ feature ของ Claude Code อะไรบ้าง
3. ปัญหาที่เจอ + วิธีแก้
4. สิ่งที่อยากลองใน 1 สัปดาห์ข้างหน้า

### Final retrospective prompt

📋 **Prompt #40**

```
Look at our entire project. Use git log and the files in .claude/ to assess:

1. Which feature of Claude Code did we use most effectively?
2. Which one did we underutilize?
3. If you were starting a new project tomorrow, what would your initial .claude/ setup look like based on what we learned?
4. What's one Claude Code anti-pattern we accidentally fell into during this workshop?

Be specific and honest.
```

---

## 📚 Take-home resources

- โค้ดทั้งหมดบน GitHub repo ของแต่ละคน
- `.claude/` template สำหรับ project ใหม่ (export จาก capstone)
- รายการ MCP servers ที่น่าลองเพิ่ม:
  - `@modelcontextprotocol/server-filesystem` — file ops ที่ปลอดภัย
  - `@modelcontextprotocol/server-slack` — โพสต์ status ทีม
  - `@modelcontextprotocol/server-sentry` — debug error จริง
- คลิป record ของ session ทั้งหมด

---

## 🎓 จบแล้วผู้เรียนควรทำอะไรต่อ

**สัปดาห์ที่ 1**: นำ template `.claude/` ไปใช้กับ project จริงที่ทำงาน  
**สัปดาห์ที่ 2**: สร้าง skill เฉพาะของ project ตัวเอง  
**สัปดาห์ที่ 3**: ลองสร้าง MCP server เอง  
**สัปดาห์ที่ 4**: แชร์ plugin ของทีมให้คนอื่นในบริษัทใช้
