---
name: api-request-exporter
description: Generate API requests for backend endpoints that can be imported into Postman or used as curl commands. Use when the user asks to generate an API request, create a Postman request, export an endpoint, or test an API. Defaults to templated curl and can generate Postman Collection v2.1 JSON when requested.
---

# API Request Exporter Workflow

This skill generates **portable, deterministic API requests** for testing and documentation.  
It assumes browser-based authentication by default and avoids guessing auth mechanisms.

Act as a precise API tooling assistant. Prefer correctness, reuse of conventions, and explicit confirmation over convenience.

---

## When to Use This Skill

**Trigger conditions:**
- User mentions testing APIs: "create a request", "curl this endpoint", "Postman request"
- User describes an endpoint (method + path)
- User wants exportable or shareable API requests

Do not invoke this workflow for purely conceptual API discussions.

---

## Hard Invariants (Must Never Be Violated)

The following rules override all other instructions:

1. NEVER generate Postman JSON unless the user explicitly says:
   - "Postman JSON"
   - "Postman collection"
   - "Postman v2.1"
   Saying "add to Postman" or "for Postman" is NOT sufficient.

2. NEVER generate more than ONE request format in a single response.

3. NEVER assume or infer authentication.
   - Do NOT add Authorization headers
   - Do NOT add cookies
   - Do NOT mention authentication at all
   unless the user explicitly specifies the auth mechanism.

4. If authentication is unclear or unspecified:
   - Generate the request WITHOUT auth
   - Do NOT explain or justify the omission

---

## Canonical Variable Registry

The following variables are **pre-approved** and MUST be reused whenever applicable:

### Known variables

- `{{api_url}}` – API base URL
- `{{organizationId}}` – organization id
- `{{projectId}}` - projectId
- `{{integrationId}}` - integrationId
- `{{repoId}}` - repoId
- `{{issueId}}` - issueId
- `{{integrationExternalProjectId}}` - integrationExternalProjectId
- `{{mappingId}}` - mappingId
- `{{repositoryId}}` - repositoryId
- `{{issueDetailId}}` - issueDetailId
- `{{reviewSessionId}}` - reviewSessionId
- `{{feedId}}` - feedId

---

## Authentication Rules (Critical)

### Default Assumption
- **Authentication is cookie-based**
- Cookies are assumed to be automatically attached (browser-originated requests)
- **Do NOT add any auth headers by default**

### When to Add Auth Explicitly
Only add authentication headers if the user explicitly states:
- “bearer auth”
- “Authorization header”
- “API token”
- “service-to-service request”
- “non-browser request”

### If Explicit Auth Is Requested
- Ask which mechanism to use **if unclear**
- Supported explicit mechanisms:
  - Bearer token (`Authorization: Bearer {{TOKEN}}`)
  - API key header (ask for header name)
- Introduce new auth variables **only after confirmation**

---

## Variable Usage Rules (Critical)

1. Always reuse an existing variable from the registry when semantically correct
2. Never introduce new variables silently
3. If no existing variable fits:
   - Pause generation
   - Ask whether a new variable should be introduced
   - Propose 1–2 clear variable names
4. Proceed only after confirmation

---

## Default Output Rules

When generating output, you MUST:

1. Generate **only one format by default**
   - Default: **templated curl**
2. Generate Postman output **only if explicitly requested**
3. Never hardcode secrets, IDs, or environment-specific values
4. Never invent headers, query params, or body fields
5. Keep output copy-pasteable

---

## Output Format

### cURL
```bash
<templated curl>
