# Pending gaps vs Zernio

Internal tracker for LazyApp vs [Zernio WhatsApp API](https://docs.zernio.com). **Code wins** — update this file when we ship or explicitly decline something.

Last reviewed: 2026-09-01

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
| Calling docs + UX | Calling API page | Meta-gated; `calling: false` default | When capability flips on |
| Block users native `/v1` | Dedicated route | Meta-compat proxy only | |
| CTWA conversions | Attribution + CAPI | Not built | Ads-heavy customers |
| Public country rate API | — | Rates in workspace DB | Platform billing dashboards |
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

## Docs — Zernio pages vs LazyApp

| Zernio page | LazyApp doc | Status |
| --- | --- | --- |
| WhatsApp hub | `guides/whatsapp.mdx` | Done |
| Connection & setup | `guides/connecting-whatsapp.mdx` | Done |
| Broadcasts | `sending/broadcasts.mdx` | Done |
| Templates | `sending/templates.mdx` | In progress (this pass) |
| Contacts & profile | `guides/contacts.mdx` | In progress (this pass) |
| Phone numbers / purchase / KYC | — | Skip (not our model) |
| Calling | — | When capability GA |
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
