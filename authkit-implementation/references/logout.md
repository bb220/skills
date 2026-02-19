# Logout

End a user's session. The user's browser must be redirected to the logout URL — the logout is completed by the browser, not the server.

After signing out, the user will be redirected to your app's **Sign-out redirect location**, which is configured in the WorkOS Dashboard under **Authentication → Redirects**.

> If no Sign-out redirect is configured in the WorkOS Dashboard, users will see an error when logging out.

---

## Operations

### Get Logout URL

Generates a logout URL using a known session ID. Redirect the user's browser to this URL to end their session.

```python
import workos

client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

logout_url = client.user_management.get_logout_url(
    session_id="session_01HQAG1HENBZMAZD82YRXDFC0B",
    return_to="https://your-app.com/signed-out",
)

# Redirect the user's browser to logout_url
# e.g., return redirect(logout_url) in Flask
```

**Parameters**

| Parameter    | Type  | Required | Description                                                                  |
|--------------|-------|----------|------------------------------------------------------------------------------|
| `session_id` | `str` | Required | The session ID. Obtain from the `sid` claim of the access token JWT.         |
| `return_to`  | `str` | Optional | URL to redirect the user to after logout. Must be a configured Sign-out redirect in the WorkOS Dashboard. |

**Returns:** `str` — The logout URL.

---

## Extracting the Session ID

The `session_id` needed for `get_logout_url()` is the `sid` claim in the decoded access token JWT.

```python
import jwt  # PyJWT

# Decode the access token (validate signature in production using JWKS)
decoded = jwt.decode(
    access_token,
    options={"verify_signature": False},  # Use JWKS verification in production
)

session_id = decoded["sid"]

logout_url = client.user_management.get_logout_url(
    session_id=session_id,
    return_to="https://your-app.com/signed-out",
)
```

---

## Flask Example

```python
from flask import Flask, redirect, session
import workos

app = Flask(__name__)
client = workos.WorkOSClient(
    api_key="sk_example_123456789",
    client_id="client_123456789",
)

@app.route("/logout")
def logout():
    session_id = session.get("workos_session_id")

    if session_id:
        logout_url = client.user_management.get_logout_url(
            session_id=session_id,
            return_to="https://your-app.com/signed-out",
        )
        session.clear()
        return redirect(logout_url)

    return redirect("/")
```

---

## JavaScript SDK Reference

The JS SDK provides an additional method for generating the logout URL directly from a sealed session cookie (without needing to track the session ID separately).

### `getLogoutUrlFromSessionCookie()`

Generates the logout URL by extracting the session ID from a sealed session cookie.

```js
const workos = new WorkOS('sk_test_123');

const logoutUrl = workos.userManagement.getLogoutUrlFromSessionCookie({
  sessionData: 'sealed_session_cookie_data',
  cookiePassword: 'password_previously_used_to_seal_session_cookie',
  returnTo: 'https://your-app.com/signed-out',
});
```

**Parameters**

| Parameter        | Type     | Required | Description                                                 |
|------------------|----------|----------|-------------------------------------------------------------|
| `sessionData`    | `string` | Required | The sealed session cookie value.                            |
| `cookiePassword` | `string` | Optional | Password used to seal the session (required to extract sid).|
| `returnTo`       | `string` | Optional | URL to redirect to after logout.                            |

**Returns:** `string` — The logout URL.

---

## Notes

- The logout URL must be visited by the **user's browser** — it is not a server-to-server API call.
- After redirecting, clear any server-side session state (e.g., cookies, database session records).
- The `return_to` URL must be a configured Sign-out redirect in the WorkOS Dashboard.
- To revoke a session programmatically without a browser redirect, use [`revoke_session()`](https://workos.com/docs/reference/authkit/session).

---

## Related

- [Session](https://workos.com/docs/reference/authkit/session)
- [Session Tokens](https://workos.com/docs/reference/authkit/session-tokens)
- [Session Helpers](https://workos.com/docs/reference/authkit/session-helpers)
- [Authentication](https://workos.com/docs/reference/authkit/authentication)