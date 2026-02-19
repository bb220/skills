# Session Helpers

After authenticating and [storing the encrypted session as a cookie](https://workos.com/docs/authkit/vanilla/nodejs/3-handle-the-user-session/save-the-encrypted-session), retrieving and decrypting the session is made easy via these session helper methods.

**Source:** https://workos.com/docs/reference/authkit/session-helpers

---

## Load Sealed Session

Load the session by providing the sealed session data and the cookie password.

```js
import { WorkOS } from '@workos-inc/node';

const workos = new WorkOS('sk_example_123456789', { clientId: 'client_123456789' });

const session = await workos.userManagement.loadSealedSession({
  sessionData: 'sealed_session_cookie_data',
  cookiePassword: 'password_previously_used_to_seal_session_cookie',
});
```

### `userManagement.loadSealedSession()`

**Parameters:** `{ sessionData: string, cookiePassword: string }`

**Returns:**

| Field | Type | Description |
|---|---|---|
| `authenticate` | function | Check if session is valid. |
| `refresh` | function | Refresh the session. |
| `getLogOutUrl` | function | Get the logout URL. |

---

## Authenticate

Unseals the session data and checks if the session is still valid.

```js
const authResponse = await session.authenticate();

if (authResponse.authenticated) {
  const { sessionId, organizationId, role, permissions, user } = authResponse;
} else {
  if (authResponse.reason === 'no_session_cookie_provided') {
    // Redirect the user to login
  }
}
```

### `session.authenticate()`

**Returns:**

| Field | Type | Description |
|---|---|---|
| `authenticated` | boolean | Whether the session is valid. |
| `accessToken` | string | The current access token. |
| `sessionId` | string | |
| `user` | User | The authenticated user. |
| `organizationId` | string (optional) | |
| `role` | string (optional) | |
| `roles` | array (optional) | |
| `permissions` | array (optional) | |
| `entitlements` | array (optional) | |
| `featureFlags` | array (optional) | |
| `impersonator` | object (optional) | |
| `reason` | string (optional) | `"invalid_jwt"`, `"invalid_session_cookie"`, or `"no_session_cookie_provided"` |

---

## Refresh

Refreshes the user's session with the refresh token. Passing in a new `organizationId` will switch the user to that organization.

```js
const refreshResult = await session.refresh();

if (!refreshResult.authenticated) {
  // Redirect to login
}

const { session: userSession, sealedSession, user, organizationId, role, permissions } = refreshResult;

// Set the sealedSession in a cookie
```

### `session.refresh()`

**Parameters:**

| Parameter | Type | Required |
|---|---|---|
| `cookiePassword` | string | optional |
| `organizationId` | string | optional |

**Returns (`RefreshSessionResponse`):**

| Field | Type |
|---|---|
| `authenticated` | boolean |
| `session` | object |
| `sealedSession` | string |
| `sessionId` | string |
| `user` | User |
| `organizationId` | string (optional) |
| `role` | string (optional) |
| `roles` | array (optional) |
| `permissions` | array (optional) |
| `entitlements` | array (optional) |
| `featureFlags` | array (optional) |
| `impersonator` | object (optional) |
| `reason` | string (optional) |

---

## Get Log Out URL

End a user's session. The user's browser should be redirected to this URL. Functionally similar to [Get Logout URL](./15-logout.md) but extracts the session ID automatically from the session data.

```js
const logOutUrl = await session.getLogOutUrl();

// Redirect the user to logOutUrl
```

### `session.getLogOutUrl()`

**Returns:** `{ logOutUrl: string }`