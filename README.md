# MetaLend

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

MetaLend is a DeFi "earn and spend" platform that aggregates lending pools across protocols (Aave, Morpho, Euler) and chains (Base, Ethereum, Polygon, Arbitrum, Linea) behind a single cross-chain Rebalancing API. It continuously repositions user-approved, self-custodied capital into the best-yielding pools while keeping funds instantly spendable via a debit card. The API is notably agent-native: it serves an AI agent flow guide at GET /SKILL.md, ships SIWE challenge/verify JWT auth with EIP-191/EIP-712/EIP-3009 wallet signing, and gates its services directory behind an x402 (HTTP 402) micropayment in USDC on Base. Smart contracts are audited by Sherlock. Backed by Pantera Capital.

**Areas:** Company · Cryptocurrency · DeFi · Lending · Yield · Rebalancing · Web3 · Stablecoins · Payments · Agent-Native · Blockchain

## MetaLend on APIs.io

Everything profiled for MetaLend is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/metalend/](https://apis.io/providers/metalend/)** — or go straight to one of the APIs or properties below.

### APIs (10)

- [MetaLend AI Agent Guide API](https://apis.io/apis/metalend/metalend-ai-agent-guide-api/) — Machine-readable flow guide for AI agents — signing formats, wizard flows, and integration gotchas
- [MetaLend Auth API](https://apis.io/apis/metalend/metalend-auth-api/) — Get challenge / verify challenge authentication
- [MetaLend Balances API](https://apis.io/apis/metalend/metalend-balances-api/) — Balance queries across pools and protocols
- [MetaLend Deposits API](https://apis.io/apis/metalend/metalend-deposits-api/) — Deposit operations for rebalancing
- [MetaLend Pools API](https://apis.io/apis/metalend/metalend-pools-api/) — Protocol and pool information
- [MetaLend Rewards API](https://apis.io/apis/metalend/metalend-rewards-api/) — Reward aggregation and claim data
- [MetaLend Services API](https://apis.io/apis/metalend/metalend-services-api/) — The Services API from MetaLend — 1 operation(s) for services.
- [MetaLend Transaction Costs API](https://apis.io/apis/metalend/metalend-transaction-costs-api/) — Transaction cost queries
- [MetaLend User Configuration API](https://apis.io/apis/metalend/metalend-user-configuration-api/) — User rebalancer configuration management
- [MetaLend Withdrawals API](https://apis.io/apis/metalend/metalend-withdrawals-api/) — Withdrawal operations from pools

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [metalend](https://apis.io/servers/metalend/metalend/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Metalend Agentic Access](https://apis.io/agentic-access/metalend/metalend-agentic-access/)

#### Collections (10)

Postman and Open Collection request collections.

- [MetaLend Rebalancing AI Agent Guide API](https://apis.io/collections/metalend/open-metalend-ai-agent-guide-api/)
- [MetaLend Rebalancing AI Agent Guide Auth API](https://apis.io/collections/metalend/open-metalend-auth-api/)
- [MetaLend Rebalancing AI Agent Guide Balances API](https://apis.io/collections/metalend/open-metalend-balances-api/)
- [MetaLend Rebalancing AI Agent Guide Deposits API](https://apis.io/collections/metalend/open-metalend-deposits-api/)
- [MetaLend Rebalancing AI Agent Guide Pools API](https://apis.io/collections/metalend/open-metalend-pools-api/)
- [MetaLend Rebalancing AI Agent Guide Rewards API](https://apis.io/collections/metalend/open-metalend-rewards-api/)
- [MetaLend Rebalancing AI Agent Guide Services API](https://apis.io/collections/metalend/open-metalend-services-api/)
- [MetaLend Rebalancing AI Agent Guide Transaction Costs API](https://apis.io/collections/metalend/open-metalend-transaction-costs-api/)
- [MetaLend Rebalancing AI Agent Guide User Configuration API](https://apis.io/collections/metalend/open-metalend-user-configuration-api/)
- [MetaLend Rebalancing AI Agent Guide Withdrawals API](https://apis.io/collections/metalend/open-metalend-withdrawals-api/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Metalend Rebalancing Overlay](https://apis.io/overlays/metalend/metalend-rebalancing-overlay/)

#### Security (2)

Authentication, trust centre, and vulnerability disclosure.

- [Metalend Authentication](https://apis.io/security/metalend/metalend-authentication/)
- [Metalend Domain Security](https://apis.io/security/metalend/metalend-domain-security/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://metalend.tech/)
- [Documentation](https://metalend-inc.gitbook.io/litepaper)
- [Getting started](https://api.metalend.tech/SKILL.md)
- [API reference](https://api.metalend.tech/openapi.json)
- [Pricing](https://metalend-inc.gitbook.io/litepaper/metalend-litepaper/apy-and-fees)
- [Sign up](https://developer.metalend.tech)
- [Support](https://metalend-inc.gitbook.io/litepaper/metalend-litepaper/support)
- [Blog](https://medium.com/metalend)
- [Compliance](https://metalend-inc.gitbook.io/litepaper/security-audits-with-sherlock)
- [Terms of service](https://metalend-inc.gitbook.io/litepaper/terms-of-service)
- [Privacy policy](https://metalend-inc.gitbook.io/litepaper/privacy-policy)

## API Evangelist rating

**Kin Score 48.8** — developing · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 73.2 |
| Contract quality | 53.5 |
| Contract governance | 18.2 |
| Operational transparency | 5.3 |
| Developer ergonomics | 71.4 |
| Access clarity | 52.6 |

**Agent readiness 38.0** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | verified |
| Rate Limit Signal | verified |
| Event Surface Described | — |
| Agent Skills | yes |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | distinct |

**Regulatory alignment 26.8** — Payments

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/metalend/](https://apis.io/providers/metalend/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for MetaLend: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/metalend/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
