---
name: convex-project
description: Initialize and standardize an opinionated Next.js + Convex + Convex Auth + shadcn project. Use when asked to set up landing/auth/dashboard app structure, Convex auth wrappers, TanStack Form with Zod, shadcn login/signup/sidebar blocks, or feature-folder Convex organization.
---

# Convex Project Initialization

Use this skill to massage an existing Next.js + Convex + Convex Auth + shadcn project into an opinionated project structure.

## Assumptions

- The project is usually already scaffolded with Convex Auth, Next.js App Router, and shadcn.
- If nothing exists, scaffold with:

```bash
npm create convex@latest -- -t nextjs-convexauth
```

- Always detect the package manager first. Do not assume npm.
  - Check for `pnpm-lock.yaml`, `yarn.lock`, `bun.lockb`, `package-lock.json`.
  - Use the detected package manager for installs and scripts.

## Important Next.js convention

For Next.js 16+, auth middleware lives in `proxy.ts`, not `middleware.ts`.

Use `proxy.ts` for Convex Auth route protection.

## Frontend structure

### Landing page

Make `/` a public landing page.

It should include:

- quick app description
- simple CTA to sign in
- optional CTA to create account

Do not protect `/` unless the user explicitly asks for a private root route.

### Auth pages

Use shadcn auth blocks:

```bash
npx shadcn@latest add login-01
npx shadcn@latest add signup-01
```

If the repo uses pnpm, prefer:

```bash
pnpm dlx shadcn@latest add login-01 signup-01
```

Rules:

- Move auth-related feature components to `features/auth`.
  - Example:

```text
features/auth/components/login-form.tsx
features/auth/components/signup-form.tsx
```

- Keep shared UI primitives in `components/ui`.
- Remove OAuth buttons from the shadcn blocks unless the user explicitly asks to keep OAuth.
- Adapt the blocks to Convex Auth password login/signup.
- Successful login/signup should redirect to the authenticated dashboard route.
- Authenticated users visiting auth pages should redirect to the dashboard route.

### TanStack Form setup

Install TanStack Form and Zod if missing:

```bash
<pkg> add @tanstack/react-form zod
```

Follow the TanStack Form large-form pattern:

- `hooks/form-context.tsx`
- `hooks/form.tsx`
- reusable field components in `components/form`

Recommended structure:

```text
hooks/form-context.tsx
hooks/form.tsx
components/form/text-field.tsx
components/form/submit-button.tsx
```

`hooks/form-context.tsx`:

```tsx
"use client";

import { createFormHookContexts } from "@tanstack/react-form";

export const { fieldContext, formContext, useFieldContext, useFormContext } =
  createFormHookContexts();
```

`hooks/form.tsx` should use `createFormHook` and register reusable field/form components. It should look like this shape:

```tsx
"use client";

import { createFormHook } from "@tanstack/react-form";
import { TextField } from "@/components/form/text-field";
import { SubmitButton } from "@/components/form/submit-button";
import { fieldContext, formContext } from "./form-context";

export const { useAppForm, withForm, withFieldGroup } = createFormHook({
  fieldContext,
  formContext,
  fieldComponents: {
    TextField,
  },
  formComponents: {
    SubmitButton,
  },
});
```

Notes:

- `form-context.tsx` owns TanStack's contexts and exports `useFieldContext` / `useFormContext`.
- `form.tsx` registers the app's reusable field and form components.
- Feature forms import `useAppForm` from `@/hooks/form`.
- Reusable field components import `useFieldContext` from `@/hooks/form-context`.
- Reusable form components, such as submit buttons, import `useFormContext` from `@/hooks/form-context`.

Use Zod schemas with TanStack Form validators, for example:

```tsx
const loginSchema = z.object({
  email: z.email("Enter a valid email"),
  password: z.string().min(1, "Required"),
});

const form = useAppForm({
  defaultValues: { email: "", password: "" },
  validators: { onSubmit: loginSchema },
  onSubmit: async ({ value }) => {
    // Convex Auth signIn
  },
});
```

### shadcn Field component

Do not hand-roll shadcn `Field` primitives. Add them via shadcn:

```bash
npx shadcn@latest add field
```

or with pnpm:

```bash
pnpm dlx shadcn@latest add field
```

Reusable TanStack fields should use shadcn `Field` APIs:

- `Field`
- `FieldLabel`
- `FieldDescription`
- `FieldError`
- `FieldGroup`

For invalid state:

- compute invalid state with `field.state.meta.isTouched && !field.state.meta.isValid`
- set `data-invalid` on `Field`
- set `aria-invalid` on the input/control
- display `FieldError` only when touched and invalid
- pass TanStack's errors directly to shadcn: `<FieldError errors={field.state.meta.errors} />`
- do not map/stringify the errors before passing them to `FieldError`

Recommended reusable `TextField` shape:

```tsx
"use client";

import { Input } from "@/components/ui/input";
import {
  Field,
  FieldDescription,
  FieldError,
  FieldLabel,
} from "@/components/ui/field";
import { useFieldContext } from "@/hooks/form-context";

export function TextField({
  label,
  type = "text",
  autoComplete,
  placeholder,
  description,
}: {
  label: string;
  type?: string;
  autoComplete?: string;
  placeholder?: string;
  description?: string;
}) {
  const field = useFieldContext<string>();
  const isInvalid = field.state.meta.isTouched && !field.state.meta.isValid;

  return (
    <Field data-invalid={isInvalid}>
      <FieldLabel htmlFor={field.name}>{label}</FieldLabel>
      <Input
        id={field.name}
        name={field.name}
        type={type}
        value={field.state.value}
        autoComplete={autoComplete}
        placeholder={placeholder}
        aria-invalid={isInvalid}
        onBlur={field.handleBlur}
        onChange={(event) => field.handleChange(event.target.value)}
      />
      {description && !isInvalid && (
        <FieldDescription>{description}</FieldDescription>
      )}
      {isInvalid && <FieldError errors={field.state.meta.errors} />}
    </Field>
  );
}
```

### Base UI shadcn variant

In Base UI based shadcn projects, do not use `asChild`.

Prefer the Base UI `render` prop pattern where generated components support it, for example:

```tsx
<SidebarMenuButton render={<a href="/dashboard" />}>
  <span>Dashboard</span>
</SidebarMenuButton>
```

Before finishing, search for accidental `asChild` usages:

```bash
rg "asChild" . --glob '!node_modules/**' --glob '!.next/**'
```

There should be no matches unless the project intentionally uses a non-Base UI shadcn style.

## Dashboard and authenticated app shell

Ask the user which authenticated route they want for the dashboard/home route.

- Examples: `/dashboard`, `/home`
- If the user does not choose, use `/dashboard`.

Ask which shadcn sidebar block variant to use.

- Variants range from `sidebar-01` to `sidebar-16`.
- If unspecified, use `sidebar-07`.

Install the sidebar block:

```bash
npx shadcn@latest add sidebar-07
```

or equivalent with the detected package manager.

The sidebar should exist throughout the authenticated app via Next.js layouts. Recommended route-group shape:

```text
app/page.tsx                  # public landing
app/login/page.tsx            # public auth
app/signup/page.tsx           # public auth
app/(app)/layout.tsx          # protected app shell with sidebar
app/(app)/dashboard/page.tsx  # protected dashboard
```

`app/(app)/layout.tsx` should wrap children in the sidebar provider and sidebar shell. Keep page content inside the layout's inset area.

Use `proxy.ts` to protect authenticated routes, e.g.:

```ts
const isAuthPage = createRouteMatcher(["/login", "/signup", "/signin"]);
const isProtectedRoute = createRouteMatcher([
  "/dashboard/:path*",
  "/settings/:path*",
  "/server/:path*",
]);

export default convexAuthNextjsMiddleware(async (request, { convexAuth }) => {
  if (isAuthPage(request) && (await convexAuth.isAuthenticated())) {
    return nextjsMiddlewareRedirect(request, "/dashboard");
  }
  if (isProtectedRoute(request) && !(await convexAuth.isAuthenticated())) {
    return nextjsMiddlewareRedirect(request, "/login");
  }
});
```

Update the protected route list for the actual app routes.

## Backend structure

Install Convex helpers if missing:

```bash
<pkg> add convex-helpers@latest
```

Add shared auth wrappers in `convex/shared/auth.ts`:

```ts
import {
  customMutation,
  customQuery,
} from "convex-helpers/server/customFunctions";
import { mutation, query } from "../_generated/server";
import { getAuthUserId } from "@convex-dev/auth/server";

export const authedQuery = customQuery(query, {
  args: {},
  input: async (ctx) => {
    const userId = await getAuthUserId(ctx);
    if (userId === null) {
      throw new Error("Not authenticated");
    }
    return {
      ctx: { ...ctx, userId },
      args: {},
    };
  },
});

export const authedMutation = customMutation(mutation, {
  args: {},
  input: async (ctx) => {
    const userId = await getAuthUserId(ctx);
    if (userId === null) {
      throw new Error("Not authenticated");
    }
    return {
      ctx: { ...ctx, userId },
      args: {},
    };
  },
});
```

Use these wrappers for user-facing Convex queries and mutations. They add `ctx.userId` automatically.

## Convex feature organization

Shared Convex utilities go in:

```text
convex/shared
```

Feature code should use folders:

```text
convex/<feature_name>/schema.ts
convex/<feature_name>/queries.ts
convex/<feature_name>/mutations.ts
convex/<feature_name>/actions.ts
convex/<feature_name>/http.ts
convex/<feature_name>/nodeActions.ts
```

Rules:

- Queries go in `queries.ts`.
- Mutations go in `mutations.ts`.
- Actions go in `actions.ts`.
- HTTP actions go in `http.ts`.
- Node actions go in `nodeActions.ts` and must include a `"use node"` directive at the top.
- Schema may be modularized as `<feature>/schema.ts` and wired into `convex/schema.ts`.

User-facing Convex functions should derive `userId` from auth wrappers, not from browser-provided args.

## Validation checklist

Before finishing:

```bash
<pkg> lint
<pkg> exec tsc --noEmit
```

Also check Base UI projects for no `asChild`:

```bash
rg "asChild" . --glob '!node_modules/**' --glob '!.next/**'
```
