---
name: motion-to-dismiss
description: Analyze a motion to dismiss (12(b)(6), 12(b)(1), 12(b)(2), 12(c), or K.S.A. 60-212(b)) from either side, claim by claim and element by element, and keep the analysis as a markdown file in the Claude Project that later runs update. Use when the user asks whether a complaint or petition survives a motion to dismiss, wants to move to dismiss, is responding to one, or asks to update an existing motion-to-dismiss analysis. Needs the Sidebar connector.
---

# Motion to dismiss analysis

You already know how to analyze a motion to dismiss. This skill adds what you
don't do on your own: one file per motion that is found and updated rather than
rewritten, the right questions asked up front, and every standard, element and
case taken from Sidebar's tools and checked.

## 1. Find the file

First read `matter.md` and `facts.md` at the Project root, if they exist. They
hold the court, judge, parties, our side, the deadlines, the other work files,
and the facts so far, each with an id (F-001 and on). Formats and rules:
`references/matter-files.md`.

List the Project's files and look in `motions/` for this motion: front matter
`skill: motion-to-dismiss` with the same case and motion. Names follow
`motions/mtd-<short-case-name>.md` (moving) or `motions/mtd-response-<short-case-name>.md` (responding).
- One match: read it and go to step 3 as an update.
- Several could match: ask which one.
- None: start a new file from the template in step 5.

## 2. Ask once, before any analysis

Ask, in one message, only what you can't find in the Project, the file, or
`matter.md` and `facts.md`:
- The court (and judge, if assigned). This decides the standard: federal and Kansas
  state courts apply different pleading standards.
- Which side we're on, and which claims (or which grounds) are at issue.
- Documents. Name each one you expected and didn't find:
  - the complaint or petition (the operative one, if amended);
  - documents attached to it or central to it and referenced in it;
  - the motion and its memorandum, if we're responding.
- For an update: only what is new since `last_run` (an amended pleading, a reply,
  a ruling, new facts).
- If `matter.md` or `facts.md` doesn't exist yet: anything its template needs
  that you can't find (the case number and posture are usually on the pleadings).

If the user wants to proceed without a document, mark it **missing** in the file
and treat what depends on it as unknown.

## 3. Ground the analysis in Sidebar

- **Standard:** call `legal_standard(motion, forum)`. Use its authorities and its
  newest binding wording. Don't state a standard from memory.
- **Elements:** call `claim_elements(claim, jurisdiction)` for each claim at issue.
  Take the elements from the binding statement it quotes, and cite that case.
- **Confirm every quote and pin** before it goes in the file: use `find_in_case`
  for the wording and `check_pin_cite` for the page.
- **Grounds beyond failure to state a claim** (limitations, jurisdiction, immunity,
  exhaustion): find the controlling authority with `search_cases` and
  `research_plan`. Read the statute with the state statute tool.
- **Good law:** call `check_good_law` on every case the analysis relies on for more
  than the standard.

## 4. Do the analysis

For each claim, and each element of it, record:
- the complaint paragraphs that allege it, cited as ¶ numbers;
- a rating: **pleaded**, **conclusory** (labels or legal conclusions only),
  **missing**, or **unknown**.

Cite a ¶ only if you read that paragraph in this run. Otherwise write
"unknown: not read". Where `facts.md` already holds the allegation, add its id:
`¶ 14 (F-006)`.

Then give the overall read:
- **Moving:** the strongest grounds, in order.
- **Responding:** which claims are exposed, and what would fix each one (an
  amendment, or facts the client can supply).

## 5. Write or update the file

**Updating:** change only what changed, in place. At the top of the Changes log,
add a dated entry:
- what new documents or law came in;
- each element whose rating moved, old → new, and why.

Leave unchanged sections alone.

**New file:**

```markdown
---
skill: motion-to-dismiss
case: <caption>
court: <court>        # e.g. D. Kan.; Kansas district court, Sedgwick County
judge: <if known>
side: moving | responding
motion: <rule and ground, e.g. Fed. R. Civ. P. 12(b)(6)>
claims: [<claim>, ...]
last_run: <YYYY-MM-DD>
sources:              # every Project document read, with its date or version
  - <file name> (<date>)
missing: [<documents asked for and not provided>]
---
# Motion to dismiss: <case>

## Changes
- <YYYY-MM-DD>: <first run | what changed>

## Short answer
<2–4 sentences: does each claim survive, and how confident>

## Standard
<from legal_standard: the binding wording, quoted, with pin cites>

## Claims
### <Claim 1> — elements per <case, pin>
| Element | Complaint ¶¶ | Rating | Notes |
|---|---|---|---|

## Other grounds
<limitations, jurisdiction, etc., each with authority>

## Exposure and fixes
<responding: what to amend or add; moving: the strongest grounds, in order>

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
3. Update the matter files, creating either one from its template in
   `references/matter-files.md` if it doesn't exist. Re-read each just before
   saving it.
   - `facts.md`: add the alleged facts the analysis turns on (status
     **alleged**, source the pleading ¶), with the next free ids. Not every
     paragraph: only those the ratings rest on.
   - `matter.md`: this file's row in Files (one-line status, today's date); the
     posture, if it moved; the response or reply deadline in Deadlines, from
     `compute_deadline`, if the trigger date is known; a dated line at the top
     of the Change log.
4. In the chat, give:
   - the short answer;
   - what changed since the last run;
   - open questions, and documents still missing.
