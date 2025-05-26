---
title: Initializing a new Cloudflare + React Routerv7 Project
slug: init-cloudflare-react-router
draft: false
date:
  created: 2025-05-26
  updated: 2025-05-26
tags:
  - Cloudflare
  - React Router
  - Wrangler
  - Cloudflare D1
  - Continuous Integration
links:
    - React Router Templates: https://github.com/remix-run/react-router-templates/tree/main
    - CF API Tokens: https://developers.cloudflare.com/workers/wrangler/migration/v1-to-v2/wrangler-legacy/authentication/#generate-tokens
    - Wrangler Env Vars: https://developers.cloudflare.com/workers/configuration/environment-variables/#compare-secrets-and-environment-variables
    - D1 Local Dev Best Practices: https://developers.cloudflare.com/d1/best-practices/local-development/
---

## Initial React Router v7 + Cloudflare D1 Setup

In a few minutes we'll transition from a new git repository to a hybrid cloud web application with best practice 
secrets management, a serverless Cloudflare D1 Database managed by the [Drizzle ORM](https://orm.drizzle.team/), and a
seemless transition from local development to global production. This is thanks to the 
[routinely updated project templates](https://github.com/remix-run/react-router-templates/tree/main) available from the 
React Router team. The latest open source code is [available in the companion GitHub repository](https://github.com/brent-stone/hybrid_cloud_AI_SaaS).

<!-- more -->

## Initialize the repository from the Cloudflare D1 Template

### Local development environment
The [companion](https://github.com/brent-stone/hybrid_cloud_AI_SaaS) and [React Router Templates](https://github.com/remix-run/react-router-templates/tree/main)
repositories include information for seting up your local [Node.js](https://github.com/nvm-sh/nvm) development environment.

### Using the React Router Cloudflare D1 template
The React Router teams' Cloudflare D1 template will guide you through the process of creating a new project incorporating:

* [Cloudflare Wrangler](https://developers.cloudflare.com/workers/wrangler/): CLI for deploying code to Cloudflare Workers
* React Router 7 TypeScript Boilerplate
* [Vite](https://vite.dev/): build and package the React Router managed assets
* A placeholder Drizzle ORM Schema

```bash
npx create-react-router@latest --template remix-run/react-router-templates/cloudflare-d1
```

#### Secrets and sensitive information management
Environment variables will be securely stored using
[Cloudflare's environment management dashboard](https://developers.cloudflare.com/workers/configuration/environment-variables/#add-environment-variables-via-the-dashboard) 
and locally in a 
[.dev.vars](https://developers.cloudflare.com/workers/configuration/environment-variables/#compare-secrets-and-environment-variables)
file which Cloudflare Wrangler uses by default. The state of both the local file and what is in the cloudflare dashboard
will need to be manually maintained.

In the future, [Cloudflare Secrets Store](https://developers.cloudflare.com/secrets-store/) will be a streamlined
and secure method for maintaining a single source of truth for environment variable secrets. As of this post, it is
currently in open beta. The main benefits of that approach is all developers working off the same account will always
have the same environment variable setup. It also eliminates the need to somehow send `.dev.vars` or `.env` files back
and forth which creates ample opportunity for exposing critical information.

#### Create a new Cloudflare D1 Database using Wrangler
The [Cloudflare D1 Database](https://developers.cloudflare.com/d1/) service can be edited using the Cloudflare dashboard
or directly from the local computer using the Wrangler CLI. Follow the directions in the 
[React Router Cloudflare D1 Template](https://github.com/remix-run/react-router-templates/tree/main/cloudflare-d1) to use
Wrangler to quickly create a new D1 database and get back metadata we'll need to save in our environment variables.

```bash
npx wrangler d1 create <name-of-your-database>
```

Save the output to environment variables in `.dev.vars` like so

=== "Output from `wrangler d1 create`"
    Created your new D1 database.

    ```json hl_lines="5-6"
    {
      "d1_databases": [
        {
          "binding": "DB",
          "database_name": "<name-of-your-database>",
          "database_id": "e04d9e3a-5357-...."
        }
      ]
    }
    ```

=== "Official `.env` example"

    Cloudflare's currently supported environment variables and example `.env` file are 
    [available here](https://developers.cloudflare.com/workers/wrangler/system-environment-variables/#example-env-file).
    Since recommended best practices change frequently, be sure to verify the current guidance before trusting the
    example in the next tab.

=== ".dev.vars"

    ```ini
    # For drizzle.config.ts
    CF_DATABASE_ID=e04d9e3a-5357-....
    CF_ACCOUNT_ID=unknown
    CF_API_TOKEN=unknown
    
    # Additional vars
    CF_DATABASE_NAME=<name-of-your-database>
    ```



## Cloudflare Wrangler and Vite
As notes in the Cloudflare Wrangler environments documentation....

!!! note

    If you're using the [Cloudflare Vite plugin](https://developers.cloudflare.com/workers/vite-plugin/), you select 
    the environment at dev or build time via the `CLOUDFLARE_ENV` environment variable rather than the --env flag. 
    Otherwise, environments are defined in your Worker config file as usual. For more detail on using environments with 
    the Cloudflare Vite plugin, refer to the [plugin documentation](https://developers.cloudflare.com/workers/vite-plugin/reference/cloudflare-environments/).