# Multi-Factor Authentication (MFA)

Enroll users in multi-factor authentication for an additional layer of security. MFA can be enabled via the [Authentication page](https://dashboard.workos.com/authentication) in the WorkOS dashboard.

**Source:** https://workos.com/docs/reference/authkit/mfa

---

## Authentication Factor Object

Represents an authentication factor.

```json
{
  "object": "authentication_factor",
  "id": "auth_factor_01FVYZ5QM8N98T9ME5BCB2BBMJ",
  "created_at": "2022-02-15T15:14:19.392Z",
  "updated_at": "2022-02-15T15:14:19.392Z",
  "type": "totp",
  "totp": {
    "issuer": "Foo Corp",
    "user": "alan.turing@example.com",
    "qr_code": "data:image/png;base64,{base64EncodedPng}",
    "secret": "NAGCCFS3EYRB422HNAKAKY3XDUORMSRF",
    "uri": "otpauth://totp/FooCorp:alan.turing@example.com?secret=NAGCCFS3EYRB422HNAKAKY3XDUORMSRF&issuer=FooCorp"
  },
  "userId": "user_01FVYZ5QM8N98T9ME5BCB2BBMJ"
}
```

### Properties

| Property | Type | Description |
|---|---|---|
| `object` | `"authentication_factor"` | |
| `id` | string | |
| `created_at` | string | |
| `updated_at` | string | |
| `type` | `"totp"` | |
| `totp.issuer` | string | The issuer label displayed in authenticator apps. |
| `totp.user` | string | The user identifier displayed in authenticator apps. |
| `totp.qr_code` | string | Base64-encoded PNG QR code for enrollment. |
| `totp.secret` | string | The TOTP secret for manual entry. |
| `totp.uri` | string | OTP auth URI. |
| `user_id` | string | The user this factor belongs to. |

---

## Authentication Challenge Object

Represents a challenge of an authentication factor.

```json
{
  "object": "authentication_challenge",
  "id": "auth_challenge_01FVYZWQTZQ5VB6BC5MPG2EYC5",
  "created_at": "2022-02-15T15:26:53.274Z",
  "updated_at": "2022-02-15T15:26:53.274Z",
  "expires_at": "2022-02-15T15:36:53.279Z",
  "authentication_factor_id": "auth_factor_01FVYZ5QM8N98T9ME5BCB2BBMJ"
}
```

### Properties

| Property | Type |
|---|---|
| `object` | `"authentication_challenge"` |
| `id` | string |
| `created_at` | string |
| `updated_at` | string |
| `expires_at` | string |
| `authentication_factor_id` | string |

---

## Enroll an Authentication Factor

Enrolls a user in a new authentication factor.

`POST /user_management/users/:id/auth_factors`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/users/user_01E4ZCR3C56J083X43JQXF3JK5/auth_factors \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{
    "type": "totp",
    "totp_issuer": "Foo Corp",
    "totp_user": "bob@example.com"
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | ✓ | The user ID. |
| `type` | `"totp"` | ✓ | The factor type. |
| `totp_issuer` | string | optional | Issuer label for authenticator apps. |
| `totp_user` | string | optional | User label for authenticator apps. |
| `totp_secret` | string | optional | Provide your own TOTP secret. |

**Returns:** `{ challenge: authentication_challenge, factor: authentication_factor }`

---

## List Authentication Factors

Lists the authentication factors for a user.

`GET /user_management/users/:id/auth_factors`

```bash
curl https://api.workos.com/user_management/users/user_01E4ZCR3C56J083X43JQXF3JK5/auth_factors \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The user ID. |

**Returns:** `{ data: array, list_metadata: object }`