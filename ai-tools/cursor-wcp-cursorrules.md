# Cursor rules for WCP (TypeScript)

This page provides a single, copy-paste block you can drop into your workspace’s `.cursorrules` so Cursor can reliably generate TypeScript code aligned with WCP’s concepts and your OpenAPI spec.

## How to use in Cursor

1. In the root of your project, create a file named `.cursorrules`.
2. Copy the entire block below and paste it into `.cursorrules`.
3. Save the file. In Cursor, run your prompt (Command-K) and reference what you want to build.
4. If rules don’t seem to apply immediately, reload the window.

## Copy this into `.cursorrules`

```text
Project: WCP
Tech stack: TypeScript (strict) + Node/Next.js (App Router by default)
Local docs to consult (read before coding):
- concepts/: actions.mdx, effects.mdx, regions.mdx
- api-reference/openapi.json (authoritative API surface)
- use-cases/

Primary objective: Generate correct, minimal, maintainable TypeScript aligned to WCP concepts and the OpenAPI contract.

Behavioral rules
- Always consult api-reference/openapi.json to derive request/response shapes and status codes before writing API-related code.
- Prefer small, composable modules with explicit names and strict types. No unused code, no speculative abstractions.
- Keep dependencies minimal. Avoid heavy clients; prefer typed fetch helpers. Only introduce a new dependency if essential.
- Default to Next.js App Router conventions when building server routes; if not a Next.js app, produce portable Node code.
- Validate inputs at boundaries. When uncertain, generate Zod schemas from OpenAPI and validate request bodies and responses.
- Provide narrow error surfaces: map server errors to typed results; don’t throw for expected error states.

TypeScript & style
- Use strict types, no any. Derive types from OpenAPI where possible.
- Name functions with clear verbs; name variables with descriptive nouns.
- Use early returns, avoid deep nesting. Keep files short and focused.
- Document non-obvious choices with concise comments (no noise).

API client generation (from OpenAPI)
- Create a light typed helper around fetch. Infer or define Request/Response types per endpoint using OpenAPI.
- Support base URL and auth headers in a single place.
- Example template (adapt to project):

  // typed-fetch.ts
  export type ApiResult<T> = { ok: true; data: T } | { ok: false; status: number; message: string };

  export async function typedFetch<T>(input: RequestInfo, init?: RequestInit): Promise<ApiResult<T>> {
    const res = await fetch(input, init);
    if (!res.ok) {
      return { ok: false, status: res.status, message: await safeText(res) };
    }
    return { ok: true, data: (await res.json()) as T };
  }

  async function safeText(res: Response): Promise<string> {
    try { return await res.text(); } catch { return ""; }
  }

- When generating endpoint-specific functions, create `getX`, `createX`, `updateX`, `deleteX` with precise types sourced from OpenAPI.
- If a response has multiple shapes by status, model a discriminated union and handle each case.

Server endpoints & middleware
- Next.js App Router: use `app/api/<resource>/route.ts` with `export async function GET/POST/...`.
- Validate request with Zod (generated or hand-written from OpenAPI); return typed JSON with accurate status codes.
- Map known error states to specific codes; avoid exposing internal errors.

WCP primitives (consult local docs first)
- Actions: see concepts/actions.mdx
  Template:
  export type Action<P = unknown> = { type: string; payload: P };
  export function createAction<P>(type: string) { return (payload: P): Action<P> => ({ type, payload }); }

- Effects: see concepts/effects.mdx
  Template intent: subscribe to actions, perform async work, dispatch follow-ups, handle errors explicitly.

- Regions: see concepts/regions.mdx
  Template intent: well-typed state containers; updates occur only via actions/effects; keep selectors pure.

Common playbooks (do these step-by-step)
1) Add a new endpoint based on OpenAPI
   - Read api-reference/openapi.json; find path and method.
   - Define Request/Response types for the operation from the spec.
   - Implement a typed client function using typedFetch<T>.
   - If server route is needed: create route handler (Next.js or Node), validate input, map errors, return typed JSON.

2) Generate CRUD for resource X
   - Identify list/get/create/update/delete operations in OpenAPI.
   - Create typed client functions for each operation.
   - Add minimal tests that assert correct URLs, methods, headers, and response typing.

3) Wire an effect to an action and update a region
   - Create an action like createAction<Payload>("resource/fetchRequested").
   - Implement an effect that calls the correct client function, dispatches success/failure actions.
   - Update the region state in response to success/failure; keep selectors pure and typed.

Testing
- Use Vitest or Jest. Mock fetch; assert request method/URL/headers/body and typed responses.
- For union responses, test at least one success and one failure shape.

Non-goals & constraints
- No dead code, no unused exports, no large frameworks for simple tasks.
- Prefer clarity over cleverness. Keep diffs small and purposeful.
```

## Notes and references

- WCP concepts:
  - `concepts/actions.mdx`
  - `concepts/effects.mdx`
  - `concepts/regions.mdx`
- API contract: `api-reference/openapi.json`
- Use cases: `use-cases/`

You can tailor the rules to your project conventions (e.g., Node-only services, different testing frameworks). The above defaults are safe for most TypeScript + Next.js setups.
