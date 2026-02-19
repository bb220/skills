# User

Represents a user identity in your application. A user can sign up in your application directly with a method like password, or they can be [JIT-provisioned](https://workos.com/docs/authkit/jit-provisioning) through an organization's SSO connection.

Users may belong to [organizations](https://workos.com/docs/reference/organization) as members.

See the [events reference](https://workos.com/docs/events/user) documentation for the user events.

---

## The User Object

```python
from workos.types.user_management import User

user = User(
    object="user",
    id="user_01E4ZCR3C56J083X43JQXF3JK5",
    email="marcelina.davis@example.com",
    first_name="Marcelina",
    last_name="Davis",
    email_verified=True,
    profile_picture_url="https://workoscdn.com/images/v1/123abc",
    last_sign_in_at="2021-06-25T19:07:33.155Z",
    created_at="2021-06-25T19:07:33.155Z",
    updated_at="2021-06-25T19:07:33.155Z",
)
```

---

## Attributes

### `User`

| Attribute             | Type   | Required | Description                                                                                                                                                                                  |
|-----------------------|--------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `id`                  | `str`  | Required | The unique ID of the user.                                                                                                                                                                   |
| `email`               | `str`  | Required | The email address of the user.                                                                                                                                                               |
| `email_verified`      | `bool` | Required | Whether the user's email has been verified.                                                                                                                                                  |
| `created_at`          | `str`  | Required | The timestamp when the user was created.                                                                                                                                                     |
| `updated_at`          | `str`  | Required | The timestamp when the user was last updated.                                                                                                                                                |
| `first_name`          | `str`  | Optional | The first name of the user.                                                                                                                                                                  |
| `last_name`           | `str`  | Optional | The last name of the user.                                                                                                                                                                   |
| `profile_picture_url` | `str`  | Optional | A URL reference to an image representing the user. Currently present for users with a profile picture from a linked Google or GitHub OAuth identity. New image sources may be added in the future. |
| `last_sign_in_at`     | `str`  | Optional | The timestamp when the user last signed in.                                                                                                                                                  |
| `external_id`         | `str`  | Optional | The external ID of the user.                                                                                                                                                                 |
| `metadata`            | `dict` | Optional | Object containing [metadata](https://workos.com/docs/authkit/metadata) key/value pairs associated with the user.                                                                            |
| `locale`              | `str`  | Optional | The user's preferred locale.                                                                                                                                                                 |

---

## Operations

### Get User

Get the details of an existing user.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

user = client.user_management.get_user(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5"
)
```

### Get User by External ID

Get the details of an existing user by an external identifier.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

user = client.user_management.get_user_by_external_id(
    external_id="f1ffa2b2-c20b-4d39-be5c-212726e11222"
)
```

### List Users

Get a list of all of your existing users matching the criteria specified.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

users = client.user_management.list_users(
    email="marcelina.davis@example.com",
    limit=10,
    order="desc",
)
```

### Create User

Create a new user in the current environment.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

user = client.user_management.create_user(
    email="marcelina.davis@example.com",
    first_name="Marcelina",
    last_name="Davis",
    email_verified=True,
    password="i8uv6g34kd490s",
)
```

### Update User

Updates properties of a user. The omitted properties will be left unchanged.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

user = client.user_management.update_user(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5",
    first_name="Marcelina",
    last_name="Davis",
    email_verified=True,
)
```

### Delete User

Permanently deletes a user in the current environment.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

client.user_management.delete_user(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5"
)
```

---

## Related

- [Events Reference – User Events](https://workos.com/docs/events/user)
- [Organizations](https://workos.com/docs/reference/organization)
- [Metadata](https://workos.com/docs/authkit/metadata)
- [JIT Provisioning](https://workos.com/docs/authkit/jit-provisioning)