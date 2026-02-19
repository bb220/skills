# Session Helpers

After authenticating and storing the encrypted session as a cookie, session helper methods make it easy to retrieve, validate, and refresh the session. Sessions are automatically "sealed" (encrypted) using a strong password that must be at least 32 characters.

> **Note:** Session helper methods (`load_sealed_session`, `session.authenticate()`, `session.refresh()`, `session.get_log_out_url()`) are currently available in the **JavaScript/Node.js SDK only**. For Python, use `authenticate_with_refresh_token()` and `get_logout_url()` directly. See the [Authentication](https://workos.com/docs/reference/authkit/authentication) and [Logout](https://workos.com/docs/reference/authkit/logout) references.

---

## Python Session Pattern

While the sealed session helpers are JS-only, here is the recommended Python pattern for managing sessions using the WorkOS Python SDK.

### Step 1 – Authenticate and Store Session

```python
import workos
import json

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# After redirect from AuthKit, exchange the code for tokens
result = client.user_management.authenticate_with_code(
    code=request.args.get("code"),
)

# Store tokens in a secure HTTP-only session/cookie
session_data = {
    "access_token": result.access_token,
    "refresh_token": result.refresh_token,
    "user_id": result.user.id,
}
# e.g. flask.session["workos"] = session_data
```

### Step 2 – Validate Session on Each Request

```python
import workos
from joserfc import jwt
from joserfc.jwk import KeySet
import requests

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Cache this at startup
jwks_url = client.user_management.get_jwks_url(client_id="client_123456789")
jwks_response = requests.get(jwks_url)
key_set = KeySet.import_key_set(jwks_response.json())

def validate_session(access_token, refresh_token):
    try:
        token = jwt.decode(access_token, key_set)
        # Token is valid
        return token.claims
    except Exception:
        # Token expired — attempt refresh
        try:
            refresh_result = client.user_management.authenticate_with_refresh_token(
                refresh_token=refresh_token,
            )
            return {
                "access_token": refresh_result.access_token,
                "refresh_token": refresh_result.refresh_token,
            }
        except Exception:
            # Refresh failed — user must re-authenticate
            return None
```

### Step 3 – End a Session

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Extract session ID from the JWT claims (sid)
logout_url = client.user_management.get_logout_url(
    session_id="session_01HQSXZGF8FHF7A9ZZFCW4387R",
    return_to="https://your-app.com/signed-out",
)

# Redirect the user's browser to logout_url
```

---

## JavaScript SDK Reference (for context)

These methods are available in the Node.js SDK and documented here for reference when coordinating Python backend + JS frontend architectures.

### `loadSealedSession()`

Loads a sealed (encrypted) session from a cookie.

```js
const session = await workos.userManagement.loadSealedSession({
  sessionData: 'sealed_session_cookie_data',
  cookiePassword: 'password_previously_used_to_seal_session_cookie',
});
```

**Returns:** A session object with `authenticate()`, `refresh()`, and `getLogOutUrl()` methods.

---

### `session.authenticate()`

Unseals the session data and checks if the session is still valid.

```js
const authResponse = await session.authenticate();

if (authResponse.authenticated) {
  const { sessionId, organizationId, role, permissions, user } = authResponse;
} else {
  if (authResponse.reason === 'no_session_cookie_provided') {
    // Redirect to login
  }
}
```

**Returns**

| Field            | Type      | Description                                                           |
|------------------|-----------|-----------------------------------------------------------------------|
| `authenticated`  | `boolean` | Whether the session is valid.                                         |
| `accessToken`    | `string`  | Current access token.                                                 |
| `sessionId`      | `string`  | Session ID (same as `sid` JWT claim).                                 |
| `user`           | `User`    | The authenticated user object.                                        |
| `organizationId` | `string`  | Organization scoped to this session.                                  |
| `role`           | `string`  | User's role in the organization.                                      |
| `roles`          | `array`   | All roles assigned to the user.                                       |
| `permissions`    | `array`   | Permissions granted to the user.                                      |
| `entitlements`   | `array`   | Feature entitlements.                                                 |
| `featureFlags`   | `array`   | Feature flags.                                                        |
| `impersonator`   | `object`  | Present if session is an impersonation session.                       |
| `reason`         | `string`  | Failure reason if `authenticated` is false.                           |

**Failure reasons:** `"invalid_jwt"`, `"invalid_session_cookie"`, `"no_session_cookie_provided"`

---

### `session.refresh()`

Refreshes the user's session using the stored refresh token. Pass a new `organizationId` to switch the user to a different organization.

```js
const refreshResult = await session.refresh({ organizationId: 'org_123' });

if (refreshResult.authenticated) {
  const { sealedSession, user, organizationId } = refreshResult;
  // Store sealedSession back in the cookie
}
```

**Parameters**

| Parameter        | Type     | Required | Description                                    |
|------------------|----------|----------|------------------------------------------------|
| `cookiePassword` | `string` | Optional | Password to re-seal the refreshed session.     |
| `organizationId` | `string` | Optional | Switch session to a different organization.    |

**Returns:** Same fields as `session.authenticate()` plus `sealedSession` (the new encrypted session cookie value).

---

### `session.getLogOutUrl()`

Generates the logout URL by extracting the session ID automatically from the session data.

```js
const logOutUrl = await session.getLogOutUrl();
// Redirect the user's browser to this URL
```

**Returns:** `string` — The logout redirect URL.

---

## Cookie Password Requirements

The cookie encryption password must be **at least 32 characters**. Generate one securely:

```bash
openssl rand -base64 24
```

---

## Related

- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Session Tokens](https://workos.com/docs/reference/authkit/session-tokens)
- [Session](https://workos.com/docs/reference/authkit/session)
- [Logout](https://workos.com/docs/reference/authkit/logout)