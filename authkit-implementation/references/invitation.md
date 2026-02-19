# Invitation

An email invitation allows a recipient to sign up for your app and join a specific [organization](https://workos.com/docs/reference/organization). When an invitation is accepted, a [user](https://workos.com/docs/reference/authkit/user) and a corresponding [organization membership](https://workos.com/docs/reference/authkit/organization-membership) are created.

Users may be invited without joining an organization, or invited to join an organization if they already have an account. Invitations can also be issued on behalf of another user — the invitation email will mention the inviter's name.

---

## The Invitation Object

```python
invitation = {
    "object": "invitation",
    "id": "invitation_01E4ZCR3C56J083X43JQXF3JK5",
    "email": "marcelina.davis@example.com",
    "state": "pending",
    "accepted_at": None,
    "revoked_at": None,
    "expires_at": "2021-07-01T19:07:33.155Z",
    "token": "Z1uX3RbwcIl5fIGJJJCXXisdI",
    "accept_invitation_url": "https://your-app.com/invite?invitation_token=Z1uX3RbwcIl5fIGJJJCXXisdI",
    "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
    "inviter_user_id": "user_01HYGBX8ZGD19949T3BM4FW1C3",
    "created_at": "2021-06-25T19:07:33.155Z",
    "updated_at": "2021-06-25T19:07:33.155Z",
}
```

---

## Attributes

### `Invitation`

| Attribute               | Type   | Description                                                                            |
|-------------------------|--------|----------------------------------------------------------------------------------------|
| `id`                    | `str`  | The unique ID of the invitation.                                                       |
| `email`                 | `str`  | The email address of the recipient.                                                    |
| `state`                 | `str`  | `"pending"`, `"accepted"`, `"revoked"`, or `"expired"`.                               |
| `accepted_at`           | `str`  | ISO 8601 timestamp when the invitation was accepted (null if not yet accepted).        |
| `revoked_at`            | `str`  | ISO 8601 timestamp when the invitation was revoked (null if not revoked).              |
| `expires_at`            | `str`  | ISO 8601 timestamp when the invitation expires.                                        |
| `token`                 | `str`  | The one-time invitation token.                                                         |
| `accept_invitation_url` | `str`  | The full URL the recipient should be directed to in order to accept the invitation.    |
| `organization_id`       | `str`  | The ID of the organization the user is being invited to join (optional).               |
| `inviter_user_id`       | `str`  | The ID of the user who sent the invitation (optional).                                 |
| `created_at`            | `str`  | ISO 8601 timestamp when the invitation was created.                                    |
| `updated_at`            | `str`  | ISO 8601 timestamp when the invitation was last updated.                               |

---

## Operations

### Get an Invitation

Retrieves the details of an existing invitation by its ID.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

invitation = client.user_management.get_invitation(
    id="invitation_01EHZNVPK3SFK441A1RGBFSHRT"
)

print(invitation.state)                 # "pending"
print(invitation.email)                 # "marcelina.davis@example.com"
print(invitation.accept_invitation_url) # full URL
```

**Parameters**

| Parameter | Type  | Required | Description                    |
|-----------|-------|----------|--------------------------------|
| `id`      | `str` | Required | The ID of the invitation.      |

**Returns:** [`Invitation`](#the-invitation-object)

---

### Find an Invitation by Token

Retrieves an existing invitation using its token. Use this to verify the invitation before accepting it.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

invitation = client.user_management.find_invitation_by_token(
    token="Z1uX3RbwcIl5fIGJJJCXXisdI"
)

# Verify the invitation email matches the signed-in user's email
if invitation.email == current_user.email:
    # safe to accept
    pass
```

**Parameters**

| Parameter | Type  | Required | Description                           |
|-----------|-------|----------|---------------------------------------|
| `token`   | `str` | Required | The invitation token from the URL.    |

**Returns:** [`Invitation`](#the-invitation-object)

---

### List Invitations

Gets a list of invitations matching the specified criteria.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

# List all invitations for an organization
invitations = client.user_management.list_invitations(
    organization_id="org_123456789",
)

# List invitations for a specific email
invitations = client.user_management.list_invitations(
    email="marcelina@example.com",
)

for invitation in invitations.data:
    print(invitation.email, invitation.state)
```

**Parameters**

| Parameter         | Type  | Required | Description                                          |
|-------------------|-------|----------|------------------------------------------------------|
| `email`           | `str` | Optional | Filter by recipient email address.                   |
| `organization_id` | `str` | Optional | Filter by organization ID.                           |
| `limit`           | `int` | Optional | Maximum number of results.                           |
| `before`          | `str` | Optional | Pagination cursor.                                   |
| `after`           | `str` | Optional | Pagination cursor.                                   |
| `order`           | `str` | Optional | `"asc"` or `"desc"`.                               |

**Returns**

| Field           | Type              | Description              |
|-----------------|-------------------|--------------------------|
| `data`          | `list[Invitation]`| List of invitations.     |
| `list_metadata` | `object`          | Pagination metadata.     |

---

### Send an Invitation

Sends an invitation email to the recipient.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

invitation = client.user_management.send_invitation(
    email="marcelina@example.com",
    organization_id="org_01E4ZCR3C56J083X43JQXF3JK5",
    expires_in_days=7,
    inviter_user_id="user_01HYGBX8ZGD19949T3BM4FW1C3",
    role_slug="member",
)

print(invitation.accept_invitation_url) # URL to share with the recipient
```

**Parameters**

| Parameter          | Type  | Required | Description                                                     |
|--------------------|-------|----------|-----------------------------------------------------------------|
| `email`            | `str` | Required | The email address to invite.                                    |
| `organization_id`  | `str` | Optional | The organization to invite the user into.                       |
| `expires_in_days`  | `int` | Optional | How many days until the invitation expires.                     |
| `inviter_user_id`  | `str` | Optional | The ID of the user sending the invitation (shown in the email). |
| `role_slug`        | `str` | Optional | The role to assign when the invitation is accepted.             |

**Returns:** [`Invitation`](#the-invitation-object)

---

### Resend an Invitation

Resends an invitation email. The invitation must be in a `pending` state.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

invitation = client.user_management.resend_invitation(
    id="invitation_01E4ZCR3C56J083X43JQXF3JK5"
)

print(invitation.state) # "pending"
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the invitation to resend.      |

**Returns:** [`Invitation`](#the-invitation-object)

---

### Accept an Invitation

Accepts an invitation and, if linked to an organization, activates the user's membership.

> **Note:** In most cases, use `authenticate_with_code()` with an `invitation_token` instead. This handles invitation acceptance and user authentication in a single call. Use `accept_invitation()` only when you need a custom flow where the user is already signed in.

Always verify that the invitation is intended for the accepting user — use `find_invitation_by_token()` and compare emails.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Verify the invitation belongs to the current user first
token = request.args.get("invitation_token")
invitation = client.user_management.find_invitation_by_token(token=token)

if invitation.email == current_user.email:
    accepted = client.user_management.accept_invitation(
        id=invitation.id
    )
    print(accepted.state) # "accepted"
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the invitation to accept.      |

**Returns:** [`Invitation`](#the-invitation-object)

---

### Revoke an Invitation

Revokes an existing invitation.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

invitation = client.user_management.revoke_invitation(
    id="invitation_01E4ZCR3C56J083X43JQXF3JK5"
)

print(invitation.state) # "revoked"
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the invitation to revoke.      |

**Returns:** [`Invitation`](#the-invitation-object)

---

## Full Invitation Flow

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

# Step 1: Admin sends an invitation
invitation = client.user_management.send_invitation(
    email="newmember@example.com",
    organization_id="org_01E4ZCR3C56J083X43JQXF3JK5",
    role_slug="member",
    inviter_user_id="user_admin_123",
)

# Step 2: User receives the email and clicks accept_invitation_url
# Your callback endpoint receives the invitation_token query param

# Step 3: Authenticate the user, passing the invitation token
# This creates the user (if new) and activates their org membership
token = request.args.get("invitation_token")

result = client.user_management.authenticate_with_code(
    code=request.args.get("code"),
    invitation_token=token,
)

user = result.user
organization_id = result.organization_id
```

---

## Related

- [Organization Membership](https://workos.com/docs/reference/authkit/organization-membership)
- [User](https://workos.com/docs/reference/authkit/user)
- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Invitations Guide](https://workos.com/docs/authkit/invitations)