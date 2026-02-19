# Identity

Represents [User](https://workos.com/docs/reference/authkit/user) identities obtained from external identity providers.

When a user authenticates using an external provider like [Google OAuth](https://workos.com/docs/integrations/google-oauth), information from that provider will be made available as one of the user's Identities. You can read more about the process in our [identity linking guide](https://workos.com/docs/authkit/identity-linking).

> **Note:** Applications should check the `type` before making assumptions about the shape of the identity. Currently only `OAuth` identities are supported, but more types may be added in the future.

**Source:** https://workos.com/docs/reference/authkit/identity

---

## The Identity Object

```json
{
  "idp_id": "4F42ABDE-1E44-4B66-824A-5F733C037A6D",
  "type": "OAuth",
  "provider": "MicrosoftOAuth"
}
```

### Properties

| Property | Type | Description |
|---|---|---|
| `idp_id` | string | The unique ID of the user in the external identity provider. |
| `type` | `"OAuth"` | The type of the identity. |
| `provider` | enum | The OAuth provider. One of: `AppleOAuth`, `GitHubOAuth`, `GoogleOAuth`, `MicrosoftOAuth`. |

---

## Get User Identities

Get a list of identities associated with the user. A user can have multiple associated identities after going through [identity linking](https://workos.com/docs/authkit/identity-linking). Currently only OAuth identities are supported.

`GET /user_management/users/:id/identities`

```bash
curl https://api.workos.com/user_management/users/user_01E4ZCR3C56J083X43JQXF3JK5/identities \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Description |
|---|---|---|
| `id` | string | The user's ID. |

**Returns:** `{ identities: array }`