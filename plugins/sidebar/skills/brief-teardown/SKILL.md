---
name: brief-teardown
description: Check the other side's brief or motion against the case law. Find misquotes, wrong pin cites, citations that don't exist, authority that is no longer good law, cases they overstate, later cases that limit or distinguish their authority, and the leading cases they left out. Keep the result as a markdown file in the Claude Project that later briefs in the same fight update. Use when the user shares or names an opposing brief, motion, response or reply and asks to check it, answer it, rebut it or find its weaknesses. Needs the Sidebar connector.
---

# Opposing brief teardown

You can critique the reasoning of a brief on your own. What you can't do
without Sidebar is check the authorities: whether each one exists, says what
the brief quotes, sits on the page cited, is still good law, and holds what
the brief claims. You also can't see which leading cases the brief leaves out.
That checking is this skill's job.

## 1. Find the file

List the Project's files and look in `briefs/` for front matter
`skill: brief-teardown` on the same motion. Names follow
`briefs/teardown-<short-case-name>-<motion>.md`.
- If the file exists, this brief is usually the next round (a reply, or a
  sur-reply). Add a section for it, and carry over any authority that both
  briefs use.
- If several files could match, ask which one.
- If none matches, start a new file.

## 2. Ask once

Ask only for what is missing:
- their brief, as a file or pasted text;
- the court;
- what it answers (our motion or brief, if it's in the Project);
- which arguments matter most to the user, if they have a view.

## 3. Check every citation

Run `check_citations` on the full text of their brief. It reports, for each
citation, whether:
- the case exists;
- the name and year match;
- each quote appears word for word;
- each pin cite is on the right page;
- each short form resolves.

List every problem it finds.

## 4. Work through their key authorities

The key authorities are the 5–12 cases their main arguments rest on. For each one:
1. **What it holds.** Use `find_passages` with their proposition as the topic.
   Compare the holding with how they describe it, and flag dicta presented as a
   holding, a dissent or concurrence cited as the court, or a fact pattern
   stretched to fit.
2. **Good law.** Run `check_good_law`. Note any negative treatment and the latest
   word from the forum's high court.
3. **Later courts that cut it back.** Use `citing_cases(case,
   query='distinguish! OR limit! OR inapposite OR "not applicable" OR declin!')`.
   Run it again with a query built from our facts, to find later cases that
   reached the opposite result on facts closer to ours.
4. **Binding or not.** If they cite out-of-forum or unpublished authority as if it
   controls, say so.

## 5. Standards, elements and omissions

- If their brief states a standard or a claim's elements, compare it with
  `legal_standard` and `claim_elements` for the forum. Flag wording that is
  outdated, softened or overstated.
- **Omissions:** the authorities those tools rank highest that they don't cite,
  and adverse binding authority they skip. Use `search_cases` on the issue,
  binding courts only.

## 6. Write or update the file

```markdown
---
skill: brief-teardown
case: <caption>
court: <court>
briefs: [<their brief and date>, ...]   # one entry per round
last_run: <YYYY-MM-DD>
---
# Teardown: <their brief>

## Changes
- <YYYY-MM-DD>: <first run | new brief added | what changed>

## The most useful points for our response
<3–6 bullets, strongest first>

## Citation problems
| Their cite (page of brief) | Problem | What the source actually says (quote, pin) |
|---|---|---|

## Key authorities
### <Case> — their use: "<their characterization>" (brief p. <n>)
- Holds: <quote, pin>
- Good law: <check_good_law result>
- Later courts: <limiting or distinguishing cases, each with its passage>
- Our response: <one or two sentences>

## Standards and elements as stated vs. as the courts state them
## What they left out
## Authorities we'd add
| Authority | For | Quote verified | Pin verified | Good law |
|---|---|---|---|---|
```

## 7. Check, save, report

1. Run `check_citations` on our file too: every case cited in it must check out.
2. Save the file with the Projects tool at its path.
3. In the chat, give the most useful points, the citation problems found, and
   anything you could not verify.
