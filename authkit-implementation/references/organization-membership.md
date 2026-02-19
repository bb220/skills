# Organization Membership

An organization membership represents a [user](https://workos.com/docs/reference/authkit/user)'s relationship with an [organization](https://workos.com/docs/reference/organization). A user may be a member of zero, one, or many organizations.

See the [events reference](https://workos.com/docs/events/organization-membership) documentation for organization membership events.

---

## The Organization Membership Object

```python
organization_membership = {
    "object": "organization_membership",
    "id": "om_01E4ZCR3C56J083X43JQXF3JK5",
    "user_id": "user_01E4ZCR3C5A4QZ2Z2JQXGKZJ9E",
    "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
    "organization_name": "Foo Corp",
    "role": {"slug": "admin"},
    "roles": [{"slug": "admin"}],
    "status": "active",
    "created_at": "2021-06-25T19:07:33.155Z",
    "updated_at": "2021-06-25T19:07:33.155Z",
}
```

---

## Attributes

### `OrganizationMembership`

| Attribute           | Type   | Description                                                                   |
|---------------------|--------|-------------------------------------------------------------------------------|
| `id`                | `str`  | The unique ID of the membership.                                              |
| `user_id`           | `str`  | The ID of the user.                                                           |
| `organization_id`   | `str`  | The ID of the organization.                                                   |
| `organization_name` | `str`  | The display name of the organization.                                         |
| `role`              | `dict` | The primary role assigned to the user (e.g., `{"slug": "admin"}`).           |
| `roles`             | `list` | All roles assigned to the user in this organization.                          |
| `status`            | `str`  | `"active"`, `"inactive"`, or `"pending"`.                                    |
| `created_at`        | `str`  | ISO 8601 timestamp when the membership was created.                           |
| `updated_at`        | `str`  | ISO 8601 timestamp when the membership was last updated.                      |

---

## Operations

### Get an Organization Membership

Retrieves the details of an existing organization membership.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

membership = client.user_management.get_organization_membership(
    id="om_01E4ZCR3C56J083X43JQXF3JK5"
)

print(membership.status)            # "active"
print(membership.role["slug"])      # "admin"
print(membership.organization_name) # "Foo Corp"
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the organization membership.   |

**Returns:** [`OrganizationMembership`](#the-organization-membership-object)

---

### List Organization Memberships

Gets a list of organization memberships matching the specified criteria. At least one of `user_id` or `organization_id` must be provided. By default, only active memberships are returned.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

# List all memberships for a user
memberships = client.user_management.list_organization_memberships(
    user_id="user_01E4ZCR3C5A4QZ2Z2JQXGKZJ9E",
)

# List all memberships in an organization (including inactive)
memberships = client.user_management.list_organization_memberships(
    organization_id="org_01E4ZCR3C56J083X43JQXF3JK5",
    statuses=["active", "inactive"],
)

for membership in memberships.data:
    print(membership.user_id, membership.status)
```

**Parameters**

| Parameter         | Type   | Required | Description                                                       |
|-------------------|--------|----------|-------------------------------------------------------------------|
| `user_id`         | `str`  | Optional | Filter by user ID. At least one of `user_id` or `organization_id` is required. |
| `organization_id` | `str`  | Optional | Filter by organization ID.                                        |
| `statuses`        | `list` | Optional | Filter by status: `["active"]`, `["inactive"]`, `["pending"]`, or a combination. Defaults to `["active"]`. |
| `limit`           | `int`  | Optional | Maximum number of results.                                        |
| `before`          | `str`  | Optional | Pagination cursor.                                                |
| `after`           | `str`  | Optional | Pagination cursor.                                                |
| `order`           | `str`  | Optional | `"asc"` or `"desc"`.                                            |

**Returns**

| Field           | Type                          | Description              |
|-----------------|-------------------------------|--------------------------|
| `data`          | `list[OrganizationMembership]`| List of memberships.     |
| `list_metadata` | `object`                      | Pagination metadata.     |

---

### Create an Organization Membership

Creates a new `active` organization membership. If a matching `inactive` membership exists, it will be activated with the specified role(s).

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

membership = client.user_management.create_organization_membership(
    user_id="user_01E4ZCR3C5A4QZ2Z2JQXGKZJ9E",
    organization_id="org_01E4ZCR3C56J083X43JQXF3JK5",
    role_slug="admin",
)

print(membership.id)     # "om_01E4ZCR3C56J083X43JQXF3JK5"
print(membership.status) # "active"
```

**Parameters**

| Parameter         | Type   | Required | Description                                          |
|-------------------|--------|----------|------------------------------------------------------|
| `user_id`         | `str`  | Required | The ID of the user to add to the organization.       |
| `organization_id` | `str`  | Required | The ID of the organization.                          |
| `role_slug`       | `str`  | Optional | The primary role slug to assign (e.g., `"admin"`).   |
| `role_slugs`      | `list` | Optional | Multiple role slugs to assign simultaneously.        |

**Returns:** [`OrganizationMembership`](#the-organization-membership-object)

---

### Update an Organization Membership

Updates the role(s) of an existing organization membership.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

membership = client.user_management.update_organization_membership(
    id="om_01E4ZCR3C56J083X43JQXF3JK5",
    role_slug="member",
)

print(membership.role["slug"]) # "member"
```

**Parameters**

| Parameter    | Type   | Required | Description                                   |
|--------------|--------|----------|-----------------------------------------------|
| `id`         | `str`  | Required | The ID of the membership to update.           |
| `role_slug`  | `str`  | Optional | New primary role slug.                        |
| `role_slugs` | `list` | Optional | Replace all roles with this list of slugs.    |

**Returns:** [`OrganizationMembership`](#the-organization-membership-object)

---

### Delete an Organization Membership

Permanently deletes an organization membership. This action cannot be undone.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

client.user_management.delete_organization_membership(
    id="om_01E4ZCR3C56J083X43JQXF3JK5"
)
```

**Parameters**

| Parameter | Type  | Required | Description                              |
|-----------|-------|----------|------------------------------------------|
| `id`      | `str` | Required | The ID of the membership to delete.      |

**Returns:** No content on success.

---

### Deactivate an Organization Membership

Deactivates an `active` membership. Emits an `organization_membership.updated` event.

- Deactivating an `inactive` membership is a no-op.
- Deactivating a `pending` membership returns an error — delete it instead.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

membership = client.user_management.deactivate_organization_membership(
    id="om_01E4ZCR3C56J083X43JQXF3JK5"
)

print(membership.status) # "inactive"
```

**Parameters**

| Parameter | Type  | Required | Description                                  |
|-----------|-------|----------|----------------------------------------------|
| `id`      | `str` | Required | The ID of the membership to deactivate.      |

**Returns:** [`OrganizationMembership`](#the-organization-membership-object)

---

### Reactivate an Organization Membership

Reactivates an `inactive` membership, restoring the user's pre-existing role(s). Emits an `organization_membership.updated` event.

- Reactivating an `active` membership is a no-op.
- Reactivating a `pending` membership returns an error — the user must accept the invitation instead.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

membership = client.user_management.reactivate_organization_membership(
    id="om_01E4ZCR3C56J083X43JQXF3JK5"
)

print(membership.status) # "active"
```

**Parameters**

| Parameter | Type  | Required | Description                                  |
|-----------|-------|----------|----------------------------------------------|
| `id`      | `str` | Required | The ID of the membership to reactivate.      |

**Returns:** [`OrganizationMembership`](#the-organization-membership-object)

---

## Related

- [User](https://workos.com/docs/reference/authkit/user)
- [Organization](https://workos.com/docs/reference/organization)
- [Invitation](https://workos.com/docs/reference/authkit/invitation)
- [Events Reference – Organization Membership Events](https://workos.com/docs/events/organization-membership)
- [Membership Management Guide](https://workos.com/docs/authkit/users-organizations/organizations/membership-management)