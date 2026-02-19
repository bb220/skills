# Authentication

Authenticate a user with a specified authentication method.

**Source:** https://workos.com/docs/reference/authkit/authentication

---

## Get an Authorization URL

Generates an OAuth 2.0 authorization URL to authenticate a user with AuthKit or SSO.

`GET /user_management/authorize`

```bash
curl https://api.workos.com/user_management/authorize -G \
  -d response_type=code \
  -d client_id=client_123456789 \
  -d redirect_uri=https://your-app.com/callback \
  -d state=dj1kUXc0dzlXZ1hjUQ== \
  -d connection_id=conn_01E4ZCR3C56J083X43JQXF3JK5
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `response_type` | `"code"` | ✓ | Must be `"code"`. |
| `client_id` | string | ✓ | Your WorkOS Client ID. |
| `redirect_uri` | string | ✓ | Where to redirect after authentication. |
| `code_challenge` | string | optional | PKCE code challenge. |
| `code_challenge_method` | `"S256"` | optional | PKCE method. |
| `connection_id` | string | optional | SSO connection ID. |
| `organization_id` | string | optional | Organization ID. |
| `provider` | string | optional | `"authkit"`, `"AppleOAuth"`, `"GitHubOAuth"`, `"GoogleOAuth"`, or `"MicrosoftOAuth"`. |
| `state` | string | optional | Arbitrary state passed back to redirect URI. |
| `login_hint` | string | optional | Pre-fill the user's email. |
| `domain_hint` | string | optional | Hint for the user's domain. |
| `screen_hint` | string | optional | `"sign-up"` or `"sign-in"`. |
| `provider_scopes` | array | optional | Additional OAuth scopes. |

**Returns:** `{ url: string }`

If using AuthKit, set `provider` to `"authkit"`. Otherwise, specify one of `connection_id`, `organization_id`, or `provider` (mutually exclusive).

### Redirect URI

WorkOS appends a `code` query parameter to the redirect URI after a successful authentication. Optionally, a `state` value is also passed back. Example:

```
https://your-app.com/callback?code=01E2RJ4C05B52KKZ8FSRDAP23J&state=dj1kUXc0dzlXZ1hjUQ==
```

Configure allowed redirect URIs in the [WorkOS Dashboard](https://dashboard.workos.com/redirects). Production environments require HTTPS (except `http://127.0.0.1` for native clients).

### PKCE

The [PKCE](https://datatracker.ietf.org/doc/html/rfc7636) flow extends OAuth 2.0 for public clients (native/SPA apps). Generate a `code_verifier`, derive a `code_challenge` from it, pass the challenge when getting the authorization URL, and pass the verifier when authenticating.

### Authorization URL Error Codes

| Error code | Description |
|---|---|
| `access_denied` | The identity provider denied access. |
| `ambiguous_connection_selector` | Could not uniquely identify a connection. |
| `connection_invalid` | No connection for the provided ID. |
| `connection_strategy_invalid` | Provider has multiple strategies per environment. |
| `connection_unlinked` | The connection is unlinked. |
| `invalid_connection_selector` | A valid connection selector must be provided. |
| `organization_invalid` | No organization for the provided ID. |
| `oauth_failed` | OAuth authorization request failed. |
| `server_error` | SSO authentication failed. |

---

## Authenticate with Code

Authenticates a user using AuthKit, OAuth or an organization's SSO connection.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "authorization_code",
    "code": "01E2RJ4C05B52KKZ8FSRDAP23J",
    "ip_address": "192.0.2.1",
    "user_agent": "Mozilla/5.0 ..."
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `client_id` | string | ✓ | |
| `client_secret` | string | optional | |
| `code_verifier` | string | optional | PKCE code verifier. |
| `grant_type` | `"authorization_code"` | ✓ | |
| `code` | string | ✓ | The authorization code. |
| `invitation_token` | string | optional | Token from a pending invitation. |
| `ip_address` | string | optional | User's IP address. |
| `user_agent` | string | optional | User's `User-Agent` header value. |

**Returns:**

| Field | Type | Description |
|---|---|---|
| `user` | user | The authenticated user. |
| `organization_id` | string (optional) | Organization the user signed into. |
| `access_token` | string | JWT for the current session. |
| `refresh_token` | string | Token to refresh the session. |
| `authentication_method` | enum | Method used: `SSO`, `Password`, `AppleOAuth`, `GitHubOAuth`, `GoogleOAuth`, `MicrosoftOAuth`, `MagicAuth`, or `Impersonation`. |
| `impersonator` | object (optional) | Details of the impersonating admin. |
| `oauth_tokens` | object (optional) | Raw OAuth tokens from the provider. |

---

## Authenticate with Password

Authenticates a user with email and password.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "password",
    "email": "marcelina@example.com",
    "password": "i8uv6g34kd490s"
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `client_id` | string | ✓ | |
| `client_secret` | string | ✓ | |
| `grant_type` | `"password"` | ✓ | |
| `email` | string | ✓ | |
| `password` | string | ✓ | |
| `invitation_token` | string | optional | |
| `ip_address` | string | optional | |
| `user_agent` | string | optional | |

**Returns:** `{ user, organization_id?, authentication_method: "Password" }`

---

## Authenticate with Magic Auth

Authenticates a user by verifying the Magic Auth code sent to their email.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "urn:workos:oauth:grant-type:magic-auth:code",
    "code": "123456",
    "email": "marcelina.davis@example.com"
  }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `client_id` | string | ✓ |
| `client_secret` | string | ✓ |
| `grant_type` | `"urn:workos:oauth:grant-type:magic-auth:code"` | ✓ |
| `code` | string | ✓ |
| `email` | string | ✓ |
| `invitation_token` | string | optional |
| `ip_address` | string | optional |
| `user_agent` | string | optional |

**Returns:** `{ user, organization_id?, authentication_method: "MagicAuth" }`

---

## Authenticate with Refresh Token

Exchange a refresh token for a new access token.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_test_123",
    "grant_type": "refresh_token",
    "refresh_token": "Xw0NsCVXMBf7svAoIoKBmkpEK"
  }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `client_id` | string | ✓ |
| `client_secret` | string | ✓ |
| `grant_type` | `"refresh_token"` | ✓ |
| `refresh_token` | string | ✓ |
| `organization_id` | string | optional |
| `ip_address` | string | optional |
| `user_agent` | string | optional |

**Returns:** `{ user, organization_id?, access_token, refresh_token, authentication_method, impersonator? }`

---

## Authenticate with Email Verification Code

Authenticates a user with an unverified email and verifies their email address simultaneously.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "urn:workos:oauth:grant-type:email-verification:code",
    "code": "123456",
    "pending_authentication_token": "ql1AJgNoLN1tb9llaQ8jyC2dn"
  }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `client_id` | string | ✓ |
| `client_secret` | string | ✓ |
| `grant_type` | `"urn:workos:oauth:grant-type:email-verification:code"` | ✓ |
| `code` | string | ✓ |
| `pending_authentication_token` | string | ✓ |
| `ip_address` | string | optional |
| `user_agent` | string | optional |

**Returns:** `{ user, organization_id?, authentication_method }`

---

## Authenticate with TOTP (MFA)

Authenticates a user enrolled in MFA using a time-based one-time password.

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "urn:workos:oauth:grant-type:mfa-totp",
    "code": "123456",
    "pending_authentication_token": "ql1AJgNoLN1tb9llaQ8jyC2dn",
    "authenticationChallengeId": "auth_challenge_01FVYZWQTZQ5VB6BC5MPG2EYC5"
  }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `client_id` | string | ✓ |
| `client_secret` | string | ✓ |
| `grant_type` | `"urn:workos:oauth:grant-type:mfa-totp"` | ✓ |
| `code` | string | ✓ |
| `authentication_challenge_id` | string | ✓ |
| `pending_authentication_token` | string | ✓ |
| `ip_address` | string | optional |
| `user_agent` | string | optional |

**Returns:** `{ user, organizationId?, authentication_method }`

---

## Authenticate with Organization Selection

Authenticates a user into an organization they are a member of (used after an `organization_selection_required` error).

`POST /user_management/authenticate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/authenticate \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "grant_type": "urn:workos:oauth:grant-type:organization-selection",
    "pending_authentication_token": "ql1AJgNoLN1tb9llaQ8jyC2dn",
    "organization_id": "org_01H93Z2SYX1D3NJ536M94T8SHP"
  }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `client_id` | string | ✓ |
| `client_secret` | string | ✓ |
| `grant_type` | `"urn:workos:oauth:grant-type:organization-selection"` | ✓ |
| `pending_authentication_token` | string | ✓ |
| `organization_id` | string | ✓ |
| `ip_address` | string | optional |
| `user_agent` | string | optional |

**Returns:** `{ user, organization_id?, authentication_method }`

---

## Authenticate with Session Cookie (Node.js SDK)

Authenticates a user using an AuthKit session cookie. Does not make a network call — it unseals the cookie and decodes JWT claims from the access token.

```js
import { AuthenticateWithSessionCookieFailureReason, WorkOS } from '@workos-inc/node';

const workos = new WorkOS('sk_example_123456789', { clientId: 'client_123456789' });

const { authenticated, ...rest } = await workos.userManagement.authenticateWithSessionCookie({
  sessionData: 'sealed_session_cookie_data',
  cookiePassword: 'password_previously_used_to_seal_session_cookie',
});

if (authenticated) {
  const { sessionId, organizationId, role, permissions } = rest;
} else {
  const { reason } = rest;
  if (reason === AuthenticateWithSessionCookieFailureReason.NO_SESSION_COOKIE_PROVIDED) {
    // Redirect to login
  }
}
```

**Parameters:** `{ sessionData: string, cookiePassword: string }`

**Returns:**

| Field | Type | Description |
|---|---|---|
| `authenticated` | boolean | Whether the session is valid. |
| `sessionId` | string | |
| `organizationId` | string (optional) | |
| `role` | string (optional) | |
| `roles` | array (optional) | |
| `permissions` | string (optional) | |
| `reason` | string (optional) | `"invalid_jwt"`, `"invalid_session_cookie"`, or `"no_session_cookie_provided"` |

---

## Refresh and Seal Session Data (Node.js SDK)

Unseals the provided session cookie, authenticates with the existing refresh token, and returns sealed data for the refreshed session.

```js
import { RefreshAndSealSessionDataFailureReason, WorkOS } from '@workos-inc/node';

const workos = new WorkOS('sk_example_123456789', { clientId: 'client_123456789' });

const { authenticated, ...rest } = await workos.userManagement.refreshAndSealSessionData({
  sessionData: 'sealed_session_cookie_data',
  cookiePassword: 'password_previously_used_to_seal_session_cookie',
});

if (authenticated) {
  const { sealedSession } = rest;
  // Set sealedSession in cookie
} else {
  const { reason } = rest;
}
```

**Parameters:** `{ sessionData: string, cookiePassword: string }`

**Returns:**

| Field | Type |
|---|---|
| `authenticated` | boolean |
| `sealedSession` | string |
| `reason` | `"invalid_jwt" \| "invalid_session_cookie" \| "no_session_cookie_provided" \| "invalid_grant" \| "organization_not_authorized"` |