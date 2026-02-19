# API Keys

API keys provide a secure way for your application's users to authenticate with your API. Organization admins create API keys through the [API Keys Widget](https://workos.com/docs/widgets/api-keys), and your application can validate these keys to authenticate API requests.

Read more about [API keys in AuthKit](https://workos.com/docs/authkit/api-keys).

**Source:** https://workos.com/docs/reference/authkit/api-keys

---

## The API Key Object

```json
{
  "object": "api_key",
  "id": "api_key_01E4ZCR3C56J083X43JQXF3JK5",
  "owner": {
    "type": "organization",
    "id": "org_01EHWNCE74X7JSDV0X3SZ3KJNY"
  },
  "name": "Production API Key",
  "obfuscated_value": "sk_...3456",
  "permissions": ["posts:read", "posts:write"],
  "created_at": "2021-06-25T19:07:33.155Z",
  "updated_at": "2021-06-25T19:07:33.155Z",
  "last_used_at": "2021-06-25T19:07:33.155Z"
}
```

### Properties

| Property | Type | Required | Description |
|---|---|---|---|
| `object` | `"api_key"` | ✓ | Distinguishes the API key object. |
| `id` | string | ✓ | The unique ID of the API key. |
| `owner` | object | ✓ | The owner of the API key. |
| `owner.type` | string | ✓ | The type of owner (e.g., `"organization"`). |
| `owner.id` | string | ✓ | The ID of the owner. |
| `name` | string | ✓ | Human-readable name assigned to the API key. |
| `obfuscated_value` | string | ✓ | Obfuscated key value showing only prefix and last few characters. |
| `permissions` | array | ✓ | Permission strings assigned to this key. |
| `created_at` | string | ✓ | |
| `updated_at` | string | ✓ | |
| `last_used_at` | string | optional | Timestamp when last used; `null` if never used. |

---

## Validate an API Key

Validates an API key and returns associated metadata for authentication and authorization.

`POST /user_management/api_keys/validate`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/api_keys/validate \
  --header "Authorization: Bearer sk_example_123456789" \
  --header "Content-Type: application/json" \
  -d '{ "value": "sk_...user_provided_key" }'
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `value` | string | ✓ | The raw API key value to validate. |

**Returns:** `api_key` object with owner and permissions details, used to authorize the incoming request.