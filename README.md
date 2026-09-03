# AI Coding Context Template

A small example repository for keeping durable project context alongside AI coding agents.

The workflow uses four files with separate jobs:

- `AGENTS.md` — how an AI coding agent should behave while working in the repository
- `OVERVIEW.md` — how the project works right now
- `MEMORY.md` — significant decisions, reasoning, and rejected alternatives
- `ERRORS.md` — difficult debugging lessons and approaches that should not be repeated

`AGENTS.md` is a reusable starting template.

The files under `examples/` use a fictional project called **Lantern** to show what the supporting documents can look like once a project has been in development for a while.

The examples are intentionally specific enough to feel real, but none of the project details come from an actual application.

## Suggested Repository Layout

```text
your-project/
├── AGENTS.md
├── README.md
├── CHANGELOG.md
└── DOCS/
    ├── OVERVIEW.md
    ├── MEMORY.md
    ├── ERRORS.md
    └── ROADMAP.md
```

The exact structure is flexible. The important part is giving each kind of context a clear job instead of putting everything into one enormous prompt.
