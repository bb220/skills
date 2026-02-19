# Logout

End a user's session. The user's browser should be redirected to the URL returned by this endpoint.

**Source:** https://workos.com/docs/reference/authkit/logout

---

## Get Logout URL

`GET /user_management/sessions/logout`

```bash
curl "https://api.workos.com/user_management/sessions/logout?session_id=session_01E4ZCR3C56J083X43JQXF3JK5" \
  --header "Authorization: Bearer sk_example_123456789"
```

**Parameters**

| Parameter | Type | Required | Description |
|---|---|---|---|
| `session_id` | string | ✓ | The ID of the session that is ending. Extractable from the `sid` claim of the access token. |
| `return_to` | string | optional | Where to redirect after logout. Allowed values are configured in the WorkOS Dashboard under [Sign-out redirects](https://workos.com/docs/authkit/sessions/configuring-sessions/sign-out-redirect). If omitted, the default sign-out redirect is used. |

**Returns:** `{ url: string }` — redirect the user's browser to this URL to sign them out.

---

> **Tip:** When using the Node.js SDK session helpers, you can call `session.getLogOutUrl()` instead — it extracts the session ID automatically. See [Session Helpers](./07-session-helpers.md#get-log-out-url).