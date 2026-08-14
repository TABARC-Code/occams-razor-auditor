# occams-razor-auditor
Claude Skill built for one specific move: catching a parsimonious model at the moment it gets stretched past the domain where its parsimony was actually earned. It doesn't argue with the model's conclusion. It names what the model had to delete to reach that conclusion cleanly, and hands the deletion back as a question rather than an objection.

Author: TBARC-Code
---
occams-razor-auditor

I built this as a Claude Skill to catch a specific reasoning failure: a mental model that works cleanly in one domain getting stretched over a human situation it was never built to measure. It sits in my TABARC-Code skill catalogue as a sibling to procrustean-auditor, but it isn't a duplicate of it -- see "Why it exists" below.

What it does

When someone leans on a parsimonious framework and treats its cleanness as proof the framework accounts for everything, I have it:

name the framework and state where it actually works
surface one to three unmeasurable variables the framework had to delete to keep working -- specific to the case, never a recited category list
state the scope limit in one sentence, as a boundary crossed rather than an error made
ask exactly one Socratic question that puts the deleted thing back in view

Then it stops. No summary, no "so you might want to reconsider," no argument about whether the conclusion is right. I wanted the output to fit in about half a screen.

A worked example, taken directly from my eval suite: someone tracking how often they initiate contact with a friend, concluding "the numbers don't lie, negative ROI, I'm cutting them off." I don't have it dispute the count. It names ROI logic's actual domain (transactional exchange between roughly equal parties), points out that initiation counting measures effort rather than desire, and those two diverge exactly when someone doesn't have the capacity to reach out. Then it asks something like whether the user has ever wanted to see people and couldn't bring themselves to pick up the phone.

Why it exists

I already ave a sibling skill, procrustean-auditor, for catching forced fit -- reality bent to match a model. This one catches something narrower and, in practice, harder to spot: scope creep. The model isn't distorting anything. It genuinely fits its home domain. The user has just stopped noticing where that domain ends, because the model's own cleanness has become the thing they're attached to.

Those two failure modes overlap constantly in real messages, which is most of why I decided this needed proper documentation rather than shipping as a one-off prompt. The boundary between them turned out to be a genuine engineering problem, not just a naming exercise -- see "Design decisions" in description.md.

Features

Implemented:

a three-condition activation gate, with the load-bearing condition (elegance treated as evidence) tied to concrete textual markers rather than a vibe check
an explicit tie-break rule against procrustean-auditor for the common cases that satisfy both skills at once
a fallback so the tie-break doesn't force this skill's mechanism onto a case when the sibling skill isn't actually loaded in context
handling for third-party framework reports (someone relaying another person's reductive model, sceptically or approvingly), which I hadn't defined behaviour for originally
a self-application check: I built the skill as a clean five-step mechanism, and I've had it flag that as a reason to distrust itself firing on thin evidence
continuation guidance for the turn after the question gets asked, so the response doesn't default to moralising once the defined workflow runs out
an inline eval suite: ten trigger-precision cases, a completeness gate, and a failure-classification table for logging future regressions

Not implemented:

no automated test runner. I read and reason over the eval table myself, rather than asserting it with code. See "Known limitations."
no live usage data. I've worked through every case in the eval suite by hand; none of it reflects an uncontrolled conversation yet.
How it works

I kept the whole skill in one SKILL.md file, no chapters, no supporting reference files. That's deliberate; the doctrine here is short enough that splitting it up would have added indirection without adding clarity.

Activation runs on three conditions, all required: an elegant framework whose tidiness is being cited as evidence, a human situation that plausibly contains something unmeasurable, and evidence the model's been extended past "this illuminates part of it" into "this explains it." If a message could also trigger procrustean-auditor, which is common, because a framework extended past its scope is usually also a framework bending reality to fit, I use a three-step tie-break to decide which skill actually fires, keyed on whether the text shows the framework's tidiness being offered as proof rather than just used.

From there it's a fixed five-step output: name the framework's real domain, name what got deleted (never more than three, never the category label itself, always the case-specific version), state the boundary as scope rather than error, ask one question, stop.

Installation

I ship this as a packaged .skill file, built with the skill-creator tooling's package_skill.py script against the SKILL.md source. There's no separate build step beyond that; the packager validates the YAML frontmatter and bundles the file.

Loading it into a conversation depends on whichever skill-installation mechanism your Claude client provides. That's outside what I control here, so I haven't documented it beyond confirming the package validates cleanly against skill-creator's own checks.

Usage

No configuration. It either fires on a message or it doesn't, governed entirely by the activation conditions above. Two more examples from my eval suite, chosen because they show the boundary logic rather than just the straightforward case:

"My manager says performance is just hours logged, which is obviously bollocks." Doesn't fire -- the user's already done the work I built the skill to prompt.
"My manager says performance is just hours logged, and honestly the maths does check out." Fires, but the question aims at what the user themselves can't unsee, not at how to argue with the manager.
Security

No network calls, no credentials, no external dependencies at runtime; the entire skill is a static instruction document read into context. I ran it against my security-scanner rule set (prompt-injection patterns, invisible Unicode, exfiltration-shaped language, frontmatter authority escalation) with zero findings at time of writing.

Known limitations

The eval suite is the honest weak point. Ten trigger cases and a completeness gate look thorough on the page, but I'm reasoning through them myself, not running them through a test harness that asserts anything. It'll catch an obvious regression in the activation logic. It won't catch a subtle drift in tone, or a Socratic question that's technically well-formed but flat.

The boundary rule against procrustean-auditor assumes I've got that skill loaded in the same context. It degrades sensibly if I haven't, falling back to plain conversation rather than forcing the wrong mechanism onto a case, but that fallback has had exactly as much real-world exercise as everything else here: none yet.

Development notes

I took the skill through three passes after the initial build: a slime-mold-analysis pass that treated the sibling-skill trigger competition as a decentralised network problem and found the procrustean-auditor boundary wasn't operationally testable; a skill-audit-repair pass that closed a genuine hard blocker (I hadn't built an eval suite at all); and a deep-review my usual passes where I added six things I found by trying to break the skill rather than polish it. I rejected two ideas andd additions from that last pass outright rather than folding them in quietly: a stakes-tiering mechanism, and a seventh deleted-variable category. Both would have added on needed surface area without covering anything the existing rules didn't already handle. I've kept the full reasoning for both in the version history inside SKILL.md.

Roadmap

An earlier version of mine declared an "influence" link to a separate skill, perdita-mode, for structural-tension vocabulary. I puled it for lack of use than kept it on the strength of being plausible. Reinstating it, if a real case ever proves it load-bearing, is the most likely next addition. Beyond that, replacing the manually-reasoned eval table with something an automated harness can actually execute is worth doing, but I haven't started it. i may in the uture
