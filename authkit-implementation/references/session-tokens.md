# Session Tokens

**Source:** https://workos.com/docs/reference/authkit/session-tokens

---

## JWKS URL

Hosts the public key used for verifying access tokens.

`GET /sso/jwks`

```bash
curl https://api.workos.com/sso/jwks/client_123456789
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `clientId` | string | Your WorkOS Client ID. |

**Returns:** `{ url: string }`

---

## Access Token

The access token returned in successful authentication responses is a JWT that can be used to verify an active session. It is signed by a JWKS retrievable from the WorkOS API.

### Decoded Access Token Example

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

### Access Token JWT Claims

| Claim | Type | Description |
|---|---|---|
| `iss` | string | Issuer (`https://api.workos.com`). |
| `sub` | string | Subject — the user's ID. |
| `act` | object (optional) | Impersonator details (present during impersonation). |
| `org_id` | string | The current organization ID. |
| `role` | string | The user's primary role. |
| `roles` | array | All roles the user has. |
| `permissions` | string[] (optional) | Permission strings assigned to the user. |
| `entitlements` | string[] (optional) | Entitlements assigned to the user. |
| `sid` | string | Session ID. |
| `jti` | string | JWT ID. |
| `exp` | DateTime | Expiration time. |
| `iat` | DateTime | Issued-at time. |

---

## Refresh Token

The refresh token can be used to obtain a new access token via the [Authenticate with Refresh Token](./04-authentication.md#authenticate-with-refresh-token) endpoint. Refresh tokens may only be used once. Refreshes succeed as long as the user's session is still active.