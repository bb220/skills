# Authentication Errors

Integrating the authentication API directly requires handling error responses for email verification, MFA challenges, identity linking, and organization selection. One or more of these responses may be returned for an authentication attempt with any authentication method.

> Hosted AuthKit handles authentication errors for you and may be a good choice if you prefer a simpler integration.

**Source:** https://workos.com/docs/reference/authkit/authentication-errors

---

## Email Verification Required Error

Indicates that a user with an unverified email address attempted to authenticate in an environment where email verification is required.

```json
{
  "code": "email_verification_required",
  "message": "Email ownership must be verified before authentication.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "email": "marcelina.davis@example.com",
  "email_verification_id": "email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3"
}
```

### Properties

| Field | Type |
|---|---|
| `code` | `"email_verification_required"` |
| `message` | string |
| `pending_authentication_token` | string |
| `email` | string |
| `email_verification_id` | string |

If the [email setting](https://workos.com/docs/authkit/custom-emails) for email verification is enabled, WorkOS automatically sends a one-time code. If not, retrieve the code manually. Use the `pending_authentication_token` and the code to [authenticate](./04-authentication.md#authenticate-with-email-verification-code) the user and verify their email.

---

## MFA Enrollment Error

Indicates that a user not enrolled in MFA attempted to authenticate in an environment where MFA is required.

```json
{
  "code": "mfa_enrollment",
  "message": "The user must enroll in MFA to finish authenticating.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "user": { ... }
}
```

### Properties

| Field | Type |
|---|---|
| `code` | `"mfa_enrollment"` |
| `message` | string |
| `pending_authentication_token` | string |
| `user` | user object |

Present an [MFA enrollment](./10-mfa.md#enroll-an-authentication-factor) UI to the user. After enrollment, present an MFA challenge UI and authenticate them with a [TOTP code](./04-authentication.md#authenticate-with-totp-mfa) and the `pending_authentication_token`.

MFA can be enabled via the [Authentication page](https://dashboard.workos.com/authentication) in the WorkOS dashboard.

---

## MFA Challenge Error

Indicates that a user enrolled in MFA attempted to authenticate and must complete an MFA challenge.

```json
{
  "code": "mfa_challenge",
  "message": "The user must complete an MFA challenge to finish authenticating.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "authentication_factors": [
    { "id": "auth_factor_01FVYZ5QM8N98T9ME5BCB2BBMJ", "type": "totp" }
  ],
  "user": { ... }
}
```

### Properties

| Field | Type |
|---|---|
| `code` | `"mfa_challenge"` |
| `message` | string |
| `pending_authentication_token` | string |
| `authentication_factors` | array |
| `user` | user object |

Challenge one of the returned factors and present a TOTP UI. Then [authenticate with TOTP](./04-authentication.md#authenticate-with-totp-mfa) using the code, `pending_authentication_token`, and challenge ID.

---

## Organization Selection Required Error

Indicates that the user is a member of multiple organizations and must select which one to sign into.

```json
{
  "code": "organization_selection_required",
  "message": "The user must choose an organization to finish their authentication.",
  "pending_authentication_token": "YQyCkYfuVw2mI3tzSrk2C1Y7S",
  "organizations": [
    { "id": "org_01H93RZAP85YGYZJXYPAZ9QTXF", "name": "Foo Corp" },
    { "id": "org_01H93S4E6GB5A8PFNKGTA4S42X", "name": "Bar Corp" }
  ],
  "user": { ... }
}
```

### Properties

| Field | Type |
|---|---|
| `code` | `"organization_selection_required"` |
| `message` | string |
| `pending_authentication_token` | string |
| `user` | user object |
| `organizations` | array |

Display the organization list and [authenticate with organization selection](./04-authentication.md#authenticate-with-organization-selection) using the `pending_authentication_token`.

---

## SSO Required Error

Indicates that a user attempted to authenticate into an organization that requires SSO using a different method.

```json
{
  "error": "sso_required",
  "error_description": "User must authenticate using one of the matching connections.",
  "connection_ids": ["conn_01DRF1T7JN6GXS8KHS0WYWX1YD"]
}
```

### Properties

| Field | Type |
|---|---|
| `error` | `"sso_required"` |
| `error_description` | string |
| `email` | string |
| `connection_ids` | array |
| `pending_authentication_token` | string (optional) |

Use one of the `connection_ids` to [get the authorization URL](./04-authentication.md#get-an-authorization-url) and redirect the user to their SSO provider.

---

## Organization Authentication Required Error

Indicates that a user attempted to authenticate with a method not allowed by the organization's [domain policy](https://workos.com/docs/authkit/organization-policies).

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

### Properties

| Field | Type |
|---|---|
| `error` | `"organization_authentication_methods_required"` |
| `error_description` | string |
| `email` | string |
| `sso_connection_ids` | array |
| `auth_methods` | object |

Present the user with the allowed authentication options so they can choose which method to continue with.