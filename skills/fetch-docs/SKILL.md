---
name: fetch-docs
description: "Fetch and cache documentation for external APIs and services. Use when integrating with any non-Laravel third-party service, API, or SDK. This skill ensures we read actual documentation instead of making overly specific web searches that produce poor results."
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
# .ai/docs/zenrows.md

## ZenRows API
- Base URL: https://api.zenrows.com/v1/
- Auth: API key as query parameter `?apikey=YOUR_KEY`
- Rate limit: Depends on plan

## Key Endpoints
- `GET /` — Scrape a URL. Params: `url`, `js_render`, `premium_proxy`, `css_extractor`
- CSS extractors return structured JSON when provided

## Our Usage
- We use ZenRows for scraping real estate platforms
- Always use `js_render=true` for SPA sites
- Use `premium_proxy=true` for sites with anti-bot protection
- CSS extractors are defined per-platform in our scraper classes
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
