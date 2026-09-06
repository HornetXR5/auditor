# Auditor handover — Claude in the project chat

Written 5 Sep 2026, refreshed 6 Sep, by the auditor chat, for the next auditor chat. This file is
**not in the repo** and the agent never reads it. It exists so this role starts
from its own account, not the agent's. Read it before `HANDOVER.md`, and read
`HANDOVER.md` as a claim to be checked, not a fact.

## The role

The agent (Claude Code, on the server as user `agent`) builds the pipeline. The
auditor (this chat) does not patch scripts. It:

- asks "which file did that figure come from?" and "which tree, which
  `run.sh`?" before believing a number;
- drafts J's instructions to the agent in one shape — what it produces, what
  "checked" means, what the fixture is, report as placed / parsed-and-checked /
  unread-and-why — and never with `<placeholders>` in a message to the agent;
- reads the previous session's account of its own work last;
- gives J commands in fish with a trailing comment stating the expected
  result; `sudo -u agent -i` alone on a line; `whoami` first when it matters.

J's usage limit: do not read project knowledge unless the answer depends on it;
project knowledge is uploaded once a day, not per merge.

## Standing checks the auditor runs (added 6 Sep)

At every project-knowledge swap, before reading anything in full:

    wc -l HANDOVER.md REFERENCE.md CSV-SCHEMA.md     # HANDOVER under 300; if not, it has become a log again
    grep -c "^## In flight" HANDOVER.md              # exactly 1
    head -1 HANDOVER.md                               # the date matches the last commit
    grep -n "^## \|^### " HANDOVER.md | head -40      # state, not sessions

These cost nothing and would have caught the 2,762-line HANDOVER a day
earlier. `verify-reference.sh §10 documents` now does the same on the
machine; the auditor still does it here, because the agent's reports are
claims and the doc is the evidence.

Before believing any figure the agent reports: which matter, which tree
(`--out` or J's `run.sh`), which commit. Before accepting a gate change:
which rows, which pages.

## How J and the agent work now (as at 6 Sep)

- Agent: Fable 5.1 at effort medium (default in `/home/agent/.claude/settings.json`
  is Opus 5 / high), worktree `~/LLM-agent`, branch `agent/main`, fenced by
  ACLs and modes, pre-push hook refuses `main`. Three read-only sub-agents
  (gate-runner, corpus-differ, fixture-prover) on Sonnet. ntfy pings J's phone
  at each stop; tmux for the agent still not done (start it at a natural stop).
- J merges with `bash ~/LLM/server/merge-gate.sh --merge`: suite, Anoop gate,
  fast-forward, push, collect. The gate has blocked three times and each time
  the block was information (a copy of a paired arrival; a cash misclassified;
  a claimed figure from a crashed run).
- Corpus gate, Anoop: rows 21,242 · accounts 22 · **matched 6,758** (6,756 →
  6,758 on 4 Sep for the copies rule; briefly 6,756 on 5 Sep when the cash rule
  misread an app-transfer memo, fixed; accepted by J) · redraws 231 /
  $644,921.00 · documents 374 parsed, 20 empty · stated destination 234 ·
  stated source 65.
- Since 5 Sep: the matter structure is built — `Inputs/{required,to-verify,
  verified}` with forms (xlsx drop-downs, yellow = required, blue = proposed),
  `verified/log.csv`, the two-layer deliverables, the task registry, the
  package (`index.md`, `summary.md`, `documents.csv`, per-question answer and
  evidence). The runner: J says "start/run ‹matter›", the agent writes a
  request, systemd units run `run.sh` as J; the agent never writes a matter.
  `merge-gate.sh --merge` runs the suite in a fresh clone of agent/main, the
  Anoop gate, then fast-forwards — J's one command. Git history was rewritten
  6 Sep with a number mapping (real account numbers → synthetic); a
  pre-commit hook refuses account numbers; matter names are permitted (a
  scrub to codes was tried and reversed). The rebuild is scripted
  (`setup-server.sh`, `machine.conf`, `rebuild-guide.md`, inventory) — a
  rehearsal on J's 4090 PC is queued. Backups now include Inputs,
  Deliverables and the requests log; encrypted sets, 14 kept.
- J's daily sheet: `docs/useful-commands.md` (in the repo; linked from Desktop).
- Every figure a person reads comes from `run.sh` on the real matter; the
  agent's `run.sh --out tests/runs/…` proves, J's run produces.

## What was verified by J, against pages, and stands

- Anoop: 64 CommSec receipts $99,065.48 in / 68 payments $81,183.35 out inside
  missing CBA 6066 statements 153–154; CommSec parser and matcher audit (2 Sep).
- Adams: financial-statement family sampled on S.R.A. p54 and SRA Plumbing
  Group (turnover misallocation found and fixed; `checked: []` hardcoded on
  comparatives found and fixed); James ANZ loan row; page 847's three
  undescribed rows carried; 022521163 named in 5 rows with no statement (a
  disclosure query); $135,000 trust distribution in the 2025 return absent from
  the statements.
- Pure: PPVIC 2024 balance sheet Total Assets 937,095, Cash 9,350 / 855,367,
  income tax 236,101 in both doc 19 and doc 21 (the "restatement" was a misread).
- Mac: 5,743 rows through `run.sh`; the agent's first figure (5,753) came from
  a hand walk, not the pipeline — the origin of the "which run.sh?" rule.
- Vsay (a matter J had finished by hand): Q1 54 pleaded — 21 FOUND, 31
  DEPARTURE ONLY (arrival in no disclosed account: the headline), 2 INTERNAL;
  Q2 16 — 15 FOUND, 1 ARRIVAL ONLY (9.l); Q3 26 pairs $996,750; Q4 56 pairs
  $1,593,515. J's report was corrected in four places on pages both had: 9.e,
  9.f (Osko pairs from 5666), 9.h ($35,000 from 5674), and the 276,900 of
  14 Oct 2021 (direction). The $119,961 Chadstone transfer of 13 Sep 2019 was
  accepted by J. Four cash pairs promoted; eight await J in
  `Inputs/to-verify/cash-pairs.csv` — the agreed Q3/Q4 figures already contain
  the 258,703 / 258,700 branch pairs and two small ones, so they need J's
  promotion or rejection.
- Vsay cash: $2,731,000 withdrawn in cash from 1172 (Vortex) at Oakleigh on
  12 Oct 2021 and $1,629,782 across three withdrawals on 15 Sep 2021 — for
  J's report, not yet read by J.
- Western Union: 38 rows, $1,187,993.97 through Vsay's accounts.

## Standing design decisions (the auditor argued for these; hold them)

- Two layers for every deliverable: evidence CSV (row, doc_ref, page, narrative
  as printed, match basis, check that ran) and a reader's sheet (numbers only,
  practitioner layout, pastes into Excel); every financial sheet also a live
  `.xlsx` with formulas for subtotals, totals, percentages.
- Status words, never blanks: FOUND / ARRIVAL ONLY / DEPARTURE ONLY / NOT IN
  DISCLOSURE / AMBIGUOUS (n) / INTERNAL / UNKNOWN ACCOUNT / CASH — possible.
- Absent ≠ 0: a cell is blank when the line is not on that year's statement;
  0 only when 0 is printed.
- One source class per column; restated comparatives shown beside, never chosen.
- Cash-to-cash pairs are never paired by the matcher; they are candidates a
  person promotes. Composites and application traces are candidates in a
  separate unverified file until J samples them.
- `Inputs/required` (J's), `Inputs/to-verify` (pipeline's), `Inputs/verified`
  (J's) with `log.csv`; rejections are recorded, not just acceptances.
- Parties: codes assigned by a person; role codes reserved (A R CA CR P D T H W);
  a guessed relationship is written "possible: …" never as fact; a disputed
  relationship (per a defence, not admitted) is reported apart.
- 200 dpi stays: 150 invented amounts on real scans (8874: 9 of 12 pages).
  Four OCR pages in flight is the default (1.3×, GPU-bound).
- A commit message is a claim; the proving run is the commit after.

## Findings since 5 Sep, verified or awaiting J

- Vsay: Q1 54 pleaded — 21 FOUND, 31 DEPARTURE ONLY (the headline), 2 INTERNAL;
  Q2 15 FOUND + 1 ARRIVAL ONLY; Q3 26 pairs $996,750; Q4 56 pairs $1,593,515 —
  J's report corrected in four places on pages both had. HDFC read (16
  statements, INR): none of the 31 departures could have landed there — the
  statements end 1 Apr 2020 (disclosure request). $2,731,000 + $1,629,782
  cash out of 1172 (Vortex) in 2021 — J's page. Western Union $1.19M.
- Adams: item 1 done and sampled; item 2 done (Lizelle's return not in the
  bundle); item 4 answered 9 of 11 periods; item 3 mis-scoped by the brief's
  role words (fixed by `position`); Adams Family Trust return reconciles at
  item 57 vs 26 ($1,561,879 un-itemised trust-estate income — a query, not
  an error); Investment Holdings paid $94,000 dividends against $34,105 the
  recipients' returns report; 223 in-scope inter-entity transfers
  $1,592,266.86. Model job 2 drafts exist; J has not judged them.
- Job 5 (model as second reader): thinking on → answered nothing; off → 109
  missed of 1,018, mostly direction. It found the OCR-balance defect (the
  generic parser read the balance as the withdrawal on OCR pages; matched
  812 → 852 on that matter) — a document that VERIFICATION had listed as
  "17 rows, unchecked" since 1 Sep and nobody read. "Read first" section
  added; rule sent: unresolved rows are never paired or counted, and any
  sheet drawing on them carries a flag.

## Open, in the auditor's view

- Adams brief items 2–5 (ITR split for husband and wife; business→personal
  vs wages; capacity to pay tax; Stannards when received) — item 1 done.
- Equity-movements task with recipient cross-checks and the distributions
  sheet (recipients down, FYs across, % table as formulas) — queued.
- Fixed-asset register family (per source class) — built on Bors; Timber has
  no schedule; Nat's schedules re-read by OCR.
- §5a summarised vs detailed statements (notes expanded) — not built.
- Job 5 (model extraction vs pipeline): first live run 8/8, missed 0; needs a
  case set before it is a score.
- Vsay parties-to-confirm: Swasti Jain, Syed Jawed (91 "loan" deposits), VK
  Homes, Freight 4U, Peter Lane, Jennings trust — J's to rule on.
- Multi-agent harness (omp.sh, deepseek-harness) — four conditions before
  adopting; a local-model agent would keep client text on-machine.
- J's own: restore rehearsal on Windows; logout/login for the `/proc/1`
  warning; office move items; the $2.7M cash page.

## Traps this role has fallen into

- **Stopped reading HANDOVER to save J's usage, and took the agent's reports
  as the state.** HANDOVER grew to 2,762 lines as a prepended log, with
  "In flight" ~1,700 lines, injected on every compaction; the doc's own first
  line forbids it. `wc` and `grep` cost nothing; the reports are claims.

- Gave J a `tee` line while a run was already going → two `run.sh` on one
  matter. Say "for the next run".
- Wrote `<placeholder>` lines J ran literally; fish reads `<` as redirection.
- Said "minutes" for a Vsay re-run that took 99 (OCR ceiling retries) — state
  what the time depends on.
- Claimed a Taildrop command from memory when REFERENCE §6 had it — read the
  doc for machine facts.
- Merged under a running `run.sh` — bash reads a script as it goes.
- Counted manifest rows as pages (append-only log) — the same shape the agent
  fixed twice in `run.sh` and `verify-reference.sh`.
