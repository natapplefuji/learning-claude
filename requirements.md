# Project: TaskFlow — Full-Stack Task Management System

> **Workshop project สำหรับเรียน Claude Code ครบทุก feature**
> ผู้เรียนจะสร้างระบบจริงตั้งแต่ empty folder จนถึง production-ready

---

## Why this project

โจทย์นี้ออกแบบให้บีบให้ผู้เรียนได้ใช้ feature ของ Claude Code ทุกตัวอย่างเป็นธรรมชาติ — ไม่ใช่แค่เรียนรู้ feature แล้วลอง แต่เป็น **ใช้ feature เพราะมันแก้ปัญหาที่เจอจริง**

| Feature ของ Claude Code | จะได้ใช้เพราะ... |
|---|---|
| `/init` + `CLAUDE.md` | project มี 2 stack ต้องบอก Claude ให้รู้จัก convention ของแต่ละฝั่ง |
| `/context`, `/compact`, `/clear` | codebase ใหญ่พอที่ context จะเต็มถ้าไม่จัดการ |
| Plan mode | feature ใหญ่ที่ต้อง design ก่อน code |
| Subagents | งาน parallel — backend + frontend + tests พร้อมกัน |
| Skills | งาน repetitive ที่ทำซ้ำได้ — generate REST endpoint, React component |
| MCP (Postgres) | query schema จริง, generate migration |
| MCP (GitHub) | สร้าง issue, PR, review |
| MCP (Playwright) | E2E test ที่ run จริง |
| Hooks | auto-format, run tests ก่อน commit |
| `settings.json` | model selection, permissions, env vars |
| `/model` | สลับ Haiku/Sonnet/Opus ตาม task |
| Plugins | ติดตั้ง community plugin มาช่วยงานเฉพาะ |

---

## Product Overview

**TaskFlow** คือระบบจัดการ task ภายในทีม — ใช้แทน Trello/Asana แบบเรียบง่าย

### User stories

**As a team member, I want to:**
1. สร้าง task พร้อม title, description, priority (low/medium/high), due date
2. เห็น task ทั้งหมดของฉันใน dashboard แยกตาม status (todo / in progress / done)
3. assign task ให้คนอื่นในทีม
4. คอมเมนต์บน task เพื่อ update progress
5. filter task ตาม assignee, priority, due date
6. เห็น activity feed ของทั้งทีมว่ามีอะไรเปลี่ยนบ้างวันนี้

**As a team lead, I want to:**
1. เห็น overview ว่าใครรับผิดชอบอะไร, ใครงานล้น
2. export report สรุป task ที่เสร็จ/คงค้างประจำสัปดาห์

---

## Tech Stack (บังคับ — เพื่อให้สอนได้สม่ำเสมอ)

### Backend
- **Java 21** + Spring Boot 3.x
- Spring Web, Spring Data JPA, Spring Security
- PostgreSQL 16
- Maven Wrapper
- JUnit 5 + Mockito + Testcontainers

### Frontend
- **React 18** + TypeScript
- Vite
- TanStack Query (data fetching)
- Tailwind CSS + shadcn/ui
- Vitest + React Testing Library

### Infrastructure
- Docker Compose (Postgres + service)
- GitHub Actions (CI)

---

## Functional Requirements

### Backend API

#### Authentication
- `POST /api/auth/register` — register user (email, password, name)
- `POST /api/auth/login` — return JWT token
- JWT validation middleware on all `/api/tasks/**` endpoints

#### Tasks
- `GET /api/tasks` — list tasks (support query params: `?assignee=`, `?status=`, `?priority=`)
- `GET /api/tasks/{id}` — get one task with comments
- `POST /api/tasks` — create task
- `PATCH /api/tasks/{id}` — update task (status, priority, assignee, etc.)
- `DELETE /api/tasks/{id}` — soft delete

#### Comments
- `POST /api/tasks/{id}/comments` — add comment
- `GET /api/tasks/{id}/comments` — list comments

#### Activity Feed
- `GET /api/activity` — last 50 events across all tasks (created, status_changed, assigned, commented)

#### Reports
- `GET /api/reports/weekly` — JSON summary of completed vs pending tasks for current week

### Frontend

#### Pages
1. **Login / Register** — สวยงาม minimal, validate inline
2. **Dashboard** — Kanban board 3 columns (todo/doing/done), drag-drop ระหว่าง column
3. **Task Detail** — modal หรือ separate page, show comments, edit fields
4. **Team View** — list ของทุกคน + task count ของแต่ละคน
5. **Activity Feed** — sidebar หรือ page แสดง event ล่าสุด

#### UX requirements
- Optimistic updates เวลา drag-drop
- Loading skeleton, error states
- Keyboard shortcuts (`c` = create task, `/` = focus search)
- Dark mode toggle

---

## Non-Functional Requirements

### Quality bar
- Backend test coverage ≥ 75% (focus on service layer)
- Frontend: critical paths covered (login flow, create task, drag-drop)
- E2E: 3 happy paths covered ด้วย Playwright (register → login → create task → mark done)

### Performance
- API p95 latency < 200ms สำหรับ list endpoints
- Initial page load < 2s บน throttled 3G

### Security
- Passwords hashed (BCrypt)
- JWT expiry 24h
- CORS whitelist (เฉพาะ frontend origin)
- SQL injection ป้องกันด้วย JPA parameterized queries
- XSS: sanitize comment content ก่อน render

---

## Project Structure (บังคับ)

```
taskflow/
├── CLAUDE.md                    # root memory — project overview
├── .claude/
│   ├── settings.json            # model preference, permissions
│   ├── skills/                  # custom skills
│   │   ├── java-endpoint/
│   │   │   └── SKILL.md
│   │   └── react-component/
│   │       └── SKILL.md
│   └── hooks/
│       ├── pre-commit.sh        # format + lint
│       └── pre-tool-use.sh      # safety check
├── backend/
│   ├── CLAUDE.md                # backend-specific conventions
│   ├── pom.xml
│   └── src/...
├── frontend/
│   ├── CLAUDE.md                # frontend-specific conventions
│   ├── package.json
│   └── src/...
├── e2e/
│   └── playwright.config.ts
├── docker-compose.yml
└── README.md
```

---

## Constraints (สำคัญสำหรับ workshop)

1. **เริ่มจาก empty folder** — ห้ามใช้ Spring Initializr UI หรือ `create-vite` ตรงๆ ให้ Claude generate scaffold เอง
2. **ทุก commit ต้อง pass hooks** — บังคับให้ผู้เรียนตั้ง hooks ทำงาน
3. **ห้ามใช้ Opus กับงาน trivial** — ฝึกการเลือก model
4. **ทุก feature ใหญ่ต้องใช้ plan mode** — ห้าม jump เขียน code ทันที
5. **CLAUDE.md ต้อง update เมื่อ convention เปลี่ยน** — เป็น living document

---

## Out of Scope (อย่าทำ)

- ไม่ต้องทำ deployment จริงไปยัง cloud
- ไม่ต้อง implement file upload
- ไม่ต้องทำ real-time (WebSocket)
- ไม่ต้องทำ mobile app
- ไม่ต้องมี notification system

---

## Success Criteria

ผู้เรียนจบ workshop แล้วต้อง:

1. **มี working app** — register/login, สร้าง task, drag-drop, ดู activity ได้จริง
2. **อธิบายได้ว่าทำไม** ใช้ Haiku ตรงไหน, Sonnet ตรงไหน, Opus ตรงไหน
3. **CLAUDE.md ของตัวเอง** ที่ Claude อ่านแล้วเข้าใจ project เลยโดยไม่ต้องอธิบายเพิ่ม
4. **Custom skill อย่างน้อย 1 ตัว** ที่ใช้ generate code ซ้ำได้
5. **MCP servers** ทั้ง 3 ตัว (Postgres, GitHub, Playwright) ต่อใช้งานได้
6. **Hooks ทำงาน** — pre-commit format + run tests
7. **Capstone feature** — เพิ่ม feature "task labels/tags" ภายใน 1 ชั่วโมง โดยใช้ทุก feature ของ Claude Code อย่างเหมาะสม
