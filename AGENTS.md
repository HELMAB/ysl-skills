# Contributing to ysl-skills

## Structure

Each skill lives in `skills/{skill-name}/` with:

- `SKILL.md` — Entry point loaded by Claude. Contains tech stack, architecture overview, workflow steps, and references to detailed docs.
- `references/` — Detailed documentation files referenced from SKILL.md via `@references/{file}.md`.

## Writing Skills

### SKILL.md

Keep it concise and actionable:

1. **One-line context** — What the agent is working on
2. **Tech stack** — Key versions and libraries
3. **Architecture overview** — Directory structure with brief annotations
4. **Workflow** — Numbered steps for common tasks (building features, fixing bugs)
5. **References** — Links to detailed reference files

### Reference Files

Detailed patterns with real code examples:

- Show **actual code** from the codebase, not generic examples
- Include **file paths** so the agent knows where things live
- Use **tables** for quick-reference lookups (conventions, constants, etc.)
- Keep each file focused on **one concern**

## Adding a New Skill

1. Create `skills/{name}/SKILL.md`
2. Create `skills/{name}/references/` with detailed docs
3. Register in `.claude-plugin/marketplace.json`
4. Update README.md

## Conventions

- Use real code snippets from the Youngsia codebase
- Keep SKILL.md under 100 lines — detail goes in references
- Reference files should be self-contained (readable without SKILL.md context)
- Use consistent heading levels: `#` for title, `##` for sections, `###` for subsections
