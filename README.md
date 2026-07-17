# READ ME FIRST — MyCaisse QA Test

This repo contains two files. **This one tells you how the session runs and what you must hand in.** The other one is the test itself:

➡️ **[`EXAMEN_TEST_MYCAISSE_SYNC.md`](EXAMEN_TEST_MYCAISSE_SYNC.md)** — the subject (sujet): environment, credentials, the four parts, grading.

## How the session runs

1. **Briefing (~5 min)** — you receive the AnyDesk ID for the caisse device. Everything else (logins, links, PINs) is already in the sujet.
2. **Read the sujet** — especially *Your environment*, the *Three rules*, and *Key terms*.
3. **Test (3h30 max, timer starts after briefing)** — Parts 1–3 are manual testing on the caisse + cloud dashboard; Part 4 is a written automation-strategy document. Manage your time freely; you may leave as soon as you hand in.
4. **Hand in = `git push` to this repository** — see below. Late pushes are not accepted: push what you have when time is up.

## What you hand in

**Everything is delivered through git, in this repository** (write access given at briefing). Create a folder `rendu/` at the repo root and push your deliverables there before you leave. Commit as you go — a clean, incremental commit history is part of a professional hand-in; don't dump one giant commit at 3h29.

Your `rendu/` folder must contain:

| # | Deliverable | Requirements |
|---|---|---|
| 1 | **Test report** — Markdown preferred (`rendu/report.md`), PDF accepted | Starts with the filled header table from the sujet (name, date, start/end time). Contains your test cases and results for Parts 1–3, structured **your way** (see *Format — your call* in the sujet) |
| 2 | **Bug reports** — inside the report, in their own section | Each bug: reproducible steps, expected vs actual, severity, screenshot. This is the #1 graded item (50%) |
| 3 | **Automation strategy document** (Part 4) | 1–2 pages, no code, the 6 points listed in the sujet — own file or a section of the report |
| 4 | **Screenshots** — `rendu/screenshots/` | Referenced from the report by relative path (Markdown images render directly on the repo) |
| 5 | **AI workflow note** — a short paragraph at the end of the report | Which AI tools you used today and how |

One single `report.md` containing everything + a screenshots folder is the ideal shape — but any layout inside `rendu/` works if it's easy to navigate.

## Before your final push — 60-second checklist

- [ ] Header table filled (name, date, start & end time)
- [ ] Every bug has steps + expected/actual + screenshot (a bug we can't reproduce counts half)
- [ ] Part 4 document is included
- [ ] Screenshots are committed and their links in the report actually resolve
- [ ] AI workflow note is there
- [ ] `git push` done — check the remote actually shows your last commit

*Questions during the test? Ask — a good tester asks rather than guesses.*
