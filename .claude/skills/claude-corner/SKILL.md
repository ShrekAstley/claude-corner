---
name: claude-corner
description: Use ONLY while a long, time-based task is running and Claude is idle, just waiting (a slow build, test suite, install, deploy, CI run, data job, or a background subagent expected to take ~5+ minutes). Lets Claude spend that dead time creating small stories, projects, notes or ideas of its own in the `corner/` folder, cheaply, using Haiku or Sonnet. Never use when Claude has real work to do, when the wait is short, or when the user has not given a long task.
---

# Claude Corner

A tiny creative space in `corner/` that Claude may use **only during idle waiting time**. It is a hobby, not a task: it must never delay, distract from, or add real cost to the user's work.

## When to use (all must be true)

1. A long task is in progress (expected wait ~5+ minutes: build, test run, install, deploy, CI, long script, background agent).
2. Claude has **nothing else useful to do** for the user right now, only waiting.
3. The wait is already running in the background (e.g. `run_in_background`), so the user's task is not blocked by the corner.

If any is false, do not use this skill. Never start a long task just to have time for the corner. Never use it for short waits or when the user is actively chatting.

## Where

Everything goes inside `corner/` and nowhere else. Never read, edit, or delete files outside it for corner purposes.

```
corner/
  INDEX.md        one line per piece: date, kind, title, path
  stories/        short fiction (<= ~400 words)
  projects/       small self-contained things (a tiny script, game idea, poem set, mini-spec) in their own subfolder
  notes/          thoughts, observations, ideas, small essays (<= ~200 words)
```

Create `INDEX.md` if missing. Name files `YYYY-MM-DD-short-slug.md`.

## Cost rules (strict)

- **Delegate the writing to a cheap subagent** via the Agent tool; do not write it in the main (more expensive) context.
  - Small piece (note, short poem, tiny idea, index update): `model: "haiku"`.
  - Larger piece (story, small project): `model: "sonnet"`.
  - Never use Opus or a larger model for the corner.
- Run the subagent in the background, with a prompt that names the exact output path and the length cap. Tell it not to read the repo or use other tools beyond writing its file.
- **One piece per wait.** At most **2 pieces per session**, and at most 3 files per piece. If `corner/INDEX.md` already shows 2 pieces today for this session, stop.
- Keep prompts short (a few lines). No research, no web, no big file reads, no long chains of follow-ups.
- Do not poll or loop on the corner. Check on the long task as you normally would; the corner never schedules its own wake-ups.

## Workflow

1. Confirm the three conditions above. Start the long task first.
2. Pick something small and fun: a story, a mini-project, a note. Prefer continuing an unfinished piece listed in `INDEX.md` over starting a new one, if it is quick to extend.
3. Spawn the Haiku/Sonnet subagent in the background to write it to `corner/...`.
4. Append one line to `corner/INDEX.md` when it finishes.
5. **The moment the long task completes, drop the corner** and return to the user's work. Do not finish a piece at the user's expense; leave it marked `(draft)` in the index.

## Guardrails

- The user's task always has priority; corner output never appears in the task's results, commits, or PRs unless the user asks.
- Do not commit or push corner files unless the user asks.
- Content should be harmless, original, and free of secrets, private data, or anything from the user's code or conversation.
- Mention it to the user in at most one short line at the end (e.g. "While waiting, I wrote a short story in `corner/stories/`"). Don't summarize it at length.
