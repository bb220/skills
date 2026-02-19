# Invitation

An email invitation allows the recipient to sign up for your app and join a specific [organization](https://workos.com/docs/reference/organization). When an invitation is accepted, a [user](https://workos.com/docs/reference/authkit/user) and a corresponding [organization membership](https://workos.com/docs/reference/authkit/organization-membership) are created.

Users may be invited without joining an organization, or to join an organization if they already have an account. Invitations may be issued on behalf of another user — the invitation email will mention the inviter's name.

**Source:** https://workos.com/docs/reference/authkit/invitation

---

## The Invitation Object

```json
{
  "id": "invitation_01E4ZCR3C56J083X43JQXF3JK5",
  "email": "marcelina.davis@example.com",
  "state": "pending",
  "accepted_at": null,
  "revoked_at": null,
  "expires_at": "2021-07-01T19:07:33.155Z",
  "token": "Z1uX3RbwcIl5fIGJJJCXXisdI",
  "accept_invitation_url": "https://your-app.com/invite?invitation_token=Z1uX3RbwcIl5fIGJJJCXXisdI",
  "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
  "inviter_user_id": "user_01HYGBX8ZGD19949T3BM4FW1C3",
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z"
}
```

---

## Get an Invitation

`GET /user_management/invitations/:id`

```bash
curl https://api.workos.com/user_management/invitations/invitation_123456789 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

**Returns:** `invitation` object

---

## Find an Invitation by Token

Retrieve an existing invitation using the token.

`GET /user_management/invitations/by_token/:token`

```bash
curl https://api.workos.com/user_management/invitations/by_token/Z1uX3RbwcIl5fIGJJJCXXisdI \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `token: string`

**Returns:** `invitation` object

---

## List Invitations

`GET /user_management/invitations`

```bash
curl https://api.workos.com/user_management/invitations \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `email` | string | optional |
| `organization_id` | string | optional |
| `limit` | number | optional |
| `before` | string | optional |
| `after` | string | optional |
| `order` | `"asc" \| "desc"` | optional |

**Returns:** `{ data: array, list_metadata: object }`

---

## Send an Invitation

Sends an invitation email to the recipient.

`POST /user_management/invitations` _(Sends email)_

```bash
curl --request POST \
  --url https://api.workos.com/user_management/invitations \
  --header "Authorization: Bearer sk_example_123456789" \
  -d email="marcelina.davis@example.com"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `email` | string | ✓ | Recipient's email. |
| `organization_id` | string | optional | Organization to invite into. |
| `expires_in_days` | int | optional | Days until the invitation expires. |
| `inviter_user_id` | string | optional | User sending the invitation. |
| `role_slug` | string | optional | Role to assign upon acceptance. |

**Returns:** `invitation` object

---

## Resend an Invitation

Resends an invitation email. The invitation must be in a `pending` state.

`POST /user_management/invitations/:id/resend` _(Sends email)_

```bash
curl --request POST \
  --url https://api.workos.com/user_management/invitations/invitation_01E4ZCR3C56J083X43JQXF3JK5/resend \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

**Returns:** `invitation` object

---

## Accept an Invitation

Accepts an invitation and, if linked to an organization, activates the user's membership in that organization.

In most cases, use existing authentication methods like `authenticateWithCode` with an `invitation_token` parameter rather than calling this endpoint directly. See the [invitations guide](https://workos.com/docs/authkit/invitations) for details.

`POST /user_management/invitations/:id/accept`

**Parameters:** `id: string`

**Returns:** `invitation` object