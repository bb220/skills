# Email Verification

Email verification is a security feature that requires users to verify their email address before they can sign in. It is **enabled by default**.

Users signing in with Magic Auth, Google OAuth, Apple OAuth, or SSO are automatically verified. For other authentication methods, an email verification flow is required.

**Source:** https://workos.com/docs/reference/authkit/email-verification

---

## The Email Verification Object

```json
{
  "id": "email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3",
  "user_id": "user_01HWWYEH2NPT48X82ZT23K5AX4",
  "email": "marcelina.davis@example.com",
  "expires_at": "2021-07-01T19:07:33.155Z",
  "code": "123456",
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z"
}
```

---

## Get an Email Verification Code

Get the details of an existing email verification code that can be used to send a verification email to a user.

`GET /user_management/email_verification/:id`

```bash
curl https://api.workos.com/user_management/email_verification/email_verification_01HYGGEB6FYMWQNWF3XDZG7VV3 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The email verification code ID. |

**Returns:** `email_verification` object