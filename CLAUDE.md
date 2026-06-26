# HC Analytics Portal — Project Memory (CLAUDE.md)

> Working memory for the HR Dashboard project. Keep concise and current.
> Owner: Earth — HR Strategy & Agentic AI Transformation Lead.

## Project
- **index.html** — single-file HTML dashboard (no backend). ข้อมูลทั้งหมดฝังเป็น JavaScript constants ในไฟล์เดียว. (เดิมชื่อ `HC_Analytics_Portal_v7.html` — เปลี่ยนเป็น index.html เพื่อให้ URL สั้นและไม่ผูกกับเลขเวอร์ชัน; เวอร์ชันดูจาก badge ในหน้า/ git.)
- Library: Chart.js 4.4.1 (CDN) + Sarabun font. ขนาด ~142 KB.
- **Live:** https://jinnaphas.github.io/HRdashboard/ (GitHub Pages, serve จาก `main`)
- **Repo:** https://github.com/jinnaphas/HRdashboard (public)
- **Tabs:** HC · OT · Salary · Resign · Training · WFP
- Raw data source: รายเดือน `Employee Database <Month> 2026.xlsx` (Google Drive, ~220 columns).

## Data status (as of Jun 2026, v7.2)
| Tab | Data through |
|-----|--------------|
| HC | Jun 2026 |
| Salary | Jun 2026 — `byRS_Jun`/`byLoc_Jun`/`byGroup_Jun` เป็นข้อมูลจริงจาก payroll register แล้ว (เลิกใช้ค่าประมาณ) |
| Resign | Jun 2026 (Jun = 4 ราย, ไม่ระบุสาเหตุ; YTD 37) |
| OT | Jun 2026 (May พีค 2,923 ใบ, Jun 2,373; YTD 11,002 / ~5.16 MB est.) |
| Training | Jun 2026 — May/Jun จากรายงานอบรม; e-learning ประจำปี = 0 ชม. จึงนับชั่วโมงจริงไม่ inflate (records/internal นับรวม e-learning ตามฟอร์แมตเดิม) |
| WFP | ⚠️ ยังเป็น snapshot เก่า (`WFP_BU_DATA` รวม CMI ≈ 796–800) — ยังไม่ได้ refresh เป็น Jun (ไม่มีไฟล์ source) |

> OT cost = est. จากสูตรในโค้ด `basic/30/8 × 3h × 1.5x` ต่อใบ (ไฟล์ OT มีแค่ doc/วันที่/ชื่อ/สถานะ ไม่มียอดเงินจริง).

HC monthly total: Jan 813 · Feb 809 · Mar 804 · Apr 804 · May 793 · Jun 794.

## Companies & Revenue Stream (RS) — verified against raw Jun 2026 data
ค่า RS ด้านล่างตรวจสอบกับข้อมูลดิบคอลัมน์ "ชื่อหน่วยงาน (ระดับ 1)" (col 125) ครบทั้ง 794 คน

| Code | บริษัท | HC (Jun) | RS (Structure ระดับ 1) |
|------|--------|----------|------------------------|
| PEM | พรีไซซ อีเลคตริค แมนูแฟคเจอริ่ง | 407 | Power Distribution & Energy Management |
| PSP | พรีไซซ ซิสเท็ม แอนด์ โปรเจ็ค | 211 | Intelligent Grid |
| SBP | สงขลาไบโอเพาเวอร์ | 64 | Renewable Energy |
| PDE | พรีไซซ ดิจิตอล อีโคโนมี่ | 45 | Digital, Automation & ESG Solution |
| PCC | พรีไซซ คอร์ปอเรชั่น | 43 | Precise |
| PCE | พรีไซซ คลีน เอ็นเนอร์จี | 10 | Renewable Energy |
| PSL | พรีไซซ สมาร์ท ไลฟ์ | 9 | Bio-Circular-Green Economy (BCG) |
| PPP | พรีไซซ เพาเวอร์ โปรดิวเซอร์ | 5 | Renewable Energy |

> CMI ออกจากกลุ่ม พ.ค. 2569 — ไม่มีใน May/Jun snapshot แล้ว.

## Employee Group (EG) — ลำดับการแสดงผลที่ถูกต้อง
Ops (กลุ่ม ปฏิบัติการ) → Prof (กลุ่ม วิชาชีพ) → Mgr (จัดการ/ชำนาญการ) → MgrPM (จัดการ/ชำนาญการ/PM) → Exec (บริหาร/ผู้เชี่ยวชาญ)
- Jun 2026: Ops 396 · Prof 192 · Mgr 79 · MgrPM 113 · Exec 14 = 794.
- คุมลำดับด้วย `EG_ORDER` ใน HTML. (ไฟล์เก่าเคยเรียง MgrPM ก่อน Mgr — แก้แล้วใน v7.)

## Employee Type
Perm (ประจำ-รายเดือน) · ContM (สัญญาจ้างกำหนดระยะ-รายเดือน) · ContSp (สัญญาจ้างพิเศษ-รายเดือน) · RetM (หลังเกษียน-รายเดือน) · ContD (สัญญาจ้าง-รายวัน)

## Key JS constants in the HTML
`HC_MONTHLY_CO` (month→company→{total,byGroup,byType,byLoc,byGender}) · `WFP_BU_DATA` · `HC_DATA`/`HC_ARR`/`MONTHS_ALL` · `COMPANIES`/`EMP_GROUP`/`EMP_TYPE`/`LOCATION` · `SALARY_DATA` · `OT_DATA` · `TRAINING_DATA`/`EDU_MATRIX` · `RESIGN_DATA`/`RESIGN_RECORDS` · `NEW_HIRE_DATA`/`NEW_HIRE_RECORDS` · `EG_ORDER`.
- Default filters: `currentPeriod='ALL'`, `currentCompany='ALL'`.

## Raw data column indices (0-based, openpyxl `data_only=True`)
emp_code 0 · gender 8 · company_code 21 · company_th 22 · location 28 · emp_type 58 · emp_group 60 · basic_sal 61 · RS/Structure1 125 · Age 183 (format "28:10") · Service Year 185.
- New-hire detection: emp_code ขึ้นต้น "69" = ปี 2569/2026, หรือเทียบชุด emp_code ระหว่างเดือน.

## Glossary
HC=Headcount · OT=Overtime · WFP=Workforce Planning · EG=Employee Group · RS=Revenue Stream (Structure L1) · BU=Business Unit · YTD=Year-to-Date.
- Structure hierarchy: L1 RS → L2 Company → L3 BU → L4 Dept → L5 Section.

## Update workflow (เมื่อมีข้อมูลเดือนใหม่)
1. ดึง raw `.xlsx` จาก Google Drive → คำนวณตัวเลขตาม column indices ด้านบน + EG order ที่ถูกต้อง.
2. อัปเดต JS constants ที่เกี่ยวข้อง (`HC_MONTHLY_CO`, `MONTHS_ALL`, `HC_DATA`/`HC_ARR`, `NEW_HIRE_*`, `COMPANIES`/`EMP_GROUP`/`EMP_TYPE`/`LOCATION`, `WFP_*`, และ default `currentPeriod`).
3. ตรวจ reconcile: ผลรวม byGroup/byType/byCompany ต้องเท่ากับ HC total ของเดือนนั้น.
4. commit + push → GitHub Pages deploy อัตโนมัติ.
