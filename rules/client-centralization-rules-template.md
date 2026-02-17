# Client Centralization Rules (Agnostic Template)

> Copy this into your project as `RULES.md`, `.cursor/rules/client-centralization.md`, or `docs/architecture/client-centralization.md`.

> Use this as a strict architecture guardrail for small teams, solo builders, and "vibe-coding" workflows.

---

## Client Centralization

All external clients, shared types, and utility functions must be centralized.
Never import SDKs directly in UI components or hooks.
Always go through your project's `lib/` layer and internal interfaces.

### Database Client (Supabase or Any Backend SDK)

- Always import the database client from one local file, such as `src/lib/db-client.ts`.
- Never import vendor SDK packages directly outside this client file.
- For type imports, import project-level types from your internal `lib` or `types` paths, not directly from vendor packages.
- Use dependency injection: functions that need database access receive a typed client or gateway as a parameter.
- Only data-access adapters/repositories call the client directly; app code uses interfaces.

```ts
// Correct: hook reads from internal interface
const { dataGateway } = useAppContext();
const comments = await dataGateway.getComments(projectId, urlPath);

// Wrong: hook imports SDK directly
import { createClient } from "@supabase/supabase-js";
const supabase = createClient(url, key);
const { data } = await supabase.from("comments").select("*");
```

### Data Access Interface

- All reads/writes go through an internal interface (for example: `DataGateway`, `CommentsRepository`, or `BackendPort`).
- Components and hooks must remain backend-agnostic.
- Adapter/repository is provided through app context or dependency injection.
- Never call a backend SDK directly from UI code.

### AI Enrichment

- AI usage is provider-agnostic from the app's point of view.
- Package/app source should not import LLM SDKs in UI/domain layers.
- Call an internal API endpoint (for example, `/api/ai/enrich`) that returns a typed response.
- Keep provider-specific implementations isolated in server routes, examples, or adapters.
- Prompt builders generate strings only; they do not call APIs.

### Shared Types

- Put shared contracts in `src/types/`.
- Centralize request/response types for API, AI, and domain entities.
- Import from internal type modules; do not redefine shapes inline in components.

### Constants

- Put all defaults and magic numbers in `src/lib/constants.ts`.
- Never hardcode debounce timings, animation durations, z-index values, or polling intervals inside components.

```ts
// Correct
import { UI_Z_INDEX, ANIMATION_MS, DEBOUNCE_MS } from "@/lib/constants";

// Wrong
style={{ zIndex: 999999 }}
```

### DOM Utilities

- Centralize DOM traversal, selector generation, and style inspection in `src/utils/`.
- UI components should call utility helpers instead of running ad-hoc traversal logic inline.

### Styling

- Use scoped component styles only.
- Avoid generic global class names that can collide with host apps.
- For theme/customization values, use namespaced CSS variables (for example `--app-*`).

### Logging

- Use a central logger module (`src/lib/logger.ts`).
- No raw `console.log` in shipped production paths.
- Allowed exceptions during development: targeted `console.warn`/`console.error` for genuine anomalies.

### Dependency Injection and Config Flow

- External dependencies (clients, endpoints, config) must flow through context or function parameters.
- Do not read `process.env` or global config deep in feature code.
- Config should be initialized once near app startup/composition root.

```ts
// Correct: config flows down through context
function useComments() {
  const { dataGateway, projectId } = useAppContext();
  return dataGateway.getComments(projectId, window.location.pathname);
}

// Wrong: feature code creates hidden dependency
function useComments() {
  const client = createClient(process.env.NEXT_PUBLIC_DB_URL!, process.env.NEXT_PUBLIC_DB_KEY!);
  return client.from("comments").select("*");
}
```

### Testing Rules

- Unit test interfaces and adapters separately.
- Mock internal interfaces in component/hook tests (not vendor SDKs directly).
- Add at least one integration test that validates adapter-to-backend contract behavior.
- Add one "boundary enforcement" test or lint rule that fails on forbidden direct SDK imports.

### Security and Reliability Rules

- Validate all external input at API boundaries.
- Enforce auth/permission checks in server routes, not in UI assumptions.
- Keep secret keys and privileged clients server-only.
- Add retries/timeouts/circuit-breaker behavior in centralized client modules, not in scattered feature code.

---

## PR Review Checklist

Before merging:

- [ ] No direct backend SDK imports outside centralized client/adapters
- [ ] UI/components/hooks use internal interfaces only
- [ ] No inline domain/API/AI type redefinitions
- [ ] No magic numbers in components
- [ ] No deep `process.env` access in feature code
- [ ] No scattered inline DOM traversal logic
- [ ] No unscoped/global style leakage
- [ ] No raw `console.log` in production code paths
- [ ] Tests cover adapter contracts and interface boundaries

---

## Quick Start (for Vibe-Coding Teams)

1. Create `src/lib/db-client.ts` and `src/lib/logger.ts`.
2. Create one internal interface (`DataGateway`) and use it everywhere in UI code.
3. Move all shared types to `src/types/`.
4. Move all defaults/magic numbers to `src/lib/constants.ts`.
5. Add a lint/test guard for direct SDK imports.
6. Add this checklist to every PR template.

> If you only do steps 1, 2, and 5, you still get most of the architectural benefit.

