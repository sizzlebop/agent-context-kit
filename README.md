# Agent Context Kit

A small system for giving AI coding agents the project context they cannot get from reading the code alone.

Code can tell an agent what exists. It usually cannot tell it why something was built that way, what already failed, which alternatives were rejected, or how you want the agent to behave while working in the repository.

That is what this kit is for.

It is four Markdown files with separate jobs, plus two skills that teach an agent how to actually use them. Copy the parts that make sense for your workflow, change whatever does not, and ignore the rest.

This is not an AI memory service, vector database, RAG system, or autonomous memory framework. It is just a lightweight repository convention for keeping useful project context in plain Markdown.

## The problem

An agent starts a session knowing nothing about your project except what it can read right now.

That is usually fine for the code itself. The code is there.

The problem is everything around it.

An agent cannot automatically know that the ordering in your save path is deliberate, so it may "clean up" the exact thing protecting user data. It may suggest the same library you already tested and rejected. It may spend an afternoon debugging a problem somebody already solved three months ago because the fix lived in a chat, an issue comment, or somebody's memory.

The usual fix is to keep adding more instructions.

That works until the instruction file becomes a giant wall of text that gets ignored, skimmed, or loaded into context every session whether it is relevant or not.

So I split the jobs up.

* Behavior rules live in one file.
* Current project truth lives in another.
* Important decisions have their own history.
* Painful debugging lessons have theirs.
* Detailed instructions for using all of this live in skills that only load when they are needed.

The basic idea is simple:

**Rules -> current state -> decisions -> hard-earned failures.**

Then:

**Read before changing. Write only after learning something worth preserving.**

## What is in here

| Path                      | What it is                                                                                                                                                          |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `AGENTS_template.md`      | A reusable template for how an agent should behave in your repository. Copy it, rename it to `AGENTS.md`, and edit the sections marked for customizing.             |
| `skills/project-context/` | Teaches an agent when to read the context files, how to use them, and what actually deserves to be written back into them.                                          |
| `skills/clear-writing/`   | Teaches an agent to write clearly without flattening everything into generic AI-sounding prose.                                                                     |
| `examples/`               | Filled-in `OVERVIEW.md`, `MEMORY.md`, and `ERRORS.md` files from a fictional project, so you can see what the system looks like after it has been used for a while. |

### The four context files

| File          | Question it answers                                  | Tense       |
| ------------- | ---------------------------------------------------- | ----------- |
| `AGENTS.md`   | How should an agent behave while working here?       | Present     |
| `OVERVIEW.md` | How does this project work right now?                | Present     |
| `MEMORY.md`   | Why is it built this way, and what did we turn down? | Past, dated |
| `ERRORS.md`   | What already failed, and what worked instead?        | Past, dated |

`AGENTS.md` stays at the repository root. The other three go in `/DOCS`.

Keeping them separate matters more than it might seem.

`OVERVIEW.md` describes the project as it exists now, so it gets edited in place when something changes.

`MEMORY.md` and `ERRORS.md` are historical. They record decisions and lessons that already happened.

If you mix both kinds of information into one giant file, it gets messy fast. You end up with old architecture mixed with current architecture, fixed bugs mixed with active problems, and decisions buried between random implementation notes.

## Why not put everything in `AGENTS.md`?

You can. I did not find that it worked very well for me.

`AGENTS.md` is usually loaded every session, so every extra rule competes with the context the agent actually needs for the task.

It also mixes together things that behave very differently.

Behavior rules should stay fairly stable. Project architecture changes. Decisions accumulate over time. Debugging history grows in a completely different way from all of them.

Splitting those concerns lets each file stay focused.

It also gives the agent a better way to retrieve context. Instead of loading one enormous instruction file every time, it can read the project map, grep for a relevant decision, or check whether a similar bug already happened.

The skills carry the detailed workflow so `AGENTS.md` does not have to explain every rule every time.

## The skills

`AGENTS.md` loads into context every session, so I try to keep it focused.

Skills only need to load when the work calls for them, which makes them a much better place for detailed procedures.

### `project-context`

This is the part that ties the system together.

It covers both the read side and the write side:

* when to read each file
* how to grep a large file instead of dumping the entire thing into context
* what to do when documentation and code disagree
* what to do when a task asks for something `MEMORY.md` already rejected
* what actually qualifies for a new entry
* what should be left out

Most of the skill is intentionally about what **not** to write.

These files stop being useful when:

* `MEMORY.md` turns into a changelog
* `ERRORS.md` turns into a bug tracker
* `OVERVIEW.md` turns into a history lesson
* routine implementation gets logged just because something changed

The goal is not to record everything.

The goal is to preserve the things an agent would otherwise have to rediscover.

### `clear-writing`

This skill came from a separate problem I kept running into: documentation can be technically correct and still be annoying to read.

It combines two approaches that mostly agree, but not completely.

ASD-STE100 Simplified Technical English is useful for instructional writing because setup steps, runbooks, errors, and reference docs should be extremely hard to misread.

Voiced prose has a different job. A README opening, release announcement, or blog post should not sound like a maintenance manual.

So the skill classifies the passage first and uses different rules depending on what kind of writing it is.

A README often needs both.

The intro can sound like a human wrote it. The install steps should be boringly clear.

## How it works in a session

### Before changing code

The agent reads what it needs.

`OVERVIEW.md` gives it the map.

`MEMORY.md` tells it whether there are existing decisions around the thing it is about to change.

`ERRORS.md` tells it whether somebody already tried something similar and got burned.

On a mature project, those files can get long. The `project-context` skill teaches the agent to inspect headings and search for the relevant part instead of loading everything.

### After the work is done

The agent writes back only if the task changed something worth preserving.

A simple check:

* Did anything in `OVERVIEW.md` become untrue? Fix it.
* Was there a real decision with an alternative you rejected? Log it in `MEMORY.md`.
* Did something fail badly enough that somebody could waste serious time on it again? Log it in `ERRORS.md`.

If the answer is no, write nothing.

That part matters.

A short file people actually read is more useful than a perfect historical record nobody wants to open.

## Getting started

1. Copy `AGENTS_template.md` into your repository root and rename it to `AGENTS.md`.
2. Work through it and replace the sections marked for customizing. Design preferences, writing style, branch strategy, release workflow, and similar rules are project-specific.
3. Copy `skills/project-context/` and `skills/clear-writing/` into your agent's skills directory.
4. Create a `/DOCS` directory.
5. Copy the three templates from `skills/project-context/templates/` into it.
6. Fill in `OVERVIEW.md` from what the code actually shows.
7. Leave a `TODO` anywhere you would otherwise have to guess.
8. Leave `MEMORY.md` and `ERRORS.md` empty apart from their headers.

For Claude Code, project skills can live in `.claude/skills/`, or in `~/.claude/skills/` if you want them available globally.

Other agents that support the `SKILL.md` format have their own skill locations.

That last step is deliberate.

Do not backfill `MEMORY.md` with reconstructed decisions just because the current code makes the old reasoning seem obvious. Reconstructed history tends to sound much more certain than it really is.

If you remember why something was done, write down what you actually remember.

If you do not know, leave it alone.

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

The exact structure is flexible.

You may not need every file. You may want different names. You may already have documentation that covers part of this.

The useful part is the separation of responsibilities, not copying my folder layout exactly.

## About the examples

The files under `examples/` use a fictional project called **Lantern**, a self-hosted reading-list app.

I wanted the examples to look like real project documentation without publishing context from one of my actual applications.

They show what `OVERVIEW.md`, `MEMORY.md`, and `ERRORS.md` can look like after a project has been around for a while.

`skills/project-context/references/examples.md` also uses them to show good entries next to bad ones, which is probably the fastest way to understand what belongs in each file.

## Use whatever helps

This is not meant to be a rigid framework.

It is just the system I ended up with after getting tired of coding agents forgetting decisions, retrying failed ideas, over-documenting everything, and occasionally "improving" something I very intentionally did not want improved.

It works well for me, so I figured it might be useful to somebody else too.

Take the whole thing, take one file, rewrite all of it, or steal the parts you like and build something better.

## License

Apache 2.0. See `LICENSE`.
