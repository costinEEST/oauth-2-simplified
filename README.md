- OAuth (authorization) is about accessing APIs. It only provides the application with an access token.
- OpenID (authentication) is about identifying users. It was created as an extension of OAuth in order to provide the application with the user’s identity information.
- Delegated authorization: OAuth provides a way for an application to get an access token to make API requests.
- Roles in OAuth:
  - Resource Owner (The User)
  - User Agent (The Device)
  - Client (The Application)
  - Authorization Server
  - Resource Server (The API)
- When a user is using Travis CI to test code from GitHub, GitHub is playing `Authorization Server` and `Resource Server` roles.
- When a user is using Travis CI to test code from GitHub, Travis CI is playing the `Client` role. A "client" in OAuth is the one that's trying to access resources using an access token.
- If you are building an iPhone app, using an external service as your Authorization Server, you are building the `Client` role.
- If you are building an API, using an external service as your Authorization Server, you are building the `Resource Server` role.
- If you are building an application with a .NET backend, you are building an `Confident` application type. Since .NET is a server-side language and can run on servers under your own control, this is considered a confidential client since you can deploy a client secret with the application.
- If you are building a mobile app, you are building a `Public` application type. Mobile apps don't have the ability to be deployed with a client secret, since users of the app would be able to extract the secret.
- Any HTTP client that makes a request to an HTTP server is using the `back channel`, even if that client is JavaScript code in a browser.
- Using the browser’s address bar to move data between two other pieces of software is using the `front channel`.
- Signed in with Github to https://developer.okta.com
- [Security -> API -> copy 'Issuer URI'](https://dev-88389792-admin.okta.com/admin/oauth2/as)
- https://oauth.school

```bash
curl 'https://oauth.school/exercise/introduction/save' -X POST -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Origin: https://oauth.school' -H 'Connection: keep-alive' -H 'Referer: https://oauth.school/exercise/introduction/' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: same-origin' -H 'Sec-Fetch-User: ?1' --data-raw 'issuer=https%3A%2F%2Fdev-88389792.okta.com%2Foauth2%2Fdefault'
```

- `authorization_endpoint=https://dev-88389792.okta.com/oauth2/default/v1/authorize`
- `token_endpoint=https://dev-88389792.okta.com/oauth2/default/v1/token`

```bash
await fetch("https://oauth.school/exercise/introduction/check", {
    "credentials": "include",
    "headers": {
        "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8",
        "Content-Type": "application/x-www-form-urlencoded",
        "Upgrade-Insecure-Requests": "1",
        "Sec-Fetch-Dest": "document",
        "Sec-Fetch-Mode": "navigate",
        "Sec-Fetch-Site": "same-origin",
        "Sec-Fetch-User": "?1"
    },
    "referrer": "https://oauth.school/exercise/introduction/",
    "body": "authorization_endpoint=https%3A%2F%2Fdev-88389792.okta.com%2Foauth2%2Fdefault%2Fv1%2Fauthorize&token_endpoint=https%3A%2F%2Fdev-88389792.okta.com%2Foauth2%2Fdefault%2Fv1%2Ftoken",
    "method": "POST",
    "mode": "cors"
});
```

- A conversation between an UserAgent, App, AuthorizationServer and an API.

  - User: I'd like to use this great app.
  - App: Hang on while I generate a new secret and hash it.
  - App: Please go to the authorization server to grant me access, take this hash with you.
  - User: I'd like to log in to {app}, here's the hash it gave me
  - AS: Here is a temporary code the app can use
  - User: Here is the temporary code, please use this to get a token
  - App: Here's the code, and the plaintext secret, please give me a token.
  - AS: Let me verify the hash of that secret.. ok here is an access token!
  - App: Please let me access this user's data with this access token!

* [PKCE (RFC 7636) aka Proof Key for Code Exchange](https://oauth.net/2/pkce)
* PKCE Code verifier (secret): random string 43-128 characters long
* PKCE Code challenge (public hash): base64url(sha256(code_verifier))
* The Authorization Code Injection aka Replay attack: https://speakerdeck.com/leastprivilege/oauth-and-openid-connect-security-best-practices?slide=28, https://codeburst.io/missing-the-point-in-securing-oauth-2-0-83968708b467#5bc5, https://github.com/larkintuckerllc/hello-oauth-pkce, https://www.thehacker.recipes/web/config/oauth-2.0#authorization-code-injection, https://arxiv.org/pdf/2110.01005.pdf

- https://github.com/koenbuyens/Vulnerable-OAuth-2.0-Applications
- https://www.scottbrady91.com/oauth/client-authentication-vs-pkce
- [OAuth 2.0 RFC Reference](https://linktr.ee/oauth2)

- The authorization endpoint

```bash
https://authorization-server.com/auth?
response_type=code&
client_id=CLIENT_ID&
redirect_uri=REDIRECT_URI&
scope=photos&
state=XXXXXX&
code_challenge=XXXXXXXXXXXXX&
code_challenge_method=s256
```

```bash
https://example-app.com/redirect?
error=access_denied&
state=XXXXXXX&
```

- The back channel request

```curl
POST https://authorization-server.com/token?
grant_type=authorization_code&
code=AUTH_CODE_HERE&
redirect_uri=REDIRECT_URI&
code_verifier=VERIFIER_STRING&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET
```

- The authorization server answers back with an access token

```json
{
  "token_type": "Bearer",
  "access_token": "G8qZt7PEha9gx",
  "expires_in": 3600,
  "scope": "photos",
  "refresh_token": "H4HSMMV2409INW"
}
```

- Using the refresh token

```curl
POST https://authorization-server.com/token?
grant_type=refresh_token&
refresh_token=REFRESH_TOKEN&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET
```

- Okta Developer dashboard -> Applications -> Applications -> Create App Integration -> OpenID Connect -> Web Application
- Sign-in redirect URIs {https://example-app.com/redirect} -> Allow everyone in your organization to access
- [Generate a random string between 43-128 characters long](https://example-app.com/pkce)

```bash
https://dev-xxxxxx.okta.com/oauth2/default/v1/authorize?
  response_type=code&
  scope={YOUR_SCOPE}&
  client_id={YOUR_CLIENT_ID}&
  state={RANDOM_STRING}&
  redirect_uri=https://example-app.com/redirect&
  code_challenge={YOUR_CODE_CHALLENGE}&
  code_challenge_method=S256
```

- https://oauth.school/exercise/web -> https://dev-88389792.okta.com/oauth2/default/v1/authorize?response_type=code&scope=photos&client_id=0oa59a68c4SkqfxL25d7&state=229c95593706a5f260d9897cc1a94042d0ae93cf0698a82dd2c031c9&redirect_uri=https://example-app.com/redirect&code_challenge=DHR3t9tybpkYQ8ISoqc9QsdWh_6ssR4tWQyxZJTiEKI&code_challenge_method=S256 -> Check your URL -> https://example-app.com/redirect?code=WhLVXTAVoQp4IYcnlppl_FQrQq_-nk8uZ1ZRFuehLTc&state=229c95593706a5f260d9897cc1a94042d0ae93cf0698a82dd2c031c9

```curl
curl -X POST https://dev-xxxxxx.okta.com/oauth2/default/v1/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id={YOUR_CLIENT_ID} \
  -d client_secret={YOUR_CLIENT_SECRET} \
  -d code_verifier={YOUR_CODE_VERIFIER} \
  -d code={YOUR_AUTHORIZATION_CODE}
```

```curl
curl -X POST https://dev-88389792.okta.com/oauth2/default/v1/token \
  -d grant_type=authorization_code \
  -d redirect_uri=https://example-app.com/redirect \
  -d client_id=0oa59a68c4SkqfxL25d7 \
  -d client_secret=bW5jA4dMf72xEPyAAhvWbIGHxPLxpw8QeVz-5PX5 \
  -d code_verifier=229c95593706a5f260d9897cc1a94042d0ae93cf0698a82dd2c031c9 \
  -d code=WhLVXTAVoQp4IYcnlppl_FQrQq_-nk8uZ1ZRFuehLTc
```

```json
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJraWQiOiI1RVR5MWFEdmJpR1FQUlpBWE1CdHZkaVI1Wm9QUXhWajJOcnRsUENLUWZRIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULk9hVWtHSkVpWTlwWWFMdEFpNkdhZlprdWNYQVRScTJmMFB4SnBDeXRhRjgiLCJpc3MiOiJodHRwczovL2Rldi04ODM4OTc5Mi5va3RhLmNvbS9vYXV0aDIvZGVmYXVsdCIsImF1ZCI6ImFwaTovL2RlZmF1bHQiLCJpYXQiOjE2NTQyNjIyOTcsImV4cCI6MTY1NDI2NTg5NywiY2lkIjoiMG9hNTlhNjhjNFNrcWZ4TDI1ZDciLCJ1aWQiOiIwMHU1NnJldnRrUmMyMURaTDVkNyIsInNjcCI6WyJwaG90b3MiXSwiYXV0aF90aW1lIjoxNjU0MjYxODU5LCJzdWIiOiJjb3N0aW5FRVNUQGdpdGh1Yi5va3RhaWRwIn0.rAZUivsF7eHcDwY8F0PQSMOoUAwP5yx7EYJ4r_RVpAKbuvU6mQ2AIKd_V5tAFppYLCPi5npJW2b2_Gx5tBbje2dzOu-Ysx1-UZXSAk6PVbl-CNp1-zL0iB_TMVt9Ueho92_wKV-oV5XL9WvHZZ85DUJBpPAgMIiA14i25uAYSxR6mbWkVvfo5gdAxhVSvY-uNCCC7YDsIfIDTstNgJ_gcTic5znCKWhRCSzgKGULVBgm6ehDI0x4o_xgajqM5lmij7hGn4UBD3NPY_62WyQEDL53rM8aqeGca4ci6-J_u--vs0-ElAt69yiMyfD7jG1ztYxvWciMLctjqxjmK48cDw",
  "scope": "photos"
}
```

- https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller
- https://developer.chrome.com/docs/android/custom-tabs
- Okta Developer dashboard -> Applications -> Applications -> Create App Integration -> OpenID Connect -> Native Application -> for GrantType check 'Refresh Token' -> Under Sign-in redirect URIs, replace the default value with https://example-app.com/redirect as the redirect URI for your application -> Allow everyone in your organization to access -> Save

```bash
    https://dev-xxxxxx.okta.com/oauth2/default/v1/authorize?
      response_type=code&
      scope=offline_access+{YOUR_SCOPE}&
      client_id={YOUR_CLIENT_ID}&
      state={RANDOM_STRING}&
      redirect_uri=https://example-app.com/redirect&
      code_challenge={YOUR_CODE_CHALLENGE}&
      code_challenge_method=S256
```

- https://oauth.school/exercise/refresh -> Authorization Request (https://dev-88389792.okta.com/oauth2/default/v1/authorize?response_type=code&scope=offline_access+photos&client_id=0oa5al8cuxwP0RfNd5d7&state=2a1f2e4fdc98cec11044d97bafab8e09d062af83c091c8f20870affe&redirect_uri=https://example-app.com/redirect&code_challenge=xnrmMW7MPnBwqkyLBUgbR2SyeVTp5XhrWxLtBtl9jQE&code_challenge_method=S256) -> Log In -> https://example-app.com/redirect?code=4CL9CvVOO6KNG5uwLj2DXM-AHLRj51d-vg5tiYUdKV0&state=2a1f2e4fdc98cec11044d97bafab8e09d062af83c091c8f20870affe -> make a POST request as bellow to get an access token:

```bash
    curl -X POST https://dev-xxxxxx.okta.com/oauth2/default/v1/token \
      -d grant_type=authorization_code \
      -d redirect_uri=https://example-app.com/redirect \
      -d client_id={YOUR_CLIENT_ID} \
      -d code_verifier={YOUR_CODE_VERIFIER} \
      -d code={YOUR_AUTHORIZATION_CODE}
```

```bash
    curl -X POST https://dev-88389792.okta.com/oauth2/default/v1/token \
      -d grant_type=authorization_code \
      -d redirect_uri=https://example-app.com/redirect \
      -d client_id=0oa5al8cuxwP0RfNd5d7 \
      -d code_verifier=2a1f2e4fdc98cec11044d97bafab8e09d062af83c091c8f20870affe \
      -d code=4CL9CvVOO6KNG5uwLj2DXM-AHLRj51d-vg5tiYUdKV0
```

```json
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJraWQiOiI1RVR5MWFEdmJpR1FQUlpBWE1CdHZkaVI1Wm9QUXhWajJOcnRsUENLUWZRIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULmswZ0JZck1iTFNvOWRWbGZ0VVBZYWNVX25xMENGU3loTlU2M2hnbFJsb28ub2FyaHlyNnV0QUpTeDFlcWo1ZDYiLCJpc3MiOiJodHRwczovL2Rldi04ODM4OTc5Mi5va3RhLmNvbS9vYXV0aDIvZGVmYXVsdCIsImF1ZCI6ImFwaTovL2RlZmF1bHQiLCJpYXQiOjE2NTQ1MTg4ODMsImV4cCI6MTY1NDUyMjQ4MywiY2lkIjoiMG9hNWFsOGN1eHdQMFJmTmQ1ZDciLCJ1aWQiOiIwMHU1NnJldnRrUmMyMURaTDVkNyIsInNjcCI6WyJvZmZsaW5lX2FjY2VzcyIsInBob3RvcyJdLCJhdXRoX3RpbWUiOjE2NTQ1MTc0NTQsInN1YiI6ImNvc3RpbkVFU1RAZ2l0aHViLm9rdGFpZHAifQ.KwgJu3wh6p3HxeYVDgWnH4Gp_D7npG4x5X9rx4x8lxVYgiLYoagCZDMeQ4hb7fmHZaWwENVwvnvU5Lo7ZqhH9AJZO4A6I7stQiIY5X-3wcP17g7IZ918Mcp2zWPgCbDM1S206z9-hVg4KdKajU8FSBv0_uMM291qRijqK4oj5v3X1eOwNK7wD1OYOg-Xuda7p2G2LVNkhgby5WqyqqYNhaVEH-T8Vdw8dn7ZQbSzYVXeHPF06QmAQIaw-C1tnl5BOsI1xNyD4MlP62LIobR0ZwrGP3U82cAln425v9eVcN5mDhZdb0m-l0k5D6eiKcL6KFXG6nXNNA1qClF5HwvVJw",
  "scope": "offline_access photos",
  "refresh_token": "cyLcl2pOLohArIC4hANybWWx8dcPbcfL7pvy5UUl9tQ"
}
```

- Use the refresh token to get a new access token:

```bash
    curl -X POST https://dev-xxxxxx.okta.com/oauth2/default/v1/token \
      -d grant_type=refresh_token \
      -d client_id={YOUR_CLIENT_ID} \
      -d refresh_token={YOUR_REFRESH_TOKEN}
```

```bash
    curl -X POST https://dev-88389792.okta.com/oauth2/default/v1/token \
      -d grant_type=refresh_token \
      -d client_id=0oa5al8cuxwP0RfNd5d7 \
      -d refresh_token=cyLcl2pOLohArIC4hANybWWx8dcPbcfL7pvy5UUl9tQ
```

```json
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJraWQiOiI1RVR5MWFEdmJpR1FQUlpBWE1CdHZkaVI1Wm9QUXhWajJOcnRsUENLUWZRIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULjRRbUZSMEVsTVBldlJucFpqQV9EWWJ3c0phOFZPcUVHVVAxSFNuLUlzUEkub2FyaHlyNnV0QUpTeDFlcWo1ZDYiLCJpc3MiOiJodHRwczovL2Rldi04ODM4OTc5Mi5va3RhLmNvbS9vYXV0aDIvZGVmYXVsdCIsImF1ZCI6ImFwaTovL2RlZmF1bHQiLCJpYXQiOjE2NTQ1MTkxNDMsImV4cCI6MTY1NDUyMjc0MywiY2lkIjoiMG9hNWFsOGN1eHdQMFJmTmQ1ZDciLCJ1aWQiOiIwMHU1NnJldnRrUmMyMURaTDVkNyIsInNjcCI6WyJvZmZsaW5lX2FjY2VzcyIsInBob3RvcyJdLCJhdXRoX3RpbWUiOjE2NTQ1MTc0NTQsInN1YiI6ImNvc3RpbkVFU1RAZ2l0aHViLm9rdGFpZHAifQ.g_OnWI_RJVoIMqHtoDVvqUdlU-nOsawsUOQ3bqu1cyLuYhSZgNLMtHY9tk_GUKCRWVqXwaF-rCTf0NZHScK2HQzr0OsFshVKAmoPvkFO6R1-ELCW2C0Jhz_XHOnmO0Fk24oFBLNesfPGqjgr8NhSzRWLefJmwFoFoFsYWyLo2IqBRgqf4zR3SbJlwfs8B6XeTt66oDdQ16Yt66rWhiphj-oF9ZmQ-Z5S5QY46Z1U2FGvLEk73YmMYibfennVRksURb4mTYNHtKtYzmJrKgnXTtV6z9EDvuk_LuWUaKZKvp0NLb3dBjs2X6Se0olociTj2s6X4FESEIPUV1bPPkkR9g",
  "scope": "offline_access photos",
  "refresh_token": "cyLcl2pOLohArIC4hANybWWx8dcPbcfL7pvy5UUl9tQ"
}
```

- https://itnext.io/using-service-worker-as-an-auth-relay-5abc402878dd + https://github.com/bartekbp/blog/tree/master/service-worker-auth
- https://blog.ropnop.com/storing-tokens-in-browser/#service-worker
- Okta Developer dashboard -> Applications -> Applications -> Create App Integration -> OpenID Connect -> Single-Page Application -> Under Sign-in redirect URIs, replace the default value with https://example-app.com/redirect as the redirect URI for your application -> Allow everyone in your organization to access -> Save

```bash
    https://dev-xxxxxx.okta.com/oauth2/default/v1/authorize?
      response_type=code&
      scope={YOUR_SCOPE}&
      client_id={YOUR_CLIENT_ID}&
      state={RANDOM_STRING}&
      redirect_uri=https://example-app.com/redirect&
      code_challenge={YOUR_CODE_CHALLENGE}&
      code_challenge_method=S256
```

- https://oauth.school/exercise/spa -> Authorization Request(https://dev-88389792.okta.com/oauth2/default/v1/authorize?response_type=code&scope=photos&client_id=0oa5b12p2lM33sFPM5d7&state=a744453c3a9eec46ef3d1437e825dbf63911a2d3745ab49350d5d52e&redirect_uri=https://example-app.com/redirect&code_challenge=Lr6VtOMZTeonOv2c403TFaFsQ27C3NYv643ICQznvHY&code_challenge_method=S256) -> Log In -> https://example-app.com/redirect?code=3D9fLHCIeUmLOipS2lON9qxR0ys8ZVz2ei0A0MAehkQ&state=a744453c3a9eec46ef3d1437e825dbf63911a2d3745ab49350d5d52e -> make a POST request for an access token:

```bash
    curl -X POST https://dev-xxxxxx.okta.com/oauth2/default/v1/token \
      -d grant_type=authorization_code \
      -d redirect_uri=https://example-app.com/redirect \
      -d client_id={YOUR_CLIENT_ID} \
      -d code_verifier={YOUR_CODE_VERIFIER} \
      -d code={YOUR_AUTHORIZATION_CODE}
```

```bash
    curl -X POST https://dev-88389792.okta.com/oauth2/default/v1/token \
      -d grant_type=authorization_code \
      -d redirect_uri=https://example-app.com/redirect \
      -d client_id=0oa5b12p2lM33sFPM5d7 \
      -d code_verifier=a744453c3a9eec46ef3d1437e825dbf63911a2d3745ab49350d5d52e \
      -d code=3D9fLHCIeUmLOipS2lON9qxR0ys8ZVz2ei0A0MAehkQ
```

```json
{
  "token_type": "Bearer",
  "expires_in": 3600,
  "access_token": "eyJraWQiOiI1RVR5MWFEdmJpR1FQUlpBWE1CdHZkaVI1Wm9QUXhWajJOcnRsUENLUWZRIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULkJfWjBEczJvUU1maVdVczdhWFpTczROaFd0Y1k5OEJsQ2JwNTJUZ0VQWWsiLCJpc3MiOiJodHRwczovL2Rldi04ODM4OTc5Mi5va3RhLmNvbS9vYXV0aDIvZGVmYXVsdCIsImF1ZCI6ImFwaTovL2RlZmF1bHQiLCJpYXQiOjE2NTQ1OTI1MjUsImV4cCI6MTY1NDU5NjEyNSwiY2lkIjoiMG9hNWIxMnAybE0zM3NGUE01ZDciLCJ1aWQiOiIwMHU1NnJldnRrUmMyMURaTDVkNyIsInNjcCI6WyJwaG90b3MiXSwiYXV0aF90aW1lIjoxNjU0NTkwNDgxLCJzdWIiOiJjb3N0aW5FRVNUQGdpdGh1Yi5va3RhaWRwIn0.vVqBqZQ6Y3qlqMO4HODctLconf-ML7_TXqOQSMEAkhKmB3LWloqVaowrbfL4IgSFlEe5IlcsSyPImsSZH0yfAa05qyhqHBcM9mDeySF9Hb09GTREP54Kga2tFnqI2LXcgPQNDv3iuIQdylIduggDdF60pjn3mXqAIcC0HkdC7MTUww0yqn2ZRqnPkrmQ0HZIQB4Vlqq3Oe1OLDAPKlWGou6CKe2P5h266O2rqbINOFLSXracte7WVvH_VcwjDsSsEOqBMjKfpMvt71-pgWeg1jRAcxXg4cL3FfZo--QxfyMWyeKdHFM2zm9E5Y7PWnk_ZPkhNjeuZvXyWPrxBUcyFA",
  "scope": "photos"
}
```

- [RFC 8628: OAuth 2.0 Device Authorization Grant](https://oauth.net/2/device-flow)
- Device flow for browserless devices:

  - User: I'd like to use this great app
  - Device: A user would like to log in
  - AuthorizationServer: Here's a temporary code, check back every 5 sec for the next 5 minutes
  - Device Go to this URL and enter this code
  - User: I'd like to log in to "The best app ever", it wants to access my photos
  - AuthorizationServer: Ok, i'll let the device know you allowed it next time it checks in
  - Device: Here is the temporary code, has the user logged in yet?
  - AuthorizationServer: Here is an access token!
  - Device: Please let me access this user's data with this access token!

  - Okta Developer dashboard -> Applications -> Applications -> Create App Integration -> API Services -> Save -> make a POST request to get an access token:

  ```bash
      curl -X POST https://dev-xxxxxx.okta.com/oauth2/default/v1/token \
      -d grant_type=client_credentials \
      -d client_id={YOUR_CLIENT_ID} \
      -d client_secret={YOUR_CLIENT_SECRET} \
      -d scope={YOUR_CUSTOM_SCOPE}
  ```

  ```bash
      curl -X POST https://dev-88389792.okta.com/oauth2/default/v1/token \
      -d grant_type=client_credentials \
      -d client_id=0oa5b2htlok3s1sKk5d7 \
      -d client_secret=SstYDOg1uLbiUdP-0XcfdXsyItJ0Tqq5h2AvtWw3 \
      -d scope=photos
  ```

  ```json
  {
    "token_type": "Bearer",
    "expires_in": 3600,
    "access_token": "eyJraWQiOiI1RVR5MWFEdmJpR1FQUlpBWE1CdHZkaVI1Wm9QUXhWajJOcnRsUENLUWZRIiwiYWxnIjoiUlMyNTYifQ.eyJ2ZXIiOjEsImp0aSI6IkFULkNjX2g1NlhsWVBVajZFSjZOU1RxVnk4LWgzdHBEVE5rc0V5SU51eUdCSmciLCJpc3MiOiJodHRwczovL2Rldi04ODM4OTc5Mi5va3RhLmNvbS9vYXV0aDIvZGVmYXVsdCIsImF1ZCI6ImFwaTovL2RlZmF1bHQiLCJpYXQiOjE2NTQ2MDE3MDIsImV4cCI6MTY1NDYwNTMwMiwiY2lkIjoiMG9hNWIyaHRsb2szczFzS2s1ZDciLCJzY3AiOlsicGhvdG9zIl0sInN1YiI6IjBvYTViMmh0bG9rM3Mxc0trNWQ3In0.AHFLr28IfKYANKdKvIsr8NkMtq8QgJMpEFUqYZHOPI22Bdcjwrpudergy1nNT4OADhQFJEOXJJ7J6AG1tSsZR_uJlD81mzaT0Q7jbZ2f3hcdKUJc5AsrmEO_jO9zITVmLcaAE2Lx84lHey7RBYRusqocUkHZKe3xboE__r1kQXjAZ0Boy1qxZKDDeSgtKTuAdhdm42xYf50svXmUIjJVLboTiJmtB19SbzE40AwbD7Pva2s6V4IISjtmQjBSf4tEc5YaZjIyqYPLVC0xJLZ3YdP-podzaHESdCON6g0K-yIxdXU9WuCLe2wnHsgNsjlPS3zAkrxbaqQN-VSY3jS4Dg",
    "scope": "photos"
  }
  ```

  - An access token is what the application gets in order to be able to make API requests. The application gets the token from an authorization server and then sends it to an API to use when making API requests
  - Applications are going to get an ID token from the authorization server and unpack it to learn about the user.
