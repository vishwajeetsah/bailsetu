# BailSetu — Custody-time tracker + bail draft assistant

**Live demo:** https://vishwajeetsah.github.io/bailsetu/

BailSetu shows which undertrials may have crossed the bail threshold under **Section 479, Bharatiya Nagarik Suraksha Sanhita (BNSS), 2023**, explains why in plain language, and drafts the bail application for review by an authorised legal professional. It is built for **MY Bharat Hack for Social Cause (HSC) 2027 — Theme 4: Governance & Civic Technology**.

> **Demonstration only.** The app ships with 8 fictional sample records. It shows *potential* eligibility only — the court decides release. No real personal data is used or stored; everything runs in the browser.

## Try it

Open `index.html` in any modern browser. No build step, no dependencies, no server — it works offline.

- **Undertrial & Family tab:** enter an undertrial ID and the family access code. Demo code for all sample records: `DEMO-2026`
- **Public tab:** counts only, no names — the problem in plain numbers.
- **Officer (Jail / DLSA / Court) tab:** full triage, reasoning, draft generation, add-record form, extraction, audit log.

## Features

- **S.479 triage engine** — half of the maximum sentence, one-third for first-time offenders; S.479(2) multiple-case block; manual review for offences punishable with death or life imprisonment; missing-data handling.
- **Delay-aware threshold dates** — delay attributable to the accused (Explanation to S.479) is subtracted from custody days, and the displayed crossing date shifts forward by the delay.
- **BNS offence lookup** — 15 verified (section, maximum sentence, death/life) entries; the Add form auto-fills max sentence and the death/life flag from the section, so a wrong pairing (e.g. dacoity at 10 years) cannot be typed in by hand. A Custom option covers anything else.
- **Reasoning for every flag** — custody days, effective days, threshold date, and the applicable rule, in the officer view.
- **Draft bail applications** — past-threshold records get a DRAFT; approaching/below records get a PRE-PREPARED draft that says "will cross the threshold on…" and must not be filed early. All averments numbered; the S.479(1) provisos are flagged as a bracketed note to counsel. Approval by an authorised legal professional is recorded in the audit log before anything counts as approved.
- **Assisted extraction** — paste remand / warrant text and the app finds dates and section numbers with transparent, rule-based pattern matching. Every extraction must be verified by a human before use.
- **Three role views** — family (plain language, access-code gated), public (counts only), officer (full function).
- **English / हिन्दी** toggle for the family and public views. The officer view is English-only by design, since court and jail records are in English.
- **Audit log** — persists in the browser and is hash-chained (each entry chains to the previous one); a Verify button recomputes the chain and reports tampering.
- **Accessibility** — keyboard-navigable table rows and tabs, visible focus, large touch targets.

## Tech stack

A single self-contained `index.html`: HTML + CSS + vanilla JavaScript. No frameworks, no build tools, no external requests, no backend.

## Known limitations

- **Sample data only.** Nothing here connects to a real prison or court system — that needs authorised access and is a deployment step, not part of this demo.
- **Demo-grade audit log.** The hash chain lives in the browser's localStorage and uses a simple FNV-1a hash to demonstrate chaining. Deployment needs a server-side, append-only log tied to authenticated accounts.
- **Demo-grade family access.** One shared demo code (`DEMO-2026`) stands in for per-family codes issued by the jail / DLSA.
- **Maximum sentence comes from the charge-sheet section.** The lookup covers common BNS offences; unusual or multi-section charges need the Custom path and human verification.
- **Threshold dates use the recorded custody start.** If the custody record is wrong, the triage is wrong — which is why every draft must be verified against authorised prison records before filing.

## Next steps (deployment)

1. Pilot with one authorised partner (one jail + its DLSA) on authorised prison records and verified court documents.
2. Server-side append-only audit log, role-based access, OTP-based family authentication.
3. AI extraction layer over authorised documents — same rules, same human review.
4. Multi-district rollout via government procurement / CSR-funded pilots.

## Licence

MIT — see `LICENSE`.
