---
name: potential-claims
description: Work out which claims a client has, or faces, from the facts. Each claim gets its elements, limitations period and last day to file, remedies, defenses and viability. The analysis is kept as a markdown file in the Claude Project that later runs update as facts come in. Use when the user asks what claims a client has, whether they can sue or be sued, what causes of action the facts support, how long they have to file, or asks to update an existing claims analysis. Needs the Sidebar connector.
---

# Potential claims analysis

You already know how to spot issues. This skill adds what you don't do on your
own: one file per matter that is found and updated rather than rewritten, the
right questions asked up front, every element and limitations period taken from
Sidebar's tools and checked, and a structured pass for claims that free recall
misses.

## 1. Find the file

List the Project's files and look in `claims/` for this matter: front matter
`skill: potential-claims` with the same parties. The name is
`claims/potential-claims-<short-matter>.md`.
- One match: read it and go to step 3 as an update.
- Several could match: ask which one.
- None: start a new file from the template in step 5.

## 2. Ask once, before any analysis

Read every document in the Project first. Then ask, in one message, only what
you can't find there or in the file:
- **Side:** are we the claimant, or the party facing the claims?
- **Parties:** each one, with its role (individual, business, insurer,
  government body).
- **Forum:** where the events happened, and where suit could be filed. This
  decides whose law and whose limitations statute applies.
- **Key dates:** contract, injury, discovery of the injury or fraud, last
  payment or performance, any notices or demands.
- **Damages:** what was lost, and what can be shown.
- **Contracts:** every agreement between the parties, especially arbitration,
  choice-of-law, forum-selection, limitation-of-liability, shortened-limitations
  and warranty clauses.
- **History:** prior demands, notices, complaints to agencies, or suits; insurance
  on either side.
- **Documents:** name each one the facts point to that you didn't find (the
  contract, warranty, closing papers, correspondence, invoices, photos).
- **For an update:** only what is new since `last_run`.

If the user wants to go on without something, list it under `missing` and treat
what depends on it as unknown.

## 3. Ground the analysis in Sidebar

- **Candidate claims:** before ranking, make one pass over each category the
  facts touch: contract (express, implied, warranty), tort (intentional,
  negligence, professional), statutory (consumer protection, securities,
  lemon-law, wage, civil rights), equitable (unjust enrichment, rescission,
  accounting, constructive trust), and claims against third parties (agents,
  guarantors, insurers, sellers up the chain). Write down each candidate before
  judging it.
- **Elements:** `claim_elements(claim, jurisdiction)` for each candidate. Take
  the elements from the binding statement it quotes, and cite that case.
- **Limitations:** `limitations_period(claim, jurisdiction)` for each candidate.
  Use the section the courts apply, the accrual rule they state, and any repose
  limit in the statute text. Never take a period from memory.
- **Statutory claims:** read the statute itself (`kansas_statute` or the other
  state tools), and `cases_citing_statute` for how courts apply it. Note
  pre-suit notice, fee-shifting and penalty provisions.
- **Similar facts:** run `search_cases` with a fact-pattern query for the
  strongest claims, and `citing_cases` on the leading case, to find outcomes on
  facts like these.
- **Confirm** every quote with `find_in_case` and every pin with
  `check_pin_cite`. Run `check_good_law` on each case the analysis relies on.

## 4. Do the analysis

For each claim, record:
- **Elements:** the facts that meet each one, cited to the document read (file
  and ¶, page or line), or "unknown: not read". Then list the facts still needed.
- **Limitations:** the statute section, the accrual date and why, the period,
  and the **last day to file** from `compute_deadline` (accrual date, period in
  years or months, the forum's court), with its safe date if it gives one.
  Flag every open question: discovery or tolling, repose, and a contract clause
  that shortens the period.
  If the accrual date is unknown, give the range, and use the earliest date for
  the deadline.
- **Remedies:** damages, statutory or punitive damages, fees, equitable relief.
- **Defenses:** the likely ones, including the arbitration and limitations
  clauses.
- **Viability:** **strong**, **possible**, **weak** or **rejected**, with one
  line on why.
- **Questions** for the client that would move the rating.

Keep rejected claims in the file, with the reason, so later runs don't propose
them again. If we face the claims, rate how strong each is against the client,
and list the defenses and counterclaims.

## 5. Write or update the file

**Updating:** change only what changed, in place. At the top of the Changes
log, add a dated entry:
- the new facts, documents or law that came in;
- each claim whose viability or last day moved: old → new, and why.

Re-sort the limitations table. Leave unchanged sections alone.

**New file:**

```markdown
---
skill: potential-claims
matter: <short description, e.g. Smith — defective manufactured home>
side: claimant | facing claims
parties:
  - <name> (<role>)
forum_options: [<court or state>, ...]
governing_law: <state, and any choice-of-law clause>
last_run: <YYYY-MM-DD>
sources:              # every Project document read, with its date or version
  - <file name> (<date>)
missing: [<documents or facts asked for and not provided>]
---
# Potential claims: <matter>

## Changes
- <YYYY-MM-DD>: <first run | what changed>

## Short answer
<the claims ranked, strongest first, one line each; the nearest deadline;
any clause (arbitration, forum, shortened limitations) that changes the plan>

## Limitations
| Claim | Statute | Accrual (date, rule) | Last day to file | Risk |
|---|---|---|---|---|
<sorted by last day, earliest first; bold every last day within 6 months of today>

## Claims
### <Claim> — <strong | possible | weak>; against <party>
Elements per <case, pin>:
| Element | Facts that meet it (source) | Still needed |
|---|---|---|
**Limitations:** <section; accrual; last day; open questions>
**Remedies:** <...>
**Defenses:** <...>
**Questions for the client:** <...>

## Rejected claims
| Claim | Why rejected | Date |
|---|---|---|

## Open questions
<for the client, the documents, or more research>

## Authorities
| Authority | Used for | Quote verified | Pin verified | Good law |
|---|---|---|---|---|

## Citation check
<check_citations summary and the date it ran>
```

## 6. Check, save, report

1. Run `check_citations` on the whole file. Fix what it flags and run it again.
   Note the result under "Citation check".
2. Save the file with the Projects tool at its path. When updating, overwrite
   the same file.
3. In the chat, give:
   - the claims, ranked;
   - the nearest deadlines;
   - what changed since the last run;
   - the questions for the client, and the documents still missing.
