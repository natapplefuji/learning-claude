# Claude Code Mastery — Workshop Overview

> 2-day intensive workshop สำหรับ developer ที่อยากเชี่ยวชาญ Claude Code

---

## ภาพรวม

ผู้เรียนจะสร้าง **TaskFlow** — full-stack task management system (Spring Boot + React) ตั้งแต่ empty folder จนถึง production-ready โดยใช้ feature ของ Claude Code ทุกตัวอย่างมีเหตุผล

### Outcome

จบ workshop แล้วผู้เรียนจะสามารถ:

1. ตั้ง project ใหม่ที่ Claude เข้าใจ context ทันทีในทุก session
2. เลือก model ให้เหมาะกับงาน (Haiku/Sonnet/Opus)
3. สร้าง skill และ subagent สำหรับงานที่ทำซ้ำในทีม
4. ต่อ MCP server เพื่อให้ Claude ทำงานกับ DB, GitHub, browser ได้
5. ตั้ง hooks เพื่อ enforce quality bar ของทีมอัตโนมัติ
6. ใช้ Claude Code ทำงานจริงได้ effective ขึ้น 3-5 เท่า

---

## ไฟล์ในชุดนี้

| ไฟล์ | สำหรับ | คำอธิบาย |
|---|---|---|
| `requirements.md` | ผู้เรียน | โจทย์ project — แจกผู้เรียนวันแรก |
| `day1-guide.md` | ผู้เรียน + ผู้สอน | Step-by-step วันแรก (5 modules) — copy prompt ได้เลย |
| `day2-guide.md` | ผู้เรียน + ผู้สอน | Step-by-step วันสอง (5 modules + capstone) |
| `instructor-notes.md` | ผู้สอน | จุดที่ต้องระวัง, expected outputs, แนวคำถาม |

---

## Schedule overview

### Day 1 — Foundation + Backend

| Time | Module | Topic | Key features |
|---|---|---|---|
| 09:00 - 10:30 | M1 | Setup & First Conversation | `/init`, CLAUDE.md hierarchy |
| 10:45 - 12:45 | M2 | Plan Mode + First Endpoint | Plan mode, Skills, model switching |
| 13:45 - 14:45 | M3 | Token & Context Management | `/context`, `/compact`, `/clear` |
| 15:00 - 17:00 | M4 | MCP Postgres | MCP setup, schema design with DB access |
| 17:00 - 18:30 | M5 | Subagents | Parallel work, specialized agents |

### Day 2 — Frontend + Integration + Polish

| Time | Module | Topic | Key features |
|---|---|---|---|
| 09:00 - 11:00 | M6 | Frontend with Skills | react-component skill, api-client subagent |
| 11:15 - 12:45 | M7 | MCP Playwright | E2E with browser MCP |
| 13:45 - 14:45 | M8 | MCP GitHub | Issue-driven workflow, PRs |
| 15:00 - 17:00 | M9 | Hooks, Settings, Plugins | settings.json, safety hooks, plugins |
| 17:00 - 18:30 | M10 | Capstone Challenge | ใช้ทุก feature ภายใน 60 นาที |

---

## Tech requirements (ผู้เรียน)

ก่อนวันแรก ผู้เรียนต้องเตรียม:

### Software
- [ ] Claude Code: `npm install -g @anthropic-ai/claude-code`
- [ ] Java 21 (Temurin หรือ Oracle): `java -version`
- [ ] Maven 3.9+: `mvn -version`
- [ ] Node.js 20+: `node -v`
- [ ] Docker Desktop running: `docker ps`
- [ ] Git: `git --version`

### Accounts
- [ ] Anthropic API key (workshop จะ provide ถ้ายังไม่มี)
- [ ] GitHub account
- [ ] GitHub Personal Access Token with `repo` scope

### Knowledge prereq
- ใช้ Java + Spring เป็น (basic level)
- ใช้ React + TypeScript เป็น
- เข้าใจ REST API, JWT, SQL basics
- เคยใช้ git และ CLI

---

## ระดับความยาก

แต่ละ module มีระดับความยาก:

- 🟢 **M1, M3, M8** — basic features, ทุกคนทำได้
- 🟡 **M2, M6, M9** — intermediate, ต้องใช้ความเข้าใจ
- 🔴 **M4, M5, M7, M10** — advanced, อาจมีคนติด

แนะนำให้มี **assistant instructor 1 คนต่อผู้เรียน 8-10 คน** ในช่วง M4 และ M10

---

## Cost budget (สำคัญ!)

ตลอด 2 วัน ผู้เรียนแต่ละคนคาดว่าใช้ token ประมาณ:

- Sonnet: 2-3M tokens (~$30-45 ต่อคน)
- Opus: 200-400K tokens (~$15-30 ต่อคน เน้นใช้ใน plan mode)
- Haiku: 500K-1M tokens (~$1-2 ต่อคน)

**รวม ~$50 ต่อคน** สำหรับ 2 วัน — ผู้สอนควร provision budget ล่วงหน้า

---

## Key teaching moments

### Day 1 highlights

**M1.3 — สร้าง CLAUDE.md ครั้งแรก**: ผู้เรียนหลายคนจะเขียนยาวเกิน — ใช้โอกาสนี้สอนเรื่อง token cost ของไฟล์ที่อ่านทุก session

**M2.2 — Plan mode**: ผู้เรียนที่เคยใช้ Cursor มาก่อนจะอยากกระโดดไป code — ต้องสอนให้อดทน, plan ดีจะ save 2-3 รอบของการแก้

**M4.3 — MCP Postgres ครั้งแรก**: ผู้เรียนจะตื่นเต้นที่ Claude "เห็น" DB ได้ — ใช้ moment นี้สอนว่า MCP เปิดประตูอะไรอีกบ้าง

**M5.3 — Subagent parallel**: ผู้เรียนต้องเห็นกับตาว่า main session ไม่ block ระหว่างที่ subagent ทำงาน

### Day 2 highlights

**M6.4 — multi-agent orchestration**: เป็นจุดที่ผู้เรียนเริ่มคิดเป็น "conductor" ไม่ใช่ "ผู้ใช้"

**M7.2 — Playwright MCP**: ปกติเป็นช่วงที่ผู้เรียนตื่นเต้นที่สุด — Claude เปิด browser คลิกจริง

**M9.2 — Pre-tool-use hook**: ทดลองให้ Claude ทำสิ่งอันตรายแล้วถูก block — เป็น lesson ที่จดจำมาก

**M10 — Capstone**: time pressure ทำให้เห็นว่าใครยังลังเลตอนเลือก feature ตัวไหน — guide ให้ตัดสินใจเร็ว

---

## วิธีปรับให้ short version

ถ้าทำได้แค่ 1 วัน เลือก:

- M1 (Setup) - 1h
- M2 (Plan + Skill) - 1.5h
- M4 (MCP Postgres) - 1.5h
- M5 (Subagents) - 1h
- M9.1-9.3 (Settings + Hooks) - 1h
- Mini capstone (เพิ่ม 1 endpoint + 1 component) - 1h

ตัดออก: frontend deep-dive, GitHub MCP, Playwright

---

## Common pitfalls และวิธีแก้

| Pitfall | สาเหตุ | วิธีแก้ |
|---|---|---|
| ผู้เรียน paste prompt แล้วไม่อ่าน Claude ตอบ | ขี้เกียจอ่าน | บังคับให้ summarize Claude's response ด้วยปากเปล่า |
| CLAUDE.md ยาว 200+ บรรทัด | คิดว่ายาว = ดี | สอน rule: "ลบจนเหลือเฉพาะที่ Claude ผิดถ้าไม่มี" |
| ใช้ Opus ตลอด | "ของดีต้องใช้" | แสดง cost dashboard ตอนพักเที่ยง |
| ไม่ใช้ plan mode | กระโดด code | กลับไป revert งานเขียนผิด ให้เห็น cost ของการไม่ plan |
| Skill ที่เขียนใช้ครั้งเดียวก็ไม่ทำงาน | spec ไม่ชัด | เขียนเป็น checklist ที่ verify ได้ |
| MCP ไม่ทำงาน | env var ไม่ถูก, version mismatch | มี troubleshooting section ใน guide |

---

## หลังจบ workshop

ส่ง follow-up email ใน 1 สัปดาห์:

```
Subject: How's your Claude Code journey going?

[name],

อาทิตย์ที่ผ่านมาได้ลองใช้ Claude Code กับงานจริงไหม?

ขอ 3 อย่าง:
1. Skill ที่เขียนเองเพื่อทีม (แชร์ในกลุ่มได้ไหม?)
2. ปัญหาที่เจอที่ workshop ไม่ได้สอน
3. รีวิว workshop หน้าที่อยากเพิ่มอะไร

อีก 2 สัปดาห์เรามี office hour 1 ชม. — สมัครได้ที่ [link]
```
