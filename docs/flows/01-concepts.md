# OAuth2 & OIDC — Core Concepts

## The Problem OAuth Solves

Before OAuth, if App B needed access to your data on App A, you gave App B your App A password. That's terrible — App B can do anything, forever, and you can't revoke it without changing your password.

OAuth solves this with **delegated authorization**: App A issues a limited, revocable token to App B instead.

---

## The 4 Roles

| Role | Also Called | In Our Project |
|------|-------------|----------------|
| Resource Owner | The User | You (Mahmoud) |
| Client | The App | Vue app, MVC app, Flutter app |
| Authorization Server | Identity Server | Our OpenIddict server |
| Resource Server | The API | Our .NET Core API |

---

## Tokens

### Access Token
- Proves the client has permission to call the API
- Short-lived (minutes to hours)
- Sent in every API request: `Authorization: Bearer <token>`
- We use **JWT** format — self-contained, signed, verifiable without calling the identity server

### Refresh Token
- Used to get a new access token without asking the user to login again
- Long-lived (days to weeks)
- Only given to trusted clients

### ID Token (OIDC only)
- Proves the user's identity to the client
- Contains claims: name, email, sub (user ID)
- Client reads this — it's not sent to the API

---

## JWT Structure

A JWT is 3 base64-encoded parts separated by dots:

```
eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiIxMjMiLCJuYW1lIjoiTWFobW91ZCIsImV4cCI6MTcwMH0.SIGNATURE
     HEADER                              PAYLOAD                                    SIGNATURE
```

**Header**: algorithm used to sign (`RS256` = RSA + SHA256)  
**Payload**: claims (who, what scopes, expiry)  
**Signature**: identity server signs with its private key — API verifies with public key

The API never needs to call the identity server to validate a JWT. It just checks the signature locally. That's what makes JWTs scalable.

---

## OAuth Flows — When to Use Which

### Client Credentials Flow
```
Client ──────────────────────────────► Identity Server
        client_id + client_secret
                                        ◄──────────────
                                          Access Token
Client ──────────────────────────────► API
        Authorization: Bearer <token>
```
**Use when**: No user involved. Background services, microservices calling each other.

---

### Authorization Code Flow
```
User clicks "Login" in Client App
Client ──────────────────────────────► Identity Server  (redirect)
        client_id, scope, redirect_uri

User enters credentials on Identity Server login page

Identity Server ─────────────────────► Client  (redirect back)
                  ?code=ABC123

Client ──────────────────────────────► Identity Server  (back channel)
        code + client_secret

Identity Server ─────────────────────► Client
                  Access Token + Refresh Token + ID Token
```
**Use when**: Server-side apps (MVC) that can safely store a client secret.

---

### Authorization Code + PKCE
Same as above but replaces the client secret with a **code_verifier/code_challenge** pair:

1. Client generates random `code_verifier`
2. Client hashes it → `code_challenge = SHA256(code_verifier)`
3. Sends `code_challenge` at the start of the flow
4. Sends `code_verifier` when exchanging the code
5. Identity Server verifies the hash matches

**Use when**: Public clients that can't keep a secret — Vue SPA, Flutter app.

---

## What We Build in Step 1

The simplest possible identity server endpoint — Client Credentials flow:

```
POST /connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id=myapi
&client_secret=mysecret
&scope=api

Response:
{
  "access_token": "eyJ...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

This is the foundation. Every other flow builds on this.
