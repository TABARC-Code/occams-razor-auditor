# Project Description

## The short version

I built `occams-razor-auditor` as a Claude Skill for one specific move: catching a parsimonious model at the moment it gets stretched past the domain where its parsimony was actually earned. It doesn't argue with the model's conclusion. It names what the model had to delete to reach that conclusion cleanly, and hands the deletion back as a question rather than an objection.

It's one of a small cluster of critical-thinking skills I've built, all circling the same general territory (frameworks doing more work than they're entitled to) from slightly different angles.
---
## The problem

The specific reasoning error here isn't "wrong framework" or "bad logic." Economics, productivity systems, game theory: these are all genuinely useful, genuinely parsimonious tools, and parsimony is a real virtue, not a flaw waiting to be corrected. The problem shows up when someone treats a model's cleanness, the fact that it's simple, that it's consistent, that "the numbers don't lie", as itself proof the model has accounted for everything relevant. It usually hasn't. Trust, capacity, history, dignity: none of these show up in the model's inputs, and their absence from the inputs quietly gets read as their absence from the situation.

That's a seductive move precisely because it feels like rigour rather than avoidance. I've found that calling it out badly (arguing the conclusion, listing the "real" factors, moralising) tends to read as soft-headedness pushing back against hard-headedness, and it loses. I built the skill because the correct response isn't a counter-argument. It's a question the clean model can't answer.

## The approach

Don't dispute the conclusion. Name the framework's actual jurisdiction, name what fell outside it, and stop talking. The Socratic question does the work an argument can't, because it can't be won or lost; it just sits there until the person answers it or doesn't.

I also had to solve a problem that wasn't obvious until it showed up in testing: my sibling skill, `procrustean-auditor`, catches a closely related failure (forced fit, reality bent to match a model), and most real messages trip both mechanisms at once. Rather than leave that to be adjudicated by feel at trigger time, I pushed the boundary into an explicit, checkable rule. See "Design decisions" below.

## Architecture

Single file. I keep frontmatter (name, description, Dewey classification, version) in `SKILL.md`, then activation logic, a boundary rule against the sibling skill, a five-step workflow, output formatting, an anti-pattern list, a relationship-contracts section, version history, and an inline eval suite. No chapter files, no glossary, no supporting reference material. I kept the whole thing comfortably under 4,000 tokens by design.

## Design decisions

The activation condition that distinguishes this skill from `procrustean-auditor`, the user treating a model's cleanness as evidence, started out as "the user is visibly satisfied with it," which sounds reasonable and turned out to be untestable. I rewrote it to require a concrete textual marker ("the numbers don't lie," "objectively," and similar), specifically so two different readings of the same message wouldn't quietly diverge depending on who was doing the reading.

I considered and rejected two additions rather than folding them in on the strength of sounding useful. A stakes-tiering mechanism, so low-stakes grand narratives (which programming language is objectively best) would get a lighter response than high-stakes ones (cutting off a friend), I rejected because the existing "name what's actually being deleted, stand down if nothing's there" instruction already does this implicitly; a low-stakes case just produces a thin or empty variable list on its own. A seventh deleted-variable category for power asymmetry I rejected in favour of folding the idea into the third-party framing section instead, on the basis that an open-ended list of categories is exactly the kind of thing I tell the skill's own user to be suspicious of.

I declared an early relationship link to `perdita-mode`, a separate structural-analysis skill, at build time on the strength of being plausible, and cut it two versions later for carrying no confirmed use. That's probably the right default going forward: link when a real case proves the connection, not before.

## Current state

Specified and internally consistent: activation logic, boundary rule, workflow, output format, and the eval suite all agree with each other as of this version. I've hand-tested it against ten written trigger cases plus a completeness gate. Not yet exercised: real, uncontrolled conversations. I've reasoned through everything here; I haven't observed it in the wild.

## Operational notes

No configuration surface. No environment variables, no external calls, nothing to deploy beyond loading the packaged `.skill` file into a Claude context that supports skills. Maintenance is entirely a matter of editing `SKILL.md` and re-running the eval table by hand.

## Limitations and awkward bits

The eval suite is the part most likely to mislead someone skimming this. Ten cases in a table with expected outcomes look like a test suite. They behave more like a checklist I read before shipping: useful, but it can't catch a regression I didn't think to write a row for, and nothing runs it automatically yet.

The boundary rule against `procrustean-auditor` is only as good as having both skills loaded together. If I don't, the rule falls back to plain conversation rather than forcing the wrong shape onto a case just because it's the only tool I've got in the room.

## Future direction

The most likely next change is reinstating the `perdita-mode` link, conditional on an actual case demonstrating it's load-bearing rather than just plausible. After that, replacing the hand-reasoned eval table with something an automated harness could execute would close the biggest honest gap in the current setup. Neither is scheduled; both are just the visible next steps.
