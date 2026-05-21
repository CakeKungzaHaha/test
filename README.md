# US Stock Econometrics Dashboard v8 Econometrics Pro

เวอร์ชันนี้ไม่ต้องใช้ Alpha Vantage API key แล้ว

ใช้ข้อมูลจาก:

- งบการเงิน: SEC EDGAR `company_tickers.json` + `companyfacts` API
- ราคาหุ้นย้อนหลัง: Stooq + Yahoo fallback historical price
- ข้อมูลปันผล: Yahoo chart dividend events fallback + SEC cash dividends concept

> หมายเหตุ: ราคาหุ้นจาก Stooq + Yahoo fallback อาจมีดีเลย์หรือบาง ticker ไม่มีข้อมูล ส่วนงบการเงินจะใหม่ตามรอบที่บริษัทส่ง 10-Q / 10-K ให้ SEC ไม่ได้อัปเดตทุกวันเหมือนราคา

## วิธีติดตั้งบน Windows

แตก zip แล้วเปิด Command Prompt หรือ PowerShell ที่โฟลเดอร์นี้ จากนั้นรัน:

```bat
npm install
npm run install:all
```

ถ้าต้องการตั้งค่า SEC User-Agent ให้ชัดเจน:

```bat
cd backend
copy .env.example .env
```

เปิด `backend/.env` แล้วแก้ email เป็นของคุณ:

```env
PORT=5000
SEC_CONTACT_EMAIL=your_email@example.com
```

กลับไปโฟลเดอร์หลัก:

```bat
cd ..
npm run dev
```

เปิดเว็บ:

```text
http://localhost:5173
```

## สิ่งที่เว็บทำได้

- Search / autocomplete ticker หุ้นสหรัฐแบบไม่ต้อง API key
- ดึงราคาหุ้นย้อนหลังจาก Stooq + Yahoo fallback
- ดึงงบการเงินจาก SEC EDGAR Company Facts
- แสดงข้อมูลที่ดึงมาและสถานะความครบของข้อมูล
- วิเคราะห์ด้วย econometrics:
  - log return
  - annualized volatility
  - VaR 95%
  - max drawdown
  - beta เทียบ SPY ด้วย OLS
  - R²
- วิเคราะห์งบ:
  - Revenue TTM
  - Net Income TTM
  - Net Margin
  - ROE
  - Debt Ratio
  - Free Cash Flow TTM
  - Dividend Yield TTM
  - Dividend Payout / Net Income
  - Dividend Payout / FCF
- สรุปความเสี่ยงระยะสั้นและระยะยาวด้วยระบบให้คะแนน รวมความเสี่ยง yield trap จากหุ้นปันผลสูง

## ข้อจำกัด

- Stooq + Yahoo fallback ไม่ใช่ real-time feed
- SEC งบการเงินเป็นข้อมูลจาก filings และอาจมี taxonomy/concept ต่างกันในแต่ละบริษัท
- บางบริษัทอาจไม่มี concept บางตัว เช่น CapEx หรือ Operating Cash Flow
- Beta จะคำนวณได้ดีเมื่อราคาหุ้นและ SPY มีวันที่ตรงกันมากพอ
- หุ้นปันผลสูงไม่ได้แปลว่าดีเสมอไป ระบบ v7 จึงเพิ่มเงื่อนไข yield สูง + payout สูง + FCF อ่อน + ราคาลงแรง เพื่อจับสัญญาณ yield trap
- เครื่องมือนี้ไม่ใช่คำแนะนำซื้อขาย

## โครงสร้างไฟล์

```text
backend/   Express API สำหรับดึง SEC + Stooq + Yahoo fallback
frontend/  React Dashboard + Charts
```


## v6 fixes

- Added Yahoo Finance chart fallback when Stooq has no data for a ticker.
- Improved SEC XBRL concept fallback for revenue, equity, cash flow, and capex.
- Improved financial row matching: quarter charts prefer true quarter-duration facts instead of YTD cumulative facts.
- Added visible diagnostics for price-provider attempts and graph data availability.
- Graphs now show a clear “ข้อมูลไม่พอ” message instead of silently appearing blank.



## v8 Econometrics Pro additions

เพิ่มแท็บ **Econometrics** สำหรับวิเคราะห์เชิงเศรษฐมิติโดยไม่ต้องใช้ API key:

- Single-factor OLS: stock return = alpha + beta × SPY return
- Multi-factor OLS: stock return = alpha + SPY + QQQ + IWM + TLT + USO + error
- Rolling Beta 30/90/180 วัน
- Residual volatility เพื่อแยกความเสี่ยงเฉพาะตัวหุ้นออกจากความเสี่ยงตลาด
- Model confidence score ตามจำนวนข้อมูล, จำนวนวันที่ match, R² และ factor coverage

คำเตือน: ผลลัพธ์เป็นการวิเคราะห์จากข้อมูลย้อนหลังและ proxy factors เท่านั้น ไม่ใช่คำแนะนำซื้อขายหรือคำทำนายแน่นอน
