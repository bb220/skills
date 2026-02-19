# Authentication

Authenticate a user with a specified authentication method.

---

## Operations

### Get Authorization URL

Generates an OAuth 2.0 authorization URL to authenticate a user with AuthKit or SSO. If using AuthKit, set `provider="authkit"`. For SSO connections, provide `connection_id`, `organization_id`, or `provider` (mutually exclusive).

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

authorization_url = client.user_management.get_authorization_url(
    provider="authkit",
    redirect_uri="https://your-app.com/callback",
    state="dj1kUXc0dzlXZ1hjUQ==",
)
```

**Parameters**

| Parameter              | Type    | Required | Description                                                                      |
|------------------------|---------|----------|----------------------------------------------------------------------------------|
| `redirect_uri`         | `str`   | Required | The URI WorkOS redirects to after authentication.                                |
| `provider`             | `str`   | Optional | `"authkit"`, `"GoogleOAuth"`, `"MicrosoftOAuth"`, `"GitHubOAuth"`, `"AppleOAuth"` |
| `connection_id`        | `str`   | Optional | SSO connection ID (mutually exclusive with `organization_id` and `provider`).    |
| `organization_id`      | `str`   | Optional | Organization ID (mutually exclusive with `connection_id` and `provider`).        |
| `state`                | `str`   | Optional | Arbitrary string to restore application state between redirects.                 |
| `login_hint`           | `str`   | Optional | Pre-fills the email field on the login page.                                     |
| `domain_hint`          | `str`   | Optional | Pre-selects the SSO provider by domain.                                          |
| `screen_hint`          | `str`   | Optional | `"sign-in"` or `"sign-up"`.                                                      |
| `code_challenge`       | `str`   | Optional | PKCE code challenge (SHA-256 hash of code verifier).                             |
| `code_challenge_method`| `str`   | Optional | `"S256"` for PKCE flows.                                                         |
| `provider_scopes`      | `list`  | Optional | Additional OAuth scopes to request from the provider.                            |

**Returns:** `str` — The authorization URL.

---

### Authenticate with Code

Authenticates a user using the authorization code returned to your redirect URI after the user completes the AuthKit, OAuth, or SSO flow.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_code(
    code="01E2RJ4C05B52KKZ8FSRDAP23J",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
refresh_token = result.refresh_token
```

**Parameters**

| Parameter          | Type  | Required | Description                                                   |
|--------------------|-------|----------|---------------------------------------------------------------|
| `code`             | `str` | Required | The authorization code from the redirect URI `code` param.   |
| `code_verifier`    | `str` | Optional | PKCE code verifier (required if `code_challenge` was used).   |
| `invitation_token` | `str` | Optional | Token from an invitation link to auto-accept membership.      |
| `ip_address`       | `str` | Optional | IP address of the user.                                       |
| `user_agent`       | `str` | Optional | User agent string of the user's browser.                      |

**Returns**

| Field                  | Type   | Description                                                        |
|------------------------|--------|--------------------------------------------------------------------|
| `user`                 | `User` | The authenticated user object.                                     |
| `organization_id`      | `str`  | The organization the user authenticated into (if applicable).      |
| `access_token`         | `str`  | JWT for verifying the user's active session.                       |
| `refresh_token`        | `str`  | Token to exchange for a new access token.                          |
| `authentication_method`| `str`  | Method used: `"SSO"`, `"Password"`, `"GoogleOAuth"`, etc.         |
| `impersonator`         | `dict` | Present if the session is an impersonation session.                |

---

### Authenticate with Password

Authenticates a user with email and password.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_password(
    email="marcelina@example.com",
    password="i8uv6g34kd490s",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
refresh_token = result.refresh_token
```

**Parameters**

| Parameter          | Type  | Required | Description                                              |
|--------------------|-------|----------|----------------------------------------------------------|
| `email`            | `str` | Required | The user's email address.                                |
| `password`         | `str` | Required | The user's password.                                     |
| `invitation_token` | `str` | Optional | Token from an invitation link to auto-accept membership. |
| `ip_address`       | `str` | Optional | IP address of the user.                                  |
| `user_agent`       | `str` | Optional | User agent string of the user's browser.                 |

**Returns:** Same structure as [Authenticate with Code](#authenticate-with-code).

---

### Authenticate with Magic Auth

Authenticates a user by verifying the Magic Auth one-time code sent to their email.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_magic_auth(
    code="123456",
    email="marcelina.davis@example.com",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
refresh_token = result.refresh_token
```

**Parameters**

| Parameter                      | Type  | Required | Description                                              |
|--------------------------------|-------|----------|----------------------------------------------------------|
| `code`                         | `str` | Required | The 6-digit one-time code from the user's email.         |
| `email`                        | `str` | Required | The user's email address.                                |
| `pending_authentication_token` | `str` | Optional | Token from a prior auth attempt that required Magic Auth.|
| `invitation_token`             | `str` | Optional | Token from an invitation link to auto-accept membership. |
| `ip_address`                   | `str` | Optional | IP address of the user.                                  |
| `user_agent`                   | `str` | Optional | User agent string of the user's browser.                 |

**Returns:** Same structure as [Authenticate with Code](#authenticate-with-code).

---

### Authenticate with Refresh Token

Exchanges a refresh token for a new access token. Refresh tokens may be rotated after use.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_refresh_token(
    refresh_token="Xw0NsCVXMBf7svAoIoKBmkpEK",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

new_access_token = result.access_token
new_refresh_token = result.refresh_token
```

**Parameters**

| Parameter         | Type  | Required | Description                                                      |
|-------------------|-------|----------|------------------------------------------------------------------|
| `refresh_token`   | `str` | Required | The refresh token from a prior authentication response.          |
| `organization_id` | `str` | Optional | Switch the session to a different organization.                  |
| `ip_address`      | `str` | Optional | IP address of the user.                                          |
| `user_agent`      | `str` | Optional | User agent string of the user's browser.                         |

**Returns**

| Field           | Type  | Description                             |
|-----------------|-------|-----------------------------------------|
| `access_token`  | `str` | New JWT for verifying the active session.|
| `refresh_token` | `str` | New refresh token (rotate after use).   |

---

### Authenticate with Email Verification

Authenticates a user with an unverified email and verifies their email address using a one-time code.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_email_verification(
    code="123456",
    pending_authentication_token="ql1AJgNoLN1tb9llaQ8jyC2dn",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
```

**Parameters**

| Parameter                      | Type  | Required | Description                                               |
|--------------------------------|-------|----------|-----------------------------------------------------------|
| `code`                         | `str` | Required | The one-time email verification code.                     |
| `pending_authentication_token` | `str` | Required | Token from the `email_verification_required` error.       |
| `ip_address`                   | `str` | Optional | IP address of the user.                                   |
| `user_agent`                   | `str` | Optional | User agent string of the user's browser.                  |

**Returns:** Same structure as [Authenticate with Code](#authenticate-with-code).

---

### Authenticate with TOTP

Authenticates a user enrolled in MFA by verifying a time-based one-time password (TOTP).

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_totp(
    code="123456",
    authentication_challenge_id="auth_challenge_01FVYZWQTZQ5VB6BC5MPG2EYC5",
    pending_authentication_token="ql1AJgNoLN1tb9llaQ8jyC2dn",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
```

**Parameters**

| Parameter                      | Type  | Required | Description                                              |
|--------------------------------|-------|----------|----------------------------------------------------------|
| `code`                         | `str` | Required | The 6-digit TOTP code from the user's authenticator app. |
| `authentication_challenge_id`  | `str` | Required | The challenge ID from the MFA challenge error.           |
| `pending_authentication_token` | `str` | Required | Token from the `mfa_challenge` error.                    |
| `ip_address`                   | `str` | Optional | IP address of the user.                                  |
| `user_agent`                   | `str` | Optional | User agent string of the user's browser.                 |

**Returns:** Same structure as [Authenticate with Code](#authenticate-with-code).

---

### Authenticate with Organization Selection

Authenticates a user into a specific organization when they are a member of multiple organizations.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

result = client.user_management.authenticate_with_organization_selection(
    organization_id="org_01H945H0YD4F97JN9MATX7BYAG",
    pending_authentication_token="ql1AJgNoLN1tb9llaQ8jyC2dn",
    ip_address="192.0.2.1",
    user_agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
)

user = result.user
access_token = result.access_token
```

**Parameters**

| Parameter                      | Type  | Required | Description                                                 |
|--------------------------------|-------|----------|-------------------------------------------------------------|
| `organization_id`              | `str` | Required | The organization the user selected.                         |
| `pending_authentication_token` | `str` | Required | Token from the `organization_selection_required` error.     |
| `ip_address`                   | `str` | Optional | IP address of the user.                                     |
| `user_agent`                   | `str` | Optional | User agent string of the user's browser.                    |

**Returns:** Same structure as [Authenticate with Code](#authenticate-with-code).

---

## Notes

- **PKCE Flow:** Generate a `code_verifier` (high-entropy random string), hash it to produce a `code_challenge`, pass the challenge to `get_authorization_url()`, and pass the verifier to `authenticate_with_code()`.
- **Redirect URIs:** Must be configured in the WorkOS dashboard. HTTPS required in production. Wildcard subdomains (`*`) supported but cannot be the default redirect URI.
- **Rate Limits:** Authentication reads: 1,000 req/10s. Authentication writes: 500 req/10s. Authenticate endpoint: 10 req/60s per email.

---

## Related

- [Authentication Errors](https://workos.com/docs/reference/authkit/authentication-errors)
- [Session Tokens](https://workos.com/docs/reference/authkit/session-tokens)
- [Magic Auth](https://workos.com/docs/reference/authkit/magic-auth)
- [Multi-Factor Auth](https://workos.com/docs/reference/authkit/mfa)
- [Email Verification](https://workos.com/docs/reference/authkit/email-verification)