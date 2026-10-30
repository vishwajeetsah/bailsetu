# BailSetu — Custody-time tracker + bail draft assistant

**Live demo:** https://vishwajeetsah.github.io/bailsetu/

BailSetu shows which undertrials may have crossed the bail threshold under **Section 479, Bharatiya Nagarik Suraksha Sanhita (BNSS), 2023**, explains why in plain language, and drafts the bail application for review by an authorised legal professional.

> **Demonstration only.** The app ships with 8 fictional sample records. It shows *potential* eligibility only — the court decides release. No real personal data is used or stored; everything runs in the browser.

## The problem

Undertrials — people in jail awaiting trial, not convicted of anything — make up **73.5% of India's prison population (about 3.9 lakh of 5.3 lakh prisoners)**, according to the NCRB's *Prison Statistics India 2023*. Section 479 already entitles many of them to release on bail once they cross a custody threshold, and jail superintendents are duty-bound to apply to the court when that happens. In practice, custody time is tracked manually across paper records, so crossings are missed and people stay in custody longer than the law permits. BailSetu fixes the tracking, not the law.

## Who it serves

- **Undertrials and their families** — a plain-language (English / हिन्दी) view that answers one question: *has the threshold been crossed, and what happens next?*
- **Jail superintendents** — the S.479(3) action queue: Section 479(3) requires the superintendent to apply in writing to the court when an undertrial crosses the threshold. BailSetu turns that duty into a tracked list instead of a memory test.
- **DLSA staff and Para Legal Volunteers** — triage of who needs a bail application, with the reasoning shown for every flag.
- **Courts and authorised legal professionals** — draft applications generated only for flagged records, reviewed and approved by a human before anything is filed.

## How the Section 479 calculation works

BailSetu implements Section 479, BNSS 2023 as a transparent rule set — the same rules a lawyer would apply by hand:

1. **Take the maximum imprisonment** specified for the offence charged (from the BNS lookup, or the Custom path with human verification).
2. **Threshold = one-half** of that maximum — or **one-third** if the person is a first-time offender (never convicted of any offence), under the first proviso to S.479(1).
3. **Compute on the calendar, not in raw days:** the threshold date is the custody-start date plus the threshold in months, so leap years cannot shift it.
4. **Exclude accused-caused delay:** detention time lost to delay caused by the accused is excluded (Explanation to S.479), which shifts the displayed threshold date forward by the same number of days.
5. **Stop conditions come first:** if investigation, inquiry or trial in more than one offence or multiple cases is pending, S.479(2) bars release under S.479 and no draft is generated; if the offence is punishable with death or life imprisonment, S.479 does not apply and the record goes to manual review.
6. **Crossing is a flag, not a release:** past the threshold, the record is marked eligible *to be considered* for release on bail. The court decides — and under the second proviso to S.479(1) it may, after hearing the Public Prosecutor and recording reasons, order continued detention instead.

### Worked example (fictional)

- Custody begins **1 March 2024**. The offence carries a maximum of **5 years**. Single case, no accused-caused delay.
- Not a first-time offender → threshold is one-half of 5 years = **30 months** → threshold date **1 September 2026** (914 days of custody).
- If he were a first-time offender → threshold is one-third = **20 months** → **1 November 2025**.
- If 20 days of delay were attributable to the accused, the threshold date would shift forward 20 days, to **21 September 2026**.
- On or after the threshold date, BailSetu flags the record, shows this reasoning, and generates a draft bail application for review by an authorised legal professional. Nothing is filed automatically.

## Jurisdiction and legal basis

- **Jurisdiction:** India. Section 479 is part of the Bharatiya Nagarik Suraksha Sanhita (BNSS), 2023, in force from 1 July 2024; it is the successor to Section 436A of the Code of Criminal Procedure, 1973.
- The statutory summary above and in the app was **verified against the text of Section 479, BNSS 2023 on 30 October 2026**. If the provision is amended, the rules and this page must be updated before the tool is relied on.

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

## Roadmap

1. Extract the S.479 triage engine from `index.html` into a standalone module with unit tests for the threshold, delay and stop-condition rules, so the calculation can be verified independently of the interface.
2. Pilot with one authorised partner (one jail + its DLSA) on authorised prison records and verified court documents.
3. Server-side append-only audit log, role-based access, OTP-based family authentication.
4. AI extraction layer over authorised documents — same rules, same human review.
5. Multi-district rollout via government procurement / CSR-funded pilots.

## Disclaimer

BailSetu is a screening and drafting aid. It provides legal information, not legal advice, and using it does not create a lawyer–client relationship. A flag means only that a record *appears* to have crossed the Section 479 threshold on the data entered; the competent court makes the final decision on release, and every draft must be verified against authorised prison and court records and approved by an authorised legal professional before filing.

## Licence

MIT — see `LICENSE`.
