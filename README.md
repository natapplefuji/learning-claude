# Claude Code Mastery Workshop

**ระยะเวลา**: 2 วัน | **ระดับ**: Developer ที่เขียน Java/Spring Boot + React เป็นแล้ว | **รูปแบบ**: Hands-on, เรียนผ่านการสร้าง project จริง

---

## Workshop คืออะไร

Workshop นี้สอนการใช้ **Claude Code** ทุก feature อย่างมีเหตุผล — ไม่ใช่แค่ลอง feature แล้วผ่านไป แต่เรียนรู้ว่า *เมื่อไหร่ควรใช้อะไร และทำไม*

ผู้เรียนจะสร้าง **TaskFlow** ซึ่งเป็น full-stack task management system ตั้งแต่ `mkdir taskflow` จนถึง working app ที่มี tests และ CI — โดยใช้ Claude Code เป็น primary tool ตลอด

---

## ไฟล์ในชุดนี้

| ไฟล์ | สำหรับ | คำอธิบาย |
|---|---|---|
| `requirements.md` | ผู้เรียน | โจทย์ project — แจกวันแรก |
| `day1-guide.md` | ผู้เรียน | Step-by-step วันแรก พร้อม prompt ที่ copy ได้เลย |
| `day2-guide.md` | ผู้เรียน | Step-by-step วันสอง + capstone challenge |
| `instructor-notes.md` | ผู้สอน | Expected outputs, red flags, recovery tips ทุก step |
| `claude-template.zip` | ผู้เรียน | `.claude/` starter template — เอาไปใช้ต่อหลัง workshop |

---

## สิ่งที่ผู้เรียนจะได้เรียน

### Day 1 — Foundation + Backend

```
Module 1  Setup & memory system      /init · CLAUDE.md hierarchy · session management
Module 2  Plan mode + Skills         plan before code · java-endpoint skill · /model switching
Module 3  Context management         /context · /compact · /clear · token optimization
Module 4  MCP Postgres               ต่อ DB จริง · query schema · generate migration
Module 5  Subagents                  parallel work · test-writer · schema-reviewer
```

### Day 2 — Frontend + Integration

```
Module 6  Frontend + Skills          react-component skill · api-client subagent · multi-agent
Module 7  MCP Playwright             E2E ผ่าน browser MCP · persistent test suite
Module 8  MCP GitHub                 issue-driven workflow · PR ผ่าน Claude
Module 9  Hooks, Settings, Plugins   settings.json · safety hooks · /review /plan /ship
Module 10 Capstone                   สร้าง feature ใหม่ใน 60 นาทีโดยใช้ทุก feature
```

---

## Tech stack ของ project

```
Backend    Java 21 · Spring Boot 3.x · PostgreSQL 16 · Maven · JUnit 5
Frontend   React 18 · TypeScript · Vite · TanStack Query · Tailwind · shadcn/ui
MCP        Postgres · GitHub · Playwright
Infra      Docker Compose · GitHub Actions
```

---

## ก่อนเริ่ม Workshop

### ติดตั้ง

```bash
# Claude Code
npm install -g @anthropic-ai/claude-code

# ตรวจ Java
java -version          # ต้องการ 21+

# ตรวจ Node
node -v                # ต้องการ 20+

# ตรวจ Docker
docker ps              # ต้อง running

# ตรวจ Maven
mvn -version           # ต้องการ 3.9+
```

### Accounts ที่ต้องมี

- **Anthropic API key** — ขอจาก [console.anthropic.com](https://console.anthropic.com)
- **GitHub account** + Personal Access Token (scope: `repo`)

### ตั้งค่า API key

```bash
export ANTHROPIC_API_KEY=sk-ant-...
# หรือใส่ใน ~/.bashrc / ~/.zshrc
```

---

## วิธีใช้ guides

แต่ละ step ใน `day1-guide.md` และ `day2-guide.md` มีโครงสร้างแบบนี้:

```
1. Terminal command (ถ้ามี) — รันก่อน
2. Prompt — copy แล้ว paste ลง Claude Code ได้เลย ไม่ต้องแก้
3. Expected outcome — รู้ว่าควรได้อะไร
4. Checkpoint — ยืนยันก่อนไป step ถัดไป
```

**ไม่ต้องรีบ** — ทุก checkpoint สำคัญ ข้ามแล้วปัญหาจะตามมาใน step หลัง

---

## Claude Code คำสั่งที่ใช้บ่อย

| คำสั่ง | ทำอะไร |
|---|---|
| `claude` | เปิด Claude Code ใน folder ปัจจุบัน |
| `/init` | สร้าง CLAUDE.md จาก project structure ที่มี |
| `/context` | ดูว่าใช้ token ไปเท่าไหร่แล้ว |
| `/compact` | ย่อ conversation history ลง คง context สำคัญไว้ |
| `/clear` | ล้าง conversation (CLAUDE.md ยังอยู่) |
| `/model` | เปลี่ยน model (haiku / sonnet / opus) |
| `/mcp` | ดู MCP servers ที่เชื่อมต่ออยู่ |
| `/review` | รัน custom review command (ตั้งใน workshop) |
| `/plan` | เข้า planning mode (ตั้งใน workshop) |
| `/ship` | commit + PR workflow (ตั้งใน workshop) |
| `Shift+Tab` × 2 | สลับ plan mode (read-only) |
| `Escape` | หยุด Claude กลางคัน |

---

## MCP Servers — วิธีติดตั้ง

ติดตั้ง **นอก** Claude Code session (ปิดก่อน แล้วเปิดใหม่หลังติดตั้ง)

```bash
# Postgres
claude mcp add postgres -- \
  npx -y @modelcontextprotocol/server-postgres \
  "postgresql://taskflow:taskflow_dev@localhost:5432/taskflow"

# GitHub
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx -- \
  npx -y @modelcontextprotocol/server-github

# Playwright
claude mcp add playwright -- \
  npx -y @playwright/mcp@latest
```

ตรวจสอบด้วย `/mcp` ใน Claude Code

---

## Model Selection Guide

เลือก model ให้เหมาะกับงาน — อย่าใช้ Opus ตลอด

| งาน | Model | เหตุผล |
|---|---|---|
| Design, planning, architecture | `opus` | ต้องการ reasoning ลึก |
| เขียน feature, implement | `sonnet` | balance คุณภาพ/ราคา |
| Generate boilerplate, tests, docs | `haiku` | mechanical task, ถูกกว่า 10x |
| Debug, review | `sonnet` | ต้องการ context แต่ไม่ต้อง opus |

```bash
/model haiku     # สลับไป Haiku
/model sonnet    # กลับมา Sonnet (default)
/model opus      # ใช้เฉพาะ plan mode
```

---

## `.claude/` Template

`claude-template.zip` ใน workshop นี้มี starter template ที่ใช้ได้กับ project ใหม่ทุกตัว

```
.claude/
├── settings.json              default model, permissions, hooks
├── skills/
│   ├── api-endpoint/          generate REST endpoint (Java/Spring)
│   ├── frontend-component/    generate React component
│   └── migration/             create DB migration
├── agents/
│   ├── test-writer.md         เขียน tests ให้ implementation ที่เพิ่งทำ
│   ├── code-reviewer.md       review diff ก่อน commit
│   └── doc-writer.md          update README/ADR/docstrings
├── hooks/
│   ├── pre-tool-use.sh        block คำสั่งอันตราย
│   └── before-commit.sh       lint + test ก่อน commit
└── commands/
    ├── review.md              /review — self-review uncommitted changes
    ├── plan.md                /plan — structured planning session
    └── ship.md                /ship — commit + push + PR flow
```

วิธีใช้:

```bash
# copy ไปยัง project ใหม่
cp -r .claude /path/to/new-project/
cp CLAUDE.md.example /path/to/new-project/CLAUDE.md

# แก้ CLAUDE.md placeholder
# แล้วเปิด claude และทดสอบ
claude
```

---

## Budget ประมาณการ

ตลอด 2 วัน ผู้เรียน 1 คนใช้ API ประมาณ:

| Model | Tokens | ค่าใช้จ่ายประมาณ |
|---|---|---|
| Sonnet | 2–3M tokens | ~$30–45 |
| Opus | 200–400K tokens | ~$15–30 |
| Haiku | 500K–1M tokens | ~$1–2 |
| **รวม** | | **~$50 ต่อคน** |

---

## Capstone Challenge (Day 2, Module 10)

เพิ่ม feature **"Task Labels/Tags"** ภายใน 60 นาที — ใช้ทุก feature ที่เรียนมา

```
Rules:
  ✓ plan mode ก่อน implement
  ✓ ใช้ skills อย่างน้อย 2 ครั้ง
  ✓ dispatch subagents อย่างน้อย 2 ครั้ง
  ✓ MCP Postgres verify schema
  ✓ MCP Playwright verify feature
  ✓ commit ผ่าน hooks (lint + tests pass)
  ✓ open PR ผ่าน GitHub MCP
  ✓ สลับ model ตาม phase: Opus → Sonnet → Haiku
```

---

## Success Criteria

จบ workshop แล้วผู้เรียนต้องทำได้:

- [ ] เริ่ม project ใหม่ด้วย `claude` แล้วให้ Claude เข้าใจ context ทันทีผ่าน CLAUDE.md
- [ ] อธิบายได้ว่าทำไมใช้ Haiku ตรงไหน, Sonnet ตรงไหน, Opus ตรงไหน
- [ ] สร้าง skill ของตัวเองที่ generate code ซ้ำได้สม่ำเสมอ
- [ ] ต่อ MCP server ทั้ง 3 ตัวและใช้ได้จริง
- [ ] ตั้ง hooks ที่ block งานอันตรายและ enforce quality
- [ ] เพิ่ม feature ใหม่ใน project ที่ไม่รู้จักมาก่อน ภายใน 1 ชั่วโมง

---

## หลังจบ Workshop

**สัปดาห์ที่ 1** — เอา `claude-template.zip` ไปใช้กับ project จริงที่ทำงาน  
**สัปดาห์ที่ 2** — เขียน skill เฉพาะของ project ตัวเอง  
**สัปดาห์ที่ 3** — ลองสร้าง MCP server เอง  
**สัปดาห์ที่ 4** — แชร์ `.claude/` template ให้ทีม

---

## Resources

- [Claude Code Docs](https://docs.claude.com/en/docs/claude-code)
- [MCP Server Registry](https://github.com/modelcontextprotocol/servers)
- [Anthropic Prompt Engineering Guide](https://docs.claude.com/en/docs/build-with-claude/prompt-engineering/overview)
- [Claude API Reference](https://docs.anthropic.com/en/api)
