# research/ file formats

Everything lives under `research/` in the Claude Project. Save with the
Projects tool. Ids are never reused:
- **Q-** questions; a memo takes its question's id;
- **T-** theories;
- **A-** legal premises not yet researched;
- **R-** runs;
- **F-** facts, owned by `facts.md`.

Every file ends with an end line, so a partial read can be spotted.

```
research/
  index.md                 read first; at most ~200 lines
  theories.md              live theories
  dispelled.md             dead theories, kept for good
  log.md                   every change, one line each, append only
  memos/Q-007-<slug>.md    one memo per subquestion
```

## index.md

Keep it short. Collapse each answered branch to one line. The full detail is
in the memos and `log.md`.

```markdown
---
skill: open-research
matter: <short description, no more than the matter.md title>
goal: <what the client wants>
side: <...>
forum: <court id, or candidates>
governing_law: <state>
last_run: <R-id> <YYYY-MM-DD>
data_through: <Sidebar's data date at the last run>
counts: {questions: <n>, theories: <n>, dispelled: <n>, memos: <n>, queue: <n>}
---
# Research index: <matter>

## Question tree
- Q-001 [open] <top question> (serves: <goal>)
  - Q-002 [answered: yes · medium] <question> → memos/Q-002-<slug>.md v2 (law checked <date>)
    - Q-005 [queued] <question>
  - Q-003 [answered: no · high · killed T-004] <question> → memos/Q-003-<slug>.md v1

## Theories
| T | Theory (one line) | For | Kind | Status | Conf. | Viab./Value | Rests on | Next |
|---|---|---|---|---|---|---|---|---|

## Legal premises not yet researched
| A | Premise | Used by | Status (unresearched / queued as Q-… / answered by Q-…) |
|---|---|---|---|

## Facts the research rests on
| F | Status when last used | Used by |
|---|---|---|

## Queue (needs the lawyer's go)
| # | Rabbit hole | Serves | What turns on it | Size | Proposed |
|---|---|---|---|---|---|

## Runs
| R | Date | Scope approved | Budget | Used | Result / where it stopped |
|---|---|---|---|---|---|

## Recent changes
<the last 10 lines of log.md>

<!-- end research/index.md · <n> questions · <n> theories · <n> memos -->
```

Sizes: **S** is one kill test (a few tool calls), **M** is one memo, **L** is
several memos.

## theories.md

One section per live theory. A dead theory moves to `dispelled.md`.

```markdown
## T-003 <theory in one line>
- kind: claim | defense | party | procedure | remedy | fee-shifting | leverage
- for: us | them
- status: open | supported | weakened | parked
- confidence: high | medium | low
- viability: H|M|L · value: H|M|L · leap: binding | persuasive | analogy
- what a court must accept: 1. <step> 2. <step>
- rests on: facts F-003 (admitted), F-007 (assumed); memos Q-002 v2; premises A-002
- best for: <cite, pin> (Q-002) · best against: <cite, pin> (Q-002)
- kill tests: [x] <test>: <result> (<source>) · [ ] <test>: <what it needs>
- would change if: kill if <fact or law>; weaken if <…>; strengthen if <…>
- lawyer's view: <if given; it moves nothing>
- history:
  - <YYYY-MM-DD> open → supported (Q-002 v2; <case, pin>)

<!-- end research/theories.md -->
```

**Status words:**
- **open:** a candidate, not yet tested or partly tested.
- **supported:** its kill tests passed, and a final memo of medium or higher
  confidence answers its controlling question in its favor.
- **weakened:** a serious problem was found, but not a fatal one.
- **parked:** the lawyer chose not to pursue it. Record why.
- **dispelled:** a binding authority, a run clock, an element the admitted or
  proven facts can't meet, or a clause ended it. It moves to `dispelled.md`.

## dispelled.md

```markdown
## T-004 <theory> — dispelled <YYYY-MM-DD> (<R-id>)
- why: <one line>. <case, pin>: "<verbatim quote>"
- memo or kill test: <Q-003 v1 | the kill test and its result>
- revive if: <the fact or law that would bring it back>

<!-- end research/dispelled.md -->
```

## Memo: research/memos/Q-<nnn>-<slug>.md

Keep it to about 1,500 words above the tables. A memo that wants to be longer
is two questions. Write the Short answer so it can be read alone.

```markdown
---
id: Q-007
title: <neutral question, at most 20 words, no client names>
parent: Q-002
theories: [T-003]
status: draft | final | needs-recheck | superseded
version: 1
answer: yes | no | probably yes | probably no | unsettled | depends
confidence: high | medium | low
created: <YYYY-MM-DD>
updated: <YYYY-MM-DD>
law_checked: <YYYY-MM-DD>       # the date the authorities were last checked
data_through: <Sidebar's data date then>
jurisdiction: <governing law; forum>
rests_on_facts: [F-003, F-011]
rests_on_memos: [Q-004 v1]
assumptions: [A-002]
stopped_because: answered | saturated | can't matter | budget | depth
verification: <verify_memo result and code, or "check_citations <date>: <n> cases, <n> problems; good law <date>">
---
# Q-007: <question>

## Short answer
<2–4 sentences: the answer, the confidence and why that level>

## Rule
<The law only: no client facts, no F-ids. Each proposition gets a verbatim
quote, its pin, binding or persuasive, and its good-law status.>

## Cases like ours
<the closest cases both ways: facts in one line, the outcome, and how ours differs>

## Application
<the rule applied to the facts by F-id; which facts carry the answer>

## Against us
<the other side's best 2–3 arguments, each with its best authority and
whether it is answered; if an adverse search found nothing, the queries run>

## What would change this
- flip if: <fact or law>
- weaken if: <...>
- strengthen if: <...>

## Open questions
<for the client, the documents, or more research; each one goes to the queue>

## Side findings
<law found that bears on something else, one line each; each one goes to the queue>

## Authorities
| Authority | For | Binding here? | Leap | Quote verified | Pin verified | Good law (date) |
|---|---|---|---|---|---|---|

## Research log
- discovery: search_cases `<query>` (<court>) → <n>; used: <cases>
- adverse: search_cases `<query>` (<court>) → <n>
- chase: citing_cases(<case>, query=`<…>`) → <n>; cited_authorities(<case>)
- recalled, then confirmed by: <case> ← <search that found it>
- coverage notices: <copied from the tools>
- dead ends: <what was tried and found nothing>

## Changes
- <YYYY-MM-DD> v1: first version (<R-id>).

<!-- end Q-007 v1 -->
```

## log.md

Append only. One line per change. Every status change names its source: an
F-id, a Q-id with its version, or a citation.

```markdown
- <YYYY-MM-DD> · <R-id> · T-003 open → supported · source: Q-002 v2 (<case, pin>)
- <YYYY-MM-DD> · fact sweep F-012 · Q-004 v1 → v2, answer unchanged, confidence medium → low · source: F-012 (assumed)
- <YYYY-MM-DD> · <R-id> · T-004 dispelled · source: <case, pin>
- <YYYY-MM-DD> · fact sweep F-012 · Q-006 checked, no effect (F-012 bears on no step)
```

## Confidence caps

Give the highest level that every condition allows.

- **high:**
  - binding authority, read in this research;
  - quote and pin verified, good law checked, latest word read;
  - adverse authority answered;
  - every fact relied on is admitted or proven.
- **medium:** at most this when:
  - the facts relied on are alleged or disputed; or
  - only persuasive authority is on point; or
  - a binding adverse case is not fully answered.
- **low:** at most this when the memo rests on:
  - analogy, or other states only; or
  - an assumed fact; or
  - an unresearched premise (an A-id); or
  - a search that was cut short.
- **A memo rests on other memos:** its confidence is no higher than the lowest
  of theirs.
- **A theory** is no more confident than the memo that answers its
  controlling question.

## Rules for memos that build on each other

- Rely on another memo's conclusion by reference (`rests_on_memos`, with its
  version). Never copy its quotes.
- Before relying on a memo, check it:
  - it is final;
  - its `law_checked` is current against Sidebar's data date;
  - its facts are unchanged;
  - its version is the one you cite.

  If any check fails, re-verify it first.
- When a memo's version changes, mark every memo and theory that rests on it
  needs-recheck, and every one downstream of those.
- A legal premise used without research gets an A-id. When a second memo
  uses it, queue it as a question.
