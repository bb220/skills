# Password Reset

Create a password reset token for a user and reset the user's password.

> When a user's password is reset, **all of their active sessions are revoked**.

Successfully resetting a password will also verify the user's email if it hasn't been verified yet.

---

## The Password Reset Object

```python
password_reset = {
    "object": "password_reset",
    "id": "password_reset_01HYGDNK5G7FZ4YJFXYXPB5JRW",
    "user_id": "user_01HWWYEH2NPT48X82ZT23K5AX4",
    "email": "marcelina.davis@example.com",
    "password_reset_token": "Z1uX3RbwcIl5fIGJJJCXXisdI",
    "password_reset_url": "https://your-app.com/reset-password?token=Z1uX3RbwcIl5fIGJJJCXXisdI",
    "expires_at": "2025-07-14T18:00:54.578Z",
    "created_at": "2025-07-14T17:45:54.578Z",
}
```

---

## Attributes

### `PasswordReset`

| Attribute             | Type  | Description                                                                         |
|-----------------------|-------|-------------------------------------------------------------------------------------|
| `id`                  | `str` | The unique ID of the password reset record.                                         |
| `user_id`             | `str` | The ID of the user this reset token belongs to.                                     |
| `email`               | `str` | The email address of the user.                                                      |
| `password_reset_token`| `str` | The one-time token used to reset the password.                                      |
| `password_reset_url`  | `str` | The full URL the user should be directed to (includes the token as a query param).  |
| `expires_at`          | `str` | ISO 8601 timestamp when the reset token expires.                                    |
| `created_at`          | `str` | ISO 8601 timestamp when the reset was created.                                      |

---

## Operations

### Get a Password Reset Token

Retrieves the details of an existing password reset token.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

password_reset = client.user_management.get_password_reset(
    id="password_reset_01HYGDNK5G7FZ4YJFXYXPB5JRW"
)

print(password_reset.password_reset_token) # "Z1uX3RbwcIl5fIGJJJCXXisdI"
print(password_reset.expires_at)           # "2025-07-14T18:00:54.578Z"
```

**Parameters**

| Parameter | Type  | Required | Description                            |
|-----------|-------|----------|----------------------------------------|
| `id`      | `str` | Required | The ID of the password reset record.   |

**Returns:** [`PasswordReset`](#the-password-reset-object)

---

### Create a Password Reset Token

Creates a one-time token that can be used to reset a user's password. Use this to send a custom password reset email.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

password_reset = client.user_management.create_password_reset(
    email="marcelina@example.com",
)

# Send the reset URL to the user via your email provider
send_email(
    to=password_reset.email,
    subject="Reset your password",
    body=f"Click here to reset your password: {password_reset.password_reset_url}",
)
```

**Parameters**

| Parameter | Type  | Required | Description                                             |
|-----------|-------|----------|---------------------------------------------------------|
| `email`   | `str` | Required | The email address of the user requesting a password reset. |

**Returns:** [`PasswordReset`](#the-password-reset-object)

> **Note:** If the WorkOS email setting for password reset is enabled, WorkOS automatically sends the reset email. If disabled, retrieve the token and send the email yourself.

---

### Reset the Password

Sets a new password using the token from the password reset link. All active sessions for the user are revoked upon success.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

result = client.user_management.reset_password(
    token="Z1uX3RbwcIl5fIGJJJCXXisdI",  # from password_reset_url query param
    new_password="newSecurePassword123!",
)

user = result.user
```

**Parameters**

| Parameter      | Type  | Required | Description                                                          |
|----------------|-------|----------|----------------------------------------------------------------------|
| `token`        | `str` | Required | The password reset token from the reset URL's `token` query param.   |
| `new_password` | `str` | Required | The new password to set for the user.                                |

**Returns**

| Field  | Type   | Description                       |
|--------|--------|-----------------------------------|
| `user` | `User` | The updated user object.          |

---

## Full Password Reset Flow

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

# Step 1: User requests a password reset (e.g., forgot password form)
password_reset = client.user_management.create_password_reset(
    email="marcelina@example.com",
)

# Step 2: Send the reset link to the user
send_email(
    to=password_reset.email,
    subject="Reset your password",
    body=f"Reset your password here: {password_reset.password_reset_url}\n"
         f"This link expires at {password_reset.expires_at}.",
)

# Step 3: User clicks the link and lands on your reset page
# Extract the token from the URL query string: ?token=Z1uX3RbwcIl5fIGJJJCXXisdI
token = request.args.get("token")

# Step 4: User submits a new password
result = client.user_management.reset_password(
    token=token,
    new_password="newSecurePassword123!",
)

# Step 5: Redirect user to login (all sessions were revoked)
user = result.user
```

---

## Related

- [User](https://workos.com/docs/reference/authkit/user)
- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Session](https://workos.com/docs/reference/authkit/session)
- [Custom Emails](https://workos.com/docs/authkit/custom-emails)