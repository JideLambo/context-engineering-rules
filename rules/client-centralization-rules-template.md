# Client Centralization Rules (Agnostic Template)

> Copy this into your project as `RULES.md`, `.cursor/rules/client-centralization.md`, or `docs/architecture/client-centralization.md`.

> Use this as a strict architecture guardrail for small teams, solo builders, and small-team workflows.

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
