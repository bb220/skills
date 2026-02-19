# Magic Auth

Magic Auth is a passwordless authentication method that allows users to sign in or sign up via a unique, six-digit one-time code sent to their email inbox. Codes expire after **10 minutes**.

AuthKit handles Magic Auth automatically when enabled. If you prefer to build a custom authentication UI, use the API below to generate and send codes yourself.

Magic Auth can be enabled in the **Authentication** section of the WorkOS Dashboard.

---

## The Magic Auth Object

```python
magic_auth = {
    "object": "magic_auth",
    "id": "magic_auth_01E4ZCR3C56J083X43JQXF3JK5",
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

### `MagicAuth`

| Attribute    | Type  | Description                                               |
|--------------|-------|-----------------------------------------------------------|
| `id`         | `str` | The unique ID of the Magic Auth code.                     |
| `user_id`    | `str` | The ID of the user this code belongs to.                  |
| `email`      | `str` | The email address the code was sent to.                   |
| `expires_at` | `str` | ISO 8601 timestamp when the code expires (10 min TTL).   |
| `code`       | `str` | The 6-digit one-time code.                                |
| `created_at` | `str` | ISO 8601 timestamp when the code was created.             |
| `updated_at` | `str` | ISO 8601 timestamp when the code was last updated.        |

---

## Operations

### Get a Magic Auth Code

Retrieves the details of an existing Magic Auth code. Use this to get the code so you can send a custom verification email.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

magic_auth = client.user_management.get_magic_auth(
    id="magic_auth_01E4ZCR3C56J083X43JQXF3JK5"
)

print(magic_auth.code)       # "123456"
print(magic_auth.email)      # "marcelina.davis@example.com"
print(magic_auth.expires_at) # "2021-07-01T19:07:33.155Z"
```

**Parameters**

| Parameter | Type  | Required | Description                        |
|-----------|-------|----------|------------------------------------|
| `id`      | `str` | Required | The ID of the Magic Auth code.     |

**Returns:** [`MagicAuth`](#the-magic-auth-object)

---

### Create a Magic Auth Code

Creates a one-time 6-digit code that can be sent to the user's email address. The code expires in 10 minutes.

After creating the code, send it to the user via your own email provider, then verify it using [Authenticate with Magic Auth](https://workos.com/docs/reference/authkit/authentication).

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

magic_auth = client.user_management.create_magic_auth(
    email="marcelina@example.com",
)

# Send magic_auth.code to the user via your email provider
print(magic_auth.code)  # "123456"
```

**Parameters**

| Parameter          | Type  | Required | Description                                                      |
|--------------------|-------|----------|------------------------------------------------------------------|
| `email`            | `str` | Required | The email address to create a Magic Auth code for.               |
| `invitation_token` | `str` | Optional | Token from an invitation to associate the code with an invitation.|

**Returns:** [`MagicAuth`](#the-magic-auth-object)

> **Note:** If the email setting for Magic Auth is enabled in the WorkOS Dashboard, WorkOS will automatically send the code email. If disabled, retrieve the code and send the email yourself.

---

## Full Custom Magic Auth Flow

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Step 1: Create the code
magic_auth = client.user_management.create_magic_auth(
    email="marcelina@example.com",
)

# Step 2: Send the code via your email provider
send_email(
    to=magic_auth.email,
    subject="Your sign-in code",
    body=f"Your code is: {magic_auth.code}. It expires in 10 minutes.",
)

# Step 3: After user submits the code, authenticate them
result = client.user_management.authenticate_with_magic_auth(
    code="123456",  # code entered by the user
    email="marcelina@example.com",
)

user = result.user
access_token = result.access_token
refresh_token = result.refresh_token
```

---

## Related

- [Authentication – Authenticate with Magic Auth](https://workos.com/docs/reference/authkit/authentication)
- [Authentication Errors](https://workos.com/docs/reference/authkit/authentication-errors)
- [Email Verification](https://workos.com/docs/reference/authkit/email-verification)