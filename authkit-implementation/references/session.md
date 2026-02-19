# Session

Represents an authenticated user's connection to your application. A session is created when a user signs in through AuthKit and contains information about the authentication method, device details, and session status.

---

## The Session Object

```python
session = {
    "object": "session",
    "id": "session_01E4ZCR3C56J083X43JQXF3JK5",
    "user_id": "user_01E4ZCR3C56J083X43JQXF3JK5",
    "organization_id": "org_01E4ZCR3C56J083X43JQXF3JK5",
    "status": "active",
    "auth_method": "password",
    "ip_address": "192.168.1.1",
    "user_agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36",
    "expires_at": "2025-07-23T15:00:00.000Z",
    "ended_at": None,
    "created_at": "2025-07-23T14:00:00.000Z",
    "updated_at": "2025-07-23T14:00:00.000Z",
}
```

---

## Attributes

### `Session`

| Attribute         | Type   | Description                                                                        |
|-------------------|--------|------------------------------------------------------------------------------------|
| `id`              | `str`  | The unique ID of the session. Also available as `sid` in the access token JWT.     |
| `user_id`         | `str`  | The ID of the user this session belongs to.                                        |
| `organization_id` | `str`  | The ID of the organization scoped to this session (if applicable).                 |
| `status`          | `str`  | `"active"` or `"revoked"`.                                                         |
| `auth_method`     | `str`  | The authentication method used: `"password"`, `"sso"`, `"magic_auth"`, etc.       |
| `ip_address`      | `str`  | The IP address of the user at the time of authentication.                          |
| `user_agent`      | `str`  | The browser/device user agent string.                                              |
| `expires_at`      | `str`  | ISO 8601 timestamp when the session expires.                                       |
| `ended_at`        | `str`  | ISO 8601 timestamp when the session was ended (null if still active).              |
| `created_at`      | `str`  | ISO 8601 timestamp when the session was created.                                   |
| `updated_at`      | `str`  | ISO 8601 timestamp when the session was last updated.                              |

---

## Operations

### List Sessions

Get a list of all active sessions for a specific user.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

sessions = client.user_management.list_sessions(
    user_id="user_01E4ZCR3C56J083X43JQXF3JK5",
)

for session in sessions.data:
    print(session.id, session.status, session.auth_method)
```

**Parameters**

| Parameter    | Type   | Required | Description                                         |
|--------------|--------|----------|-----------------------------------------------------|
| `user_id`    | `str`  | Required | The ID of the user whose sessions to retrieve.      |
| `limit`      | `int`  | Optional | Maximum number of sessions to return.               |
| `before`     | `str`  | Optional | Pagination cursor — return sessions before this ID. |
| `after`      | `str`  | Optional | Pagination cursor — return sessions after this ID.  |
| `order`      | `str`  | Optional | `"asc"` or `"desc"`. Default is `"desc"`.          |

**Returns**

| Field           | Type            | Description                          |
|-----------------|-----------------|--------------------------------------|
| `data`          | `list[Session]` | List of session objects.             |
| `list_metadata` | `object`        | Pagination metadata (`before`, `after`). |

---

### Revoke Session

Revokes a session, immediately ending it. The user will need to sign in again.

```python
import workos

client = workos.WorkOSClient(api_key="sk_example_123456789")

client.user_management.revoke_session(
    session_id="session_01E4ZCR3C56J083X43JQXF3JK5"
)
```

**Parameters**

| Parameter    | Type  | Required | Description                         |
|--------------|-------|----------|-------------------------------------|
| `session_id` | `str` | Required | The ID of the session to revoke.    |

**Returns:** No content on success.

---

## Session Configuration

Session behavior can be configured in the WorkOS Dashboard under **Authentication → Sessions**:

| Setting                | Description                                                                      |
|------------------------|----------------------------------------------------------------------------------|
| **Maximum session length** | How long a session remains valid before requiring re-authentication.         |
| **Access token duration**  | How frequently the access token expires and must be refreshed.               |
| **Inactivity timeout**     | Session ends if no refresh occurs within this period.                        |

---

## Related

- [Session Tokens](https://workos.com/docs/reference/authkit/session-tokens)
- [Session Helpers](https://workos.com/docs/reference/authkit/session-helpers)
- [Authentication](https://workos.com/docs/reference/authkit/authentication)
- [Logout](https://workos.com/docs/reference/authkit/logout)
- [Events Reference – Session Events](https://workos.com/docs/events)