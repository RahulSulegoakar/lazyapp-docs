# LazyApp documentation

The [Mintlify](https://mintlify.com) site for the LazyApp WhatsApp API.

## Local development

```bash
npm i -g mint
mint dev
```

Serves on http://localhost:3000.

## Structure

| Path | Contents |
| --- | --- |
| `docs.json` | Navigation, theme, tabs |
| `index.mdx`, `quickstart.mdx`, `authentication.mdx` | Getting started |
| `concepts/` | Workspaces, conventions, idempotency, pagination, errors, capabilities |
| `sending/` | Messages, templates, media, events, verify, broadcasts |
| `webhooks/` | Overview, signature verification, event reference |
| `platform/` | Reselling: customers, setup links, embedded components |
| `migrating/` | Moving from Meta's Cloud API |
| `reference/` | Scopes, rate limits, versioning, changelog |
| `api-reference/openapi.json` | The endpoint reference Mintlify renders |

## Keeping it true

The application repo is the source of truth. Before changing anything factual, confirm it there — see [AGENTS.md](./AGENTS.md) for the file-by-file map.

## Checks

```bash
mint broken-links
python3 -c "import json; json.load(open('api-reference/openapi.json'))"
```

## Deployment

Pushes to `main` deploy through the Mintlify GitHub app.
