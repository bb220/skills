# Authentication Errors

When integrating the authentication API directly (custom UI), your application must handle specific error responses that can be returned during any authentication attempt. These errors signal that additional steps are required before authentication can complete.

> **Tip:** Hosted AuthKit handles all of these errors automatically. Use it if you prefer a simpler integration.

---

## Error Handling Pattern (Python)

```python
import workos
from workos.exceptions import AuthenticationError

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

try:
    result = client.user_management.authenticate_with_password(
        email="marcelina@example.com",
        password="i8uv6g34kd490s",
    )
    # Authentication succeeded
    user = result.user

except AuthenticationError as e:
    if e.code == "email_verification_required":
        handle_email_verification(e)
    elif e.code == "mfa_enrollment":
        handle_mfa_enrollment(e)
    elif e.code == "mfa_challenge":
        handle_mfa_challenge(e)
    elif e.code == "organization_selection_required":
        handle_org_selection(e)
    elif e.code == "sso_required":
        handle_sso_redirect(e)
    elif e.code == "organization_authentication_methods_required":
        handle_auth_method_selection(e)
```

---

## Error Types

### `email_verification_required`

A user with an unverified email address attempted to authenticate in an environment where email verification is required.

**Error payload:**

```json
{
  "code": "email_verification_required",
  "message": "Email ownership must be verified before authentication.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "email": "marcelina.davis@example.com",
  "email_verification_id": "email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3"
}
```

| Field                      | Type  | Description                                                                |
|----------------------------|-------|----------------------------------------------------------------------------|
| `code`                     | `str` | `"email_verification_required"`                                            |
| `message`                  | `str` | Human-readable error message.                                              |
| `pending_authentication_token` | `str` | Token to pass to `authenticate_with_email_verification()`.            |
| `email`                    | `str` | The user's email address.                                                  |
| `email_verification_id`    | `str` | Use with `get_email_verification()` to fetch the code if sending manually. |

**Resolution:** Pass the `pending_authentication_token` and the user-entered code to [`authenticate_with_email_verification()`](https://workos.com/docs/reference/authkit/authentication).

---

### `mfa_enrollment`

A user who is not enrolled in MFA attempted to authenticate in an environment where MFA is required.

**Error payload:**

```json
{
  "code": "mfa_enrollment",
  "message": "The user must enroll in MFA to finish authenticating.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "user": { ... }
}
```

| Field                          | Type   | Description                                              |
|--------------------------------|--------|----------------------------------------------------------|
| `code`                         | `str`  | `"mfa_enrollment"`                                       |
| `message`                      | `str`  | Human-readable error message.                            |
| `pending_authentication_token` | `str`  | Token to pass to `authenticate_with_totp()` after enrollment. |
| `user`                         | `User` | The user who needs to enroll.                            |

**Resolution:** Present an MFA enrollment UI, call [`enroll_auth_factor()`](https://workos.com/docs/reference/authkit/mfa), then challenge the user and authenticate with [`authenticate_with_totp()`](https://workos.com/docs/reference/authkit/authentication).

---

### `mfa_challenge`

A user enrolled in MFA attempted to authenticate and must complete a TOTP challenge.

**Error payload:**

```json
{
  "code": "mfa_challenge",
  "message": "The user must complete an MFA challenge to finish authenticating.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "authentication_factors": [
    {
      "id": "auth_factor_01FVYZ5QM8N98T9ME5BCB2BBMJ",
      "type": "totp"
    }
  ],
  "user": { ... }
}
```

| Field                          | Type     | Description                                                            |
|--------------------------------|----------|------------------------------------------------------------------------|
| `code`                         | `str`    | `"mfa_challenge"`                                                      |
| `message`                      | `str`    | Human-readable error message.                                          |
| `pending_authentication_token` | `str`    | Token to pass to `authenticate_with_totp()`.                           |
| `authentication_factors`       | `list`   | The factors the user is enrolled in.                                   |
| `user`                         | `User`   | The user who must complete the challenge.                              |

**Resolution:** Challenge one of the returned factors, present a TOTP input UI, and call [`authenticate_with_totp()`](https://workos.com/docs/reference/authkit/authentication) with the code, challenge ID, and pending token.

---

### `organization_selection_required`

The user is a member of multiple organizations and must select which one to sign in to.

**Error payload:**

```json
{
  "code": "organization_selection_required",
  "message": "The user must choose an organization to finish their authentication.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "organizations": [
    {"id": "org_01H93RZAP85YGYZJXYPAZ9QTXF", "name": "Foo Corp"},
    {"id": "org_01H93S4E6GB5A8PFNKGTA4S42X", "name": "Bar Corp"}
  ],
  "user": { ... }
}
```

| Field                          | Type   | Description                                                       |
|--------------------------------|--------|-------------------------------------------------------------------|
| `code`                         | `str`  | `"organization_selection_required"`                               |
| `message`                      | `str`  | Human-readable error message.                                     |
| `pending_authentication_token` | `str`  | Token to pass to `authenticate_with_organization_selection()`.    |
| `organizations`                | `list` | List of organizations the user belongs to.                        |
| `user`                         | `User` | The authenticated user.                                           |

**Resolution:** Display the list of organizations, then call [`authenticate_with_organization_selection()`](https://workos.com/docs/reference/authkit/authentication) with the selected org and pending token.

---

### `sso_required`

The user attempted to authenticate into an organization that requires SSO using a non-SSO method.

**Error payload:**

```json
{
  "error": "sso_required",
  "error_description": "User must authenticate using one of the matching connections.",
  "connection_ids": ["conn_01DRF1T7JN6GXS8KHS0WYWX1YD"]
}
```

| Field                | Type   | Description                                              |
|----------------------|--------|----------------------------------------------------------|
| `error`              | `str`  | `"sso_required"`                                         |
| `error_description`  | `str`  | Human-readable error message.                            |
| `email`              | `str`  | The user's email address.                                |
| `connection_ids`     | `list` | SSO connection IDs that can complete authentication.     |
| `pending_authentication_token` | `str` | Optional — may be included.                 |

**Resolution:** Use one of the `connection_ids` with [`get_authorization_url()`](https://workos.com/docs/reference/authkit/authentication) and redirect the user to the SSO login flow.

---

### `organization_authentication_methods_required`

The user attempted to authenticate with a method not allowed by their organization's domain policy.

**Error payload:**

```json
{
  "error": "organization_authentication_methods_required",
  "error_description": "User must authenticate using one of the methods allowed by the organization.",
  "sso_connection_ids": ["conn_01DRF1T7JN6GXS8KHS0WYWX1YD"],
  "auth_methods": {
    "apple_oauth": false,
    "github_oauth": false,
    "google_oauth": true,
    "magic_auth": false,
    "microsoft_oauth": false,
    "password": false
  }
}
```

| Field                | Type   | Description                                                        |
|----------------------|--------|--------------------------------------------------------------------|
| `error`              | `str`  | `"organization_authentication_methods_required"`                   |
| `error_description`  | `str`  | Human-readable error message.                                      |
| `email`              | `str`  | The user's email address.                                          |
| `sso_connection_ids` | `list` | SSO connection IDs available to complete authentication.           |
| `auth_methods`       | `dict` | Map of auth methods to booleans indicating which are allowed.      |

**Resolution:** Present the allowed authentication options and redirect the user to the appropriate flow.

---

## Related

- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Email Verification](https://workos.com/docs/reference/authkit/email-verification)
- [Multi-Factor Auth](https://workos.com/docs/reference/authkit/mfa)
- [Organization Policies](https://workos.com/docs/authkit/organization-policies)