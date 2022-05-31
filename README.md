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

* Code verifier (secret): random string 43-128 characters long
* Code challenge (public hash): base64url(sha256(code_verifier))

```curl
https://authorization-server.com/auth? response_type=code&
client_id=CLIENT_ID&
redirect_uri=REDIRECT_URI&
scope=photos& state=XXXXXX&
code_challenge=XXXXXXXXXXXXX& code_challenge_method=s256
```

```curl
https://example-app.com/redirect? error=access_denied&
state=XXXXXXX&
```

```curl
POST https://authorization-server.com/token?
grant_type=authorization_code&
code=AUTH_CODE_HERE&
redirect_uri=REDIRECT_URI&
code_verifier=VERIFIER_STRING&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET
```

```json
{
  "token_type": "Bearer",
  "access_token": "G8qZt7PEha9gx",
  "expires_in": 3600,
  "scope": "photos",
  "refresh_token": "H4HSMMV2409INW"
}
```

```curl
POST https://authorization-server.com/token?
grant_type=refresh_token&
refresh_token=REFRESH_TOKEN&
client_id=CLIENT_ID&
client_secret=CLIENT_SECRET
```
