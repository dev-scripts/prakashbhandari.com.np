---
title: "Centralized Identity with OAuth 2.0 Token Exchange (RFC 8693)"
date: 2026-03-22T14:00:00+11:00
showToc: true
TocOpen: true
tags: [
  "Security",
  "OAuth2",
  "Identity",
  "JWT",
  "SSO"
]
categories: [
  "Security",
  "Identity & Access Management"
]
keywords: [
  "OAuth 2.0 Token Exchange",
  "RFC 8693",
  "centralized identity provider",
  "SSO single sign-on",
  "JWT token exchange",
  "just-in-time provisioning",
  "post-acquisition identity management",
  "IdP token exchange Node.js",
  "multi-tenant identity",
  "token exchange endpoint",
  "OAuth2 grant type",
  "cross-company SSO",
  "identity federation",
  "JWT audience validation",
  "microservices authorization"
]
cover:
  image: "images/posts/oauth2-token-exchange-rfc8693-centralized-identity-sso/oauth2-token-exchange-rfc8693-centralized-identity-sso.png"
  alt: "Centralized Identity with OAuth 2.0 Token Exchange (RFC 8693)"
  caption: "Centralized Identity with OAuth 2.0 Token Exchange (RFC 8693)"
  relative: false
author: "Prakash Bhandari"
description: "Behind every seamless cross-system login is a token exchange. Learn how OAuth 2.0 Token Exchange (RFC 8693) lets a central identity provider issue system-specific JWTs for SSO across multiple applications, including post-acquisition identity management."
---

## Introduction

Managing user identity across multiple systems is one of the hardest problems in modern software architecture. Especially when those systems belong to different companies. **OAuth 2.0 Token Exchange (RFC 8693)** is the standardized solution: it lets a central identity provider (IdP) authenticate a user once and issue system-specific access tokens for any downstream application, even across organizational boundaries.

In this post, we'll explore how to build a **central IdP system**, exchange tokens for different applications, and handle users who may not exist in every system — a problem often seen **after a company acquisition**.

---

## What Is OAuth 2.0 Token Exchange (RFC 8693)?

**OAuth 2.0 Token Exchange** is an extension to the OAuth 2.0 framework, standardized in [RFC 8693](https://datatracker.ietf.org/doc/html/rfc8693). It defines a new grant type that allows a client to exchange one security token for another — typically to obtain a token scoped to a specific audience or system.

The core grant type identifier is:

```
urn:ietf:params:oauth:grant-type:token-exchange
```

Unlike a standard authorization code flow where a user interactively logs in, token exchange happens server-to-server. The user authenticates once at a central IdP, and their token is then *exchanged* for a new token valid for a specific downstream application — all transparently, with no additional login prompts.

**Key use cases for OAuth 2.0 Token Exchange:**

- **Post-acquisition SSO** :  seamlessly onboard users from an acquired company without merging databases
- **Microservices authorization** :  derive service-scoped tokens from a user's session token
- **Multi-tenant SaaS** : issue per-tenant tokens with tenant-specific roles and permissions
- **Cross-organization access** : federate identity across partner systems

## The Post-Acquisition Identity Problem

Imagine your company just acquired another business. Overnight, you have thousands of new users who need access to your internal tools — but their accounts only exist in the acquired company's identity store.

Your options without token exchange are painful:

- **Force new account creation** : bad user experience, support overhead, confusion
- **Full database migration** : slow, error-prone, and risky for data integrity
- **Manual provisioning** : doesn't scale, creates a backlog from day one

OAuth 2.0 Token Exchange solves this elegantly. Users authenticate against their existing IdP. That token is then exchanged for a token valid in your system. If they don't have a record yet, **Just-In-Time provisioning** creates one automatically. No migration required.

## Architecture: Central IdP with Token Exchange
I have added architecture diagram above. Here we can discuss about the architecture of the system. There are three components:

- **Central IdP** :  the single source of truth for authentication. It issues primary tokens (JWT_A) and handles all token exchange requests. Every user — across every company — is known here.
- **Downstream applications** : each app trusts only tokens issued for its own `audience`. They validate tokens independently, with no dependency on the IdP at request time.
- **Token exchange endpoint** :  accepts a subject token, resolves the user's identity in the target system, and returns a new audience-scoped token (JWT_B).


## Step-by-Step: The OAuth 2.0 Token Exchange Flow

### Step 1 - User Authenticates at the Central IdP

The user visits Company B's app and is redirected to the central IdP. After a successful login (password, MFA, or federated SSO), the IdP issues a primary access token — **JWT_A**:

```json
{
  "sub": "user123",
  "email": "alice@companya.com",
  "iss": "https://auth.yourcompany.com",
  "exp": 1711234567
}
```

JWT_A is a short-lived token (5–10 minutes) tied to the IdP. It is **not** directly accepted by Company B.

### Step 2 - Token Exchange Request (RFC 8693)

Company B's app sends JWT_A to the token exchange endpoint and requests a token scoped to its own audience:

```http
POST /oauth/token
Content-Type: application/x-www-form-urlencoded

grant_type=urn:ietf:params:oauth:grant-type:token-exchange
&subject_token=<JWT_A>
&subject_token_type=urn:ietf:params:oauth:token-type:access_token
&requested_token_type=urn:ietf:params:oauth:token-type:access_token
&audience=companyB
```

> **Important:** RFC 8693 requires `application/x-www-form-urlencoded` encoding — not JSON. Using JSON will cause the exchange to fail.

### Step 3 - IdP Validates the Subject Token

The IdP performs the following checks on JWT_A:

1. Verifies the RS256 signature using its own public key
2. Confirms the token has not expired
3. Extracts the user's `sub` and `email` claims
4. Checks whether this user exists in Company B's system
5. Triggers JIT provisioning if no record is found (optional)

### Step 4 -IdP Issues a System-Specific Token (JWT_B)

The token exchange response follows the RFC 8693 format:

```json
{
  "access_token": "<JWT_B>",
  "issued_token_type": "urn:ietf:params:oauth:token-type:access_token",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

JWT_B is scoped to Company B and contains that system's specific roles and permissions:

```json
{
  "sub": "user123",
  "email": "alice@companya.com",
  "aud": "companyB",
  "roles": ["viewer"],
  "permissions": ["read:orders", "read:products"],
  "iss": "https://auth.yourcompany.com",
  "iat": 1711230967,
  "exp": 1711234567
}
```

### Step 5 - Company B Validates JWT_B and Grants Access

Company B verifies JWT_B by checking:

- **Signature** — using the IdP's public key
- **`aud` claim** — must equal `companyB` exactly
- **Expiry** — token must not be expired

It then enforces access using the `roles` and `permissions` claims. Done — the user is in, with no login prompt and no manual provisioning.

## Just-In-Time (JIT) User Provisioning

When a user is authenticated by the central IdP but has no record in the target system, JIT provisioning creates one automatically on first access.

```javascript
async function getOrProvisionUser(decoded, targetSystem) {
  let user = await db.findUserByExternalId(decoded.sub, targetSystem);

  if (!user) {
    console.log(`[JIT] Provisioning ${decoded.sub} for ${targetSystem}`);
    user = await db.createUser({
      external_id: decoded.sub,
      email: decoded.email,
      system: targetSystem,
      role: 'viewer',            // least privilege default
      provisioned_at: new Date(),
      provisioned_by: 'jit'
    });
  }

  return user;
}
```

**JIT provisioning best practices:**

- **Always default to the least privileged role** — a new user should have `viewer` access until an admin grants more
- **Log every provisioning event** — you need an audit trail for compliance and debugging
- **Use a `pending` state for sensitive systems** — create the account but require admin approval before granting access
- **Sync profile data** — use the token claims to populate the user's name and email automatically

## Node.js Implementation

Here is a production-ready token exchange endpoint using Express and `jsonwebtoken`:

```javascript
const express = require('express');
const jwt = require('jsonwebtoken');

const app = express();
app.use(express.urlencoded({ extended: true })); // RFC 8693: must use form encoding

const ALLOWED_AUDIENCES = ['companyA', 'companyB', 'companyC'];

app.post('/oauth/token', async (req, res) => {
  const { grant_type, subject_token, audience } = req.body;

  if (grant_type !== 'urn:ietf:params:oauth:grant-type:token-exchange') {
    return res.status(400).json({ error: 'unsupported_grant_type' });
  }

  if (!subject_token || !audience) {
    return res.status(400).json({
      error: 'invalid_request',
      error_description: 'subject_token and audience are required'
    });
  }

  let decoded;
  try {
    decoded = jwt.verify(subject_token, process.env.IDP_PUBLIC_KEY, {
      algorithms: ['RS256'],
      issuer: 'https://auth.yourcompany.com'
    });
  } catch (err) {
    return res.status(401).json({ error: 'invalid_token', error_description: err.message });
  }

  if (!ALLOWED_AUDIENCES.includes(audience)) {
    return res.status(400).json({ error: 'invalid_target', error_description: 'Unknown audience' });
  }

  let user;
  try {
    user = await getOrProvisionUser(decoded, audience);
  } catch (err) {
    console.error('JIT provisioning failed:', err);
    return res.status(500).json({ error: 'server_error' });
  }

  const exchangedToken = jwt.sign(
    {
      sub: decoded.sub,
      email: decoded.email,
      aud: audience,
      roles: [user.role],
      permissions: getRolePermissions(user.role),
      iss: 'https://auth.yourcompany.com'
    },
    process.env.IDP_PRIVATE_KEY,
    { algorithm: 'RS256', expiresIn: '1h' }
  );

  return res.json({
    access_token: exchangedToken,
    issued_token_type: 'urn:ietf:params:oauth:token-type:access_token',
    token_type: 'Bearer',
    expires_in: 3600
  });
});

function getRolePermissions(role) {
  const permissionMap = {
    viewer:  ['read:orders', 'read:products'],
    editor:  ['read:orders', 'read:products', 'write:orders'],
    admin:   ['read:orders', 'read:products', 'write:orders', 'manage:users']
  };
  return permissionMap[role] ?? [];
}
```

## More Real-World Examples

| Scenario | Subject Token (JWT_A) | Exchanged Token (JWT_B) |
|---|---|---|
| Post-acquisition onboarding | Company A employee token | Company B viewer token, JIT provisioned |
| Microservice delegation | User session token | Service-scoped token with limited permissions |
| Partner portal access | Internal staff token | Partner system token with read-only access |
| Multi-tenant SaaS | Platform-level token | Tenant-specific token with tenant roles |

## Security Considerations

Getting token exchange right means getting the security right. These are non-negotiable:

**Validate the `aud` claim without exception.** A JWT_B issued for Company B must be rejected by Company A. Skipping audience validation means a stolen token can be replayed against any system that trusts your IdP.

**Use asymmetric signing (RS256 or ES256).** Symmetric `HS256` requires every app to share the same secret — which defeats the purpose of scoped tokens. With RS256, downstream apps verify using the IdP's *public* key only.

**Keep token lifetimes short.** Subject tokens should expire in 5–10 minutes. Exchanged tokens should be no longer than 1 hour. Long-lived JWTs extend the blast radius of any compromise.

**Maintain an explicit audience allowlist.** Never exchange a token for an unrecognised audience. An attacker who can register a new `audience` value could extract identity data from your IdP.

**Audit every exchange.** Log the subject `sub`, the requested `audience`, whether JIT provisioning fired, and whether the exchange succeeded or failed.

**Plan for revocation.** JWTs are stateless — once issued, you can't un-issue them. For high-security systems, maintain a short-lived blocklist or use opaque tokens with RFC 7662 token introspection.

## When to Use This Pattern

This pattern is a strong fit when:

- You're managing identity across a **company acquisition or merger**
- You run a **multi-tenant SaaS platform** where each tenant has its own roles and data boundaries
- You have **microservices** that need narrowly scoped tokens derived from a user's session
- You need to **federate access** across partner organizations without exposing your user database

It is probably overkill when you have a single application, a single user store, and no cross-system access requirements. A standard authorization code flow is simpler and easier to audit in that case.

## Conclusion

OAuth 2.0 Token Exchange (RFC 8693) is a powerful but underused tool in the identity toolkit. By separating authentication (who you are) from authorization (what you can do in this system), it gives you a clean, scalable way to federate identity across organizational boundaries.

Whether you're managing the fallout of a company acquisition, building a multi-tenant SaaS platform, or chaining microservices together, the pattern is the same: authenticate once, exchange as needed, enforce locally.

As identity complexity grows more acquisitions, more SaaS integrations, more microservices — RFC 8693 will become foundational knowledge for every engineer working on authentication. The future of identity isn't one giant user database. It's many systems, each enforcing their own rules, coordinated by a central authority that everyone trusts.


---