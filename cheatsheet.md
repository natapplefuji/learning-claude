# Claude Code Cheatsheet

> Quick reference สำหรับ Claude Code — เปิดดูได้ตลอดเวลาทำงาน

---

## 🚀 Getting started

```bash
npm install -g @anthropic-ai/claude-code   # ติดตั้ง
claude                                      # เปิดใน folder ปัจจุบัน
claude --help                              # ดู help
claude --version                           # เช็ค version
```

---

## ⌨️ Keyboard shortcuts

| Shortcut | ทำอะไร |
|---|---|
| `Shift + Tab` × 1 | สลับ auto-accept mode (ไม่ confirm ทุก tool call) |
| `Shift + Tab` × 2 | สลับ plan mode (read-only — คิดได้แต่ไม่แก้ไฟล์) |
| `Esc` | หยุด Claude กลางคัน |
| `Esc` × 2 | rewind conversation ย้อนกลับไป prompt ก่อนหน้า |
| `Ctrl + C` | exit Claude Code |
| `Ctrl + L` | clear terminal screen (ไม่ลบ context) |
| `↑` / `↓` | เลื่อน prompt history |
| `Ctrl + R` | ค้นหา prompt history |
| `\` ก่อน Enter | newline ใน prompt (multi-line) |

---

## 🔧 Slash commands — built-in

| Command | ทำอะไร |
|---|---|
| `/init` | scan project แล้วสร้าง CLAUDE.md ให้ |
| `/context` | ดูว่า context window ใช้ไปเท่าไหร่ + อะไรกินมาก |
| `/compact` | summarize conversation ลง คง context สำคัญไว้ |
| `/compact <focus>` | compact โดยเน้นเรื่องที่ระบุ เช่น `/compact focus on auth feature` |
| `/clear` | ล้าง conversation ทั้งหมด (CLAUDE.md ยังอยู่) |
| `/model` | เปลี่ยน model — `/model sonnet`, `/model opus`, `/model haiku` |
| `/mcp` | ดู MCP servers + tools ที่ใช้ได้ |
| `/agents` | จัดการ subagents (list, create, edit) |
| `/permissions` | ดู/แก้ permissions ของ tools |
| `/cost` | ดูค่าใช้จ่าย session ปัจจุบัน |
| `/login` | login Anthropic account |
| `/logout` | logout |
| `/doctor` | check installation + diagnostic |
| `/help` | ดู commands ทั้งหมด |
| `/exit` หรือ `/quit` | ออกจาก Claude Code |

---

## 🎯 Mode indicators

```
default     ทำงานปกติ — ขอ confirm tool ที่อันตราย
auto-accept ไม่ขอ confirm (ใช้กับงานที่ trust แล้ว)
plan        read-only — คิดได้ แก้ไม่ได้ (ใช้ design)
```

ดู mode ปัจจุบันที่มุมล่างของ Claude Code

---

## 📁 Project structure

```
your-project/
├── CLAUDE.md                  ← memory ที่ Claude อ่านทุก session
├── .claude/
│   ├── settings.json          ← project-level config
│   ├── settings.local.json    ← personal config (gitignore)
│   ├── skills/                ← reusable workflows
│   │   └── {skill-name}/
│   │       └── SKILL.md
│   ├── agents/                ← specialized subagents
│   │   └── {agent-name}.md
│   ├── hooks/                 ← lifecycle scripts
│   │   ├── pre-tool-use.sh
│   │   └── before-commit.sh
│   └── commands/              ← custom slash commands
│       └── {command-name}.md
├── backend/CLAUDE.md          ← stack-specific memory
└── frontend/CLAUDE.md
```

---

## 📝 CLAUDE.md — memory file

ไฟล์ที่ Claude อ่านทุก session — ใส่ context ที่ต้องรู้ตลอด

### โครงสร้างที่ดี

```markdown
# Project Name

1-line description

## Stack
- Language + framework + version

## Conventions
1. ...
2. ...

## Don't
- anti-pattern ที่เคยเจอ
```

### Hierarchy (ทำงานร่วมกัน)

```
~/.claude/CLAUDE.md           ← user-level (ทุก project)
project/CLAUDE.md             ← project-level
project/backend/CLAUDE.md     ← folder-specific (โหลดเพิ่มตอนทำงานใน folder นี้)
```

### Rules of thumb

- **กระชับ** — ทุกบรรทัดกิน token ทุก session
- **เฉพาะที่ Claude ผิดถ้าไม่มี** — อย่าใส่ best practice ที่ Claude รู้อยู่แล้ว
- **Living document** — update เมื่อ convention เปลี่ยน
- **อย่าซ้ำ** — root vs sub ห้ามมีเนื้อหาทับซ้อน

---

## 🛠️ Skills — reusable workflows

โฟลเดอร์ใน `.claude/skills/{name}/SKILL.md`

### Template

```markdown
---
name: api-endpoint
description: Add REST endpoint. Use when user asks to "add", "create" endpoint.
---

# When to use
- Trigger conditions ชัดเจน

# Required input
- ข้อมูลที่ต้องถามก่อน

# Process
1. Step 1...
2. Step 2...

# Checklist
- [ ] verify item 1
- [ ] verify item 2
```

### Tips

- `description` สำคัญที่สุด — Claude ใช้ตัดสินว่าจะ activate ไหม
- เขียนเป็น checklist ที่ verify ได้ ไม่ใช่ best practice แห้ง ๆ
- Skills trigger อัตโนมัติ — ไม่ต้อง `/skill {name}`

---

## 🤖 Subagents — specialized helpers

ไฟล์เดี่ยวใน `.claude/agents/{name}.md`

### Template

```markdown
---
name: test-writer
description: Write tests for new code. Use after implementing a feature.
model: claude-sonnet-4-6
tools:
  - Read
  - Write
  - Edit
  - Bash
---

# Role
You are a specialized test writer. You write tests only.

# Process
1. Read implementation
2. Generate tests matching project conventions
3. Run tests to verify
4. Report results

# Never
- Modify production code
- Skip tests
```

### เมื่อไหร่ใช้ subagent

- **Parallel work** — main session ทำต่อระหว่าง subagent ทำงาน
- **Specialized domain** — test, review, doc
- **Avoid context pollution** — งานที่ tool result ใหญ่ (long stack trace, large grep)

---

## 🎨 Slash commands — custom

ไฟล์ใน `.claude/commands/{name}.md`

```markdown
---
description: Review uncommitted changes
---

# /review

Steps:
1. Run `git diff`
2. Read CLAUDE.md
3. Dispatch code-reviewer agent
4. Report findings
```

ใช้: `/review` ใน Claude Code

---

## 🔌 MCP servers

### Lifecycle

```bash
# add
claude mcp add <name> -- <command>
claude mcp add <name> -e KEY=value -- <command>   # with env

# list
claude mcp list

# remove
claude mcp remove <name>

# debug
claude mcp list --debug
```

### Common MCP servers

```bash
# Postgres
claude mcp add postgres -- \
  npx -y @modelcontextprotocol/server-postgres "$DATABASE_URL"

# GitHub
claude mcp add github \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx -- \
  npx -y @modelcontextprotocol/server-github

# Playwright (browser automation)
claude mcp add playwright -- \
  npx -y @playwright/mcp@latest

# Filesystem (safe file ops)
claude mcp add filesystem -- \
  npx -y @modelcontextprotocol/server-filesystem /safe/path

# Slack
claude mcp add slack \
  -e SLACK_BOT_TOKEN=xoxb-xxx \
  -e SLACK_TEAM_ID=Txxx -- \
  npx -y @modelcontextprotocol/server-slack
```

⚠️ Restart Claude Code หลัง add MCP — ถึงจะ load

---

## ⚙️ settings.json

`.claude/settings.json` — project-level config

```json
{
  "model": "claude-sonnet-4-6",
  "permissions": {
    "allow": [
      "Read", "Write", "Edit",
      "Bash(git:*)",
      "Bash(npm:*)",
      "Bash(mvn:*)"
    ],
    "deny": [
      "Bash(rm -rf /:*)",
      "Bash(sudo:*)"
    ]
  },
  "env": {
    "DATABASE_URL": "postgresql://..."
  },
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{
          "type": "command",
          "command": "./.claude/hooks/pre-tool-use.sh"
        }]
      }
    ]
  }
}
```

### Permission patterns

```
"Read"                    ← all reads
"Write"                   ← all writes
"Bash(git:*)"             ← any git command
"Bash(npm install *)"     ← only npm install with args
"WebFetch(domain:*.com)"  ← fetch from .com domains
"mcp__postgres__*"        ← all postgres MCP tools
```

---

## 🪝 Hooks — lifecycle scripts

### Hook events

| Event | Triggered when | Use case |
|---|---|---|
| `PreToolUse` | ก่อน Claude ใช้ tool | block dangerous commands |
| `PostToolUse` | หลัง tool ใช้สำเร็จ | log, notify |
| `UserPromptSubmit` | user ส่ง prompt | inject context |
| `Stop` | Claude หยุดทำงาน | run tests, format |
| `SubagentStop` | subagent จบ | aggregate results |
| `PreCompact` | ก่อน /compact | save state |
| `Notification` | tool ขอ permission | desktop alert |

### Hook script template

```bash
#!/bin/bash
# read JSON from stdin
input=$(cat)

# extract command (for Bash tool)
command=$(echo "$input" | jq -r '.tool_input.command')

# logic
if echo "$command" | grep -q "rm -rf /"; then
  echo "Blocked: dangerous command" >&2
  exit 2   # exit 2 = block
fi

exit 0     # exit 0 = allow
```

### Exit codes

```
0   → allow / continue
2   → block (PreToolUse) or report failure
1+  → error (depends on hook)
```

⚠️ อย่าลืม `chmod +x .claude/hooks/*.sh`

---

## 🎚️ Model selection

```bash
/model haiku     # claude-haiku-4-5  — fast, cheap
/model sonnet    # claude-sonnet-4-6 — balance (default)
/model opus      # claude-opus-4-7   — most capable
```

| Task | Model | Why |
|---|---|---|
| Simple queries, lookup | Haiku | overkill ถ้าใช้ Sonnet |
| Boilerplate, mechanical translation | Haiku | ถูกกว่า 10x |
| Test generation | Haiku-Sonnet | depends on complexity |
| Implementation | Sonnet | balance |
| Debug | Sonnet | context-heavy |
| Architecture, planning | Opus | reasoning ลึก |
| Code review (deep) | Opus | catch subtle issues |

---

## 💰 Token & context management

### ดู usage

```
/context     ← breakdown ของ context ปัจจุบัน
/cost        ← cost ของ session
```

### เมื่อ context ใกล้เต็ม

```
50%  → ปกติ
70%  → /compact ได้แล้ว
85%  → /compact หรือ /clear ทันที
```

### Decision tree

```
ทำงานต่อจากเดิม?
├── YES → /compact
└── NO  → /clear

session ทำงานนานหลายชั่วโมง?
└── YES → /compact + restart Claude
```

### ลด token usage

- **CLAUDE.md กระชับ** — ลบของไม่จำเป็น
- **อย่า paste ไฟล์ใหญ่ลง prompt** — ให้ Claude `Read` แทน
- **dispatch subagent** สำหรับงานที่ tool result ใหญ่
- **ใช้ Haiku** เมื่อ task เหมาะ

---

## 🔄 Common workflows

### Plan → Implement

```
1. /model opus
2. Shift+Tab × 2  (plan mode)
3. "Plan how to add feature X"
4. review plan
5. Shift+Tab × 2  (exit plan mode)
6. /model sonnet
7. "Implement the plan"
```

### Fresh feature

```
1. /clear
2. "Read CLAUDE.md and tell me what you understand"
3. /model opus
4. plan mode → design
5. /model sonnet
6. implement using skills
7. dispatch test-writer agent
8. /review (custom command)
9. /ship (commit + PR)
```

### Debug session

```
1. แสดง error message + stack trace
2. ให้ Claude reproduce
3. ใช้ Read + Grep + Bash หาสาเหตุ
4. fix
5. รัน test verify
6. /compact หลังจบ
```

---

## 🚨 Troubleshooting

### Claude ไม่อ่าน CLAUDE.md

```bash
pwd                # ตรวจ CWD
ls CLAUDE.md       # มีไฟล์ไหม
cat CLAUDE.md      # อ่านได้ไหม
```

ต้องเปิด `claude` จาก folder ที่มี CLAUDE.md

### MCP server ไม่ขึ้น

```bash
claude mcp list --debug    # ดู error
# restart Claude Code หลัง add MCP
# ตรวจ env vars ที่ส่งให้ server
```

### Hook ไม่ทำงาน

```bash
chmod +x .claude/hooks/*.sh           # executable?
ls -la .claude/hooks/                  # permission ถูกไหม
cat .claude/settings.json | jq .hooks  # registered ไหม
```

ทดสอบ hook standalone:
```bash
echo '{"tool_input":{"command":"rm -rf /"}}' | ./.claude/hooks/pre-tool-use.sh
echo $?    # ควรได้ 2 (blocked)
```

### Skill ไม่ trigger

- `description` ใน frontmatter clear พอไหม
- ลองใช้คำที่ตรงกับ description ใน prompt
- หรือ explicit: "Use the api-endpoint skill to..."

### Context เต็มเร็วเกินไป

- ตรวจ `/context` ดูว่าอะไรกินเยอะ
- CLAUDE.md ยาวเกินไป?
- มี tool result ใหญ่ ๆ (เช่น `find . -name "*.java"`)?
- dispatch subagent แทนทำใน main

### Subagent fail

- ตรวจ tool list ใน frontmatter — มีพอไหม
- description ตรงกับงานไหม
- ลด scope ของงานต่อครั้ง

---

## 📚 Useful resources

- **Docs**: https://docs.claude.com/en/docs/claude-code
- **MCP servers**: https://github.com/modelcontextprotocol/servers
- **Best practices**: https://www.anthropic.com/engineering/claude-code-best-practices
- **API reference**: https://docs.anthropic.com/en/api

---

## 🎯 Pro tips

1. **เริ่มทุก feature ใหม่ด้วย `/clear`** — context สะอาดได้ผลดีกว่า
2. **`/init` แล้วต้องแก้** — ที่ generate มามัน generic เกินไป
3. **CLAUDE.md ทุก folder** — backend/frontend ต่าง stack ใช้คนละ rule
4. **Plan mode ก่อน feature ใหญ่** — save 2-3 รอบของการแก้
5. **Subagent ไม่ใช่ทุกที่** — main session มี value ของมัน
6. **อ่าน Claude ตอบ** — อย่า paste prompt แล้ว skip ผลลัพธ์
7. **Hooks เป็น safety net** — ตั้งครั้งเดียว ป้องกันตลอด
8. **`/cost` ทุกวัน** — รู้ว่าใช้ไปเท่าไหร่ ปรับนิสัยทันเวลา
9. **Skills > prompt copy-paste** — อะไรทำซ้ำ 3 ครั้งขึ้น = สร้าง skill
10. **CLAUDE.md เป็น living doc** — update เมื่อ convention เปลี่ยน
