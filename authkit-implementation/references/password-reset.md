# Password Reset

Create a password reset token for a user and reset the user's password.

> When a user's password is reset, all of their active sessions are revoked.

**Source:** https://workos.com/docs/reference/authkit/password-reset

---

## The Password Reset Object

```json
{
  "id": "password_reset_01HYGDNK5G7FZ4YJFXYXPB5JRW",
  "user_id": "user_01HWWYEH2NPT48X82ZT23K5AX4",
  "email": "marcelina.davis@example.com",
  "password_reset_token": "Z1uX3RbwcIl5fIGJJJCXXisdI",
  "password_reset_url": "https://your-app.com/reset-password?token=Z1uX3RbwcIl5fIGJJJCXXisdI",
  "expires_at": "2025-07-14T18:00:54.578Z",
  "created_at": "2025-07-14T17:45:54.578Z"
}
```

---

## Get a Password Reset Token

Get the details of an existing password reset token.

`GET /user_management/password_reset/:id`

```bash
curl https://api.workos.com/user_management/password_reset/password_reset_01HYGDNK5G7FZ4YJFXYXPB5JRW \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The password reset ID. |

**Returns:** `password_reset` object

---

## Create a Password Reset Token

Creates a one-time token that can be used to reset a user's password.

`POST /user_management/password_reset` _(Sends email)_

```bash
curl --request POST \
  --url https://api.workos.com/user_management/password_reset \
  --header "Authorization: Bearer sk_example_123456789" \
  -d email="marcelina.davis@example.com"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✓ | The user's email address. |

**Returns:** `password_reset` object

---

## Reset the Password

Sets a new password using the token from the reset link. Successfully resetting the password will also verify a user's email if it hasn't been verified yet.

`POST /user_management/password_reset/confirm`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/password_reset/confirm \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{
    "token": "stpIJ48IFJt0HhSIqjf8eppe0",
    "new_password": "i8uv6g34kd490s"
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `token` | string | ✓ | The reset token from the email link. |
| `new_password` | string | ✓ | The user's new password. |

**Returns:** `{ user: user }`