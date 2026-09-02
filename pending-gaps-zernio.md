---
title: "Pending gaps vs Zernio"
description: "Internal tracker of product and documentation gaps compared to Zernio's WhatsApp API."
---

# Pending gaps vs Zernio

Internal tracker for LazyApp vs [Zernio WhatsApp API](https://docs.zernio.com). **Code wins** — update this file when we ship or explicitly decline something.

Last reviewed: 2026-09-02 (Flows guide + OpenAPI path matrix)

<Note>
This page is for the LazyApp team and implementers comparing surfaces to Zernio. Customer-facing docs link here only where we explicitly document a skip.
</Note>

## Zernio OpenAPI spec (`zernio-api-openapi.yaml`)

**What it is:** Zernio’s full public API contract (OpenAPI 3.1, version **1.0.4**, base `https://zernio.com/api/v1`). ~**418** path entries — mostly **multi-network** (IG, X, TikTok, ads, analytics, posting queue), not WhatsApp-only. Also published for RapidAPI (`x-documentation`, `x-badges`).

**Use it for:** machine-readable diff of path names, request shapes, and WhatsApp-specific routes under `/v1/whatsapp/*` and `/v1/inbox/*`. Do **not** treat it as LazyApp’s contract — confirm everything in `lazyapp/routes/v1.php` and `docs/api-contract.json` (~**92** operations).

### Path-by-path (WhatsApp-relevant)

| Zernio path(s) | LazyApp | Verdict |
| --- | --- | --- |
| `POST /v1/inbox/conversations…/messages` | `POST /v1/messages` | Keep LazyApp model |
| `GET /v1/inbox/conversations`, `…/messages` | `GET /v1/conversations`, `…/messages` | Covered |
| `…/typing`, `…/read`, `…/reactions`, `DELETE …/messages/{id}`, search | Console typing/archive; reactions via `POST /v1/messages`; no public delete/search | Gap / skip |
| `/v1/broadcasts` + `/send` + `/recipients` + `/schedule` | `POST /v1/broadcasts` + `start`; `schedule_at` on create | Keep single-call; gaps: per-recipient vars, add recipients |
| `/v1/whatsapp/templates`, `template-library` | `/v1/templates` + Meta-compat | Gap: library API |
| `/v1/whatsapp/flows/*`, `flows/send`, `flow-responses` | `/v1/flows/*` + interactive send + responses | Covered (no hosted endpoint) |
| `/v1/whatsapp/wa-groups/*` + participants/invite/join | `/v1/groups` create/list/show + `group_id` send | Partial |
| `/v1/whatsapp/calls*`, recording, estimate, permissions, `/v1/voice/*` | `GET/POST /v1/calls` permission + history | Partial — no bridge |
| `/v1/whatsapp/phone-numbers/purchase`, KYC, countries, available | Embedded Signup / BYO | Out of scope |
| `/v1/whatsapp/sandbox/sessions` | Test mode + console claim | Out of scope |
| `/v1/whatsapp/number-info`, `account-events`, `block-users` | Capabilities + webhooks; block via Meta-compat | Partial |
| `/v1/whatsapp/business-profile/*` | Meta-compat profile proxy | Partial |
| `/v1/ads/ctwa`, `/v1/whatsapp/dataset`, `/conversions` | Not built (referral on webhook) | Gap / skip until demand |
| `/v1/contacts`, `/contacts/bulk`, channels/fields | `/v1/contacts` upsert/consent/merge | Gaps: bulk, tags/segments API |
| `/v1/sequences/*` | Workflows + `send_at` | Out of scope |
| `/v1/verify/verifications` | `/v1/otp`, `/v1/otp/verify` | Covered (different shape) |
| `/v1/connect/whatsapp/*` | Console ES + setup links | Out of scope (no headless credentials API) |
| `/v1/whatsapp/media/{id}` | `/v1/media`, inbound `GET /v1/messages/{uuid}/media` | Covered |

**LazyApp-only:** `/v1/events`, automation packs, `/v1/capabilities`, Meta-compat `/meta/whatsapp/...`, platform customers / setup links / embed tokens, contact suppress/consent/merge, wallet `402`, idempotency on writes.

## Product — build later

| Gap | Zernio | LazyApp today | Notes |
| --- | --- | --- | --- |
| Broadcast per-recipient vars | `variableMapping` from contact fields | Static `variable_map` only | Highest broadcast DX gap; keep single-call create |
| Contact segments API | Create/filter segments via API | Console-only; API accepts `contact_segment_id` | Needed for programmatic campaigns |
| Add broadcast recipients after create | `POST …/recipients` | Audience fixed on `POST /v1/broadcasts` | Lower priority if per-recipient vars land |
| Meta Template Library via API | `library_template_name` + lookup | Console link to Meta; standard submit flow | Faster onboarding |
| Contact bulk import API | `POST /v1/contacts/bulk` (1k) | Loop `POST /v1/contacts` or console | |
| Contact tags via API | Tags on create/update | Tags in console; `attributes` JSON on API | |
| Groups participant / invite / join APIs | Add/remove participants, invite link, join requests | Create, list, show, group send only | `groups` + `messages.group` gated; non-coexistence |
| Calling docs + UX | Calling API page | Permission request + webhook history; no PSTN/SIP bridge | `guides/calling.mdx` documents today; Zernio bridge backlog |
| Outbound dial + forwardTo API | Live dial + tel/sip/wss | `business_initiated` stub only | Full CPaaS bridge |
| Block users native `/v1` | Dedicated route | Meta-compat proxy only | |
| CTWA conversions | Attribution + CAPI | Not built | Ads-heavy customers |
| Public country rate API | — | Rates in workspace DB | Platform billing dashboards |
| Live number-info / health API | `GET /v1/whatsapp/number-info`, account health | Cached fields on `GET /v1/capabilities`; live read console-only | Liveness after coexistence disconnect |
| Analytics API | Listed as No on Zernio hub | Console usage only | Low priority |
| Hosted Flow data endpoints | Managed encryption | Customer hosts `data_endpoint_uri` | Documented in `guides/flows.mdx` |
| Flow starters / duplicate via API | — | Console only | Low priority |
| Public typing indicator API | Inbox typing endpoint | Console session only | Low priority if embeds cover UX |
| Public archive / pin thread API | Archive on inbox conversations | Console per-user thread state | |
| Voice note flag on `POST /v1/messages` | `voiceNote: true` | Console + Meta-compat `audio.voice`; not on V1 JSON | |
| Delete outbound message via API | `DELETE …/messages/{id}` | Coexistence revoke → `message.deleted` only | |
| Inbox conversation search API | `GET /v1/inbox/conversations/search` | Filter `status` / `assignee_id` on list only | |
| Dedicated business-profile routes | `/v1/whatsapp/business-profile/*` | Meta-compat `whatsapp_business_profile` proxy | |

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
