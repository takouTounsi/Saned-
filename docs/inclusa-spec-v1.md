# Inclusa: Défi 2 Spec v1

**Social Tech Challenge 2026** · Défi 2: aide à la constitution et au dépôt des demandes
**Team:** Yassine Krichen, Seifeddine Amiri, Mohamed Masmoudi
**Status:** v1, agreed in the team meeting, to be refined with organizers and mentors
**Stack:** not covered here, it comes in a separate doc after the tech meeting

---

## 1. Problem

Beneficiaries travel to the ULPS, wait, and only then learn that a document is missing, unreadable or wrong. They go back, fix it, return, and sometimes a second problem appears. Social workers lose time on incomplete dossiers instead of meeting the person.

Three root causes:
- **Info is hard to find.** Many categories, formal names, unclear documents.
  → *"What is an "extrait de naissance" and where do I get it?"*
- **Same data typed again and again.** Name, CIN, address, in every dossier.
  → *Same fields, 5 forms, 5 times.*
- **No visibility.** The beneficiary can't tell if the dossier is complete. After the visit, nobody knows what stage it is at.
  → *"Is it processed? Is something blocking it?"*

## 2. Goal

> **Every beneficiary arrives at the ULPS with a complete dossier, in one prepared visit, with an appointment already booked.**

What this is **not**:
- It does **not** remove the physical visit. The organizers want the social worker to meet the person.
- It does **not** decide anything. The social worker keeps the final validation.
- It is **not** a legal verdict. We say **"complete and ready for appointment"**, never "valid" or "eligible".

## 3. Users

| Role | What they get |
|---|---|
| **Beneficiary** | Finds the right dossier, understands it, fills it quickly, knows it's ready, books a visit, follows its status |
| **Social worker** | Receives a pre-checked dossier and a summary before the meeting, can ask for missing items, one click to update status |
| **Admin / Ministry** | Configures services and required documents (config file in the MVP), sees basic stats (full vision) |

## 4. Design principles

1. **Rules decide, AI explains, human validates.** Required documents and completeness are checked by fixed rules. The AI reads documents and explains problems in simple words. The social worker decides.
2. **Prevent errors before the trip**, don't just digitize the form.
3. **Complete ≠ valid.** The system says whether a dossier is complete. The worker says whether it's valid.
4. **Consent and privacy by design.** Personal data is reused only with the user's consent. Handicap dossiers contain health data. Check Tunisian data protection rules (INPDP) with mentors.
5. **Simple first.** A few common documents and few services at first, then expand.

---

## 5. MVP / POC (one week, qualification round)

Goal: a demo where one beneficiary goes from login to a booked appointment with a complete dossier, and the social worker sees it.

### Beneficiary side

| # | Feature | What the user does | Done when |
|---|---|---|---|
| M1 | **Login with e-Houwiya** + profile setup | Logs in, profile created from their identity | Login works (mock login as fallback if access isn't ready) |
| M2 | **Assistant in Tunisian dialect** + dossier suggestion | Says what they need, gets the right dossier and the list of required documents explained simply | Answers come from the MySocial data, not invented. Tested on ~50 real dialect phrases |
| M3 | **Reuse of previous data** | Starts a new dossier, known fields (name, CIN...) are already filled | Works on the 2nd dossier. **Change-aware:** if the dossier changes personal data (e.g. address), those fields are left empty and not filled from old data |
| M4 | **Scan + extract + coherency check** | Photographs a document, remaining fields are extracted, a mismatch is flagged | Works on a few common documents. Accuracy measured against manual transcription |
| M5 | **"Ready for appointment" check, anytime** | Opens the checklist, sees what's missing or wrong and how to fix it | Checklist is rule-based and updates live |
| M6 | **Minimal appointment booking** | Dossier is complete, picks a slot from a fixed list | Slot saved, confirmation shown. No smart scheduling |

### Social worker side (minimal, but present)

| # | Feature | What the worker does | Done when |
|---|---|---|---|
| W1 | **Dossier inbox + summary** | Sees upcoming appointments with the dossier already organized and flagged | One screen with summary, documents, flags |
| W2 | **Request a missing item** | Asks the beneficiary for a document or info | The beneficiary gets the request and answers in the assistant |
| W3 | **One-click status** | Marks "received at the appointment" | Status updates for the beneficiary |

### Admin side
- A **config file** lists services, required documents per service and rules. No admin UI in the MVP.
  → *Adding a service = editing the file, so extension to other services is built in.*

### Status tracking (lite)

| Status | Who triggers it |
|---|---|
| Draft | Automatic |
| Incomplete (what's missing is listed) | Automatic |
| Ready for appointment | Automatic |
| Appointment booked | Automatic |
| Received by the social worker | **Worker, one click** |

Why this split: everything before the visit is known by the system. Only the worker knows what happens after, so it takes one click.
→ *This is also the worker's reason to use the system: they get a prepared dossier in return for one click.*

### MVP scope limits (to stay doable in a week)
- **1 domain for the demo** (from the 3 pilot domains), a handful of services.
- **A few common documents only** (the ones that appear in most dossiers).
- **Text assistant** (voice later).
- **No ticketing, no seasonal suggestions, no stats dashboard.**

### Demo story
*A mother needs a social aid → asks the assistant in dialect → gets the right dossier, explained → her data is prefilled → she photographs a document, the blurry one is rejected on the spot → checklist turns green → she books a slot → the social worker opens a prepared dossier → status updates.*

---

## 6. Full vision (ultimate version)

### Beneficiary
- Voice input in dialect, simple screens for low digital literacy, Arabic/French.
- Wider document coverage and all services of the 3 pilot domains, then other domains.
- Smart document check: type recognition, readability, consistency across documents.
- **Expiry alerts** for documents close to expiry or expired.
  → *"Your residence certificate expires in 15 days."*
- **Renewal reminders based on the user's own past dossiers.** The state already sends an SMS the next year, but only to people who already did the dossier. Ours reminds at the right period of the year, with the old dossier already prefilled.
  → *"School year starts: renew last year's aid, your dossier is prefilled."*
- **Suggested programs for people who never applied.** Needs data from the Ministry and a privacy review. Not before it's validated.
- Notifications by SMS, e-mail and in-app: appointment changed or cancelled, request from the worker, status changes.
- Full status tracking including processing and decision.
- Assisted mode at the office for people without a phone or internet.

### Social worker
- Assisted verification: the system highlights missing, inconsistent or low-confidence fields.
- Appointment management: availability, slots, cancellations.
- Smart assignment of the dossier to a worker based on the chosen slot.
- Workload view per worker.

### Admin / Ministry
- Admin interface: add or remove services, set forms, documents, rules and availability.
- Statistics: dossiers, appointments, rejection causes, activity per ULPS.
  → *"40% of incomplete dossiers miss the same document" → fix the instructions.*
- Multi-ULPS deployment, integration with the Ministry site and existing platforms (MySocial).

### Possible integrations (only if validated)
- Identity and social data (CIN, CNSS, CNRPS) to pre-fill and avoid scanning documents the state already has.

---

## 7. Appointment vs ticketing

**Decision: appointment is core, ticketing is roadmap.**

- **Appointment** = planned visit. The organizers ask for it.
- **Ticketing** = managing the queue on the day. They don't ask for it, and it needs real-time updates plus a change in how offices work.
- **They can coexist:** appointment holders check in and get priority, walk-ins take a ticket. We only build this if the ULPS has no queue system today.
- **"How busy is it" can be shown without ticketing,** from the number of appointments per slot per worker (the organizers provide appointment counts per worker).

To ask mentors: do ULPS already have a queue system, and what share of visitors come without an appointment?

---

## 8. Risks and mitigations

| Risk | Mitigation |
|---|---|
| e-Houwiya access not ready | Mock login with the same interface, swap when access is confirmed |
| Tunisian dialect quality | Use Maghrebi dialect models, test on ~50 phrases, text only at first |
| OCR accuracy on photos | Compare against manual transcription on sample files, flag low-confidence fields for the user to confirm |
| MySocial data hard to use | Required documents come from the structured list given by the organizers, the assistant only explains |
| Worker doesn't update status | Only one click, and it gives them a prepared dossier in return |
| Privacy (health data in handicap dossiers) | Consent, access control, audit log, check INPDP with mentors |
| One week, partly available team | MVP cut to the minimum above, one domain, few documents |

## 9. Success metrics

- **Complete on first visit:** % of dossiers with nothing missing at the appointment.
- **Wasted trips avoided.**
- **OCR field accuracy** on common documents (target to set after the first test).
- **Time to complete a dossier** with vs without the app.
- **Social worker prep time** per dossier.

## 10. Open questions (organizers and mentors)

1. e-Houwiya: when and how do we get access, and which profile data does it return?
2. MySocial: what exactly can we use, and is it up to date?
3. Which of the 3 domains is best for the demo, and which services are most frequent?
4. Which documents appear in most dossiers (to focus OCR on)?
5. Can data from previous dossiers be reused legally, and under what consent?
6. Do ULPS already have a queue system, and how do they handle walk-ins?
7. Which statuses do workers already track today?
