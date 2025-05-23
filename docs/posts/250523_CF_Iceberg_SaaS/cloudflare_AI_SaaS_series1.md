---
title: Run a modern AI Software as a Service (SaaS) for <$100/month
slug: cloudflare-ai-saas-1
draft: false
date:
  created: 2025-05-23
  updated: 2025-05-23
tags:
  - Cloudflare
  - AI
  - Apache Iceberg
  - SaaS
  - Cloudflare R2
  - Cloudflare R2 Data Catalog
  - Cloudflare D1
  - Cloudflare KV
  - Cloudflare Tunnel
  - BetterAuth
  - React Router
  - Prefect
  - Dask
  - RAPIDS
  - Data Engineering
links:
    - Cloudflare Dashboard: https://dash.cloudflare.com/
---
The beta release of the [Cloudflare R2 Data Catalog](https://developers.cloudflare.com/r2/data-catalog/)
opens the door for a fully featured, lowest cost (for the cloud/hybrid space), and
relatively simple deployment of AI Software as a Service (SaaS) products. Combined with
[Prefect's bring your own compute](https://www.prefect.io/pricing) and a RAPIDS enabled
Dask server running on a consumer "gaming" PC at your home or office, it's
possible to develop, operate, and maintain a modern AI SaaS offering for under
$100 a month and one developer. **This post is not sponsored** by Cloudflare, Prefect,
or any other company.

<!-- more -->
# Building a Low-Cost Artificial Intelligence (AI) Software as a Service (SaaS): Part 1

## Goals
There are four driving goals for this series of blog posts and [the corresponding
technical implementation](https://github.com/brent-stone/hybrid_cloud_AI_SaaS):

1. Refine my own knowledge of the individual and integrated technology involved
2. Help others on their own self-improvement journey
3. Develop a (hopefully) value added SaaS supporting youth science, technology,
   engineering, and math (STEM) education and achievement
4. Empirically validate the maximum value balance of on premises and cloud computing
   offerings

## The initial design

### Hybrid-Cloud Architecture Diagram
![Hybrid-Cloud Architecture Diagram](AI_SaaS_Diagram-Dark.png#only-dark)
![Hybrid-Cloud Architecture Diagram](AI_SaaS_Diagram-Light.png#only-light)
/// caption
High-Level System Architecture
///

#### Frontend
[React Router 7.0+](https://reactrouter.com/) framework mode and
[Google's OpenID Connect](https://developers.google.com/identity/openid-connect/openid-connect)
as the primary ICAM provider via the [BetterAuth](https://www.better-auth.com/)
TypeScript framework. Styling driven by [Tailwind Plus](https://tailwindcss.com/).

Key Points for Tech Selection:

* Pure TypeScript
* Deployment agnostic (self-hosted, cloudflare, AWS, Google, Azure, etc.)
* The lead maintainer of React Router ([Shopify](https://www.shopify.com/)) makes
  money by the framework having a great developer experience.
  [Next.js](https://nextjs.org/) is the other popular React Framework that's
  primarily maintained by [Vercel](https://vercel.com/). Vercel makes money by
  locking in Next.js devs into their higher cost cloud hosting service.
* No fees from a third-party identity management provider like
  [Clerk](https://clerk.com/), [Auth0](https://auth0.com/), or
  [Firebase](https://firebase.google.com/).
* Cognitively easy to understand developer experience through
  [file based nested routing](https://reactrouter.com/start/framework/routing) and
  error boundaries.
* Tailwind is the leading CSS design language system. Tailwind Plus provides loads of
  production ready templates and examples for an affordable one-time purchase.
* 1st class support for [Vite](https://vite.dev/) and
  [Cloudflare Wrangler](https://developers.cloudflare.com/workers/wrangler/) makes
  the transition from localhost dev to global production become effortless, fast, &
  reliable.

#### Cloud Hosted Backend Services
Almost pure Cloudflare with the addition of
[Prefect Cloud](https://www.prefect.io/cloud) to manage complex data engineering
workflows triggered by the frontend but executed on premises.

Key Points for Tech Selection:

* 1st class support for a Python-only backend
* Storage services (D1, R2, KV, R2 data catalog) provide extreme value & performance
* Very minimal computation occurring in the cloud greatly minimizes cost & likely
  stays in free tier limits for small/medium projects. Best-in-industry pricing
  model when scaling to higher user volume

#### Self-Hosted Backend Services
Pure Python API and data engineering services capable of supporting production
workloads and efficient data engineering pipelines.

Key Points for Tech Selection:

* [FastAPI's](https://fastapi.tiangolo.com/) ground-up support of asynchronous tasks,
  [Pydantic](https://docs.pydantic.dev/latest/) input validation, and
  [Swagger](https://swagger.io/) make it the superior Python REST API platform
* [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
  and the provided container image makes for effortless and secure communication between
  cloud and self-hosted services
* [Apache Airflow](https://airflow.apache.org/),
  [Apache Beam](https://beam.apache.org/), [Dagster](https://dagster.io/),
  [Pachyderm](https://docs.pachyderm.com/), and many other options exist for DAG
  based data engineering pipeline creation and monitoring.
  [Prefect](https://www.prefect.io/) provides a completely free, self-hosted, pure
  Python, and modern option that has first-class support for
  [Dask](https://www.dask.org/) and enterprise logging.
* [Dask](https://www.dask.org/) is slightly less performant for some tasks compared to
  options built on [Ray](https://www.ray.io/) and [Polars](https://pola.rs/).
  However, Dask remains the leader for ease of use, deployment, and compatibility
  with the rest of the Python ecosystem like
  [scikit-learn](https://scikit-learn.org/stable/) and [SciPy](https://scipy.org/).
  Since this will be self-hosted, development velocity is preferred over
  saving a few seconds of computation time here and there (which would otherwise
  add up to expensive cloud service provider charges).
* [RAPIDS](https://rapids.ai/) is an NVIDIA sponsored project that has first class
  support by Dask and Prefect. It allows for easy integration of GPU accelerated
  computation in our data workflows.
* [PyIceberg](https://py.iceberg.apache.org/) will allow us to perform CRUD
  operations on the Cloudflare R2 Data Catalog which implements the
  [Apache Iceberg](https://iceberg.apache.org/) data standard and protocol.

!!! Note "In-Depth Workshops"

     For anyone curious about the many Python ecosystem data engineering tools available
     and wanting to try them out, check out my
     [2024 Avengercon Workshop](https://github.com/brent-stone/avengercon_2024) and
     follow up
     [2025 Avengercon Workshop](https://github.com/brent-stone/avengercon_2025).

## Hybrid-cloud is the sweet spot
Cloud compute costs kill otherwise promising efforts to improve, modernize, and
quickly deliver value. Hybrid-cloud deployment models allow use of the cloud where it
provides outsized value like cheap, globally accessible, and failsafe blob storage like
AWS S3 or Cloudflare R2.
Fixed cost and cheap on-premises self-hosted hardware can be seamlessly integrated
with high value cloud services for free using technology like [Cloudflare Tunnels](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)
and [wireguard](https://www.wireguard.com/).

For less than an economy car, even individuals can afford to build a rack mount or
desktop server with cutting edge GPUs, terabytes of ultra-fast NVMe storage, over a
hundred physical CPU cores, and a decent network security setup. This effectively drops
the cost to run AI models, data engineering pipelines, and APIs for the price of a few
months doing the same on cloud hosted hardware. Better yet, if the business or
project fails, the hardware purchased has residual value and can be sold to recoup some
of the upfront cost. Self-hosting computational tasks also mitigates the risk of waking
up to find an astronomical bill from your cloud provider because of a silly, but all to
easy to make, mistake.

### Using popular and proven technology
Using popular technology options maintains velocity thanks to search engines, LLMs, and
[stackoverflow.com](http://stackoverflow.com/) likely having accurate answers to any
question a developer may have. Python, React, and SQL are the _lingua franca_ of modern
software development related to AI-enabled SaaS webapps. The
[2024 Stack Overflow survey](https://survey.stackoverflow.com/2024/technology#most-popular-technologies)
overwhelmingly backs this up. Pursuing solutions that don't or barely support these
languages will cause pain organizationally (hiring, training, knowledge management) and
technologically by constraining your organization's options to pivot.

### Heavy use of Cloudflare
This post and series is in no way sponsored by Cloudflarre. Cloud and web veterans will
notice the conspicuous lack of AWS, Azure, Google Cloud, Vercel, and other mainstream
cloud hosting options from this architecture. This is because Cloudflare has steadily
positioned itself to steal market share and make our lives as entrepreneurs easier in
three key areas:

#### No nonsense developer experience
Cloudflare's services are routinely simple enough that clicking around for a
few minutes often solves blockers. Fully working and routinely updated templates
made by cloudflare get projects up and going fast. Services are also generally global,
redundant, and optimized by default. No more inter-zone, inter-region, 100+ hour cost
optimization setup cycles semi-intentionally designed to squeeze you for cash at any
minor mistake.

#### Aggressive pricing rates and policies
Cloudflare is the only major cloud service provider with no ingress or egress
fees for data. This categorically eliminates a double-digit percentage of total cloud
spend involved with storing data at any other reputable service provider. As
mentioned in the intro, their introduction of [Cloudflare R2 Data Catalog](https://developers.cloudflare.com/r2/data-catalog/)
makes them a rock-bottom price data lake service provider orders of magnitude less
costly than industry leaders like [Snowflake](https://www.snowflake.com/),
[Data Bricks](https://www.databricks.com/), [Delta Lake](https://delta.io/), and
[BigQuery](https://cloud.google.com/bigquery).

A few other notable free or nearly free key offerings:
* Cloudflare Tunnels for securely directing traffic to self-hosted API and services
* DNS Registrar for quickly and for no cost (beyond ICANN fee) acquire domain
  names like brent-stone.com
* Cloudflare Pages and Workers for serverless hosting of interactive websites
* DDoS protection
* Zero-trust access controls to effortlessly apply a world-class layer of security
  to self-hosted services

#### Concise but impactful portfolio of services
I believe Cloudflare's limited suite of service offerings, compared to the
[seemingly endless options at AWS](https://docs.aws.amazon.com/whitepapers/latest/aws-overview/amazon-web-services-cloud-platform.html),
Google Cloud Platform, and Microsoft Azure, is one of its strengths. Time and again
I discover a super-tailored and compelling offering like the [Google Cloud Agones Project](https://cloud.google.com/blog/products/containers-kubernetes/introducing-agones-open-source-multiplayer-dedicated-game-server-hosting-built-on-kubernetes)
to provide a high performance multiplayer backend for game development. After half an
hour of researching how to use it, random reddit comments and blog posts confirm the
cloud provider no longer supports it.

With Cloudflare, I don't believe they've ever shut down a service. This slow and
focused business growth strategy gives small teams confidence that development effort
integrating their services will pay off for years to come.

!!! Note
    In this particular instance, Agones thankfully survived being killed off by the
    cloud service provider and [lives on as an independent project](https://agones.dev/site/)



[//]: # (### Use Kubernetes &#40;K8s&#41; when you can... but don't let it hinder an MVP)

[//]: # (Kubernetes, Helm, Terraform, and other leading cloud orchestration tools are )

[//]: # (tremendously valuable when used by trained engineers. Nearly every technology )

[//]: # (organization that's growing will need to use and master them at some point. However, )

[//]: # (Docker Compose and Docker Swarm provide completely functional options for )

[//]: # (development of a minimal viable product &#40;MVP&#41; that uses software likely already on your)

[//]: # (development and production computers. Prematurely introducing complexity and trying )

[//]: # (to troubleshoot the myriad problems that can arise from a K8s deployment is not a )

[//]: # (good use of time early in an AI SaaS project's lifecycle. When it's time to )

[//]: # (transition to Kubernetes, [tools like Kompose]&#40;https://kompose.io/&#41; can minimize the )

[//]: # (pain and time to do so.)
