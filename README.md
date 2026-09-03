# Agent Context Kit

A starting point for giving AI coding agents the project context they cannot get from reading the code.

It is four Markdown files with separate jobs, plus two skills that tell an agent how to actually use them. Copy what you need into your own repository and adjust it.

## The problem

An agent starts every session knowing nothing about your project except what it can read right now. That is fine for the code, which is right there. It is a problem for everything else.

It cannot tell that the ordering in your save path is deliberate, so it "cleans up" the one thing protecting user data. It re-proposes the library you already evaluated and rejected. It spends an afternoon on a bug someone else solved in March, because the fix lived in a Slack thread and nowhere else.

The usual answer is to write more instructions. That works until the instruction file gets long enough that agents skim it. So this splits the job up: behavior rules in one file, project knowledge in three others, and the detailed how-to in skills that load only when they are needed.

## What is in here

| Path | What it is |
|---|---|
| `AGENTS_template.md` | A reusable template for how an agent should behave in your repository. Copy it, rename it to `AGENTS.md`, and edit the sections marked for customizing. |
| `skills/project-context/` | Teaches an agent when to read your context files and what actually earns an entry in them. |
| `skills/clear-writing/` | Teaches an agent to write clearly and to strip the patterns that make text read as machine-generated. |
| `examples/` | Filled-in `OVERVIEW.md`, `MEMORY.md`, and `ERRORS.md` from a fictional project, so you can see the shape before you write your own. |

### The four context files

| File | Question it answers | Tense |
|---|---|---|
| `AGENTS.md` | How should an agent behave while working here? | Present |
| `OVERVIEW.md` | How does this project work right now? | Present |
| `MEMORY.md` | Why is it built this way, and what did we turn down? | Past, dated |
| `ERRORS.md` | What already failed, and what worked instead? | Past, dated |

`AGENTS.md` sits at the repository root. The other three go in `/DOCS`.

Keeping them separate matters more than it looks. `OVERVIEW.md` describes the present, so it gets edited in place when something changes. `MEMORY.md` and `ERRORS.md` are append-only logs of things that already happened. Mixing those two behaviors in one file is how documentation turns into an unreadable pile of half-corrected history.

## The skills

`AGENTS.md` loads into context every single session, so it has to stay short. Skills load only when the work calls for them, so they can carry the detail that would bloat an always-on file.

**`project-context`** covers the read side and the write side. When to read which file, how to grep a large one instead of dumping it into context, what to do when a file contradicts the code, and what to do when a task asks for something `MEMORY.md` already rejected.

Most of it is about what *not* to write. These files only work if they stay short enough that people trust them, and the four ways they rot are predictable: `MEMORY.md` turning into a changelog, `ERRORS.md` turning into a bug tracker, `OVERVIEW.md` turning into a history, and logging routine implementation that had no decision in it.

**`clear-writing`** is a merge of two writing standards that mostly agree and disagree about exactly one thing. ASD-STE100 Simplified Technical English gives you hard sentence limits and one word per concept, which is right for a runbook step someone reads at 2am. An AI-pattern audit tells you that uniform rhythm is the strongest signal that a machine wrote something, which is right for a README opening.

Both are correct for their own kind of text, so the skill classifies the passage first and routes to the matching ruleset. A README needs both: the intro is voiced prose, the install steps are instructional.

## How it works in a session

**Before changing code**, the agent reads. `OVERVIEW.md` for the map, `MEMORY.md` for decisions covering the area, `ERRORS.md` for approaches that already failed. On a mature project these files get long, so the skill teaches grepping headings first rather than reading everything.

**After the work is done and verified**, the agent writes, but only if the work cleared the bar:

- Did anything in `OVERVIEW.md` become untrue? Fix that sentence.
- Was there a real fork in the road with an option you rejected? Log it in `MEMORY.md`.
- Did something take more than two attempts, or have a cause nowhere near the symptom? Log it in `ERRORS.md`.

If the answer is no, nothing gets written. A short file people read beats a long file people skip.

## Getting started

1. Copy `AGENTS_template.md` into your repository root and rename it to `AGENTS.md`.
2. Work through it and replace the sections marked for customizing. Design preferences, writing style, branch strategy, and release workflow are all project-specific and the template only holds placeholders.
3. Copy `skills/project-context/` and `skills/clear-writing/` into your agent's skills directory. For Claude Code that is `.claude/skills/` in the project, or `~/.claude/skills/` to make them available everywhere. Other agents that read the `SKILL.md` format have their own location.
4. Create a `/DOCS` directory.
5. Copy the three templates from `skills/project-context/templates/` into it.
6. Fill in `OVERVIEW.md` from what the code actually shows. Leave a `TODO` marker anywhere you would have to guess.
7. Leave `MEMORY.md` and `ERRORS.md` empty apart from their headers.

That last step is deliberate. Do not backfill decisions you reconstructed after the fact, because reconstructed history reads confident and is usually wrong. If you want past decisions captured, write down what you actually remember, not what seems likely.

## Suggested repository layout

```text
your-project/
├── AGENTS.md
├── README.md
├── CHANGELOG.md
├── .claude/
│   └── skills/
│       ├── project-context/
│       └── clear-writing/
└── DOCS/
    ├── OVERVIEW.md
    ├── MEMORY.md
    ├── ERRORS.md
    └── ROADMAP.md
```

The exact structure is flexible. The important part is giving each kind of context a clear job instead of putting everything into one enormous prompt.

## About the examples

The files under `examples/` use a fictional project called **Lantern**, a self-hosted reading-list app, to show what the supporting documents look like once a project has been in development for a while.

They are specific enough to feel real, but none of the project details come from an actual application. `skills/project-context/references/examples.md` pulls from them again to show bad entries next to good ones, which is the faster way to learn the format.

## License

Apache 2.0. See `LICENSE`.
