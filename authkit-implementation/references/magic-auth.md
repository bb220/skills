# Magic Auth

Magic Auth is a passwordless authentication method that allows users to sign in or sign up via a unique, six-digit one-time-use code sent to their email inbox. To verify the code, [authenticate the user with Magic Auth](./04-authentication.md#authenticate-with-magic-auth).

**Source:** https://workos.com/docs/reference/authkit/magic-auth

---

## The Magic Auth Object

```json
{
  "id": "magic_auth_01E4ZCR3C56J083X43JQXF3JK5",
  "user_id": "user_01HWWYEH2NPT48X82ZT23K5AX4",
  "email": "marcelina.davis@example.com",
  "expires_at": "2021-07-01T19:07:33.155Z",
  "code": "123456",
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z"
}
```

---

## Get a Magic Auth Code

Get the details of an existing Magic Auth code that can be used to send an email to a user for authentication.

`GET /user_management/magic_auth/:id`

```bash
curl https://api.workos.com/user_management/magic_auth/magic_auth_01E4ZCR3C56J083X43JQXF3JK5 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The Magic Auth code ID. |

**Returns:** `magic_auth` object

---

## Create a Magic Auth Code

Creates a one-time authentication code that can be sent to the user's email address. The code expires in **10 minutes**. To verify the code, [authenticate the user with Magic Auth](./04-authentication.md#authenticate-with-magic-auth).

`POST /user_management/magic_auth` _(Sends email)_

```bash
curl --request POST \
  --url https://api.workos.com/user_management/magic_auth \
  --header "Authorization: Bearer sk_example_123456789" \
  -d email="marcelina.davis@example.com"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✓ | The user's email address. |
| `invitation_token` | string | optional | Token from a pending invitation. |

**Returns:** `magic_auth` object