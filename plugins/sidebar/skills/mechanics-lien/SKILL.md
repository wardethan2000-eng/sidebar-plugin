---
name: mechanics-lien
description: Prepare a Kansas mechanic's lien statement for a contractor, subcontractor or supplier, on residential or commercial property. Works out the filing deadline and every notice, warning-statement and service step from the current statute text, then ties every dollar of the claim to a contract, change order, invoice or other document so the lien can't be attacked for lack of support. Builds Exhibit A (the invoices with a reconciliation, or a cost-and-work table when there are no invoices), drafts the verified lien statement, gives the client a list of documents still needed, and keeps it all as a markdown file in the Claude Project that later runs update. Use when the user wants to file, draft, check or update a mechanic's lien or lien statement in Kansas, or asks whether a client can still lien a job. Needs the Sidebar connector.
---

# Kansas mechanic's lien

You can fill in a lien form on your own. What goes wrong with liens in practice
is a missed deadline or notice, and a claimed amount the documents don't
support. This skill puts the deadline first, reads the statute fresh every
time, and makes every dollar of the claim traceable to a document before
anything is drafted. Everything is kept in one file per lien.

A lien statement is judged from its four corners, and the amount can't be
increased by amendment later (K.S.A. 60-1105(b)). Both points drive this skill:
the itemization has to be in the filed statement, and the amount has to be
right the first time.

## 1. Find the file

First read `matter.md` and `facts.md` at the Project root, if they exist.
Formats and rules: `references/matter-files.md`.

List the Project's files and look in `liens/` for front matter
`skill: mechanics-lien` with the same property and claimant. Names follow
`liens/lien-<short-job-name>.md`, e.g. `liens/lien-elm-st-roof.md`.
- One match: read it and go on as an update (new documents, a payment, filing
  done, service done). Re-run steps 3 and 4 on anything that changed.
- Several could match: ask which one.
- None: start a new file from the template in step 9.

## 2. Ask once

Ask, in one message, only what you can't find in the Project, the file, or
`matter.md` and `facts.md`. Put the deadline questions first: if the deadline
is close, the user should see that before anything else.

**Deadline and role**
- Who the claimant is, and who hired them: the owner (original contractor),
  or a contractor or subcontractor (subcontractor or supplier). The name and
  address of the contractor above them.
- The **last day** the claimant furnished labor or materials at the site, what
  that last work was, and what shows the date (daily log, delivery ticket,
  timesheet, dated photo, invoice). Also the first day.
- Whether any lien statement or notice of extension was already filed on this
  job, and when.

**The property**
- The owner of record (from the deed or county records, not the client's
  memory), the street address, the county, and the legal description.
- Residential or not: a structure built for use as a residence for no more than
  two families? If residential: did the owner live there when the claimant
  started (an improvement), or is it new construction? Has the new home been
  sold, or is a sale coming?
- Public property or a public project (a school, city, county or state job).
  If so, there is usually a bond claim, not a lien (K.S.A. 60-1111): say so and
  stop the lien work unless the user wants to go on.
- Any mortgage lender or other holder of a recorded interest (needed for
  service by a subcontractor).

**The money and the documents** (ask for the documents themselves, uploaded to
the Project, not a summary)
- The contract or proposal, and every change order, with signatures or the
  approval email or text.
- Every invoice sent for this job.
- Every payment received, with dates, and any credits or back-charges.
- Delivery tickets, material receipts, timesheets or daily logs.
- Any lien waiver or release the claimant signed on this job.
- **Subcontractors on residential improvements:** the warning statement under
  K.S.A. 60-1103a and proof it was mailed, or the owner's signed acknowledgment.
- **New residential property after a sale:** the notice of intent to perform
  under K.S.A. 60-1103b, and its filing date.

**For the statement**
- Who will sign the verification: a person with authority to act for the
  claimant who knows the facts, and their title.
- The attorney signature block, if the user's form has one and it isn't in the
  Project.

If the user wants to go on without something, list it under `missing` and say
in the report what depends on it.

## 3. Read the statute, every run

Call `kansas_statute` for K.S.A. 60-1101, 60-1102 and 60-1105, and add 60-1103
(subcontractors and suppliers), 60-1103a (residential improvement), 60-1103b
(new residential) and 60-1111 (public works) when they apply. Work from the
text it returns, not from memory: the periods, the extension rule, the notice
requirements and the service rules all come from it. Quote the subsection for
each requirement you list.

Then build the **deadline table** with `compute_deadline` (court `kansas`,
period in months, from the last-furnished date). One row each, where it applies:
- the lien filing deadline;
- the notice-of-extension deadline and the extended filing deadline (property
  other than residential only; read the extension subsection for who the notice
  must be mailed to and how);
- service of the lien statement after filing (subcontractors and suppliers:
  read 60-1103(c) for who must be served and how);
- the deadline to sue to foreclose, one year from filing (60-1105(a)).

Plan to file by the plain anniversary date, before any weekend or holiday
rollover. If the rollover is the only thing keeping the lien timely, say so
plainly and research whether it applies to lien statements before relying on
it.

**Stop and say so at the top of the report** if:
- the deadline has passed (and no timely extension was filed);
- a subcontractor or supplier on a residential improvement has no warning
  statement and no owner acknowledgment, and the claim is over the statute's
  threshold;
- new residential property has passed to a buyer and no notice of intent to
  perform was filed before the deed was recorded;
- the job is public and bonded.

Give what the statute says and the date math, and let the attorney decide.

**The last-furnished date.** The deadline is only as good as this date. Rate
it **documented** (a dated record shows work or delivery at the site that day),
**client's word**, or **doubtful**. Ask whether the last work was part of the
contract work itself, or a warranty call, a punch-list touch-up, a repair of
the claimant's own defective work, or a trip made to extend the deadline. If it
was any of those, research with `search_cases` (Kansas, then `kansas all`)
whether that work counts as the last furnishing, and compute the deadline from
the last undisputed date as well. Put both dates in the table and say which
one to file by.

## 4. The support ledger

This is the core of the skill. Build one table where every dollar of the
claimed amount has a row:

| # | Date | Work or material | Amount | Source document | Approved by owner/contractor? | At this site? | Status |
|---|---|---|---|---|---|---|---|

Statuses:
- **Documented**: a signed contract or approved change order sets the price,
  and an invoice, delivery ticket, timesheet or log shows it was furnished at
  this site.
- **Partly documented**: one side is missing, e.g. an invoice with no
  approval for extra work, or work with no record that it was done. Say which
  side.
- **Unsupported**: only the client's word, or a number with no document behind
  it.
- **Not lienable**: see below.

**Reconcile to the claimed amount.** Show the arithmetic in full:
contract price + approved change orders + documented extras − work not
performed − payments − credits and back-charges = amount claimed. Then check
that the line items add up to the same total, to the cent. Any gap between the
total and what the documents account for is an unsupported row of its own.
A lien whose attachment did not account for part of the total is exactly what
an owner or contractor attacks (see *In re Lien Against the District at City
Center*, below).

**Lienable items.** Read 60-1101's text for what a lien covers (labor,
equipment, material or supplies furnished at the site, and transport). Flag
each row that may fall outside it and research the category with Sidebar
before keeping it, for example: interest, late fees, finance charges,
attorney's fees, collection costs, lost profit or overhead on work not done,
delay or disruption damages, materials never delivered to the site, work on
another property, and charges under a different contract. Keep the research
short: one controlling Kansas authority per category, checked with
`check_good_law`.

**Recommendation.** Give the amount the documents support (documented rows),
the amount if partly documented rows are included, and what is excluded and
why. The choice is the attorney's. Say plainly that an amount left out now
can't be added to this lien later (60-1105(b)), while an amount that can't be
supported invites a challenge to the lien. Excluded amounts are still owed
under the contract; note them for a contract claim.

## 5. The client request list

For every row that isn't **documented**, and for every doubt about the
last-furnished date, list exactly what would fix it and which row it
supports, in plain words the client can act on:
- "Signed change order or the text or email where the owner approved the extra
  vent work (row 7, $1,240)."
- "The delivery ticket for the shingles delivered on or about March 3 (rows 2
  and 3; also proves the start date)."

Put the most valuable items first: the ones that move the most dollars to
documented, then the ones that protect the deadline. Keep it ready to send.

## 6. Exhibit A

The itemization must be part of the filed, verified statement: attached,
referred to in the statement, and covered by the verification.

- **Invoices cover the amount:** attach the invoices, with a one-page
  reconciliation on top: each invoice, each payment and credit, the balance.
  The balance must equal the claimed amount.
- **A signed contract or note evidences the whole amount:** the statute allows
  a copy of it instead of the itemized statement (60-1102(a)(4)). Add the
  reconciliation of payments anyway if any were made.
- **No invoices, or invoices that don't cover it all:** build a cost-and-work
  table from the ledger, using `references/exhibit-a-table.md`. One row per
  item of work or material: date or date range, description specific enough
  for the owner to recognize the work, quantity and unit, rate, amount. Then
  the reconciliation. Leave out internal notes, ratings and sources.

The test the courts apply is whether the itemization lets the owner tell
whether the work was done and the charges are fair; it should be neither
padded nor bare. A single lump sum is not enough.

## 7. Draft the lien statement

Use the user's form if it is in the Project; otherwise
`references/lien-statement-template.md`. Fill it from the ledger, the deadline
table and the facts:
- owner(s) of record, exactly as on the deed;
- the claimant's name and an address sufficient for service of process, in the
  body of the statement (not only on an attached invoice);
- the contractor's name, required for a subcontractor or supplier;
- the street address, county and full legal description;
- whom the claimant contracted with (the owner, or the named contractor);
- the amount in words and figures, matching the reconciliation;
- the last date labor was performed and the last date materials were
  furnished, as documented;
- Exhibit A referred to by name;
- the verification by the person named in step 2, with a notary block;
- for a subcontractor on a residential improvement, the affidavit that the
  warning statement was given (60-1103(a)(2)).

Produce it as a Word document if the user's form is Word, and say which blanks
are left for signing and notarizing.

## 8. Check before filing

Go through each requirement of the statute text from step 3, one line each:
the requirement, the subsection, where the draft meets it, **met** or **not
met**. Then:
- the amount in words, in figures, in the reconciliation and in the
  "asserts a lien" sentence all match;
- names match the deed and the contract exactly (an LLC's full name);
- the dates match the ledger and the deadline table;
- the statement stands on its own without anything outside its four corners.

Then the post-filing steps, with dates from the deadline table: service of a
copy where required (who, and by which method the statute allows), mailing of
any notice of extension, and the foreclosure deadline.

## 9. Write or update the file

**Updating:** change only what changed, in place. At the top of the Changes
log, add a dated entry: documents received, rows whose status moved (old → new,
and why), the amount if it changed, and any filing or service done.

**New file:**

```markdown
---
skill: mechanics-lien
claimant: <name>
role: original contractor | subcontractor | supplier
contractor: <who hired the claimant, if not the owner>
owner: <owner(s) of record>
property: <street address, county>
residential: improvement | new construction | no
last_furnished: <YYYY-MM-DD> (<documented | client's word | doubtful>)
file_by: <YYYY-MM-DD>
status: gathering | ready to file | filed <YYYY-MM-DD> | served <YYYY-MM-DD>
amount_claimed: <$>
last_run: <YYYY-MM-DD>
sources:
  - <file name> (<date>)
missing: [<documents asked for and not provided>]
---
# Mechanic's lien: <claimant> — <property>

## Changes
- <YYYY-MM-DD>: <first run | what changed>

## Deadlines
| Date | What | Source |
|---|---|---|

## Gatekeepers
<role, residential status, public job, warning statement, notice of intent,
last-furnished date and its rating — each met, not met or unknown, with the
statute subsection>

## Support ledger
<the table from step 4, then the reconciliation>

## Recommended amount
<documented / with partly documented / excluded and why>

## Client requests
<the list from step 5>

## Pre-filing check
<the table from step 8>

## After filing
<service, extension mailing, foreclosure deadline>

## Authorities
| Authority | Used for | Quote verified | Pin verified | Good law |
|---|---|---|---|---|
```

## 10. Check, save, report

1. Write the whole file into `verify_memo`: the full text you would save,
   never an extract. Fix what it lists and run it again. Stop when it says
   final, or when the only items left are ones you have checked and found
   fine; name those in the chat.
2. Save the file `verify_memo` returned, exactly as returned, with the
   Projects tool at its path. (Without `verify_memo`, run `check_citations` on
   the whole file and save your file.)
3. Update the matter files (`references/matter-files.md`), re-reading each
   just before saving: the facts the lien rests on in `facts.md` (the dates,
   the contract, the payments, each with its source); in `matter.md`, this
   file's row in Files, every date from the deadline table in Deadlines, and a
   dated Change log line.
4. In the chat, lead with the file-by date and any stop condition. Then the
   recommended amount and what was excluded, the client request list, and the
   blanks left on the statement.

## Starting points

These are leads for the research above, not a substitute for it. Read each
with `find_passages` and run `check_good_law` before relying on it, and look
for newer cases.
- Reasonable itemization: *Kopp's Rug Co. v. Talbot*, 5 Kan. App. 2d 565, 572
  (1980); *Scott v. Strickland*, 10 Kan. App. 2d 14 (1984); *Huber Co. v.
  DeSouza*, 32 Kan. App. 2d 614, 615–17 (2004) (four corners; insufficient
  itemization invalid).
- Strict compliance to perfect, liberal construction after: *Haz-Mat Response,
  Inc. v. Certified Waste Services Ltd.*, 259 Kan. 166, 170 (1996); *Buchanan
  v. Overley*, 39 Kan. App. 2d 171, 173–75 (2008) (attachments, verification,
  address for service).
- Verification: *Trane Co. v. Bakkalapulo*, 234 Kan. 348, 352 (1983).
- Unitemized part of the total: *In re Lien Against the District at City
  Center*, No. 121,184 (Kan. Ct. App. Feb. 28, 2020).
- Naming the contractor, amendment after the deadline: *Tradesmen
  International, Inc. v. Wal-Mart Real Estate Business Trust*, 35 Kan. App. 2d
  146 (2006).
- Service and the owner's liability for subcontractor liens: *Owen Lumber Co.
  v. Chartrand*, 283 Kan. 911 (2007).
