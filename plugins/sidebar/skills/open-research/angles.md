# Finding theories: checklists and search templates

These are prompts for thinking, not statements of law. Every theory they
suggest is a candidate until Sidebar's tools support it or kill it.

## 1. Fact hooks

For each fact in `facts.md` that matters, note what it could trigger:

| Hook | Ask |
|---|---|
| Relationship | Contract, employment, landlord–tenant, consumer–merchant, lender–borrower, insurer–insured, principal–agent, trustee or other fiduciary, professional–client, government–citizen? |
| Transaction | A sale of goods, services, a lease, credit or a debt, an ownership interest or securities, real property, an insurance policy, a bond? |
| Conduct | A statement, a silence, a promise, a threat, retaliation, a failure to act, a record kept or destroyed? |
| Harm | Personal injury, property damage, economic loss only, emotional harm, reputation, a statutory right? |
| Party status | Consumer, merchant, public body, licensee, minor, protected class, insolvent, insured? |
| Documents | Each clause: arbitration, forum, choice of law, limitation of liability, shortened limitations, notice and cure, fees, integration, waiver, release |
| Dates | Each date starts a clock: limitations, repose, notice of claim, contract notice, appeal |

## 2. Lenses

Walk every lens. Write each candidate down before judging any of them.

- **Claims:**
  - **Contract:** express, implied, good faith and fair dealing, warranty,
    third-party beneficiary, promissory estoppel.
  - **Tort:** negligence, negligent misrepresentation, fraud and fraud by
    silence, conversion, interference, defamation, nuisance or trespass,
    breach of fiduciary duty, conspiracy, aiding and abetting.
  - **Statute:** any statute that governs the relationship, the transaction
    or the conduct (consumer protection, landlord–tenant, wages, UCC, lending
    and collection, civil rights, licensing), and whether it gives a private
    right of action.
  - **Equity:** unjust enrichment, constructive trust, accounting, rescission,
    reformation, injunction, declaratory judgment.
- **Defenses, ours and theirs:**
  - limitations and repose;
  - release, waiver and accord;
  - contract clauses;
  - comparative fault;
  - economic loss;
  - statute of frauds, parol evidence;
  - immunity, preemption;
  - standing, exhaustion, conditions precedent (notice, demand);
  - privilege;
  - mitigation;
  - estoppel, laches, unclean hands;
  - setoff and counterclaims.
- **Other parties:**
  - employer or principal;
  - the owners behind an entity (alter ego, personal participation in the
    wrong);
  - successors;
  - guarantors and sureties (contractor, license and fidelity bonds);
  - sellers up the chain;
  - professionals who advised (lawyer, accountant, engineer, broker);
  - insurers;
  - government bodies.
- **Procedural vehicles:**
  - state or federal court, venue, removal;
  - declaratory judgment;
  - TRO or injunction;
  - class or collective action;
  - pre-suit notice or demand;
  - arbitration, to compel or to avoid;
  - offer of judgment, for fee and cost leverage;
  - an early dispositive motion, for either side;
  - an agency route first.
- **Remedies:**
  - the best measure of damages (benefit of the bargain or out of pocket);
  - consequential damages;
  - statutory damages or penalties;
  - punitive damages;
  - interest;
  - restitution, specific performance.
- **Fee-shifting:** a statute, a contract clause, bad faith, an offer of
  judgment. It often decides whether a small case is worth bringing.
- **Regulators and insurance:**
  - Who regulates this conduct: an agency complaint, a regulation as the
    standard of care, a licensing board?
  - Who insures the loss or the liability? Who pays changes what the case is
    worth.
- **Constitutional:** only if a government actor, or a statute's validity, is
  in play.
- **Their theories:** what will the other side plead or assert? Track each one
  as a theory "for: them".

## 3. Analogy: search the shape, not the subject

1. Write the dispute's structure in one or two lines:
   - who relied on whom;
   - who controlled or knew what;
   - what was promised, and to whom;
   - who could most cheaply have prevented the harm;
   - what the harm was, and when it was found.
2. Turn the structure into 2–3 searches with generic words, joined by `/p`.
   Run them in the forum's courts, and with no court (or `order_by:
   'most_cited'`) to see other doctrines and states. Examples:
   - `(knew OR aware OR conceal!) /p (relied OR reliance) /p (buyer OR purchaser OR customer)`
   - `(deposit OR "down payment" OR advance!) /p (abandon! OR "never started" OR "failed to perform")`
   - `(employer OR principal) /p (knew OR "should have known") /p (prior OR previous) /p (complaint! OR incident!)`
3. For the best 5–10 matches, call `find_passages(case, 'count OR claim OR "cause of action" OR theory')`
   to see what was pleaded and what survived.
4. Each doctrine a court used on facts like these is a candidate theory. Note
   the case as its first lead.
5. Also chase the leading case of each strong theory forward with the facts:
   `citing_cases(<case>, query='<3–5 distinctive fact words joined by OR or /p>')`.

## 4. Persuasive ladder (when the forum is silent)

Climb in this order. Stop when the question is answered well enough for the
goal.

1. Federal courts predicting the state's law:
   `<term> /p (predict! OR "has not addressed" OR "would hold")` in the
   federal courts of the state and its circuit.
2. The state's unpublished decisions, cited with their status.
3. Has the state adopted the Restatement section or the uniform act?
   `"Restatement (Second) of <Torts|Contracts>" /s <section> /p (adopt! OR follow!)`
   in the state's courts. If it has, look next at other states that adopted
   the same text.
4. Majority and minority rule:
   `<term> /p ("majority rule" OR "majority of jurisdictions" OR "minority rule" OR "weight of authority")`,
   newest first, in the high courts.
5. Openings in the forum's own cases:
   - `"first impression" /p <term>`
   - `("need not decide" OR "need not reach" OR "express no opinion" OR "leave for another day") /p <term>`
   - dissents: `find_passages(<case>, 'dissent')`, then `citing_cases` on the
     dissent's case to see whether later courts approved it.

Label every persuasive authority with its leap (binding, persuasive or
analogy), and with what a court would have to accept for it to work here.

## 5. Kill tests

Run the cheapest first. One that ends the theory saves all the others.

| Test | How |
|---|---|
| The clock has run | `limitations_period(claim, state)`, then `compute_deadline` from the accrual date; also notice-of-claim and contract notice periods |
| An element can't be met | `claim_elements(claim, jurisdiction)`, read against the admitted and proven facts |
| A document bars it | read the contract or release in the Project: arbitration, exculpatory, limitation, shortened-limitations and waiver clauses |
| Binding authority rejects it | `search_cases('<term> /p (reject! OR "no duty" OR "not actionable" OR "does not apply" OR declin!)', court=<binding courts>)`; `citing_cases(<leading case>, query='reject! OR declin! OR distinguish!')` |
| A doctrine bars it | economic loss, privity, immunity, preemption, standing, exhaustion |
| The remedy isn't available | the statute's text, and `claim_elements` on the remedy |

Record each test in `theories.md` with its result, source and date.
- **Killed:** goes to `dispelled.md`, with a "revive if".
- **Wounded:** marked weakened.
- **Survived:** stays open.

## 6. Ranking

| | High | Medium | Low |
|---|---|---|---|
| Viability | elements met on admitted or proven facts; binding law supports | law supports, but the facts are contested; or persuasive law only | analogy, or a court would have to extend the law |
| Value | dispositive, fees, multiplied damages, or decisive leverage | real recovery or a real defense | marginal |

Order the list:
1. high value, viability medium or better;
2. then by viability;
3. then by cost (S, M, L).

High-value long shots stay on the list, with their leap and what a court
would have to accept. Theories "for: them" are ranked by the threat they pose.

## 7. Bias checks before you report

- Is each question worded neutrally? "Does X apply to Y", not "Can we show X".
- Did every memo run an adverse search before its Application was written?
- Is any theory supported only by authority you found looking for support?
  If so, run the adverse search now.
- Does the report give the bad news first?
- Did any status move because of the lawyer's view, or yours? Move it back,
  and record the view beside it.
