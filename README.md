# Identity Server Workshop

Step-by-step implementation of an OAuth2/OIDC Identity Server with multiple client types.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Identity Server | ASP.NET Core 8 + OpenIddict |
| Protected API | ASP.NET Core 8 Web API |
| MVC Client | ASP.NET Core 8 MVC |
| SPA Client | Vue 3 + Vite |
| Mobile Client | Flutter |

## Project Structure

```
src/
  IdentityServer/       # OAuth2/OIDC server (OpenIddict)
  ApiResource/          # Protected API resource
  Clients/
    MvcClient/          # Server-side MVC web app
    VueClient/          # Vue.js SPA (Authorization Code + PKCE)
    FlutterClient/      # Flutter mobile app
docs/
  flows/                # OAuth flow diagrams and explanations
```

## Learning Path

### Phase 1 — The Identity Server (Steps 1–4)
- **Step 1**: Project setup, OpenIddict, user store, `/token` endpoint (Client Credentials flow)
- **Step 2**: Authorization Code flow — login page, consent, code exchange
- **Step 3**: PKCE extension — securing public clients (SPA, mobile)
- **Step 4**: Refresh tokens — token lifecycle management

### Phase 2 — Client Integrations (Steps 5–8)
- **Step 5**: .NET Core API — JWT validation middleware, protected endpoints
- **Step 6**: MVC Client — Authorization Code flow (server-side)
- **Step 7**: Vue.js SPA — Authorization Code + PKCE (public client)
- **Step 8**: Flutter Mobile — Authorization Code + PKCE (native app)

### Phase 3 — Production Patterns (Steps 9–11)
- **Step 9**: Scopes, Claims, Roles — fine-grained access control
- **Step 10**: External providers — Google / Microsoft login
- **Step 11**: Token introspection, revocation, security hardening

## OAuth Flows Covered

| Flow | Used By | When |
|------|---------|------|
| Client Credentials | Machine-to-machine APIs | No user involved |
| Authorization Code | MVC Client, APIs | Server-side apps |
| Authorization Code + PKCE | Vue SPA, Flutter | Public clients (no secret) |
| Refresh Token | All clients | Silent token renewal |

---

> Start with `docs/flows/01-concepts.md` to understand the theory before touching code.
