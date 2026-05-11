# Reference 10 — Family-Business Tionghoa Accounting Context (Generic Pattern Template)

> **Purpose:** Generic accounting / tax / control pattern reference for **Tionghoa-Indonesian family-business SME archetype**. Reusable across INDUSIA agency clients matching the archetype (Batam, Jakarta, Surabaya, Medan, etc.). NOT bound to any specific person or project.
>
> **Project Variable Lock:** Operators inject project-specific values via `{{placeholder}}` syntax. For IRN project, see `D:/Projects/Indusia-AI-Logistik/project-variables.md` §1.

---

## 1. The Pattern — Owner-Family-Blended Cashflow

### Typical structure

Tionghoa Indonesian family-business SME (Batam, Jakarta, Surabaya, Medan — common pattern) features:

- **Owner (`{{owner_honorific}}`)** controls operations + final financial decisions
- **Wife (often "Cici" / `{{wife_honorific}}`)** holds informal financial veto / often runs household accounting + sometimes business AR collection
- **Anak / menantu** may hold formal roles (operations / IT / accounting) OR informal advisor positions — relevance depends on `{{owner_age}}` (younger owner = anak kecil, family-of-origin more relevant; older owner = anak/menantu IT-translator gateway common)
- **Cash flow blurs** between business and household — owner takes "kasbon owner" routinely, household expenses sometimes paid from business account, business expenses sometimes paid from personal account
- **Trust internal, distrust external** — family members trusted with cash; external employees subject to control matrix
- **Single bookkeeper / accountant**, often female, often Tionghoa or trusted long-term Indonesian, often paid above-market for loyalty

### Why this matters for audit / compliance

The blended pattern is **NOT illegal** if properly documented. It becomes risk when:
1. **Kasbon owner not recorded** → underreports owner draw, distorts profit
2. **Personal expense charged business** → understates profit fiscally, but flags audit
3. **Business expense paid personal** → understates fiscal expense, owner loses tax deduction
4. **Inter-family loans informal** → no contract, no PPh 23 withholding, no interest rate documentation = tax exposure
5. **Cash counter without daily reconciliation** → fraud risk + audit failure
6. **e-Faktur not synced with actual transactions** → Coretax / DJP cross-check finds gap

### Goal of this reference

Equip akuntan to ADVISE Tionghoa Batam SME owner on **how to maintain family-business operational flexibility WHILE achieving audit-defensible Coretax-compliant pembukuan**. Not "you must change everything" — instead "here's how to make the existing pattern legal + traceable + Coretax-ready".

---

## 2. The 7 Family-Business Accounting Issues + Resolution

### Issue 1 — Kasbon owner (owner draw)

**Pattern:** Owner takes Rp 5-50 jt routinely from business cash for personal use.

**Risk:**
- If recorded as "biaya operasional" → understates fiscal profit → DJP cross-check + denda
- If unrecorded → underreports owner income tax (PPh OP)
- If recorded as "piutang owner" but never settled → DJP audit flag (artificial receivable)

**Resolution:**
- Create **"Akun Drawing Owner"** in equity section (NOT expense)
- Every owner draw → journal: Dr. Drawing Owner / Cr. Cash or Bank
- Year-end → adjust against owner equity capital balance
- Reflects in SPT Tahunan as owner's prive (not deductible expense, properly handled equity flow)
- Coretax neutral (drawing is not a transaction subject to e-Faktur)

**Communication script to owner:**
> *"`{{owner_honorific}}`, kalau Bapak ambil uang dari kasir untuk pribadi, kita catat di akun 'Drawing Owner' di equity. Tidak masuk biaya, tidak masuk pendapatan. Bapak tetap bebas ambil — tapi jelas tercatat. Akhir tahun saya konsolidasi ke modal Bapak. Aman saat audit."*

### Issue 2 — Personal expense charged to business

**Pattern:** Owner pays family dinner / istri shopping / anak tuition with business debit card / business cash.

**Risk:** Understates fiscal profit (DJP koreksi positif), denda + bunga.

**Resolution:**
- Educate owner: only **legitimate business expense** masuk pembukuan
- Personal expenditure on business card → re-class to Drawing Owner at month-end
- Implement **petty cash + business card separation** — business card untuk strictly-business, personal card untuk strictly-personal
- Owner pays personal card from business via Drawing Owner journal (clean trail)

**Communication script:**
> *"`{{owner_honorific}}`, makan keluarga Sabtu malam pakai kartu PT — saya re-class ke Drawing Owner. Bukan biaya bisnis. DJP cek 5 tahun ke belakang, kita aman karena tercatat clear."*

### Issue 3 — Business expense paid personally

**Pattern:** Owner pays vendor / repair / supplier cash from personal pocket; doesn't recover from business.

**Risk:** Owner loses tax deduction; business understates expense fiscally.

**Resolution:**
- Track in **"Akun Reimbursement Owner"** (liability)
- Owner submits receipt + amount → journal: Dr. Biaya X / Cr. Hutang Owner
- Owner reimburses from business cash periodically → journal: Dr. Hutang Owner / Cr. Cash
- Maintains owner deduction + business expense recognition

### Issue 4 — Inter-family informal loans

**Pattern:** Owner lends Rp 100jt to brother/saudara for business expansion / receives loan from istri's family / advances anak for studi.

**Risk:**
- No PPh 23 withholding on interest = denda PPh 23 final 15% + bunga
- No interest rate documented = DJP impute market rate, owner pays additional PPh
- Transfer Pricing exposure if related-party
- AR/AP not in pembukuan = audit flag

**Resolution:**
- **Formal loan agreement** with: borrower, lender, amount, term, interest rate (≥market or zero with TP documentation), repayment schedule
- **Witness signed** (preferably notarized for >Rp 100jt)
- PPh 23 withhold if interest charged + bayar ke DJP monthly
- Record in pembukuan: AR / AP family member
- Annual recap to owner — outstanding family receivables/payables

### Issue 5 — Cash counter without daily reconciliation

**Pattern:** Kasir (often istri-trusted niece / family-trusted external) handles cash daily, no end-of-day count, owner trusts.

**Risk:**
- Fraud (PT Asta Rp 730jt case, PT Cipta Niaga Rp 90jt case — both kasir + no SOD)
- Judol embezzlement pattern (gambling addiction) common in Indonesian fraud cases
- Internal audit fail

**Resolution (LOCK-3 CEO AI Assistant integration):**
- **Daily cash count + reconciliation** by 2nd person (admin OR Asisten prompted check)
- **CEO AI Assistant query:** *"`{{owner_honorific}}`, kas hari ini reconcile? `{{admin_honorific}} {{admin_first_name}}` laporkan ada Rp 2,3 jt, sistem catat Rp 2,3 jt — match ✓"* — automated end-of-day cross-check
- **Segregation of Duties** even if family member — istri pegang kas ≠ admin record ≠ owner approve
- **Surprise audit quarterly** — owner spot-check unannounced
- **Coretax audit trail** via Asisten conversation log

### Issue 6 — e-Faktur not synced

**Pattern:** Admin issues invoice manually / via spreadsheet, e-Faktur done monthly batch end-of-month, gaps in between, sometimes missed transactions.

**Risk:** DJP cross-check via NLE / SP2 / Coretax finds transaction without matching e-Faktur → denda PPN + PPh.

**Resolution:**
- **Real-time e-Faktur sync** via Coretax API integration (sister skill `smart-fleet-architect/09-integration-ai-ml-layer.md`)
- **CEO AI Assistant query:** *"`{{owner_honorific}}`, e-Faktur status hari ini: 47/47 OK. Tidak ada gap."* — daily proactive
- Admin training on PER-11/PJ/2025 e-Bupot Unifikasi rules
- Monthly closing checklist includes e-Faktur sync verify

### Issue 7 — Religious calendar accounting impact

Tionghoa-specific accounting events:

| Event | Accounting Impact |
|---|---|
| **Imlek bonus / angpao karyawan** | Operating expense (legit) — must be documented with karyawan signature list, recipient ID, amount. Subject to PPh 21 if >Rp 600k threshold per kary per event. |
| **Imlek bonus / angpao customer / vendor** | Business entertainment — deductible if business-purpose documented (PER-Dirjen specific rules). Cap on entertainment expense per PER. |
| **Klenteng donation** | Charitable expense — deductible only if to registered yayasan with NPWP + tanda terima resmi. Klenteng must have legal form (yayasan) to qualify. |
| **Family ancestor Qingming expense** | Personal — Drawing Owner, NOT business expense |
| **Imlek operational shutdown** | No revenue impact recognition — accrual continues. Karyawan tetap dibayar (paid leave per UU Ketenagakerjaan). Reflects in cashflow forecast. |
| **Vesak donation Buddhist** | Same rule as klenteng — registered yayasan + bukti |

**Communication script:**
> *"`{{owner_honorific}}`, angpao karyawan Imlek bisa masuk biaya kalau ada list nama + tanda tangan + jumlah, dan PPh 21 dipotong kalau per orang >Rp 600k. Saya buatkan template biar Bapak tahun depan tidak ribut DJP."*

---

## 3. Family-Business Sub-Account Phase 2 (LOCK-3 Asisten Integration)

When CEO AI Assistant Phase 2 expands sub-account access (Asisten architecture §3.7):

| Family-business sub-account | Scope | Akuntansi Implication |
|---|---|---|
| **Istri (wife) / `{{wife_honorific}}`** | Finance read + cashflow query + AR aging | Can ask Asisten: *"Cashflow minggu ini gimana?"* — read-only, no transaction execution |
| **Anak (son) — IT translator** | Full read + tech ops + escalation | Can configure Asisten preferences, NO finance write |
| **Menantu — finance role (if formal employee)** | Scoped finance write + tax sync | Can execute reconciliation + e-Faktur sync, but NO bank transfer authority |
| **Agency-side advisor (e.g., `{{gtm_protagonist}}` if family-connected to owner)** | Cross-architecture view | Read-only on Asisten arch + analytics, NO financial transaction access |

**Anti-pattern:** Single shared family-account login. Each family member has own sub-account with audit trail of which person did what.

---

## 4. Audit-Defensible Pembukuan Template for Tionghoa Family-Business SME

### Mandatory chart-of-accounts additions (vs generic)

```
EQUITY
  3000  Modal Owner
  3100  Drawing Owner          ← NEW for family-business
  3200  Tambahan Modal Disetor (capital injections from family)

LIABILITIES
  2300  Hutang Owner / Family  ← NEW for reimbursement + family loans

CURRENT ASSETS
  1400  Piutang Owner / Family ← NEW for kasbon + family advances

OPERATING EXPENSE (with documentation requirements)
  6300  Angpao Karyawan (Imlek/THR Tionghoa)  ← documented with name+ID+signature list
  6310  Entertainment & Hubungan Bisnis (limit per PER)
  6320  Donasi Charitable (to yayasan with NPWP only)
  6330  Religious Calendar Operational (Imlek shutdown payroll, etc.)
```

### Mandatory monthly closing checklist (additions to generic)

```
✓ Drawing Owner balance reconciled — match cash withdrawn vs ledger
✓ Hutang/Piutang Owner reconciled — family transaction trail clean
✓ e-Faktur Coretax sync verified — daily flag via Asisten
✓ Cash count cross-check — admin + Asisten + monthly owner spot
✓ Religious calendar events documented (kalau bulan ini ada Imlek/Cap Go Meh)
✓ Inter-family loan PPh 23 withhold if interest charged
```

### Year-end SPT preparation (additions)

```
✓ Drawing Owner sum → Owner SPT OP prive declaration
✓ Hutang/Piutang Family ≥Rp 100jt → potential TP Doc requirement
✓ Family-member salary (anak/menantu kalau formal kary) → PPh 21 normal + dokumentasi market-rate
✓ Klenteng/yayasan donation → only deductible with valid tanda terima resmi
```

---

## 5. Coretax-Ready Audit Trail Integration

Coretax DJP requires real-time + auditable transaction trail. For family-business pattern:

| Transaction Type | Coretax Treatment | Asisten Integration |
|---|---|---|
| Sales invoice | e-Faktur real-time sync | Asisten query: *"`{{owner_honorific}}`, e-Faktur INV-5678 sudah Coretax sync ✓"* |
| Purchase invoice | e-Bupot Unifikasi (PER-11/PJ/2025) | Asisten query: *"PPh 23 vendor X sudah dipotong + setor"* |
| Drawing Owner | NOT subject to e-Faktur (equity flow) | Asisten log: drawing_owner amount + audit trail in `asisten_conversation_log` |
| Hutang/Piutang Owner | Not subject to e-Faktur kalau no interest. PPh 23 if interest charged. | Asisten flag if family transaction missing PPh withhold |
| Karyawan angpao Imlek | PPh 21 monthly include | Asisten reminder pre-Imlek + verify withhold |
| Klenteng donation | Deductible kalau yayasan-registered | Asisten verify yayasan NPWP before approval |

---

## 6. Communication Tone — Owner Layer (Tionghoa Practical-Skeptic)

When advising Tionghoa-Indonesian SME owner on family-business accounting issues, register matches `{{owner_register}}`:

- **Practical, not preachy** — *"`{{owner_honorific}}`, ini cara aman buat keluarga Bapak, audit lewat tanpa ribut"* > *"Bapak harus tertib pembukuan"*
- **ROI-numerate** — quantify the cost of NOT doing it: *"Kalau DJP audit dan tidak ada documentation, denda 4x kurang bayar — kalau kasbon owner Rp 500jt setahun, denda potensial Rp 50jt+"*
- **Respect family-trust pattern** — DON'T frame istri / anak as fraud-risk. Frame as "audit-defensibility for family protection"
- **Religious calendar respect** — acknowledge Imlek as operational reality, plan accounting around it (not against it)
- **Singapore B2B context if applicable** — cross-border transactions trigger additional TP / customs / e-Faktur rules; flag proactively

---

## 7. Anti-Patterns Specific to Family-Business Tionghoa

- ❌ **"Pisahkan total keuangan keluarga dan bisnis"** — unrealistic + culturally tone-deaf. Instead: "Documented overlap with audit trail."
- ❌ **Frame istri pegang kas as fraud-risk** — culturally insulting. Frame as "SOD for protection of trusted family member."
- ❌ **Refuse to handle inter-family loan** — owner will do it anyway. Educate on formal documentation.
- ❌ **Default-deduct klenteng donation without yayasan check** — common admin shortcut; risk audit denial.
- ❌ **Treat anak/menantu salary at below-market** — Transfer Pricing exposure; document market-rate.
- ❌ **Imlek shutdown without payroll planning** — karyawan must be paid (UU Ketenagakerjaan); cashflow forecast must accommodate.
- ❌ **English-only audit report to `{{owner_honorific}}`** — Bahasa Indonesia primary; technical English term inline OK.

---

## 8. WS7 Integration Notes (Pending)

WS7 (Indonesian culture deep-dive) running. Expected findings on Karyawan layer (sopir / admin / mekanik) face-saving + religious calendar (Eid / Ramadhan / Christmas) will inform **karyawan-facing payroll communication + Imlek-vs-Eid dual-calendar payroll planning**.

This file (owner-layer Tionghoa) doesn't block on WS7 — owner-pattern is well-defined via LOCK-4 archetype. WS7 ripples to:
- `08-internal-control-antifraud.md` — anti-fraud-with-dignity payroll communication
- `09-coaching-komunikasi.md` — sopir-facing wage explanation Bahasa Indonesia register
- This file §6 cross-calendar payroll planning

---

## 9. Reusability for Other Tionghoa SME Clients

Reuse pattern across INDUSIA agency clients:

- **Tionghoa Batam / Jakarta / Surabaya / Medan SME** — full reuse with minor regional honorific + setting adjustments
- **Tionghoa-Indonesian PMA (foreign-investment company)** — additional layer: TP Doc + transfer pricing primary scope, this file as secondary
- **Indonesian Muslim family-business SME** — DO NOT REUSE — different cultural / accounting nuances (zakat, halal supply chain, Eid bonus rules different). Needs separate Muslim-family-business overlay file.

---

## 10. Cross-References

- Owner persona archetype + project bindings → project-level `project-variables.md` (e.g., `D:/Projects/Indusia-AI-Logistik/project-variables.md` for IRN)
- 2-layer cultural model LOCK-5 → memory + `CLAUDE.md` §"2-Layer Cultural Model"
- Internal control + segregation of duties → `08-internal-control-antifraud.md`
- Coaching komunikasi owner → `09-coaching-komunikasi.md`
- PPh 21 angpao / bonus → `05-pajak-pph-lengkap.md` Addendum
- e-Faktur Coretax PER-11 → `06-ppn-efaktur-coretax.md`
- CEO AI Assistant audit-trail integration (LOCK-3) → `smart-fleet-architect/11-ceo-ai-assistant-architecture.md` §4
- Anti-fraud-with-dignity cross-layer (kasir-trusted family member case) → `smart-fleet-architect/10-anti-fraud-with-dignity.md`
- Creative direction Tionghoa overlay (matched pair) → `creative-video-director/07-tionghoa-batam-overlay.md`
- UX Tionghoa owner overlay (matched pair) → `senior-ux-architect-id/09-tionghoa-batam-owner-overlay.md`
- Tacit Batam business culture context → `pakar-logistik-batam/06-tacit-batam-context.md` Addendum I (Tionghoa demographic)
