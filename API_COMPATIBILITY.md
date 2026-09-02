# API compatibility

The hosted MCP endpoint is stable:

```text
https://mcp.retrodiffusion.ai/mcp
Authorization: Bearer <Retro Diffusion API key>
```

MCP clients do not choose a Retro Diffusion HTTP API version. The hosted server
does that internally and defaults to v2. Existing MCP installations require no
configuration change.

## Error behavior

The hosted implementation accepts the legacy v1 error shapes and the canonical
v2 envelope:

```json
{
  "error": {
    "code": "upstream_failure",
    "message": "The generation provider is temporarily unavailable.",
    "request_id": "request-id"
  }
}
```

It exposes a safe message to the MCP client and retains the HTTP status,
machine-readable code, `X-Request-ID`, optional details, and `Retry-After`
metadata for diagnostics. Provider response bodies, tokens, and other raw
upstream data are not included in client errors or logs.

Read-only requests may be retried once for a transport interruption, rate limit,
or temporary 502/503/504 response. Paid POST operations are not retried
automatically. A failed request is never replayed against another API version,
because that could create duplicate work or charges.

## Version rollout

- v1 remains supported and unchanged.
- v2 is the hosted server default.
- Rollback changes the hosted server setting back to v1; MCP clients stay
  untouched.
- V1 remains available as a rollback and has no retirement plan.

The runnable dual-version examples, generated v2 OpenAPI document, error-code
catalog, and migration guide live in
[Retro-Diffusion/api-examples](https://github.com/Retro-Diffusion/api-examples).
Any future v1 retirement requires a separately approved lifecycle proposal and
notice period.
