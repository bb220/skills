# Email Verification

Email verification is a security feature that requires users to verify their email address before they can sign in. It is **enabled by default**.

Users signing in with **Magic Auth, Google OAuth, Apple OAuth, or SSO** are automatically verified. For all other authentication methods (e.g., password), an email verification flow is required.

---

## The Email Verification Object

```python
email_verification = {
    "object": "email_verification",
    "id": "email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3",
    "user_id": "user_01HWWYEH2NPT48X82ZT23K5AX4",
    "email": "marcelina.davis@example.com",
    "expires_at": "2021-07-01T19:07:33.155Z",
    "code": "123456",
    "created_at": "2021-06-25T19:07:33.155Z",
    "updated_at": "2021-06-25T19:07:33.155Z",
}
```

---

## Attributes

### `EmailVerification`

| Attribute    | Type  | Description                                                      |
|--------------|-------|------------------------------------------------------------------|
| `id`         | `str` | The unique ID of the email verification code.                    |
| `user_id`    | `str` | The ID of the user this verification code belongs to.            |
| `email`      | `str` | The email address the code was issued for.                       |
| `expires_at` | `str` | ISO 8601 timestamp when the code expires.                        |
| `code`       | `str` | The 6-digit one-time verification code.                          |
| `created_at` | `str` | ISO 8601 timestamp when the code was created.                    |
| `updated_at` | `str` | ISO 8601 timestamp when the code was last updated.               |

---

## Operations

### Get an Email Verification Code

Retrieves the details of an existing email verification code. Use this to get the code when you want to send the verification email yourself (i.e., when the WorkOS email setting for email verification is disabled).

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

email_verification = client.user_management.get_email_verification(
    id="email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3"
)

print(email_verification.code)       # "123456"
print(email_verification.email)      # "marcelina.davis@example.com"
print(email_verification.expires_at) # "2021-07-01T19:07:33.155Z"
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the email verification code.   |

**Returns:** [`EmailVerification`](#the-email-verification-object)

---

## Full Email Verification Flow

### When WorkOS sends the email automatically

When email verification is enabled in the WorkOS Dashboard, WorkOS automatically sends the verification code when a user with an unverified email attempts to sign in.

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
except AuthenticationError as e:
    if e.code == "email_verification_required":
        # WorkOS auto-sent the code; store the pending token for the next step
        pending_token = e.pending_authentication_token
        email_verification_id = e.email_verification_id
        # Show the user a form to enter their code

# After user enters the code:
result = client.user_management.authenticate_with_email_verification(
    code="123456",
    pending_authentication_token=pending_token,
)

user = result.user
access_token = result.access_token
```

### When you send the email yourself

When the WorkOS email setting is disabled, retrieve the code and send it via your own email provider.

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
except AuthenticationError as e:
    if e.code == "email_verification_required":
        pending_token = e.pending_authentication_token
        email_verification_id = e.email_verification_id

        # Fetch the code
        ev = client.user_management.get_email_verification(
            id=email_verification_id
        )

        # Send the code via your email provider
        send_email(
            to=ev.email,
            subject="Verify your email",
            body=f"Your verification code is: {ev.code}",
        )

# After user enters the code:
result = client.user_management.authenticate_with_email_verification(
    code="123456",
    pending_authentication_token=pending_token,
)
```

---

## Related

- [Authentication – Authenticate with Email Verification](https://workos.com/docs/reference/authkit/authentication)
- [Authentication Errors – Email Verification Required](https://workos.com/docs/reference/authkit/authentication-errors)
- [Custom Emails](https://workos.com/docs/authkit/custom-emails)