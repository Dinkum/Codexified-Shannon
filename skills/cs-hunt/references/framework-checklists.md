# Framework Checklists

Use this file only when `bootstrap/handoff.json` identifies a matching framework. These are prompts, not a rigid matrix. Extend them when the repo's actual stack suggests better checks.

## Next.js

- API routes, route handlers, and server actions missing auth or authz middleware
- `getServerSideProps`, server actions, or loaders leaking secrets or internal data
- `next.config.js` rewrites, redirects, headers, and image proxy behavior
- `dangerouslySetInnerHTML`, MDX, markdown rendering, and preview or draft mode
- differences between middleware coverage and route-level protection

## Express, Fastify, Or NestJS

- sensitive routes missing middleware attachment
- `trust proxy`, permissive CORS, missing `helmet`, weak cookie flags, or session defaults
- proxy, webhook, file upload, and raw `res.render` or `res.send` paths
- decorator or route-level guards that are not backed by service-layer checks

## Django

- `@csrf_exempt`, `DEBUG=True`, `mark_safe`, open redirects, and raw SQL
- DRF or class-based view querysets missing object ownership or tenant scoping
- file upload, template rendering, signed URL generation, and admin exposure

## Flask Or FastAPI

- debug mode, permissive CORS, weak session or cookie handling
- dependency-based auth gaps, object access after path-param parsing, or response-model leaks
- Jinja rendering, SSRF-style proxy routes, file upload and download paths

## Gin Or Echo

- middleware attached to one route group but not another
- binding or validation assumptions that do not hold at the handler sink
- template rendering, reverse proxies, uploads, downloads, and admin routes

## Generic SPA Plus API Split

- auth enforced in frontend routes but missing in backend API routes
- admin or support APIs assumed to be unreachable because the UI hides them
- file upload, markdown, and rich-text preview paths split between client and server
