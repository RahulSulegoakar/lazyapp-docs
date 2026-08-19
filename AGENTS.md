# LazyApp documentation

Mintlify site for the LazyApp WhatsApp API. Pages are MDX with YAML frontmatter; config is `docs.json`.

## The one rule that matters

**This site documents a real API. Never write an endpoint, field, enum value, header or status code you have not confirmed in the `lazyapp` application repo.**

The application is the source of truth, at `../lazyapp`:

| To confirm | Read |
| --- | --- |
| Endpoints, methods, required scope | `routes/v1.php` |
| Request fields and validation | `app/Http/Requests/Api/V1/*.php` |
| Response shapes | `app/Http/Controllers/Api/V1/*.php` (`payload()` methods) |
| Webhook event types | `app/Enums/**/CustomerWebhookEvent.php` |
| Scopes | `app/Enums/ApiKeyScope.php` |
| Response headers | `app/Http/Middleware/AddApiVersionHeaders.php`, `AssignRequestId.php` |
| Rate limits | `config/messaging.php`, `app/Providers/AppServiceProvider.php` |
| Idempotency behaviour | `app/Http/Middleware/EnforceIdempotency.php` |
| Webhook signing and headers | `app/Domain/Delivery/PerformWebhookDelivery.php` |
| Product intent | `lazyapp-prd.md` |

If a doc and the code disagree, the code wins — fix the doc, or fix the code and then the doc. Do not split the difference.

## The API reference is generated

`api-reference/openapi.json` is the endpoint reference. Mintlify renders it into the **API reference** tab.

When the API changes, update the spec — do not hand-write endpoint pages in MDX that duplicate it. Keep `operationId`-free paths consistent with the route table so links stay stable.

## Terminology

- **Workspace**, not project, tenant or account.
- **Connection** for a WhatsApp Business Account plus its numbers.
- **Mode** for `live` / `test`. Never "environment".
- **Platform customer** for a reseller's customer. Plain "customer" means the business using LazyApp.
- **Contact** for the person being messaged. Never "user" — a user is someone who logs into LazyApp.
- **Template**, never "message template" after first use on a page.

## Style

- Active voice, second person.
- Sentence case headings.
- One idea per sentence. Cut hedging.
- Explain *why*, not just *how* — the 24-hour window, why cursors beat offsets, why a capability is false. That is what makes a doc worth reading twice.
- Lead with the code sample when a reader clearly came for the code.
- Bold for UI elements: click **Settings**.
- Code formatting for fields, endpoints, headers, files.
- Phone numbers in examples: E.164, Indian, e.g. `+919812345670`.
- Example domains: `acme.example` for a customer, `yourapp.example` for the reader's app.
- Do not invent limits or defaults. Confirm them.

## Content boundaries

Document the public API and the console features a customer uses. Do not document:

- Filament admin panels or internal operator tooling
- Deployment, infrastructure or Laravel Cloud configuration
- Internal queue names, table names or class names
- Anything gated off by default without saying it is gated and why
