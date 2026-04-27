# Instructor Notes — Expected Outputs & Teaching Cues

> เอกสารนี้สำหรับผู้สอนเท่านั้น — มี expected output ของแต่ละ step, สิ่งที่ต้องสังเกต, และวิธีช่วยผู้เรียนที่ติด

---

## วิธีใช้เอกสารนี้

แต่ละ step มี 4 ส่วน:

1. **✅ Expected output** — ผลลัพธ์ที่ถือว่าผ่าน
2. **🚩 Red flags** — สัญญาณว่าผู้เรียนกำลังหลงทาง
3. **💬 Discussion cues** — คำถามที่ควรถามเพื่อกระตุ้นความเข้าใจ
4. **🛠️ Recovery** — วิธีช่วยถ้าติด

---

# DAY 1

## Module 1 — Setup & First Conversation

### Step 1.1 — เริ่ม Claude Code ครั้งแรก

✅ **Expected output**:
- ผู้เรียน summarize ได้ว่าเป็น full-stack task management
- ระบุ feature 5 ตัวที่ relevant (subagents, skills, MCP, plan mode, CLAUDE.md)
- ถามคำถามที่มีน้ำหนัก เช่น "ใช้ Spring Security เวอร์ชันไหน", "deploy ไปไหน"

🚩 **Red flags**:
- ผู้เรียน paste prompt แล้วไม่อ่าน Claude ตอบ (skip ไป step ต่อ)
- Claude ตอบ generic ไม่ reference content ของ requirements.md เลย → แสดงว่า Claude ไม่ได้อ่านไฟล์จริง

💬 **Discussion cues**:
- "Claude ตอบจาก requirements.md จริงๆ หรือเดาจากชื่อ project?"
- "ถ้าคำถามที่ Claude ถามเป็นเรื่องที่ requirement ระบุชัดอยู่แล้ว แปลว่าอะไร?"

🛠️ **Recovery**:
- ถ้า Claude ไม่อ่านไฟล์: บอกให้ใช้ explicit `Read requirements.md first.`
- ถ้าผู้เรียนข้าม step: บังคับให้อ่าน 3 จุด key ของ requirements ออกเสียง

---

### Step 1.2 — สร้าง CLAUDE.md ด้วย /init

✅ **Expected output**:
- `/init` สร้าง CLAUDE.md ที่ generic (10-15 บรรทัด)
- หลัง prompt #3 — CLAUDE.md ของแต่ละคนยาว 60-80 บรรทัด มีเนื้อหาตรงตาม requirement (project name, stack, structure, conventions, anti-patterns)

🚩 **Red flags**:
- CLAUDE.md ยาว > 100 บรรทัด → over-engineering
- มี implementation detail ที่ยังไม่เกิดขึ้น (เช่น "use BCrypt" ทั้งที่ยังไม่ได้คุยเรื่อง auth)
- copy structure จาก requirements.md มาเฉยๆ ไม่ adapt

💬 **Discussion cues**:
- "ถ้าเปิด session ใหม่พรุ่งนี้ Claude จะเข้าใจ project ผ่าน CLAUDE.md นี้ใน 30 วินาทีไหม?"
- "ทุกบรรทัดใน CLAUDE.md ถ้าลบไป Claude จะทำผิดอะไร?"

🛠️ **Recovery**:
- ถ้ายาวเกิน: prompt ว่า `Cut this CLAUDE.md by 40% without losing essential information`
- ถ้าสั้นเกิน: ถาม "Claude จะรู้ได้ยังไงว่าเรา prefer TanStack Query?"

---

### Step 1.3 — Folder structure

✅ **Expected output**:
```
taskflow/
├── CLAUDE.md
├── .claude/
│   ├── settings.json (อาจยังเปล่า)
│   ├── skills/
│   ├── hooks/
├── backend/
│   └── CLAUDE.md
├── frontend/
│   └── CLAUDE.md
├── e2e/
├── docker-compose.yml (อาจยังเปล่า)
└── README.md
```

🚩 **Red flags**:
- Claude สร้างไฟล์ `pom.xml` หรือ `package.json` ทั้งที่บอกว่ายังไม่ implement
- ลืมสร้าง `.gitkeep` ใน empty folder

---

### Step 1.4 — Backend & Frontend CLAUDE.md

✅ **Expected output**:
- `backend/CLAUDE.md`: 30-50 บรรทัด, **ไม่มี** เนื้อหาซ้ำกับ root (เช่น project name)
- `frontend/CLAUDE.md`: 30-50 บรรทัด, **ไม่มี** Spring/Java เลย

🚩 **Red flags**:
- ทั้งสองไฟล์มี "tech stack" repeat กันหมด → ไม่เข้าใจ hierarchy
- ผู้เรียนเขียนเรื่องที่ยังไม่ได้ตัดสินใจ (เช่น API base URL ทั้งที่ยังไม่ได้ implement)

💬 **Discussion cues**:
- "ถ้า Claude ทำงานที่ root level (เช่น docker-compose) มันจะอ่าน backend/CLAUDE.md ไหม?"
- "ทำไมไม่รวม convention ทั้งหมดไว้ใน root?"

### ✅ Module 1 Final Checkpoint

ก่อนไป Module 2 ให้ผู้เรียน:
1. ทำ `/clear`
2. ถาม: `What is this project? What stack? What's our test naming convention for backend?`

ถ้า Claude ตอบได้ → CLAUDE.md ใช้งานได้จริง  
ถ้าตอบไม่ได้ → กลับไปแก้ไฟล์

---

## Module 2 — Plan Mode + First Endpoint

### Step 2.2 — Plan mode

✅ **Expected output (plan)**:
- Database schema: users table with id (UUID), email (unique), password_hash, name, created_at
- Java classes: User entity, UserRepository, UserService, AuthController, RegisterRequest DTO, LoginRequest DTO, AuthResponse DTO, UserAlreadyExistsException, JwtService, SecurityConfig
- Test strategy: unit test service (mock repo, BCrypt), integration test controller (MockMvc, @WithMockUser), no test for entity
- Implementation order: Entity → Repo → DTO → Service → Controller → Exception handler → SecurityConfig → JWT (mention chicken-egg of needing JwtService for filter)

🚩 **Red flags**:
- Plan mention specific code (annotations, code blocks) → ออกนอก purpose ของ plan mode
- ลืม security config / JWT entirely
- Test strategy ไม่ระบุ layer

💬 **Discussion cues**:
- "Plan นี้ถ้าให้ junior dev ทำตาม จะมีอะไรงงไหม?"
- "ถ้าสลับ order step 3 กับ 5 จะทำได้ไหม? ทำไมต้องตาม order นี้?"

🛠️ **Recovery**:
- ถ้า plan สั้นเกิน: prompt `What did you not include? Are there assumptions you're making?`
- ถ้า Claude เขียน code ทั้งที่อยู่ plan mode: บอกให้ผู้เรียนสังเกตว่ายังอยู่ plan mode (มี indicator) แสดงว่า Claude แค่อธิบาย ไม่ได้สร้างไฟล์จริง

---

### Step 2.3 — สร้าง skill: java-endpoint

✅ **Expected output**:
- `.claude/skills/java-endpoint/SKILL.md` ขนาด 80-150 บรรทัด
- มี frontmatter หรือ section "When to use" ชัดเจน
- Process แบ่งเป็น step ที่ verify ได้
- มี output checklist

🚩 **Red flags**:
- skill บอกแค่ "create endpoint" ไม่มีรายละเอียด → จะไม่ trigger ตอนใช้
- ไม่มี example
- "When to use" คลุมเครือ — เช่น "when implementing API" ทุกอย่างใน project ก็ตรงหมด

💬 **Discussion cues**:
- "ถ้า dev ใหม่ไม่เคยเห็น project นี้เลย skill นี้พอให้เขา implement ได้ไหม?"
- "Trigger condition ของ skill นี้ — มันจำเพาะพอที่ Claude จะรู้ว่าตอนไหนต้องใช้ ตอนไหนไม่ใช้?"

---

### Step 2.4 — Implement auth ใช้ skill

✅ **Expected output**:
- ไฟล์ที่ควรมีหลัง register endpoint:
  - `backend/src/main/java/com/taskflow/auth/dto/RegisterRequest.java`
  - `backend/src/main/java/com/taskflow/auth/dto/UserResponse.java`
  - `backend/src/main/java/com/taskflow/auth/entity/User.java`
  - `backend/src/main/java/com/taskflow/auth/repository/UserRepository.java`
  - `backend/src/main/java/com/taskflow/auth/service/AuthService.java`
  - `backend/src/main/java/com/taskflow/auth/controller/AuthController.java`
  - `backend/src/main/java/com/taskflow/auth/exception/UserAlreadyExistsException.java`
  - tests สำหรับ service และ controller
- `mvn test` ผ่าน

🚩 **Red flags**:
- Claude ไม่ใช้ skill (แสดง progress ที่ต่างจาก process ใน skill) → skill ไม่ trigger
- รวม register + login ใน prompt เดียว → ผู้เรียน rush

🛠️ **Recovery**:
- ถ้า skill ไม่ trigger: explicit `Use the java-endpoint skill to...`
- ถ้า test ไม่ผ่าน: ใช้ Claude debug ให้เห็น iteration cycle จริง

---

## Module 3 — Token & Context Management

### Step 3.1 — `/context`

✅ **Expected output**:
- เห็น breakdown:
  - System prompt: ~5-10K tokens
  - CLAUDE.md (root + backend + frontend): ~2-5K tokens (รวมกัน)
  - Conversation: 10-30K tokens (ขึ้นกับว่าทำมามาก)
  - Total: 20-50% ของ window

🚩 **Red flags**:
- Total > 70% หลัง M2 → ผู้เรียนใส่ไฟล์ใหญ่เกินจำเป็น (เช่น attach pom.xml ที่ generate มา)

💬 **Discussion cues**:
- "ดู Conversation ใช้ไปเท่าไหร่ — หลัก ๆ คือ tool result ของอะไร?"
- "ถ้า session ใหม่ตอนนี้ — กี่ token หายไปเลย?"
- "ถ้า CLAUDE.md เพิ่มอีก 50 บรรทัด นั่นคือ token เพิ่มกี่ตัวต่อ session?"

---

### Step 3.2-3.3 — `/compact` vs `/clear`

✅ **Expected output**:
- หลัง `/compact` — context drops 30-50%, summary แสดงเรื่อง auth ที่ทำเสร็จ
- หลัง `/clear` — Claude ตอบว่า "I don't have context of previous conversation but I can see CLAUDE.md..."

🚩 **Red flags**:
- ผู้เรียนใช้ `/clear` แทน `/compact` ระหว่าง task → เสีย context สำคัญ
- ใช้ `/compact` ทุก 5 prompt → ไม่จำเป็น

💬 **Discussion cues**:
- "เมื่อไหร่ใช้ /compact, เมื่อไหร่ /clear, เมื่อไหร่ปิด session?"
- "ถ้า /compact ดีขนาดนี้ ทำไมไม่ใช้ทุกครั้งหลังเสร็จ task?"

---

### Step 3.4 — Optimize CLAUDE.md

✅ **Expected output**:
- Diff แสดงการลบ:
  - tech stack ที่ซ้ำในทั้ง root และ sub
  - "we will" predictions ที่ outdated (เช่น "we will use BCrypt" ทั้งที่ทำแล้ว)
  - generic best practices ที่ Claude รู้อยู่แล้ว
- ลบได้ 20-30%

🚩 **Red flags**:
- ลบของจำเป็นออก เช่น naming convention
- ไม่กล้าลบเลย → ติดความคิด "ยิ่งเยอะยิ่งดี"

---

## Module 4 — MCP Postgres

### Step 4.1 — Docker Compose

✅ **Expected output**:
- `docker compose up -d` สำเร็จ
- `docker ps` เห็น postgres container running
- `docker compose exec postgres psql -U taskflow -d taskflow -c '\dt'` คืนค่า empty (no tables)

🛠️ **Common issues**:
- Port 5432 ใช้แล้ว (มี local Postgres) → เปลี่ยนเป็น 5433 หรือ stop local
- Mac ARM: ระบุ `platform: linux/amd64` ไม่ต้อง — postgres image รองรับ multi-arch

---

### Step 4.2 — ติดตั้ง Postgres MCP

✅ **Expected output**:
- `claude mcp list` เห็น postgres
- `/mcp` ใน Claude Code เห็น `postgres` server with tools (query, list tables, describe table)

🚩 **Red flags**:
- ใส่ password ผิดใน connection string → fail silently
- Claude Code เปิดอยู่ตอน add MCP → ต้อง restart

🛠️ **Recovery**:
- ถ้า MCP ไม่ขึ้น: `claude mcp list --debug`, ดู log ของ npx process

---

### Step 4.3 — Schema design ด้วย MCP

✅ **Expected output**:
- 3 migration files: V1__create_users.sql (อาจมีอยู่แล้วถ้า M2 ทำแล้ว) , V2__create_tasks.sql, V3__create_comments.sql, V4__create_activities.sql
- หลัง apply: `\dt` แสดง 4 tables
- Claude ใช้ MCP query schema ก่อนแล้ว show output

🚩 **Red flags**:
- Migration ไม่มี foreign key constraint → schema review จะ fail M5
- ไม่มี index บน assignee_id, status (จะใช้ filter)
- Soft delete ไม่ทำเป็น `deleted_at TIMESTAMP NULL` → ใช้ flag boolean ผิด

💬 **Discussion cues**:
- "ลองเทียบ — ถ้าไม่มี MCP postgres, Claude generate migration ยังไง? มันรู้ได้ไงว่าตอนนี้มี table อะไรบ้าง?"

---

### Step 4.4 — Generate JPA entities

✅ **Expected output**:
- 3-4 entity files ที่ field types ตรงกับ DB จริง
- `mvn compile` ผ่าน
- Claude เคย call MCP `describe table` ก่อน generate

🚩 **Red flags**:
- Entity field type ไม่ตรง schema (เช่น `LocalDate` ทั้งที่ DB เป็น `TIMESTAMP`)

---

## Module 5 — Subagents

### Step 5.2 — สร้าง test-writer subagent

✅ **Expected output**:
- `.claude/agents/test-writer.md` มี frontmatter ครบ
- description ชัดว่าเมื่อไหร่ใช้
- tools list จำกัด — ไม่มี Bash อย่างไม่จำกัด

🚩 **Red flags**:
- ใส่ tools มากเกิน เช่น web_search, mcp_postgres → subagent ทำงานเกิน scope
- description วาง vague → main agent ไม่ dispatch ตอนควร

---

### Step 5.3 — Implement Tasks API + parallel test

✅ **Expected output**:
- 5 endpoints ของ tasks ทำงาน
- ในระหว่าง main agent implement endpoint #2, Task tool launch test-writer สำหรับ endpoint #1
- ผู้เรียนเห็น output ในรูปแบบ "Launching test-writer agent..." แล้วสักพัก "Test writer completed"

🚩 **Red flags**:
- main agent เขียน test เอง ไม่ dispatch → subagent ไม่ได้ใช้
- dispatch แต่รอ subagent จบทุกครั้ง → ไม่ใช่ parallel

💬 **Discussion cues**:
- "Main agent context กับ subagent context อะไรคนละก้อนกัน — ถ้าพิมพ์อะไรใน main, subagent เห็นไหม?"
- "ถ้า subagent fail, main agent รู้ไหม?"

---

### Step 5.4 — schema-reviewer subagent

✅ **Expected output**:
- Report format:
  ```
  V2__create_tasks.sql
  ✅ PASS: All foreign keys present
  ⚠️  WARNING: Missing index on assignee_id
  ❌ FAIL: tasks table missing updated_at column
  ✅ PASS: Naming convention (snake_case)
  ```

🚩 **Red flags**:
- subagent แก้ไฟล์เอง — ไม่ทำตาม "report only" rule
- ไม่ใช้ MCP postgres ตรวจของจริง

---

# DAY 2

## Day 2 Warmup

### Recap prompt (#24)

✅ **Expected output**:
- Claude อ่าน `.claude/`, ทั้ง 3 CLAUDE.md, scan `backend/src/`
- รายงานชัด: ที่เสร็จ (auth, tasks CRUD, schema), ที่ค้าง (comments, activity, reports endpoints), suggestion (เริ่มจาก backend ที่ค้างก่อน frontend)

🚩 **Red flags**:
- "Project looks good!" แบบ generic → ไม่ได้อ่านจริง
- ลืม mention `.claude/skills/` หรือ subagents ที่สร้างไว้

💬 **Discussion cues**:
- "ถ้า Day 1 ทำ CLAUDE.md ดี — Claude ตอบครบโดยไม่ต้องอ่านโค้ดเลยใช่ไหม?"

---

## Module 6 — Frontend with Skills + Subagents

### Step 6.1 — Scaffold frontend

✅ **Expected output**:
- `npm run dev` รันบน :5173 แสดงหน้าว่าง
- ไฟล์: `vite.config.ts`, `tailwind.config.js`, `tsconfig.json` (strict: true), `components.json` (shadcn)
- `src/lib/utils.ts` มี `cn()` helper ของ shadcn

🚩 **Red flags**:
- Claude ใช้ `npm create vite@latest` แม้ถูกห้าม
- TypeScript ไม่ strict
- Tailwind ไม่ทำงาน — content paths ใน config ไม่ถูก

🛠️ **Recovery**:
- หน้าจอเปล่า แต่ HTML render: ตรวจ `index.html` ว่ามี `<div id="root">`
- Tailwind ไม่ทำงาน: ตรวจ `@tailwind base; @tailwind components; @tailwind utilities;` ใน CSS

---

### Step 6.2 — react-component skill

✅ **Expected output**:
- skill spec ครอบคลุม presentational vs container
- มีรายการไฟล์ที่ต้องสร้าง (component + stories + test)
- ตัวอย่างที่ specific (ไม่ใช่ "use proper TypeScript")

🚩 **Red flags**:
- skill list 20 items — overload, จะใช้ไม่ครบ
- ไม่มี checklist verify ที่ปลายทาง

---

### Step 6.3 — api-client-generator subagent

✅ **Expected output**:
- subagent ใช้ Haiku (cost concern)
- frontmatter ระบุ `model: claude-haiku-4-5`
- tools: Read, Write, Edit, Grep (ไม่ต้อง Bash)

🚩 **Red flags**:
- ใช้ Sonnet/Opus → over-spec สำหรับ mechanical task

💬 **Discussion cues**:
- "ทำไม Haiku พอสำหรับงานนี้? ถ้าให้ Opus ทำจะ output ดีขึ้นไหม?"
- "ถ้า backend DTO เปลี่ยน frontend types จะ sync ยังไง? subagent นี้ช่วยตรงนี้ได้ไหม?"

---

### Step 6.4 — Multi-agent orchestration

✅ **Expected output (plan)**:
- Auth context + login: main session (cross-cutting concern)
- API client for tasks: dispatch api-client-generator (mechanical)
- Kanban board: main session (UI design decisions)
- Task card: dispatch react-component-generator subagent (or use skill)
- Drag-drop: main session (need to decide library, integrate)

🚩 **Red flags**:
- dispatch ทุกอย่างไป subagent → main session ไม่มีอะไรทำ ผิดจุดประสงค์
- ไม่ dispatch อะไรเลย → ไม่ใช้ feature
- main session คอย wait result subagent ทุกครั้ง → ไม่ parallel

💬 **Discussion cues**:
- "งานไหนเหมาะ subagent งานไหนไม่? rule of thumb คืออะไร?"

---

## Module 7 — MCP Playwright

### Step 7.1 — Install Playwright MCP

✅ **Expected output**:
- `/mcp` แสดงทั้ง postgres และ playwright
- playwright tools include: navigate, click, fill, screenshot, etc.

🛠️ **Common issues**:
- Browser binary ไม่มี: รัน `npx playwright install chromium`
- Permission denied (Mac): grant accessibility permission

---

### Step 7.2 — Run E2E ผ่าน Claude

✅ **Expected output**:
- Claude เปิด browser จริง — ผู้เรียนเห็นหน้าต่าง browser popup
- Screenshots ถูกบันทึกที่ตำแหน่งที่ Claude ระบุ
- หลัง flow: task อยู่ใน Done column

🚩 **Red flags**:
- App ไม่รัน — backend หรือ frontend ลืมเปิด
- Selector แตก เพราะ component ใช้ random class — Claude debug → opportunity to teach data-testid

💬 **Discussion cues**:
- "Claude เห็นอะไรในหน้าเว็บ? อ่าน DOM หรือเห็นภาพ?"
- "ถ้าหน้าเว็บใช้ JavaScript heavy ทำให้ Claude มี issue ไหม? ทำไม?"

---

### Step 7.3 — Persistent Playwright tests

✅ **Expected output**:
- `e2e/tests/` มี 3 ไฟล์ test
- Page object pattern: `e2e/pages/LoginPage.ts`, `DashboardPage.ts`
- `npx playwright test` รัน 3 tests pass

🚩 **Red flags**:
- Tests dependency กัน (test 2 ต้องรัน test 1 ก่อน) → ไม่ standalone
- Hardcoded user credentials

---

## Module 8 — MCP GitHub

### Step 8.1 — Install GitHub MCP

✅ **Expected output**:
- `/mcp` เพิ่ม github
- `mcp__github__list_repos` หรือ tool คล้ายๆ ใช้ได้

🛠️ **Common issues**:
- PAT scope ไม่พอ — ต้อง `repo` (full)
- PAT expired — สร้างใหม่

---

### Step 8.2-8.3 — Create repo + issues

✅ **Expected output**:
- Repo `taskflow-workshop` สร้างบน GitHub (private)
- 3 issues สร้าง พร้อม body ที่มี acceptance criteria + technical notes
- Issue 3 มี label "good first task"

🚩 **Red flags**:
- Issue body แค่ title repeat → ไม่มีเนื้อหาที่ใช้ไกด์ implementer ได้
- ไม่มี label

---

### Step 8.4 — Implement → PR

✅ **Expected output**:
- Branch: `feat/comments-api`
- Commit message: `feat: add comments endpoints (#1)`
- PR description: ลิงก์ issue, list of changes, how to test
- Self-review comments อย่างน้อย 2 อัน บนจุดที่ improve ได้จริง

🚩 **Red flags**:
- PR description copy issue ทั้งดุ้น → ไม่ใช่ summary ของ change
- Self-review เป็น "looks good" — ไม่ใช่ critique

💬 **Discussion cues**:
- "Self-review จับอะไรได้บ้างที่ Claude ตอน implement ไม่จับ?"
- "ถ้าให้ subagent ทำ self-review จะดีกว่าไหม? ทำไม?"

---

## Module 9 — Hooks, Settings, Plugins

### Step 9.1 — settings.json

✅ **Expected output**:
- ไฟล์ format ถูก JSON
- permissions section คั่น allow/deny ชัด
- env section มี DATABASE_URL
- model: claude-sonnet-4-6

🚩 **Red flags**:
- allow `Bash(*)` → กว้างเกิน เสี่ยง
- ใส่ secrets (ของจริง) ใน env — ต้อง `.gitignore` ตัว settings.json หรือใช้ `.env`

💬 **Discussion cues**:
- "Project-level vs user-level settings — เมื่อไหร่ใช้อันไหน?"
- "Permissions แล่น ไม่แล่น — ต่างกับ hooks ยังไง?"

---

### Step 9.2 — Pre-tool-use hook

✅ **Expected output**:
- `.claude/hooks/pre-tool-use.sh` executable (`chmod +x`)
- ทดลอง: Claude พยายามรัน `git push --force` → hook block พร้อม message
- settings.json มี hook registered:
  ```json
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "./.claude/hooks/pre-tool-use.sh" }]
      }
    ]
  }
  ```

🚩 **Red flags**:
- ไม่มี `chmod +x` → hook ไม่ทำงาน silent
- exit 0 แม้เจอ forbidden command → ไม่ block จริง

🛠️ **Recovery**:
- ตรวจ `ls -la .claude/hooks/` ดู permission
- ทดลอง standalone: `echo '{"tool_input":{"command":"rm -rf /"}}' | ./.claude/hooks/pre-tool-use.sh`

---

### Step 9.3 — Pre-commit hook

✅ **Expected output**:
- Hook smart — รู้ว่าไฟล์ไหนเปลี่ยน
- ถ้า test fail → exit 1, commit aborted
- Format ถูก apply auto

🚩 **Red flags**:
- รัน `mvn test` ทุกครั้งแม้ frontend ไฟล์เปลี่ยน → ช้าเกิน
- Format ไม่ stage หลัง apply → commit เก่ายังไม่ formatted

---

### Step 9.4 — Custom slash command

✅ **Expected output**:
- `.claude/commands/review.md` มี content แบบ instruction
- รัน `/review` แล้ว Claude อ่าน diff, output structured review

🚩 **Red flags**:
- `/review` output generic — ไม่ reference convention จาก CLAUDE.md
- ไม่มี PASS/CONCERN/BLOCK structure

---

### Step 9.5 — Plugin

✅ **Expected output**:
- ลง community plugin ได้ หรือ
- สร้าง local plugin ที่ bundle skill + agent ในโครง:
  ```
  .claude/plugins/taskflow-helpers/
  ├── plugin.json
  ├── skills/
  │   └── java-endpoint/
  └── agents/
      └── test-writer.md
  ```

🛠️ **Common issues**:
- plugin marketplace ยังไม่ stable ตอน workshop → fallback เป็น local plugin demo

---

## Module 10 — Capstone

### ก่อนเริ่ม

ผู้สอนเตรียม:
- Timer ใหญ่ บนจอแสดงให้ทุกคนเห็น
- เตือนทุก 15 นาที

### During

🚩 **สังเกต**:
- ผู้เรียนข้าม plan mode → time pressure ทำให้กลับสู่ habit เก่า
- ใช้ Opus ทุก step → cost spike
- Skill/subagent ไม่ trigger — ลืม phrase trigger
- ไม่ commit ตลอดทาง — ทุกอย่างยัดท้าย

🛠️ **Coach moments** (ใช้สั้นๆ ไม่ขัดจังหวะ):
- "Plan แล้วยัง?"
- "Model ตอนนี้คืออะไร?"
- "Skill ใช้หรือเปล่า?"

### After (assessment)

ผู้เรียนแต่ละคน rate ตัวเอง 1-5 ใน:

1. ใช้ plan mode ก่อน implement (1 = ไม่ใช้ ... 5 = ใช้ทุกครั้ง)
2. เลือก model เหมาะกับ task (1 = ใช้ Opus ตลอด ... 5 = สลับ 3 model)
3. dispatch subagent (1 = ทำเอง ... 5 = parallel หลายงาน)
4. ใช้ MCP (1 = ลืม ... 5 = ใช้ทั้ง 3 MCP ถูกจุด)
5. CLAUDE.md ทำงาน (1 = ไม่ update ... 5 = update + test)

ค่าเฉลี่ย ≥ 3.5 = pass

---

# Common Cross-Module Issues

## Token usage spike

**สัญญาณ**: budget alert, `/context` แสดง 80%+

**สาเหตุ**:
- ลืม `/compact` ระหว่าง long session
- attach ไฟล์ใหญ่หลายครั้ง (เช่น whole pom.xml repeat)
- tool result ที่ใหญ่ (เช่น `find . -name "*.java"`)

**แก้**:
- `/compact` ทันที
- ถ้ายังเยอะ — `/clear` แล้ว reload

## Subagent ใช้ context หมด

**สัญญาณ**: subagent error mid-task

**สาเหตุ**: subagent context window เล็กกว่า main; ส่ง context มากเกิน

**แก้**:
- ลด scope ของ subagent ต่อครั้ง
- ส่งแค่ file paths ไม่ใช่ contents

## CLAUDE.md ไม่ถูก load

**สัญญาณ**: Claude ถามคำถามที่ CLAUDE.md ตอบแล้ว

**สาเหตุ**:
- ไฟล์ผิดที่ (ต้องอยู่ที่ CWD ตอนเปิด `claude`)
- file name ผิด case sensitivity
- เปิด Claude Code นอก project folder

**แก้**: `pwd` ตรวจ, `cat CLAUDE.md` ตรวจไฟล์มีจริง

---

# Workshop logistics tips

## Internet
ต้อง stable — MCP servers + Anthropic API ใช้ตลอด เตรียม backup mobile hotspot

## Pair programming
แนะนำ: pair 2 คน 1 เครื่อง สลับกันคุม Claude ทุก 30 นาที

## Energy management
- Module 4 (MCP setup) มัก drain energy → schedule หลัง break
- Module 7 (Playwright "wow moment") schedule หลังพัก เป็น energizer
- Module 10 (Capstone) — กินช่วงท้ายวัน, ทำหลัง coffee break ครั้งสุดท้าย

## Cleanup
จบ Day 2 ก่อนกลับ ขอผู้เรียน:
1. `git push` ทุกอย่าง
2. Export `.claude/` folder ของตัวเอง (zip ส่งกลุ่ม)
3. หยุด docker containers
