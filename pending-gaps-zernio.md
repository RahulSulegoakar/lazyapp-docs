---
title: "Pending gaps vs Zernio"
description: "Internal tracker of product and documentation gaps compared to Zernio's WhatsApp API."
---

# Pending gaps vs Zernio

Internal tracker for LazyApp vs [Zernio WhatsApp API](https://docs.zernio.com). **Code wins** — update this file when we ship or explicitly decline something.

Last reviewed: 2026-09-01

<Note>
This page is for the LazyApp team and implementers comparing surfaces to Zernio. Customer-facing docs link here only where we explicitly document a skip.
</Note>

## Product — build later

| Gap | Zernio | LazyApp today | Notes |
| --- | --- | --- | --- |
| Broadcast per-recipient vars | `variableMapping` from contact fields | Static `variable_map` only | Highest broadcast DX gap; keep single-call create |
| Contact segments API | Create/filter segments via API | Console-only; API accepts `contact_segment_id` | Needed for programmatic campaigns |
| Add broadcast recipients after create | `POST …/recipients` | Audience fixed on `POST /v1/broadcasts` | Lower priority if per-recipient vars land |
| Meta Template Library via API | `library_template_name` + lookup | Console link to Meta; standard submit flow | Faster onboarding |
| Contact bulk import API | `POST /v1/contacts/bulk` (1k) | Loop `POST /v1/contacts` or console | |
| Contact tags via API | Tags on create/update | Tags in console; `attributes` JSON on API | |
| Groups GA | Full groups API | `/v1/groups` exists; `groups: false` in capabilities | Non-coexistence numbers |
| Calling docs + UX | Calling API page | Permission request + webhook history; no PSTN/SIP bridge | `guides/calling.mdx` documents today; Zernio bridge backlog |
| Outbound dial + forwardTo API | Live dial + tel/sip/wss | `business_initiated` stub only | Full CPaaS bridge |
| Block users native `/v1` | Dedicated route | Meta-compat proxy only | |
| CTWA conversions | Attribution + CAPI | Not built | Ads-heavy customers |
| Public country rate API | — | Rates in workspace DB | Platform billing dashboards |
| Live number-info / health API | `GET /v1/whatsapp/number-info`, account health | Cached fields on `GET /v1/capabilities`; live read console-only | Liveness after coexistence disconnect |
| Analytics API | Listed as No on Zernio hub | Console usage only | Low priority |
| Hosted Flow data endpoints | Managed encryption | Customer hosts `data_endpoint_uri` | Documented; no managed hosting |

## Intentionally out of scope

| Zernio surface | LazyApp stance |
| --- | --- |
| Buy phone numbers via API ($3–21/mo, KYC) | Connect via Embedded Signup / setup links |
| `POST /connect/whatsapp/credentials` | Embedded Signup + setup links; console paste for own onboarding |
| Conversation-centric inbox create | Recipient-centric `POST /v1/messages` |
| Multi-network (IG, FB, Telegram, SMS) | WhatsApp-only |
| Account-days / social-seat billing | Wallet + plans + platform customer billing |
| SSO / SCIM | Not built |
| Raw Meta webhook forward | LazyApp-shaped payloads only |
| Serverless webhook/Flow functions | Customer infra + workflows |
| Sequences / drip product | Scheduled messages + workflows |
| MCP server | Not a public surface |
| Zernio 3-step broadcast flow | Single create is deliberate |
| Zernio sandbox sessions API | Test mode + console claim + test recipients |

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
| Group chats | — | When capability GA |
| Inbox (platform) | `platform/inbox.mdx` + embeds | Partial |
| Flows | API reference only | Guide TBD |
| CTWA | — | Skip |
| Pricing (platform) | `billing/*` | Partial |
| Media & limits reference | `sending/media.mdx` | Partial |
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
