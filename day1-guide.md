# Day 1 — Foundation + Backend

> 🎯 **เป้าหมายวันนี้**: สร้าง backend API ที่ใช้งานได้จริง พร้อมเรียนรู้ feature หลักของ Claude Code

**เวลา**: 8 ชั่วโมง (รวมพัก) | **Modules**: 5

---

## 📋 Pre-workshop checklist

ก่อนเริ่ม ผู้เรียนควรมี:

- [ ] Claude Code ติดตั้งแล้ว (`npm install -g @anthropic-ai/claude-code`)
- [ ] Java 21 + Maven
- [ ] Node.js 20+
- [ ] Docker Desktop
- [ ] GitHub account + personal access token
- [ ] folder ว่าง `taskflow/` พร้อมไฟล์ `requirements.md` อยู่ข้างใน

---

## Module 1 — Setup & First Conversation (1.5 hr)

> **Learning goal**: เข้าใจ `/init`, CLAUDE.md hierarchy, และ memory system

### Step 1.1 — เริ่ม Claude Code ครั้งแรก

```bash
cd taskflow
claude
```

📋 **Prompt #1 — ให้ Claude อ่านโจทย์ก่อน**

```
Read requirements.md. Don't write any code yet. Tell me back:
1. What you understand we're building (3 sentences)
2. Which 5 features of Claude Code you think will help us most for this project
3. One thing in the requirements you'd push back on or want to clarify
```

> 💡 **เกร็ด**: นี่คือ habit สำคัญ — ให้ Claude อ่าน context ก่อนทำงาน อย่าให้กระโดดไป code เลย

---

### Step 1.2 — สร้าง root CLAUDE.md ด้วย /init

📋 **Prompt #2**

```
/init
```

Claude จะ scan folder และสร้าง CLAUDE.md ให้ — แต่ที่ generate มามันยังไม่ดีพอ

📋 **Prompt #3 — ปรับปรุง CLAUDE.md**

```
The CLAUDE.md you generated is too generic. Rewrite it so a future Claude session can understand this project in 30 seconds. Include:

- Project name and 1-line description
- Tech stack (backend + frontend)
- Folder structure that we will create
- 3 conventions we'll follow (naming, commit style, error handling approach)
- Note that backend/ and frontend/ will have their own CLAUDE.md with stack-specific rules
- A "Don't do this" section with 3 anti-patterns

Keep it under 80 lines.
```

> 💡 **Key insight**: CLAUDE.md is **read every session** — token cost matters เขียนกระชับ ตรง

---

### Step 1.3 — สร้าง folder structure

📋 **Prompt #4**

```
Create the folder structure exactly as specified in requirements.md (the "Project Structure" section). Create empty placeholder files where needed (.gitkeep is fine for empty dirs). After creating, run `tree -L 3` and show me the result.
```

---

### Step 1.4 — Backend CLAUDE.md (specific context)

📋 **Prompt #5**

```
Create backend/CLAUDE.md. This file should ONLY contain rules that apply to backend work. Structure it with these sections (use ## headings):

## Versions
Java + Spring Boot version, use Maven wrapper (`./mvnw`) — never plain `mvn`

## Package Structure
com.taskflow.{layer}.{feature} — list the layers (controller, service, repository, dto, entity, exception)

## Layer Rules
One line per layer — what each layer is allowed to do and NOT do:
- Controller: HTTP only, delegate to service
- Service: business logic only, no HTTP knowledge, throw domain exceptions
- Repository: JPA only, no logic
- DTO: separate Request/Response per endpoint, never expose entity directly
- Entity: DB mapping only

## Exceptions
How exceptions flow: domain exception → @ControllerAdvice → HTTP status

## Testing
- Naming convention
- What to mock at each layer

Keep it under 55 lines. Do not duplicate anything from root CLAUDE.md.
```

จากนั้นทำเดียวกันกับ frontend:

📋 **Prompt #6**

```
Create frontend/CLAUDE.md with the same philosophy: only frontend-specific rules. Include:

- React 18, Vite, TypeScript strict mode
- Component file convention (PascalCase.tsx, one component per file)
- Folder structure: components/, hooks/, pages/, lib/, types/
- TanStack Query for all server state, useState only for UI state
- Tailwind: prefer composition over @apply
- Test colocation: Button.tsx + Button.test.tsx
```

---

### ✅ Module 1 Checkpoint

- [ ] folder structure ครบตาม requirements
- [ ] root CLAUDE.md กระชับ < 80 บรรทัด
- [ ] backend/CLAUDE.md และ frontend/CLAUDE.md มีเนื้อหาเฉพาะของแต่ละ stack
- [ ] เปิด `/clear` แล้วถาม `What is this project about?` — Claude ต้องตอบได้โดยไม่ต้องบอกอะไรเพิ่ม

---

## Module 2 — Plan Mode + First Endpoint (2 hr)

> **Learning goal**: ใช้ plan mode ก่อนเขียน code, สร้าง skill แรก

### Step 2.1 — สลับ model สำหรับงาน design

📋 **Prompt #7**

```
/model opus
```

> 💡 **เกร็ด**: design ใช้ Opus เพราะคิดยาก, เขียน boilerplate ใช้ Haiku/Sonnet เพราะถูกกว่า ผู้เรียนจะเห็น cost difference จริงๆ ในตอนท้ายวัน

---

### Step 2.2 — เข้า Plan Mode

กด `Shift+Tab` 2 ครั้ง เพื่อเข้า plan mode (จะมี indicator ขึ้นว่า "plan mode")

📋 **Prompt #8**

```
We need to implement the authentication feature first: POST /api/auth/register and POST /api/auth/login as specified in requirements.md.

In plan mode, give me a complete plan covering:
1. Database schema for User table
2. Java classes we'll create (Entity, DTO, Repository, Service, Controller, Exception)
3. Security config approach (JWT setup, password hashing)
4. Test strategy: which tests at which layer
5. Order of implementation (what depends on what)

Don't write code. Make sure the plan would survive a code review.
```

ดูแผน → กด `Shift+Tab` ออกจาก plan mode → approve

> 💡 **เกร็ด**: Plan mode = read-only, Claude คิดได้แต่ไม่แก้ไฟล์ เหมาะ design phase

---

### Step 2.3 — สร้าง skill แรก: java-endpoint

ก่อน implement auth — สร้าง skill ที่ใช้ generate REST endpoint ซ้ำได้

📋 **Prompt #9**

```
/model sonnet

Create .claude/skills/java-endpoint/SKILL.md. This skill activates whenever I ask to add a new REST endpoint to the backend.

The skill must include:
1. **When to use** — clear trigger conditions
2. **Input** — what info I'll provide (HTTP method, path, request/response shape, auth required?)
3. **Process** — exact steps:
   a. Create DTO classes (request + response)
   b. Add service method
   c. Add controller method with proper annotations
   d. Add validation (@Valid, custom validators)
   e. Add unit test for service
   f. Add integration test for controller (MockMvc)
   g. Update OpenAPI annotations
4. **Conventions** — match backend/CLAUDE.md
5. **Output checklist** — what files should exist after, what should pass

Make the skill specific enough that another developer could follow it without me explaining anything.
```

---

### Step 2.4 — Implement auth ใช้ skill

📋 **Prompt #10**

```
Use the java-endpoint skill to implement POST /api/auth/register.

Spec:
- Request: { email: string, password: string (min 8), name: string }
- Response 201: { id: string, email: string, name: string }
- Response 400: validation errors
- Response 409: email already exists
- Password must be BCrypt hashed before save
- Generate UUID for user id

Run the tests after to verify.
```

หลังจาก register สำเร็จ:

📋 **Prompt #11**

```
Now use the same skill to implement POST /api/auth/login.

Spec:
- Request: { email, password }
- Response 200: { token: string, expiresAt: ISO timestamp }
- Response 401: invalid credentials
- Token = JWT signed with HS256, 24h expiry, claims: sub=userId, email
- Add JwtService bean for token generation/validation
- Add SecurityFilterChain that validates JWT on /api/tasks/**
```

---

### ✅ Module 2 Checkpoint

- [ ] Plan mode ใช้คล่อง
- [ ] `.claude/skills/java-endpoint/SKILL.md` มีเนื้อหาครบ
- [ ] `/api/auth/register` และ `/api/auth/login` ใช้งานได้ (ลองด้วย curl)
- [ ] tests ผ่านทั้งหมด
- [ ] เห็น diff ของ token usage ระหว่าง Opus (plan) vs Sonnet (code)

---

## Module 3 — Token & Context Management (1 hr)

> **Learning goal**: เข้าใจ context window, จัดการเมื่อใหญ่เกิน

### Step 3.1 — สังเกต context ที่ใช้

📋 **Prompt #12**

```
/context
```

จะเห็นว่าใช้ token ไปเท่าไหร่ — ตอนนี้น่าจะ ~15-25% ของ window

> 💬 **อภิปราย**: ตอนนี้ context ประกอบด้วยอะไรบ้าง? CLAUDE.md, ไฟล์ที่เปิดอ่าน, conversation history แต่ละส่วนกินไปเท่าไหร่?

---

### Step 3.2 — ทดลอง /compact

ทำงานต่อไปอีก 4-5 prompts (เพิ่ม endpoint อื่นๆ) จนเห็น context ขยับขึ้นเยอะ

📋 **Prompt #13**

```
/context
```

แล้ว:

📋 **Prompt #14**

```
/compact Focus on the auth feature we just built and the conventions established. Drop earlier exploratory discussion.
```

`/compact` จะ summarize conversation history ให้สั้นลง คงความจำสำคัญไว้

---

### Step 3.3 — เปรียบ /compact vs /clear

ทดลอง:

```
/clear
```

แล้วถาม:

```
What were we just working on?
```

Claude จะลืมหมด เพราะ `/clear` = reset ทั้ง conversation (CLAUDE.md ยังอยู่)

> 💡 **เมื่อไหร่ใช้อะไร**:
> - `/compact` — งานต่อเนื่อง context จะเต็ม แต่ยังต้องการความจำ
> - `/clear` — เริ่มงานใหม่ที่ไม่เกี่ยวกับเดิม
> - ปิด session ไปเลย — เริ่มต้นใหม่ในวันใหม่ (CLAUDE.md จะ load ให้เอง)

---

### Step 3.4 — Optimize CLAUDE.md เพื่อประหยัด token

📋 **Prompt #15**

```
Audit our root CLAUDE.md and backend/CLAUDE.md. Identify:
1. Redundant content between the two files
2. Anything that's "nice to know" vs "must know" — Claude should only see "must know" every session
3. Anything outdated based on what we've actually built

Suggest specific edits. Don't apply yet — show me a diff first.
```

เลือก edit ที่เห็นด้วย แล้ว apply

---

### ✅ Module 3 Checkpoint

- [ ] เข้าใจ output ของ `/context` ทุกบรรทัด
- [ ] ใช้ `/compact` แล้วยังทำงานต่อได้
- [ ] CLAUDE.md ทุกไฟล์รวมกันไม่เกิน 200 บรรทัด

---

## Module 4 — MCP Postgres (2 hr)

> **Learning goal**: ติดตั้ง MCP server, ใช้ query schema จริง

### Step 4.1 — ตั้ง Docker Compose + Postgres

📋 **Prompt #16**

```
Create docker-compose.yml with:
- PostgreSQL 16 service
- volume for data persistence
- exposed on port 5432
- env: POSTGRES_DB=taskflow, POSTGRES_USER=taskflow, POSTGRES_PASSWORD=taskflow_dev

Then run `docker compose up -d` and verify it's running.
```

---

### Step 4.2 — ติดตั้ง Postgres MCP

ออกจาก Claude Code ก่อน แล้วรัน:

```bash
claude mcp add postgres -- npx -y @modelcontextprotocol/server-postgres "postgresql://taskflow:taskflow_dev@localhost:5432/taskflow"
```

เปิด Claude Code ใหม่:

```bash
claude
```

ตรวจสอบ:

📋 **Prompt #17**

```
/mcp
```

ควรเห็น `postgres` server พร้อมใช้

---

### Step 4.3 — ให้ Claude ออกแบบ schema โดย query DB จริง

📋 **Prompt #18**

```
Using the postgres MCP, first check what tables currently exist in the database. Then design and create the schema for our Task feature.

Requirements (from requirements.md):
- tasks: id, title, description, status (todo/doing/done), priority (low/med/high), assignee_id, creator_id, due_date, created_at, updated_at, deleted_at (soft delete)
- comments: id, task_id, user_id, content, created_at
- activities: id, task_id, user_id, event_type, payload (jsonb), created_at

Generate Flyway migration files in backend/src/main/resources/db/migration/. Use V1__, V2__ etc.
After creating migrations, apply them and use postgres MCP to verify the tables exist with correct columns.
```

> 💡 **เกร็ด**: MCP ทำให้ Claude "เห็น" DB จริง ไม่ต้องเดา ผู้เรียนจะรู้สึกว่ามันต่างกับการ generate SQL แห้งๆ

---

### Step 4.4 — Generate JPA entities จาก schema จริง

📋 **Prompt #19**

```
Now query the postgres MCP to inspect the actual schema you just created. Generate matching JPA entities in backend/src/main/java/com/taskflow/entity/task/. 

Use:
- @Entity, @Table with explicit name
- UUID for id
- @ManyToOne for foreign keys (lazy fetch)
- @Enumerated(EnumType.STRING) for status and priority
- Hibernate types for JSONB column

Then verify by running `./mvnw compile`.
```

---

### ✅ Module 4 Checkpoint

- [ ] Postgres รันด้วย Docker
- [ ] MCP postgres ใช้งานได้ (เห็น tables ผ่าน Claude)
- [ ] Migration files สร้างแล้ว apply แล้ว
- [ ] JPA entities ตรงกับ DB schema จริง

---

## Module 5 — Subagents สำหรับ Parallel Work (1.5 hr)

> **Learning goal**: ใช้ subagent แยก context, ทำงาน parallel

### Step 5.1 — เข้าใจ subagent คืออะไร

> 💬 **บรรยายสั้นๆ**: subagent = Claude session ย่อยที่มี context แยกของตัวเอง — ใช้สำหรับ
> - งาน parallel ที่ context ต่างกัน
> - งานที่ pollute main context ไม่ดี (เช่น debug long stack trace)
> - งานเฉพาะทาง (test writer, code reviewer, etc.)

---

### Step 5.2 — สร้าง subagent: test-writer

```bash
mkdir -p .claude/agents
```

📋 **Prompt #20**

```
Create .claude/agents/test-writer.md. This subagent specializes ONLY in writing tests.

It should:
- Read the implementation file thoroughly
- Use the testing conventions from backend/CLAUDE.md
- Generate JUnit 5 + Mockito tests for service layer
- Generate MockMvc tests for controllers
- Aim for 80%+ branch coverage
- Test edge cases (null, empty, boundary values)
- NEVER modify production code, only test files

Frontmatter must include:
- name: test-writer
- description: when to use (after implementing service/controller, before commit)
- model: claude-sonnet-4-6 (good enough, cheaper than Opus for this)
- tools: Read, Write, Edit, Bash (for running tests)
```

---

### Step 5.3 — Implement Tasks API พร้อม parallel test generation

📋 **Prompt #21**

```
Use the java-endpoint skill to implement these endpoints in this order:
1. POST /api/tasks
2. GET /api/tasks (with query filters)
3. GET /api/tasks/{id}
4. PATCH /api/tasks/{id}
5. DELETE /api/tasks/{id} (soft delete)

After each endpoint's implementation is done, dispatch the test-writer subagent to write tests in parallel while you continue with the next endpoint.
```

> 💡 **สังเกต**: main session จะแสดงให้เห็นว่าได้ launch subagent ตอนไหน, subagent ทำเสร็จเมื่อไหร่ ผู้เรียนจะเข้าใจ "ทำงานควบคู่กัน" ของจริง

---

### Step 5.4 — สร้างอีก subagent: schema-reviewer

📋 **Prompt #22**

```
Create .claude/agents/schema-reviewer.md. This agent reviews any database migration before commit.

It should:
- Connect to postgres MCP
- Check if migration is reversible
- Check for missing indexes on foreign keys
- Check for nullable columns that should be NOT NULL
- Check for missing created_at/updated_at on new tables
- Check naming convention (snake_case, plural table names)
- Output a checklist with PASS/FAIL/WARNING for each item

It should not modify the migration, only report.

Frontmatter:
- name: schema-reviewer  
- description: review database migrations
- model: claude-sonnet-4-6
- tools: Read, Bash (for psql), mcp__postgres__*
```

ใช้:

📋 **Prompt #23**

```
Run the schema-reviewer subagent on our V1 and V2 migrations. Show me its report.
```

---

### ✅ Module 5 Checkpoint

- [ ] Subagent ทำงานได้ พร้อม frontmatter ครบ
- [ ] เคยใช้ subagent parallel กับ main task
- [ ] Tasks API endpoints ครบ 5 ตัว มี tests
- [ ] เปรียบเห็นชัดว่า main context ไม่บวมเพราะแยกงานไป subagent

---

## 🎯 End of Day 1

ตอนนี้ควรมี:

- ✅ Backend ที่ register, login, CRUD task ได้จริง
- ✅ Tests ครบ coverage > 75%
- ✅ Postgres ทำงาน, schema ถูกต้อง
- ✅ CLAUDE.md hierarchy ทั้ง 3 ไฟล์ทำงานร่วมกันดี
- ✅ 1 skill (java-endpoint) + 2 subagents (test-writer, schema-reviewer)
- ✅ ใช้ MCP Postgres คล่อง

### 📝 การบ้านก่อน Day 2

ลองทำ endpoints ที่เหลือเอง: comments, activity, reports — ใช้ทุก feature ที่เรียนวันนี้

พรุ่งนี้เจอกันใน Day 2: Frontend + MCP เพิ่ม + Hooks + Capstone
