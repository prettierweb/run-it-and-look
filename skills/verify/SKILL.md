---
name: verify
description: Prove a change works by running it and looking, before calling it done. Use before saying "done", "fixed" or "shipped" in any project, before a commit or deploy, when a check keeps failing, and when the user reports a bug that slipped through. Finds or writes the project's own VERIFY.md (how to start it, what to look at, a list of past breakages).
---

# Verify

Based on the Claude Code team's post
[How the Claude Code team thinks about verification](https://claude.dev/blog/how-the-claude-code-team-thinks-about-verification/)
(Addy Osmani, claude.dev). Their main point: passing tests are not proof. Running the real thing is.

## 1. Run it and look

Done means: the app, game or site started, the changed thing was used the way the user will use it, and you
saw the result (screenshot, frames, page, real output). Not "types pass", not "0 errors".

- Web: drive it in a browser tool and screenshot it. Desktop app: launch it and screenshot it (ask before
  sending clicks or keys to the user's screen). Game: run it and grab frames from the player's view.
  Backend: call the real endpoint with real input.
- Put that picture or output in the reply.

## 2. Use the project's VERIFY.md

Every project should have a `VERIFY.md` at its root. Read it first. If there is none, write one the first time
you verify there, under 40 lines:

```
# Verify <project>
Start:   <one command>, ready when <sign>
Look:    <where to look, which view, which size; phone too if it has one>
Quick:   <the compile or build that must pass, under a minute>

## Breakages (trigger -> check, date)
- touches <file or area> -> <what to open and look at> (<date>: <what broke>)
```

Run only the breakage lines whose trigger matches your diff, usually a handful. Nobody has to remember old
breakages; the file does.

## 3. Three tries, then stop

If a check or a fix still fails after three rounds, stop and tell the user in a few lines: what fails, what
you tried, what evidence would settle it. Do not keep circling.

## 4. Never bend the check to pass

- Do not edit a test, threshold, screenshot baseline or VERIFY.md line in the same change it is judging to
  make that change pass. If the check is wrong, say so and fix it as its own step.
- Flaky check: no sleeps, retries or skips. Find the cause, or delete it and write down why.
- The quick compile or build runs before the look, not instead of it.

## 5. Turn misses into lines

When the user finds a bug that verify missed, add one breakage line to that project's VERIFY.md: a trigger you
can read from a diff, the check that would have caught it, the date. When a line no longer applies, delete
it. Short beats complete.

## 6. Two cheap tests worth writing (only where they fit)

Don't build a test suite for its own sake. These two earn their place because one small test catches what
the agent would miss, since its inputs don't come from the agent's own assumptions:

- **Old vs new** (rewrites, ports, migrations): run both on the same inputs or the same view and compare the
  outputs or screenshots. Every difference is either listed as intended or is a bug.
- **Round-trip and never-breaks** (parsers, save and load, sync, money): save then load gives the same thing;
  a total never goes negative. Throw many random inputs at it in one short run (fast-check, Hypothesis,
  proptest).

## 7. Reviewing

One reviewer, improved over rounds, beats stacking more reviewers. If the project keeps a REVIEW.md, the
reviewer reads it from the main branch, never from the change under review.

## Report

Three to five lines: what changed, how you verified it (what you ran, what you looked at), the picture, and
anything you did not verify, said plainly.
