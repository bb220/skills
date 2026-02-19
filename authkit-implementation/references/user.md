# User

Represents a user identity in your application. A user can sign up in your application directly with a method like password, or they can be [JIT-provisioned](https://workos.com/docs/authkit/jit-provisioning) through an organization's SSO connection.

Users may belong to [organizations](https://workos.com/docs/reference/organization) as members.

See the [events reference](https://workos.com/docs/events/user) documentation for the user events.

**Source:** https://workos.com/docs/reference/authkit/user

---

## The User Object

```json
{
  "object": "user",
  "id": "user_01E4ZCR3C56J083X43JQXF3JK5",
  "email": "marcelina.davis@example.com",
  "first_name": "Marcelina",
  "last_name": "Davis",
  "email_verified": true,
  "profile_picture_url": "https://workoscdn.com/images/v1/123abc",
  "last_sign_in_at": "2021-06-25T19:07:33.155Z",
  "external_id": "f1ffa2b2-c20b-4d39-be5c-212726e11222",
  "metadata": {
    "timezone": "America/New_York"
  },
  "locale": "en-US",
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z"
}
```

### Properties

| Property | Type | Required | Description |
|---|---|---|---|
| `object` | `"user"` | ✓ | Distinguishes the user object. |
| `id` | string | ✓ | The unique ID of the user. |
| `email` | string | ✓ | The email address of the user. |
| `first_name` | string | optional | The first name of the user. |
| `last_name` | string | optional | The last name of the user. |
| `email_verified` | boolean | ✓ | Whether the user's email has been verified. |
| `profile_picture_url` | string | optional | A URL reference to an image representing the user. Currently present for users with a Google or GitHub OAuth profile picture. |
| `last_sign_in_at` | string | optional | The timestamp when the user last signed in. |
| `external_id` | string | optional | The external ID of the user. |
| `metadata` | object | ✓ | Object containing [metadata](https://workos.com/docs/authkit/metadata) key/value pairs associated with the user. |
| `locale` | string | optional | The user's preferred locale. |
| `created_at` | string | ✓ | The timestamp when the user was created. |
| `updated_at` | string | ✓ | The timestamp when the user was last updated. |

---

## Get a User

Get the details of an existing user.

`GET /user_management/users/:id`

```bash
curl https://api.workos.com/user_management/users/user_01E4ZCR3C56J083X43JQXF3JK5 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The ID of the user. |

**Returns:** [`user`](#the-user-object)

---

## Get a User by External ID

Get the details of an existing user by an [external identifier](https://workos.com/docs/authkit/metadata/external-identifiers).

`GET /user_management/users/external_id/:external_id`

```bash
curl https://api.workos.com/user_management/users/external_id/f1ffa2b2-c20b-4d39-be5c-212726e11222 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `external_id` | string | The external ID of the user. |

**Returns:** [`user`](#the-user-object)

---

## List Users

Get a list of all of your existing users matching the criteria specified.

`GET /user_management/users`

```bash
curl https://api.workos.com/user_management/users \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | optional | Filter by email. |
| `organization_id` | string | optional | Filter by organization. |
| `limit` | number | optional | Max results to return. |
| `before` | string | optional | Pagination cursor. |
| `after` | string | optional | Pagination cursor. |
| `order` | `"asc" \| "desc"` | optional | Sort order. |

**Returns:** `{ data: array, list_metadata: object }`

---

## Create a User

Create a new user in the current environment.

`POST /user_management/users`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/users \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{
    "email": "marcelina@example.com",
    "password": "i8uv6g34kd490s",
    "first_name": "Marcelina",
    "last_name": "Davis",
    "email_verified": false
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✓ | The user's email. |
| `password` | string | optional | Plain-text password. |
| `password_hash` | string | optional | Pre-hashed password. |
| `password_hash_type` | string | optional | Algorithm used for the hash. |
| `first_name` | string | optional | |
| `last_name` | string | optional | |
| `email_verified` | boolean | optional | |
| `external_id` | string | optional | |
| `metadata` | object | optional | |

**Returns:** [`user`](#the-user-object)

---

## Update a User

Updates properties of a user. Omitted properties will be left unchanged.

`PUT /user_management/users/:id`

```bash
curl --request PUT \
  --url https://api.workos.com/user_management/users/user_01EHQ7ZGZ2CZVQJGZ5ZJZ1ZJGZ \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{
    "first_name": "Marcelina",
    "last_name": "Davis",
    "email_verified": true,
    "external_id": "2fe01467-f7ea-4dd2-8b79-c2b4f56d0191",
    "metadata": { "timezone": "America/New_York" },
    "locale": "en-US"
  }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | ✓ | User ID. |
| `first_name` | string | optional | |
| `last_name` | string | optional | |
| `email` | string | optional | |
| `email_verified` | boolean | optional | |
| `password` | string | optional | |
| `password_hash` | string | optional | |
| `password_hash_type` | string | optional | |
| `external_id` | string | optional | |
| `metadata` | object | optional | |
| `locale` | string | optional | |

**Returns:** [`user`](#the-user-object)

---

## Delete a User

Permanently deletes a user in the current environment. It cannot be undone.

`DELETE /user_management/users/:id`

```bash
curl --request DELETE \
  --url https://api.workos.com/user_management/users/user_01F3GZ5ZGZBZVQGZVHJFVXZJGZ \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | User ID. |