# Context Engineering Rules

Practical architecture rules and guardrails for AI-assisted coding. Copy-paste templates for Cursor, Windsurf, and AI coding workflows.

---

## What is this?

When you use AI coding assistants (Cursor, Windsurf, Copilot, etc.), the **rules and context you give them** matter more than the prompts you type. This repo is a collection of copy-paste rule templates that keep AI-generated code clean, consistent, and maintainable.

Each template is a standalone markdown file you can drop into your project as a `.cursor/rules/` file, a `RULES.md`, or reference in your docs.

## Rules

| Template | What it covers |
|----------|---------------|
| [Client Centralization](rules/client-centralization-rules-template.md) | Centralize SDK clients, enforce dependency injection, keep UI code backend-agnostic. Covers database clients, AI enrichment, shared types, constants, logging, testing, and security. |
| [Spec & Sprint Standards](rules/spec-and-sprint-standards-template.md) | Force clear planning before building. Spec structure, sprint slice format, implementation protocol, behavioral test standards, and a verification walkthrough. |

## How to Use

**Option 1: Copy-paste**
1. Open any template above
2. Click the "Raw" button or copy the markdown
3. Paste into your project as `.cursor/rules/` or `RULES.md`

**Option 2: Download**
1. Click the template link above
2. Click "Raw", then right-click and "Save As"

**Option 3: Reference**
1. Link to the template from your project docs or PR templates

## Who is this for?

- Solo builders and indie hackers using AI coding tools
- Small teams who want guardrails without overhead
- Anyone who wants AI-generated code to follow consistent architecture patterns

## Coming Soon

- More rule templates (error handling, API design, state management, etc.)
- Tips and patterns for effective context engineering
- Framework-specific variants (Next.js, SvelteKit, etc.)

## Contributing

Have a rule template that works well for your team? PRs are welcome. Each template should be a standalone markdown file in the `rules/` or `tips/` directory.
