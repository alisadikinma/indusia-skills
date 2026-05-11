# Reference 05 — Voice UX Low-Cognitive-Load (Asisten Phase 2)

> **Anchor — Layer: Owner primary (Phase 2 only — voice deferred per CEO AI Assistant roadmap).** Owner uses voice when driving, eating, in mosque/klenteng, or hands-busy. Cognitive load must be MINIMAL — owner shouldn't have to think about syntax or wait for slow responses.
>
> **Owner Visibility Gap covered:** ALL 7 — same as text chat, accessed via voice.

---

## 1. Phase Status

**Phase 1 (Bulan 4-6):** TEXT-ONLY via WhatsApp chat. Voice NOT in scope.
**Phase 2 (Bulan 7-9):** Voice call gateway added — Twilio + Whisper STT + ElevenLabs/Google TTS.
**Phase 3 (Bulan 10-12):** Voice optimization + multi-tenant.

This file specifies Phase 2 design — guidance for when voice ships, not current implementation.

---

## 2. Design Principles (Voice-Specific)

1. **Latency budget ≤1.5 sec** end-to-end (STT + LLM + TTS) — anything slower feels broken
2. **Owner can interrupt anytime** — barge-in supported (don't lock owner into listening full reply)
3. **Brief spoken response default** — <30 sec spoken (longer = owner asks for text follow-up)
4. **STT confidence threshold** — if <80%, ask owner to repeat ("Maaf Pak, kurang jelas — bisa ulang?")
5. **TTS Indonesian voice quality matters** — ElevenLabs > Google Cloud (validate during Phase 2 procurement)
6. **No "press 1 for X" menus** — natural-language input only, LLM handles intent classification
7. **Voice + WhatsApp dual-channel** — voice call ends, summary text sent to WhatsApp ("Pak, ringkasan tadi: [summary]")
8. **Religious-calendar quiet mode** applies — voice calls suppressed in Imlek week unless critical

---

## 3. Call Flow Pattern

### Inbound from Owner

```
owner calls Asisten (saved as WhatsApp contact "Asisten owner"
or dedicated number)

[ring 1] [ring 2]
Asisten: "Pak, saya di sini. Mau cek apa?"
        (1.0 sec opener, then await speech)

owner: "Solar hari ini gimana?"
        (Whisper STT, ~600ms)

Asisten: "5 truk aktif Pak. B-5678 ada anomali 15 liter
         di SPBU Mukakuning jam 9:15. Mau saya tahan
         invoice B-5678?"
        (LLM + TTS, ~1.0 sec to start speaking)

owner: "Iya, tahan."
        (Barge-in OK — owner can speak before Asisten
         finishes if needed)

Asisten: "Oke, invoice ditahan. Saya kirim ringkasan ke
         WhatsApp ya Pak."
        (~0.8 sec)

[call ends, Asisten triggers WhatsApp summary push]
```

### Outbound from Asisten (Phase 2+ proactive voice)

Reserve for **urgent critical only** (fraud >Rp 5jt, fleet-stopping event). Default = WhatsApp text push, not voice call.

```
Asisten calls owner:
[ring 1]
owner: "Halo?"
Asisten: "`{{owner_honorific}}`, mohon maaf urgent — B-5678 fuel anomali
         50 liter gap, suspected fraud. Saya tahan invoice
         dulu, mau Bapak bicara dengan Pak Rohim langsung?"
owner: "Ya, sambungkan."
Asisten: "Sebentar Pak, saya conference Rohim... [connection]
         Rohim, ada `{{owner_honorific}}`."
```

Conference-call pattern (Phase 3) — Asisten as moderator.

---

## 4. Turn-Taking + Interrupt Handling

| Scenario | Behavior |
|---|---|
| Owner speaks during Asisten reply | Stop TTS immediately, process new input |
| Owner silent >2 sec after speaking | Assume turn complete, send to LLM |
| Owner silent >5 sec mid-call | Asisten: "Pak, saya tunggu..." prompt |
| Owner silent >10 sec | Asisten: "Pak, masih di sana? Kalau perlu lanjut nanti, saya tutup ya." |
| Asisten LLM uncertain | "Maaf Pak, saya kurang yakin — bisa ulang pertanyaannya?" |
| STT confidence <80% | "Maaf Pak, kurang jelas — bisa ulang?" |
| Background noise high | Continue if STT confidence still OK; else "Pak, suaranya ada noise — coba pindah tempat tenang?" |

---

## 5. STT Confidence Handling

Whisper STT returns confidence per phrase. UI rule:

| Confidence | Action |
|---|---|
| ≥95% | Process directly |
| 80-95% | Process, but Asisten confirms ambiguity: "Maksud Bapak [X], betul?" |
| 60-80% | Ask owner repeat: "Maaf Pak, saya dengar [X] — itu yang Bapak maksud?" |
| <60% | "Maaf Pak, kurang jelas — bisa ulang?" |

**Edge:** owner mungkin code-switch Indonesia + Hokkien/Mandarin (LOCK-4 anak/menantu IT translator role). Phase 2 STT primary Indonesian; Hokkien/Mandarin detection nice-to-have Phase 3.

---

## 6. TTS Voice Selection

Owner-facing voice should be:
- Indonesian native (not foreign-accented)
- Mid-pitch, calm, conviction-over-enthusiasm
- Gender: validate with persona Q14 — owner preference (male/female)
- Pace: 150-170 WPM Indonesian (slightly slower than English)
- ElevenLabs Indonesian voice models — pick during Phase 2 procurement

**Anti-pattern:**
- Robotic / synthetic-obvious voice
- Hyper-enthusiastic ("Halo Pak! Wah, hari ini gimana? 😊!") — wrong register
- Foreign accent
- Variable pitch dramatic (news-anchor style)

---

## 7. Voice + Text Dual-Channel

After every voice call, Asisten sends WhatsApp summary:

```
[After call ends]

Asisten → owner WhatsApp:
"Pak, ringkasan call tadi (14:35-14:38):
• Cek solar hari ini — 5 truk aktif
• B-5678 anomali 15 L, Pak Rohim klarifikasi pending
• Invoice fuel B-5678 ditahan ✓
• Sharah (accounting) saya cc

Detail anomali di [link]. Kabari kalau ada update."
```

**Why:** Voice is ephemeral. Text summary creates artifact owner can scroll back, share, audit-trail.

---

## 8. Religious + Working-Hours Aware

| Time/Date | Voice Call Behavior |
|---|---|
| Outside `asisten_owner_preference.working_hours` window | Asisten DOESN'T initiate outbound voice call. Critical alert still WhatsApp text. |
| Imlek day 1-3 | Outbound voice call SUPPRESSED unless owner-defined "always-on" preference |
| Eid day 1-2 (kalau owner enable) | Same as Imlek |
| Quiet hours (e.g., 22:00-04:00) | Outbound voice call SUPPRESSED |
| Owner pre-set "Do Not Disturb" | All voice + push suppressed; critical fraud still SMS-fallback |

---

## 9. Phase 2 Procurement Decisions (TBD)

- **STT vendor:** Whisper (open-source Indonesian) baseline. Validate against Google Speech-to-Text Indonesian + AWS Transcribe Indonesian. Cost vs accuracy.
- **TTS vendor:** ElevenLabs (premium voice quality) vs Google Cloud TTS (cheaper, but Indonesian voice quality lower) vs Indonesian-specific (Bahasa Kita, Robovoice).
- **Call gateway:** Twilio Indonesia vs Vonage vs direct SIP via Telkomsel Enterprise.
- **Conference call (Phase 3):** Twilio supports natively.
- **Voice recording / audit:** Mandatory for compliance — call recording stored in `asisten_conversation_log` (with `tool_name='voice_call'`).

---

## 10. Performance Budget

- STT (Whisper streaming): ≤600ms first transcript
- LLM (Claude Sonnet 4.6 tool-use): ≤800ms first token
- TTS (ElevenLabs streaming): ≤300ms first audio chunk
- **End-to-end target:** ≤1.5 sec from owner finished speaking to Asisten started speaking
- Connection: 4G stable required; degrade to text fallback if connection drops

---

## 11. Anti-Patterns

❌ **IVR menu** ("Tekan 1 untuk fleet, 2 untuk keuangan") — natural language only
❌ **Long spoken intro** — straight to query
❌ **Reading numbers digit-by-digit** — say "lima belas liter" not "satu lima liter"
❌ **Reading URLs / IDs verbatim** — say "container EISU empat lima enam tujuh delapan sembilan" only if necessary; prefer reference "container yang ke Batamindo"
❌ **No barge-in support** — owner locked into listening = frustration
❌ **No text summary follow-up** — voice ephemeral, owner loses thread

---

## 12. Open Questions (TBD — pending persona + procurement)

- owner preferred TTS voice gender (Q14)
- owner working hours actual (Q8) — affects when voice allowed
- STT vendor decision (Whisper vs Google vs AWS) — Phase 2 evaluation
- Voice call usage frequency expectation (daily? weekly?) — affects telco contract sizing
- Conference-call escalation pattern viability (Phase 3) — validate with owner workflow
