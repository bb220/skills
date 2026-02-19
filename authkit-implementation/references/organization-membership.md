# Organization Membership

An organization membership is a top-level resource that represents a [user](https://workos.com/docs/reference/authkit/user)'s relationship with an [organization](https://workos.com/docs/reference/organization). A user may be a member of zero, one, or many organizations.

See the [events reference](https://workos.com/docs/events/organization-membership) documentation for organization membership events.

**Source:** https://workos.com/docs/reference/authkit/organization-membership

---

## The Organization Membership Object

```json
{
  "object": "organization_membership",
  "id": "om_01E4ZCR3C56J083X43JQXF3JK5",
  "user_id": "user_01E4ZCR3C5A4QZ2Z2JQXGKZJ9E",
  "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
  "organization_name": "Foo Corp",
  "role": { "slug": "admin" },
  "roles": [{ "slug": "admin" }],
  "status": "active",
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z"
}
```

### Properties

| Property | Type | Description |
|---|---|---|
| `object` | `"organization_membership"` | |
| `id` | string | |
| `userId` | string | |
| `organizationId` | string | |
| `organizationName` | string | |
| `role` | object | Primary role. |
| `roles` | array | All roles. |
| `status` | `"active" \| "inactive" \| "pending"` | |
| `createdAt` | string | |
| `updatedAt` | string | |

---

## Get an Organization Membership

`GET /user_management/organization_memberships/:id`

```bash
curl https://api.workos.com/user_management/organization_memberships/om_01E4ZCR3C56J083X43JQXF3JK5 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

**Returns:** `organization_membership` object

---

## List Organization Memberships

Get a list of all organization memberships matching the criteria. At least one of `user_id` or `organization_id` must be provided. By default only active memberships are returned; use `statuses` to filter.

`GET /user_management/organization_memberships`

```bash
curl https://api.workos.com/user_management/organization_memberships \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `user_id` | string | optional |
| `organization_id` | string | optional |
| `statuses` | array | optional |
| `limit` | number | optional |
| `before` | string | optional |
| `after` | string | optional |
| `order` | `"asc" \| "desc"` | optional |

**Returns:** `{ data: array, list_metadata: object }`

---

## Create an Organization Membership

Creates a new `active` organization membership. Calling this API with a matching `inactive` membership will reactivate it with the specified role(s).

`POST /user_management/organization_memberships`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/organization_memberships \
  --header "Authorization: Bearer sk_example_123456789" \
  -d user_id="user_01E4ZCR3C5A4QZ2Z2JQXGKZJ9E" \
  -d organization_id="org_01E4ZCR3C56J083X43JQXF3JK5" \
  -d role_slug="admin"
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `user_id` | string | ✓ |
| `organization_id` | string | ✓ |
| `role_slug` | string | optional |
| `role_slugs` | array | optional |

**Returns:** `organization_membership` object

---

## Update an Organization Membership

`PUT /user_management/organization_memberships/:id`

```bash
curl --request PUT \
  --url https://api.workos.com/user_management/organization_memberships/om_01E4ZCR3C56J083X43JQXF3JK5 \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{ "role_slug": "admin" }'
```

**Parameters**

| Parameter | Type | Required |
|---|---|---|
| `id` | string | ✓ |
| `role_slug` | string | optional |
| `role_slugs` | array | optional |

**Returns:** `organization_membership` object

---

## Delete an Organization Membership

Permanently deletes an organization membership. Cannot be undone.

`DELETE /user_management/organization_memberships/:id`

```bash
curl --request DELETE \
  --url https://api.workos.com/user_management/organization_memberships/om_01E4ZCR3C56J083X43JQXF3JK5 \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

---

## Deactivate an Organization Membership

Deactivates an `active` membership. Emits an `organization_membership.updated` event.

- Deactivating an `inactive` membership is a no-op (no event emitted).
- Deactivating a `pending` membership returns an error — delete it instead.

`PUT /user_management/organization_memberships/:id/deactivate`

```bash
curl --request PUT \
  --url https://api.workos.com/user_management/organization_memberships/om_01E4ZCR3C56J083X43JQXF3JK5/deactivate \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

**Returns:** `organization_membership` object

---

## Reactivate an Organization Membership

Reactivates an `inactive` membership, retaining existing role(s). Emits an `organization_membership.updated` event.

- Reactivating an `active` membership is a no-op (no event emitted).
- Reactivating a `pending` membership returns an error — the user must accept the invitation.

`PUT /user_management/organization_memberships/:id/reactivate`

```bash
curl --request PUT \
  --url https://api.workos.com/user_management/organization_memberships/om_01E4ZCR3C56J083X43JQXF3JK5/reactivate \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters:** `id: string`

**Returns:** `organization_membership` object