---
title: Integrating Better Auth with React Router and Cloudflare D1
slug: better-auth-react-router-cloudflare-d1
draft: false
date:
  created: 2025-06-07
  updated: 2025-06-07
tags:
  - SaaS Tutorial
  - Better Auth
  - Cloudflare
  - React Router
  - Kysely
  - Cloudflare D1
links:
  - Better Auth Remix Integration: https://www.better-auth.com/docs/integrations/remix
  - Better Auth Drizzle Integration: https://www.better-auth.com/docs/adapters/drizzle
  - Kysely D1 Adapter: https://github.com/aidenwallis/kysely-d1
---

We'll be integrating the [Better Auth](https://www.better-auth.com/docs/integrations/remix) authentication framework with
React Router v7 and the Cloudflare D1 SQLite database. This choice ensures the project can maintain portability to 
practically any cloud, hybrid cloud, or fully self-hosted deployment option and avoids an additional cost source from using
fully managed offerings like [Clerk](https://clerk.com/), [WorkOS](https://workos.com/), or [Auth0](https://auth0.com/).
Leveraging Better Auth also allows the project to implicitly keep up with authentication best practices and avoids 
excessive engineering time from maintaining a completely custom authentication layer.

<!-- more -->

## Updating Project Dependencies
Add Better Auth as a project dependency. Refer to the 
[Better Auth installation guide](https://www.better-auth.com/docs/installation) for more info including
configuration of environment variables. Please note that you shouldn't follow too far into the basic guide's setup
instructions as there's significant changes for using React Router, Drizzle, and Cloudflare D1.

```bash
npm install better-auth
```

!!! note "Drizzle Natively Supports Cloudflare D1"

    As the time of this post, Drizzle natively supports Cloudflare D1 database connections. Various documentation still 
    refer to [the Kysely D1 Adapter](https://github.com/aidenwallis/kysely-d1) as a needed plugin, but this is no longer
    tha case. The [Drizzle D1 getting started](https://orm.drizzle.team/docs/get-started/d1-new) documentation 
    implicitly reflects this updated support through use of the TypeScript import:

    ```typescript
    import { drizzle } from 'drizzle-orm/d1';
    ...
    ```

## Reviewing Drizzle configuration from the React Router Template
The Drizzle configuration boilerplate was mostly completed by the React Router Cloudflare D1 template. Let's quickly
review what was created compared to the directions in the [official Drizzle D1 getting started documentation](https://orm.drizzle.team/docs/get-started/d1-new)

#### Basic file structure
There's only two minor changes here

* The Drizzle recommendation of a `src/db/schema.ts` setup is instead at `database/schema.ts`
* `wrangler.toml` replaced by the Cloudflare recommended `wrangler.jsonc` format

The file location of the essential Drizzle ORM binding is not explicitly specified in the Drizzle documentation. The
React Router team implemented the binding using the name `db` and included some additional boilerplate for the 
Cloudflare API within `workers\app.ts`. Take note that the `db` binding is established as a subset of the `react-router`
declaration instead of a standalone exported interface.

```typescript hl_lines="1 11" linenums="1"
import { drizzle, type DrizzleD1Database } from "drizzle-orm/d1";
import { createRequestHandler } from "react-router";
import * as schema from "../database/schema";

declare module "react-router" {
  export interface AppLoadContext {
    cloudflare: {
      env: Env;
      ctx: ExecutionContext;
    };
    db: DrizzleD1Database<typeof schema>;
  }
}
```

!!! note "React Router `AppLoadContext`"

    As of this post, the functionality of [Extending app `Context` types](https://reactrouter.com/how-to/route-module-type-safety#4-typing-apploadcontext)
    is not well documented for React Router v7. Essentially, this re-declaration of `react-router` is adding additional
    global variables, `cloudflare` and `db`, for use throughout the application. This avoids needing to constantly
    import these configurations seperately from importing `react-router`.

    The `db` and `cloudflare` context declarations are available from React Router server side functions. For example,
    here's an except from `/app/routes/home.tsx`:

    ```typescript hl_lines="1 5-6"
    import type { Route } from "./+types/home";

    ...

    export async function loader({ context }: Route.LoaderArgs) {
      const guestBook = await context.db.query.guestBook.findMany({
        columns: {
          id: true,
          name: true,
        },
      });
    ```

#### `wrangler.jsonc`
The Cloudflare D1 database binding information is included in `wrangler.jsonc` under the `d1_databases` key. This is
simply a different format but same key-value information recommended by Drizzle using a `wrangler.toml` file format.

#### `drizzle.config.ts`
A slightly different Drizzle configuration default export was generated in `drizzle.config.ts` at the project root.
Drizzle recommends using `defineConfig` while the template used `Config` from the `drizzle-kit` SDK. the `schema` has
also been moved to `\database\schema.ts` which is reflected in the entry in the Drizzle configuration.

## Setup Better Auth boilerplate
The following is taken from the following documenation sources:

* [The Better Auth Remix Integration](https://www.better-auth.com/docs/integrations/remix)
* [The Better Auth Drizzle Integration](https://www.better-auth.com/docs/adapters/drizzle)
* [Drizzle Cloudflare D1 Integration](https://orm.drizzle.team/docs/connect-cloudflare-d1)
* 

=== "`app\utils\auth.server.ts`"

    ```typescript
    import { betterAuth } from "better-auth";
    import { drizzleAdapter } from "better-auth/adapters/drizzle";
    import { db } from "react-router"; // your drizzle instance
    
    export const auth = betterAuth({
        database: drizzleAdapter(db, {
            provider: "sqlite", // or "mysql", "sqlite", "pg"
        })
    });
    ```


### Add Better-Auth's `usersTable` to the database
Add an additional `usersTable` schema declaration in `database\schema.ts` 
[according to the Drizzle documentation](https://orm.drizzle.team/docs/get-started/d1-new#step-4---create-a-table):

```typescript
import { int, sqliteTable, text } from "drizzle-orm/sqlite-core";

export const usersTable = sqliteTable("users_table", {
  id: integer().primaryKey({ autoIncrement: true }),
  name: text().notNull(),
  age: integer().notNull(),
  email: text().notNull().unique(),
});
```

