---
name: fetch-docs
description: "Fetch and cache documentation for external APIs and services. Use when integrating with any non-Laravel third-party service, API, or SDK. This skill ensures we read actual documentation instead of making overly specific web searches that produce poor results."
argument-hint: "[service name or docs URL]"
---

## The Problem This Solves

When CC needs to integrate with an external API (like ZenRows, WhatsApp Business, Stripe, etc.), its default behavior is to search Google for "how to use [service] to do [specific thing]" — which produces blog posts and Stack Overflow answers instead of actual documentation.

## The Process

### Step 1: Find the Official Documentation
- Check if Boost's `search-docs` covers it (it covers Laravel ecosystem packages)
- If not, look for the service's official docs URL
- Check if they have an OpenAPI/Swagger spec or `llms.txt`

### Step 2: Read the Actual Docs
- Fetch the documentation index/overview page
- Navigate to the specific section relevant to the integration
- Read the API reference for the endpoints/methods needed

### Step 3: Cache Key Learnings
After reading the docs, create a summary file:

```
.ai/docs/[service-name].md
```

Include:
- Base URL and authentication method
- Key endpoints/methods we use
- Request/response formats
- Rate limits or important constraints
- Code examples relevant to our use case

### Example

```markdown
# .ai/docs/stripe.md

## Stripe API
- Base URL: https://api.stripe.com/v1/
- Auth: Bearer token via `Authorization: Bearer sk_test_...`
- Rate limit: 100 read requests/sec, 100 write requests/sec

## Key Endpoints
- `POST /customers` — Create a customer
- `POST /checkout/sessions` — Create a Checkout session
- `POST /subscriptions` — Create a subscription
- `GET /invoices` — List invoices for a customer

## Our Usage
- We use Stripe for payment processing via Checkout sessions
- Webhooks handled at `/webhooks/stripe` (verified via signature)
- Using `cashier` package for subscription management
- All amounts in cents (e.g., $10.00 = 1000)
```

### Step 4: Reference in Future Sessions
In CLAUDE.md or relevant skill, add a pointer:
```
For ZenRows API integration details, see .ai/docs/zenrows.md
```

## DO NOT
- Search Google for "how to use X to do Y specifically"
- Rely on blog posts or tutorials as primary documentation source
- Assume API behavior without reading the docs
- Skip caching — the next session will need the same information
