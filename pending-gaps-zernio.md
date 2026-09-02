---
title: "Pending gaps vs Zernio"
description: "Internal tracker of product and documentation gaps compared to Zernio's WhatsApp API."
---

# Pending gaps vs Zernio

Internal tracker for LazyApp vs [Zernio WhatsApp API](https://docs.zernio.com). **Code wins** — update this file when we ship or explicitly decline something.

Last reviewed: 2026-09-02 (Wave 3 API gaps shipped)

<Note>
This page is for the LazyApp team and implementers comparing surfaces to Zernio. Customer-facing docs link here only where we explicitly document a skip.
</Note>

## Zernio OpenAPI spec (`zernio-api-openapi.yaml`)

**What it is:** Zernio’s full public API contract (OpenAPI 3.1, version **1.0.4**, base `https://zernio.com/api/v1`). ~**418** path entries — mostly **multi-network** (IG, X, TikTok, ads, analytics, posting queue), not WhatsApp-only. Also published for RapidAPI (`x-documentation`, `x-badges`).

**Use it for:** machine-readable diff of path names, request shapes, and WhatsApp-specific routes under `/v1/whatsapp/*` and `/v1/inbox/*`. Do **not** treat it as LazyApp’s contract — confirm everything in `lazyapp/routes/v1.php` and `docs/api-contract.json` (~**115** operations after Wave 3).

### Path-by-path (WhatsApp-relevant)

| Zernio path(s) | LazyApp | Verdict |
| --- | --- | --- |
| `POST /v1/inbox/conversations…/messages` | `POST /v1/messages` | Keep LazyApp model |
| `GET /v1/inbox/conversations`, `…/messages` | `GET /v1/conversations`, `…/messages` | Covered |
| `…/typing`, archive/pin, search | `/v1/conversations` typing, PATCH flags, `q` | Covered (workspace flags, not per-agent) |
| Reactions | `POST /v1/messages` `type: reaction` | Covered |
| `DELETE …/messages/{id}` | — | Skip (Cloud API has no general delete) |
| `/v1/broadcasts` + recipients + schedule | `POST /v1/broadcasts` + `start` + `…/recipients` + per-recipient vars | Covered (single-call create kept) |
| `/v1/whatsapp/templates`, `template-library` | `/v1/templates` + Meta-compat | Gap: library API |
| `/v1/whatsapp/flows/*` | `/v1/flows/*` + starters + duplicate | Covered (no hosted endpoint) |
| `/v1/whatsapp/wa-groups/*` + participants/invite/join | `/v1/groups` create/list/show + `group_id` send | Partial — skip GA-gated participant APIs |
| `/v1/whatsapp/calls*` | `GET/POST /v1/calls` | Partial — no bridge |
| Number shop / KYC / sandbox sessions | BYO + console claim | Out of scope |
| `block-users`, business-profile, number-info | `/v1/connections/{uuid}/…` | Covered |
| CTWA / dataset / conversions | — | Skip |
| Contacts bulk / tags / segments | `/v1/contacts/bulk`, `/v1/tags`, `/v1/contact-segments` | Covered |
| Sequences | Workflows + `send_at` | Out of scope |
| Verify | `/v1/otp` | Covered |
| Analytics / rates | `/v1/analytics/messaging`, `/v1/pricing/rates` | Covered (thin) |

**LazyApp-only:** `/v1/events`, automation packs, `/v1/capabilities`, Meta-compat `/meta/whatsapp/...`, platform customers / setup links / embed tokens, contact suppress/consent/merge, wallet `402`, idempotency on writes.

## Product — build later

| Gap | Zernio | LazyApp today | Notes |
| --- | --- | --- | --- |
| Meta Template Library via API | `library_template_name` + lookup | Console link to Meta; standard submit flow | Lower ROI than Wave 3 DX |
| Groups participant / invite / join APIs | Add/remove participants, invite link, join requests | Create, list, show, group send only | Capability-gated GA |
| Outbound dial + forwardTo / recording | Live dial + tel/sip/wss | Permission + history stub | CPaaS bridge |
| CTWA conversions | Attribution + CAPI | Not built | Ads-heavy customers |
| Hosted Flow data endpoints | Managed encryption | Customer hosts `data_endpoint_uri` | Large infra product |
| Delete outbound message via API | `DELETE …/messages/{id}` | Coexistence revoke → `message.deleted` only | Meta limitation |
| Per-agent inbox pins via API | User-scoped archive | Workspace-level `archived`/`pinned` on conversations | API keys have no user |

## Intentionally out of scope

| Zernio surface | LazyApp stance |
| --- | --- |
| Buy phone numbers via API ($3–21/mo, KYC) | Connect via Embedded Signup / setup links |
| `POST /connect/whatsapp/credentials` | Embedded Signup + setup links; console paste for own onboarding |
| Conversation-centric inbox create | Recipient-centric `POST /v1/messages` |
| Unified inbox (IG/FB comments + reviews + DMs) | WhatsApp DMs only |
| Multi-network (IG, FB, Telegram, SMS) | WhatsApp-only |
| Account-days / social-seat billing | Wallet + plans + platform customer billing |
| SSO / SCIM | Not built |
| Raw Meta webhook forward | LazyApp-shaped payloads only |
| Serverless webhook/Flow functions | Customer infra + workflows |
| Sequences / drip product | Scheduled messages + workflows |
| MCP server | Not a public surface |
| Zernio 3-step broadcast flow | Single create is deliberate |
| Zernio sandbox sessions API | Test mode + console claim + test recipients |
| Zernio Usage plan (10k free outbound / mo) | Subscription + prepaid wallet + free rules |

## Docs — Zernio pages vs LazyApp

| Zernio page | LazyApp doc | Status |
| --- | --- | --- |
| WhatsApp hub | `guides/whatsapp.mdx` | Done |
| Connection & setup | `guides/connecting-whatsapp.mdx` | Done |
| Broadcasts | `sending/broadcasts.mdx` | Done |
| Templates | `sending/templates.mdx` | Done |
| Contacts & profile | `guides/contacts.mdx` | Done |
| Phone numbers / purchase / KYC | `guides/phone-numbers.mdx` | Done (BYO model; skip shop/KYC) |
| Calling | `guides/calling.mdx` | Done (partial — no Zernio bridge) |
| Sandbox | `guides/sandbox.mdx` | Done (test mode; skip Zernio sessions API) |
| Group chats | `guides/groups.mdx` | Done (partial — no participant/join APIs) |
| Inbox | `guides/inbox.mdx` + `platform/inbox.mdx` + embeds | Done (recipient-centric; console for typing/archive) |
| Flows | `guides/flows.mdx` + API reference | Done |
| CTWA | — | Skip (referral passthrough on webhooks only) |
| Pricing & costs | `billing/overview.mdx`, `billing/whatsapp-rates.mdx`, `billing/mechanics.mdx` | Done |
| Media & limits reference | `reference/media-and-limits.mdx` + `sending/media.mdx` | Done |
| Migrate from Kapso | `migrating/from-kapso.mdx` | Done |
| Migrate from Cloud API | `migrating/from-cloud-api.mdx` | Done |

## Where LazyApp is ahead (no gap)

- Broadcast compliance (consent, exclusions, quiet hours, pacing)
- Verify OTP, events, automation packs
- Platform customers, setup links, embed inbox
- Meta proxy for Kapso / Cloud API migrants
- Idempotency, cursor pagination, wallet usage ledger

## Suggested build order

1. Broadcast per-recipient template variables  
2. Contact segments API  
3. Groups / calling when Meta enables  
4. CTWA if customer demand  
5. Template library API if onboarding friction is high  
