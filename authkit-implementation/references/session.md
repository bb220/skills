# Session

Represents an authenticated user's connection to your application. A session is created when a user signs in through AuthKit and contains information about the authentication method, device details, and session status.

**Source:** https://workos.com/docs/reference/authkit/session

---

## The Session Object

```json
{
  "object": "session",
  "id": "session_01E4ZCR3C56J083X43JQXF3JK5",
  "user_id": "user_01E4ZCR3C56J083X43JQXF3JK5",
  "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
  "status": "active",
  "auth_method": "password",
  "ip_address": "192.168.1.1",
  "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
  "expires_at": "2025-07-23T15:00:00.000Z",
  "ended_at": null,
  "created_at": "2025-07-23T14:00:00.000Z",
  "updated_at": "2025-07-23T14:00:00.000Z"
}
```

---

## List Sessions

Get a list of all active sessions for a specific user.

`GET /user_management/users/:id/sessions`

```bash
curl "https://api.workos.com/user_management/users/user_01E4ZCR3C56J083X43JQXF3JK5/sessions" \
  -H "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `id` | string | ✓ | The user ID. |
| `limit` | number | optional | |
| `before` | string | optional | Pagination cursor. |
| `after` | string | optional | Pagination cursor. |
| `order` | `"asc" \| "desc"` | optional | |

**Returns:** `{ data: array, list_metadata: object }`

---

## Revoke Session

Revoke a session.

`POST /user_management/sessions/revoke`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/sessions/revoke \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{ "session_id": "session_01E4ZCR3C56J083X43JQXF3JK5" }'
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `session_id` | string | The ID of the session to revoke. |