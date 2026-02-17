# Spec & Sprint Standards (Agnostic Template)

> Copy this into your project as `.cursor/rules/spec-and-sprint.md` or `docs/process/spec-and-sprint.md`.

> Use this to force clear planning, scoped implementation, and test-backed delivery.

---

## Spec & Sprint Standards

### Skills and Rule Dependencies (Portable Setup)

Use capability categories, then map them to whatever tools/skills your team has.

**Required capability categories during implementation:**

- **UI/UX Design Capability** — Used when implementing UI from a spec.
- **User Copy/Psychology Capability** — Used when writing user-facing copy, onboarding, and empty states.
- **Performance Capability** — Used for React/Next.js performance and rendering decisions.
- **Architecture Boundaries Rule** — Used to enforce centralized clients, internal interfaces, and no direct SDK usage in feature/UI code.

**Project mapping example (replace with your own):**

```md
UI/UX Design Capability -> /frontend-design
User Copy/Psychology Capability -> /marketing-psychology
Performance Capability -> /vercel-react-best-practices
Architecture Boundaries Rule -> client-centralization.md
```

If a named skill is unavailable, you must still apply the capability category using your team's documented standards.

### When to Write a Spec

Write a spec before implementing any feature that:

- Spans more than 3 files
- Involves product decisions (what should the user see/experience?)
- Has multiple valid approaches
- Will take more than one working session

### Spec Document Structure

Every spec goes in `docs/specs/` and follows this structure:

```markdown
# Spec: {Feature Name}

**Status:** Draft | In Review | Approved | In Progress | Complete
**Owner:** {who makes the final call}
**Spec date:** {date}
**Target milestone:** {reference to implementation plan milestone}

## Problem

One paragraph. What's broken or missing? Who feels the pain?

## User Experience

### What does the user see first?
Describe the entry point. What screen, what state, what triggers this feature.

### Happy path
Step-by-step walkthrough of the ideal flow. Use numbered steps.
Include UI mockups (ASCII art is fine) for any new screens.

### Edge cases
Table format:

| Scenario | Behavior |
|----------|----------|
| {edge case} | {what happens} |

### Error states
What can go wrong? What does the user see when it does?

## Design Direction

### Visual and interaction approach
Describe layout and interaction intent. Apply your UI/UX Design Capability.
Include:
- Layout approach (floating, anchored, inline, panel, etc.)
- Key interaction patterns (hover, click, expand, dismiss, etc.)
- Animation/motion intent (fade in, slide, staggered reveal, etc.)
- Empty states and zero-state design

### Psychology and copy approach
Describe mental models and tone for user-facing text. Apply your User Copy/Psychology Capability.
Include:
- Key principles (for example: friction reduction, progress visibility, motivation loops)
- Tone (helpful assistant, neutral system, expert guide, etc.)
- Copy patterns to follow or avoid

## Technical Approach

### Data model
New types, schema changes, and contracts. Include TypeScript/SQL when needed.

### Component architecture
New components, hooks, utilities, and how they connect.

### Key implementation details
Architecture decisions, performance considerations, and constraints.
Apply your Performance Capability and Architecture Boundaries Rule.

## Scope

### MVP (this slice/sprint)
Bulleted list of what ships. "Working" must mean demo-able end-to-end.

### Deferred (later milestone)
Bulleted list of what is explicitly cut. Include why deferred.

### Out of scope
Related items that are not part of this feature.

## Open Decisions

Numbered list of questions that need a call before building.
Format: 1. **{Question}** — {context and tradeoffs}. Options: A) ... B) ...

Mark resolved decisions inline: 1. {Question} — **Decision:** {what was decided}

## Sprint Breakdown

(See sprint format below)
```

### Sprint Breakdown Format

Break every spec into vertical slices. Each slice is a self-contained unit of work that leaves the product in a working state.

```markdown
## Sprint Breakdown

### Slice 1: {Name} — {one-line description}
**Delivers:** {what the user can do after this ships}
**Files:**
- path/to/file.ts — {what changes}
- path/to/file.tsx — {what changes}
**Tasks:**
- [ ] {specific task}
- [ ] {specific task}
**Depends on:** None | Slice N

### Slice 2: {Name} — {one-line description}
...
```

**Slice rules:**

- Each slice must be demo-able on its own (no "infrastructure only" slices)
- Order slices by dependency, then value (highest value first)
- A slice should be completable in 1-2 working sessions
- The first slice should be the smallest end-to-end proof
- List actual file paths to force implementation clarity

### Decisions Log

Maintain a running decisions log at `docs/decisions-log.md`.

Format:

```markdown
# Decisions Log

## {Date} — {Topic}
**Context:** {why this decision came up}
**Options considered:** {A, B, C}
**Decision:** {what was decided}
**Rationale:** {why}
**Impact:** {what this changes in the spec/plan}
```

Every decision recorded here should also be reflected in the relevant spec.

### Spec Review Checklist

Before starting implementation, verify:

- [ ] Every new UI element has a mockup (ASCII art minimum)
- [ ] Happy path is walkable step-by-step
- [ ] Edge cases table is filled out
- [ ] MVP scope is explicit (not "we'll figure it out")
- [ ] Deferred items include a reason
- [ ] Open decisions are resolved or flagged as blockers
- [ ] Sprint slices are ordered and each is demo-able
- [ ] File paths in slices reference actual codebase locations
- [ ] Data model changes are specified (types/SQL/contracts)
- [ ] Slice dependencies are explicit
- [ ] UI/UX Design Capability applied for UI slices
- [ ] User Copy/Psychology Capability applied for user-facing text
- [ ] Performance Capability applied for React/Next.js code
- [ ] Architecture Boundaries Rule applied (no direct SDK imports in feature/UI code)

---

### Slice Implementation Protocol

Follow this protocol when implementing slices from a spec.

#### Phase 1: Extract Requirements (Before Writing Code)

1. Read the slice tasks and "Delivers" statement from the spec.
2. List each requirement as a checklist item.
3. Verify exact file paths from the spec; avoid keyword-based guessing.
4. Identify dependencies from prior slices.

#### Phase 2: Implement with Verification

For each spec file path:

1. Open the exact path from the spec.
2. Implement the stated change.
3. Cross-reference implementation against spec wording.

**Common mistakes to avoid:**

- Searching by keyword instead of using exact spec paths
- Implementing only part of a requirement
- Modifying a similar-but-wrong file
- Importing SDKs directly in feature/UI code instead of using centralized boundaries

#### Phase 3: Write Behavioral Tests

Tests must verify behavior, not placeholders.

```ts
// Wrong: placeholder
it("does something", () => {
  expect(true).toBe(true);
});

// Right: behavior assertion
it("returns stable selector for element with id", () => {
  const element = document.createElement("button");
  element.id = "submit-btn";
  document.body.appendChild(element);

  const path = generateDomPath(element);
  const found = document.querySelector(path);

  expect(found).toBe(element);
});
```

#### Phase 4: Verification Walkthrough (Before Marking Complete)

Go back to the spec and verify each deliverable.

```markdown
## Slice Verification

| Spec Requirement | Implementation | Verified |
|------------------|----------------|----------|
| "User can enter comment mode" | CommentToggle.tsx sets mode in context | Y |
| "Elements highlight on hover" | ElementHighlighter.tsx adds hover delegation | Y |
| "Click anchors comment input" | CommentAnchor.tsx positions input near target | Y |
```

Only mark a slice complete when every row is verified.

#### Common Failure Modes

| Failure | How to Prevent |
|---------|----------------|
| Wrong file modified | Use spec's exact path |
| Partial implementation | Extract all requirements before coding |
| Placeholder tests | Assert actual behavior with realistic setup |
| Style leakage | Keep styling scoped and namespaced |
| Performance regression | Apply performance capability checks |
| Direct SDK import in feature code | Apply architecture boundaries rule |

