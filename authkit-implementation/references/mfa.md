# Multi-Factor Authentication (MFA)

Enroll users in multi-factor authentication (MFA) for an additional layer of security. WorkOS supports TOTP (Time-Based One-Time Password) as the MFA method.

MFA can be enabled via the **Authentication** page in the WorkOS Dashboard.

---

## The Authentication Factor Object

Represents an enrolled authentication factor for a user.

```python
factor = {
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
        "uri": "otpauth://totp/FooCorp:alan.turing@example.com?secret=NAGCCFS3EYRB422HNAKAKY3XDUORMSRF&issuer=FooCorp",
    },
    "user_id": "user_01FVYZ5QM8N98T9ME5BCB2BBMJ",
}
```

### `AuthenticationFactor` Attributes

| Attribute      | Type     | Description                                                            |
|----------------|----------|------------------------------------------------------------------------|
| `id`           | `str`    | Unique ID of the factor.                                               |
| `type`         | `str`    | Factor type — currently always `"totp"`.                               |
| `user_id`      | `str`    | The ID of the user this factor belongs to.                             |
| `created_at`   | `str`    | ISO 8601 timestamp when the factor was enrolled.                       |
| `updated_at`   | `str`    | ISO 8601 timestamp when the factor was last updated.                   |
| `totp.issuer`  | `str`    | The issuer name displayed in the authenticator app.                    |
| `totp.user`    | `str`    | The user identifier displayed in the authenticator app.                |
| `totp.qr_code` | `str`    | Base64-encoded PNG QR code for scanning with an authenticator app.     |
| `totp.secret`  | `str`    | The TOTP secret key (for manual entry in authenticator apps).          |
| `totp.uri`     | `str`    | The `otpauth://` URI for deep-linking into authenticator apps.         |

---

## The Authentication Challenge Object

Represents a challenge of an authentication factor. Generated when a user needs to prove their enrolled factor during sign-in.

```python
challenge = {
    "object": "authentication_challenge",
    "id": "auth_challenge_01FVYZWQTZQ5VB6BC5MPG2EYC5",
    "created_at": "2022-02-15T15:26:53.274Z",
    "updated_at": "2022-02-15T15:26:53.274Z",
    "expires_at": "2022-02-15T15:36:53.279Z",
    "authentication_factor_id": "auth_factor_01FVYZ5QM8N98T9ME5BCB2BBMJ",
}
```

### `AuthenticationChallenge` Attributes

| Attribute                  | Type  | Description                                              |
|----------------------------|-------|----------------------------------------------------------|
| `id`                       | `str` | Unique ID of the challenge.                              |
| `authentication_factor_id` | `str` | The factor this challenge belongs to.                    |
| `expires_at`               | `str` | ISO 8601 timestamp when the challenge expires.           |
| `created_at`               | `str` | ISO 8601 timestamp when the challenge was created.       |
| `updated_at`               | `str` | ISO 8601 timestamp when the challenge was last updated.  |

---

## Operations

### Enroll an Authentication Factor

Enrolls a user in a new TOTP authentication factor. Returns both the factor (with QR code) and an initial challenge.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

result = client.user_management.enroll_auth_factor(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5",
    type="totp",
    totp_issuer="Foo Corp",
    totp_user="alan.turing@example.com",
)

factor = result.authentication_factor
challenge = result.authentication_challenge

# Display the QR code to the user
qr_code_data_url = factor.totp.qr_code  # data:image/png;base64,...
totp_secret = factor.totp.secret         # for manual entry
```

**Parameters**

| Parameter      | Type  | Required | Description                                                                |
|----------------|-------|----------|----------------------------------------------------------------------------|
| `user_id`      | `str` | Required | The ID of the user to enroll.                                              |
| `type`         | `str` | Required | Factor type — must be `"totp"`.                                            |
| `totp_issuer`  | `str` | Optional | Name shown in the authenticator app (e.g., your app name).                 |
| `totp_user`    | `str` | Optional | User identifier shown in the authenticator app (e.g., the user's email).   |
| `totp_secret`  | `str` | Optional | Provide your own TOTP secret instead of having WorkOS generate one.        |

**Returns**

| Field                    | Type                     | Description                            |
|--------------------------|--------------------------|----------------------------------------|
| `authentication_factor`  | `AuthenticationFactor`   | The enrolled factor with QR code data. |
| `authentication_challenge` | `AuthenticationChallenge` | An initial challenge for the factor. |

---

### List Authentication Factors

Lists all authentication factors enrolled for a user.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

auth_factors = client.user_management.list_auth_factors(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5",
)

for factor in auth_factors.data:
    print(factor.id, factor.type, factor.created_at)
```

**Parameters**

| Parameter | Type  | Required | Description                                         |
|-----------|-------|----------|-----------------------------------------------------|
| `user_id` | `str` | Required | The ID of the user whose factors to list.           |
| `limit`   | `int` | Optional | Maximum number of results to return.                |
| `before`  | `str` | Optional | Pagination cursor — return factors before this ID.  |
| `after`   | `str` | Optional | Pagination cursor — return factors after this ID.   |
| `order`   | `str` | Optional | `"asc"` or `"desc"`.                               |

**Returns**

| Field           | Type                       | Description                              |
|-----------------|----------------------------|------------------------------------------|
| `data`          | `list[AuthenticationFactor]` | List of authentication factors.        |
| `list_metadata` | `object`                   | Pagination metadata.                     |

---

## Full MFA Enrollment Flow

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Step 1: Enroll the user in TOTP
result = client.user_management.enroll_auth_factor(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5",
    type="totp",
    totp_issuer="My App",
    totp_user="marcelina@example.com",
)

factor = result.authentication_factor
challenge = result.authentication_challenge

# Step 2: Display QR code to user, wait for them to scan and enter a TOTP code
# (render factor.totp.qr_code as an <img> tag, or show factor.totp.secret for manual entry)

# Step 3: On next sign-in, when mfa_challenge error is returned, authenticate with TOTP
auth_result = client.user_management.authenticate_with_totp(
    code="123456",  # user-entered TOTP
    authentication_challenge_id=challenge.id,
    pending_authentication_token="ql1AJgNoLN1tb9llaQ8jyC2dn",  # from mfa_challenge error
)

user = auth_result.user
access_token = auth_result.access_token
```

---

## Related

- [Authentication – Authenticate with TOTP](https://workos.com/docs/reference/authkit/authentication)
- [Authentication Errors – MFA Challenge](https://workos.com/docs/reference/authkit/authentication-errors)
- [WorkOS MFA Reference (standalone)](https://workos.com/docs/reference/mfa)