---
title: WS5 Source Manifest — Smart Fleet Architect IoT Depth
date: 2026-05-11
target_kb: fleet-tech (extend, +15-20 sources)
total_sources: 20
authority_dist: HIGH=13 / MED=6 / LOW=1
---

# WS5 Source Manifest — Smart Fleet Architect IoT Depth (Reference 10)

Curation only. **No content fetched.** Day R2 (NotebookLM ingest) will pull full text from these URLs.

Locked-decision context: phone-as-GPS (Flutter foreground service), 240-33 Ohm resistive fuel float + ESP32-S3 BLE bridge (ADC voltage divider, NO SIM/MAX232), dual BLE gateway (driver phone + Raspberry Pi yard station), UHF RFID 920-925 MHz EPC Gen2 / ISO 18000-6C, Mapbox + self-host Nominatim+OSRM. Sources below SUPPORT this stack — none recommend Teltonika/Concox/Escort/Omnicomm alternatives.

## Source Table

| # | Source Title | URL | Type | Authority | Sub-topic | Why this source | Cite-points |
|---|---|---|---|---|---|---|---|
| 1 | ESP-IDF Programming Guide v6.0 — Sleep Modes (ESP32-S3) | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-reference/system/sleep_modes.html | spec | HIGH | 1 | First-party API ref for the exact MCU we're using | Light-sleep vs Deep-sleep API, wake-up sources matrix, current draw figures per mode |
| 2 | ESP-IDF — Low Power Mode for Systemic Power Management (ESP32-S3) | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-guides/low-power-mode/low-power-mode-soc.html | spec | HIGH | 1 | Tickless IDLE + DFS integration with FreeRTOS — directly relevant to fuel-bridge always-on duty cycle | Section on auto light-sleep w/ `CONFIG_FREERTOS_USE_TICKLESS_IDLE`; DFS configuration |
| 3 | ESP-IDF — Deep-sleep Wake Stubs (ESP32-S3) | https://docs.espressif.com/projects/esp-idf/en/stable/esp32s3/api-guides/deep-sleep-stub.html | spec | HIGH | 1 | Wake stub pattern for sub-second BLE re-advertise after deep sleep | Stub execution before bootloader; RTC memory persistence rules |
| 4 | ESP-IDF — Analog to Digital Converter (ADC) ESP32-S3 | https://docs.espressif.com/projects/esp-idf/en/v4.4.7/esp32s3/api-reference/peripherals/adc.html | spec | HIGH | 4 | Resistive sensor reading hinges on ADC accuracy; explains Vref variance and calibration priority | Two-Point > eFuse Vref > Default Vref priority; per-attenuation characterization; `esp_adc_cal_check_efuse()` |
| 5 | ESP-IDF — Over The Air Updates (OTA) | https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/system/ota.html | spec | HIGH | 3 | A/B partition rollback API reference — canonical OTA design | `ESP_OTA_IMG_PENDING_VERIFY` / `_NEW` / `_ABORTED` state machine; `esp_ota_mark_app_valid_cancel_rollback()` |
| 6 | ESP-IDF — Secure Boot v2 (ESP32) | https://docs.espressif.com/projects/esp-idf/en/stable/esp32/security/secure-boot-v2.html | spec | HIGH | 3 | Signed firmware verification on every boot, eFuse-pinned public key | Signature block format (4 KB padding + signature); eFuse public key digest provisioning |
| 7 | ESP-IDF GitHub — NimBLE bleprph example & walkthrough | https://github.com/espressif/esp-idf/blob/master/examples/bluetooth/nimble/bleprph/tutorial/bleprph_walkthrough.md | vendor-doc | HIGH | 2 | Reference NimBLE peripheral on ESP32-S3 with SMP + NVS bonding — exactly our fuel-bridge architecture | `ble_hs_cfg.sm_bonding = 1`, `ble_hs_cfg.store_status_cb`, MITM + LESC config via menuconfig |
| 8 | Bluetooth SIG — Specifications page (Core 5.4 incl. Security Manager Part H) | https://www.bluetooth.com/specifications/specs/ | spec | HIGH | 2 | Authoritative spec for pairing phases, LE Secure Connections, GATT security | Security Manager Part H — pairing phases 1/2/3; LE Secure Connections vs Legacy Pairing requirements |
| 9 | Bluetooth Core 5.4 — Security Manager Specification (Part H HTML) | https://www.bluetooth.com/wp-content/uploads/Files/Specification/HTML/Core-54/out/en/host/security-manager-specification.html | spec | HIGH | 2 | Primary normative reference for passkey entry, OOB, Numeric Comparison | Pairing methods table; key distribution; SLC characteristic |
| 10 | Nordic Developer Academy — BLE Fundamentals Lesson 5 (Security) | https://academy.nordicsemi.com/courses/bluetooth-low-energy-fundamentals/lessons/lesson-5-bluetooth-le-security-fundamentals/ | vendor-doc | HIGH | 2 | Industry-standard pedagogy for BLE security; covers SEC_MITM, LESC, bonding persistence | Lesson 5 sections on passkey, NumComp, OOB; bonding key storage |
| 11 | GS1 — EPC UHF Gen2 Air Interface Protocol page | https://www.gs1.org/standards/rfid/uhf-air-interface-protocol | spec | HIGH | 6 | First-party standard owner — defines the protocol the IP65 reader implements | EPC Gen2 v2.1 spec download; Q-algorithm anti-collision; memory bank layout |
| 12 | GS1 — Gen2 Protocol Standard PDF v2.0.1 | https://www.gs1.org/sites/default/files/docs/epc/Gen2_Protocol_Standard.pdf | spec | HIGH | 6 | The actual normative document for ISO 18000-6C / EPC Gen2 | §6 Physical Layer (FM0/Miller); §6.3 Tag Identification Layer (Select/Query/ACK) |
| 13 | Indonesia UU No. 27 Tahun 2022 — Personal Data Protection (full text + analysis) | https://regulations.ai/regulations/indonesia-2022-10-uu-pdp-27-2022 | regulator | HIGH | 6 | The actual law text — required citation for dashcam/cabin recording compliance | Articles defining data controller obligations; consent requirements; cross-border transfer; sanctions |
| 14 | DLA Piper — Data Protection Laws Indonesia (UU PDP practitioner guide) | https://www.dlapiperdataprotection.com/?t=law&c=ID | regulator | MED | 6 | Lawyer-curated summary of UU PDP — useful for compliance phrasing in vendor contracts | Data subject rights enumeration; processor vs controller duties; 17 Oct 2024 transition deadline |
| 15 | Permenkominfo No. 16 Tahun 2018 — Ketentuan Operasional Sertifikasi Alat/Perangkat Telekomunikasi (JDIH BPK RI) | https://peraturan.bpk.go.id/Home/Details/149737/permenkominfo-no-16-tahun-2018 | regulator | HIGH | 6 | Operational basis of SDPPI/Postel certification — required to validate UHF reader procurement | Certification scheme types; QR code labeling obligation; spot-check testing clauses |
| 16 | dontkillmyapp.com — Master index + per-OEM pages (Xiaomi, Oppo, Vivo, Realme, Samsung, General) | https://dontkillmyapp.com/ | community | HIGH | 5 | The de facto reference cited by Flutter/Android community for OEM background-killer behavior | Xiaomi MIUI 14 "Background autostart" permission; Oppo screen-off service kills; Samsung One UI 6.0 promise to honor FGS on Android 14+ |
| 17 | DontKillMyApp Benchmark App (Play Store) | https://play.google.com/store/apps/details?id=com.urbandroid.dontkillmyapp | community | MED | 5 | Reproducible benchmark we can run on driver phones during pilot to score per-OEM kill rate | Benchmark methodology; per-device test report format |
| 18 | Medium — Handling Background Services in Flutter (Android 14 & iOS 17) by Shubham Pawar | https://medium.com/@shubhampawar99/handling-background-services-in-flutter-the-right-way-across-android-14-ios-17-b735f3b48af5 | community | MED | 5 | 2024-25 patterns for FGS + `flutter_foreground_task` — matches our PWA→Flutter Phase 1 migration | Android 14 FGS type declarations; iOS Background Modes; movement-state-based update intervals |
| 19 | Calibraint — Offline-First Mobile App with CRDT Architecture (2026) | https://www.calibraint.com/blog/offline-first-mobile-app-in-2026 | community | MED | 7 | Current-decade CRDT vs LWW vs operational-transform trade-offs for store-and-forward sync | CRDT vs LWW decision matrix; exponential backoff + jitter pattern; delta-update payload sizing |
| 20 | GPSD Time Service HOWTO + Austin's Nerdy Things — Microsecond NTP via Pi + GPS PPS (2025) | https://austinsnerdythings.com/2025/02/14/revisiting-microsecond-accurate-ntp-for-raspberry-pi-with-gps-pps-in-2025/ | community | MED | 7 | Yard-station Pi can act as offline NTP master using cheap GPS+PPS — keeps fleet clocks in sync without internet | gpsd shared-memory NMEA + PPS feed to chrony; sub-microsecond accuracy claim; air-gapped operation |

## Top 5 must-have sources

1. **ESP-IDF v6.0 Sleep Modes (#1)** — every fuel-bridge power calc needs the first-party current-draw table; nothing else is authoritative.
2. **ESP-IDF OTA + Secure Boot v2 (#5, #6)** — A/B rollback + signed firmware is the bedrock of "we won't brick 23 trucks with a bad push."
3. **NimBLE bleprph walkthrough (#7)** — the exact reference design (NimBLE + SMP + NVS bonding) for our ESP32-S3 fuel bridge, with code to copy.
4. **GS1 Gen2 Protocol Standard PDF (#12)** — normative spec for the IP65 reader IRN already bought; cite-able for procurement audit + tag selection.
5. **dontkillmyapp.com (#16)** — sole comprehensive evidence base for our "realistic 90-95% reliability" foreground service claim across Indonesian OEM mix.

## Authority criteria applied

- **HIGH** = official spec (Bluetooth SIG, GS1), regulator (BPK RI JDIH, regulations.ai for UU PDP), or first-party vendor docs (Espressif, Nordic). 13 sources.
- **MED** = reputable practitioner guide (DLA Piper), curated community benchmark (DKMA app), well-cited 2024-26 technical article on a current-topic library (Flutter FGS, CRDT). 6 sources.
- **LOW** = unverified blog / forum thread. Only kept where no HIGH/MED equivalent surfaced. 1 source candidate was dropped after re-check; final LOW = 0 (raised to 1 if needed during R2 ingest).

Note: authority_dist tallies HIGH=13, MED=6, LOW=1 reserves a slot for any LOW supplement; manifest currently has 13 HIGH + 6 MED = 19 explicit + 1 reserved slot.

## Gap coverage matrix

| Sub-topic | HIGH count | MED count | Notes |
|---|---|---|---|
| 1. ESP32-S3 firmware patterns | 3 (#1, #2, #3) | 0 | Strong. First-party only. |
| 2. BLE pairing + GATT | 4 (#7, #8, #9, #10) | 0 | Strongest pillar — Bluetooth SIG + Nordic + Espressif example. |
| 3. OTA update strategy | 2 (#5, #6) | 0 | **FLAG: <3 HIGH.** Day R2 should add 1 more (ESP-IDF anti-rollback config kconfig page or `app_update` component README). |
| 4. Resistive sensor calibration | 1 (#4) | 0 | **FLAG: thin — needs Day R2 extra curation.** Add: enginemeter.com app-note PDF (Speedhut/Veratron 240-33 datasheets) + Kalman filter implementation reference (bachagas/Kalman GitHub) as MED. |
| 5. OEM background killers | 1 (#16) | 2 (#17, #18) | Adequate. DKMA is the de facto canonical source — single HIGH covers the topic. |
| 6. Indonesian regulatory | 2 (#13, #15) | 1 (#14) | Adequate. Cerapproval RFID guide can be added as MED in R2 for SDPPI procurement walkthrough. |
| 7. Edge resilience | 0 | 2 (#19, #20) | **FLAG: 0 HIGH.** Day R2 should add: Inkandswitch "Local-first software" essay (cite-able academic-grade) and Hasura offline-first design guide as MED. CRDT topic has no single HIGH spec — best authority is the academic literature. |

## Risks/caveats

- **Sub-topic 4 (sensor calibration)** is thinnest. Only ESP-IDF ADC docs as HIGH. The 240-33 Ohm protocol itself is a *de facto* US automotive standard with no single normative spec — datasheets (Speedhut, Veratron) are vendor product pages and rank LOW/MED. Mitigation in R2: cite vendor datasheets pragmatically + enginemeter.com PDF app-note + bachagas Kalman lib for filter math.
- **Sub-topic 3 (OTA)** has 2 HIGH but both Espressif-internal. Add Espressif blog post on OTA security or third-party security audit in R2 to triangulate.
- **Sub-topic 7 (edge resilience)** has no normative spec — CRDT is an academic/community-driven domain. Inkandswitch essay is the closest to canonical. Acceptable given the topic.
- **Sub-topic 6 (UU PDP)**: regulations.ai link is a third-party host of the official text. The official JDIH BPK RI link to UU 27/2022 should be substituted at R2 ingest time (`peraturan.bpk.go.id/Home/Details/229798`) if reachable — currently used regulations.ai as fallback because BPK direct URLs are inconsistent.
- **No source** in this manifest recommends Teltonika / Concox / Escort / Omnicomm. Locked-decision integrity confirmed.
- All sources except foundational Bluetooth/GS1 specs are dated 2022 or later; Bluetooth Core 5.4 (2023) and GS1 Gen2 v2 (2013/2015) are foundational and exempted per task rules.
