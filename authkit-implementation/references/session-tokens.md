# Session Tokens

Access tokens and refresh tokens are returned in successful authentication responses. The access token is a JWT signed by WorkOS, and the refresh token can be exchanged for a new access token.

---

## Access Token

The access token is a JSON Web Token (JWT) that can be used to verify a user's active session. It is signed by a JWKS hosted by WorkOS. Validate it on each request using a JWT library such as `PyJWT` or `python-jose`.

### Decoded Access Token

```json
{
  "iss": "https://api.workos.com",
  "sub": "user_01HBEQKA6K4QJAS93VPE39W1JT",
  "act": {
    "sub": "admin@foocorp.com"
  },
  "org_id": "org_01HRDMC6CM357W30QMHMQ96Q0S",
  "role": "member",
  "roles": ["member"],
  "permissions": ["posts:read", "posts:write"],
  "entitlements": ["audit-logs"],
  "sid": "session_01HQSXZGF8FHF7A9ZZFCW4387R",
  "jti": "01HQSXZXPPFPKMDD32RKTFY6PV",
  "exp": 1709193857,
  "iat": 1709193557
}
```

### JWT Claims

| Claim          | Type       | Description                                                                       |
|----------------|------------|-----------------------------------------------------------------------------------|
| `iss`          | `str`      | Issuer — always `https://api.workos.com`.                                         |
| `sub`          | `str`      | Subject — the user's WorkOS ID.                                                   |
| `act`          | `object`   | Present during impersonation; contains the impersonator's email as `sub`.         |
| `org_id`       | `str`      | The organization the session is scoped to.                                        |
| `role`         | `str`      | The primary role of the user's organization membership.                           |
| `roles`        | `list`     | All roles assigned to the user in the organization.                               |
| `permissions`  | `list`     | Permissions granted to the user in the organization.                              |
| `entitlements` | `list`     | Feature entitlements assigned to the user.                                        |
| `sid`          | `str`      | Session ID — use this when calling logout or revoking sessions.                   |
| `jti`          | `str`      | JWT ID — unique identifier for this specific token.                               |
| `exp`          | `int`      | Expiration timestamp (Unix epoch).                                                |
| `iat`          | `int`      | Issued-at timestamp (Unix epoch).                                                 |

---

## Refresh Token

The refresh token is returned alongside the access token in successful authentication responses. It can be used to obtain a new access token once the current one expires.

- Refresh tokens may only be used **once** (they rotate after use).
- Refreshes succeed as long as the user's session is still active.
- Store refresh tokens securely on the backend (database, cache, or secure HTTP-only cookie).

See [Authenticate with Refresh Token](https://workos.com/docs/reference/authkit/authentication) for usage.

---

## Get JWKS URL

Retrieves the URL of the JSON Web Key Set (JWKS) used to verify access tokens.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

jwks_url = client.user_management.get_jwks_url(client_id="client_123456789")
# Returns: "https://api.workos.com/sso/jwks/client_123456789"
```

**Parameters**

| Parameter   | Type  | Required | Description             |
|-------------|-------|----------|-------------------------|
| `client_id` | `str` | Required | Your WorkOS client ID.  |

**Returns:** `str` — The JWKS URL.

---

## Verifying an Access Token

Use a JWT library to fetch the JWKS and validate the access token on each request.

```python
import requests
from joserfc import jwt
from joserfc.jwk import KeySet
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Fetch and cache the JWKS (cache this in production)
jwks_url = client.user_management.get_jwks_url(client_id="client_123456789")
response = requests.get(jwks_url)
key_set = KeySet.import_key_set(response.json())

# Validate the access token
token = jwt.decode(access_token, key_set)
claims = token.claims

user_id = claims["sub"]
org_id = claims.get("org_id")
role = claims.get("role")
permissions = claims.get("permissions", [])
```

> **Tip:** Cache the JWKS and refresh it periodically rather than fetching on every request.

---

## Related

- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Session](https://workos.com/docs/reference/authkit/session)
- [Session Helpers](https://workos.com/docs/reference/authkit/session-helpers)
- [Logout](https://workos.com/docs/reference/authkit/logout)