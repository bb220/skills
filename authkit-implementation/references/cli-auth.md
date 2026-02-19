# CLI Auth

CLI Auth enables command-line applications to authenticate users through the web using the [OAuth 2.0 Device Authorization Flow](https://datatracker.ietf.org/doc/html/rfc8628).

The CLI Auth flow involves two main endpoints:

1. The **device authorization URL** — initiates the flow by obtaining a device code, user code, and verification URIs.
2. The **device access token URL** — the device exchanges the device code for access and refresh tokens after the user authenticates.

Read more about [CLI Auth](https://workos.com/docs/authkit/cli-auth).

**Source:** https://workos.com/docs/reference/authkit/cli-auth

---

## CLI Auth Object Fields

| Field | Description |
|---|---|
| `device_code` | A unique identifier for the authorization request used when polling the token endpoint. |
| `user_code` | A short, user-friendly code that users enter to authorize the device. |
| `verification_uri` | The URL where users can enter the user code to authorize the device. |
| `verification_uri_complete` | A URL with the user code pre-filled for one-click authorization. |

---

## Device Authorization Flow

### Step 1 — Get Device Authorization

Request a device code to begin the flow.

`POST /user_management/cli_auth/device_authorization`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/cli_auth/device_authorization \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789"
  }'
```

**Returns:**

```json
{
  "device_code": "GmRh...Lgr1",
  "user_code": "WDJB-MJHT",
  "verification_uri": "https://your-authkit-domain.com/device",
  "verification_uri_complete": "https://your-authkit-domain.com/device?user_code=WDJB-MJHT",
  "expires_in": 1800,
  "interval": 5
}
```

### Step 2 — User Authenticates

Direct the user to visit the `verification_uri` and enter the `user_code` (or open `verification_uri_complete` directly).

### Step 3 — Poll for Access Token

Poll the token endpoint using the `device_code` until the user completes authentication.

`POST /user_management/cli_auth/token`

```bash
curl --request POST \
  --url https://api.workos.com/user_management/cli_auth/token \
  --header "Content-Type: application/json" \
  -d '{
    "client_id": "client_123456789",
    "client_secret": "sk_example_123456789",
    "device_code": "GmRh...Lgr1",
    "grant_type": "urn:ietf:params:oauth:grant-type:device_code"
  }'
```

**Returns (on success):**

```json
{
  "access_token": "...",
  "refresh_token": "...",
  "user": { ... }
}
```

**Returns (while pending):** `{ "error": "authorization_pending" }` — continue polling at the specified `interval`.