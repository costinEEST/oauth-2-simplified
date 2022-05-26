* OAuth (authorization) is about accessing APIs. It only provides the application with an access token. 
* OpenID (authentication) is about identifying users. It was created as an extension of OAuth in order to provide the application with the user’s identity information.
* Delegated authorization: OAuth provides a way for an application to get an access token to make API requests.
* Roles in OAuth:
  - Resource Owner (The User)
  - User Agent (The Device)
  - Client (The Application)
  - Authorization Server
  - Resource Server (The API)
* When a user is using Travis CI to test code from GitHub, GitHub is playing `Authorization Server` and `Resource Server` roles.
* When a user is using Travis CI to test code from GitHub, Travis CI is playing the `Client` role. A "client" in OAuth is the one that's trying to access resources using an access token.
* If you are building an iPhone app, using an external service as your Authorization Server, you are building the `Client` role.
* If you are building an API, using an external service as your Authorization Server, you are building the `Resource Server` role.
* If you are building an application with a .NET backend, you are building an `Confident` application type. Since .NET is a server-side language and can run on servers under your own control, this is considered a confidential client since you can deploy a client secret with the application.
* If you are building a mobile app, you are building a `Public` application type. Mobile apps don't have the ability to be deployed with a client secret, since users of the app would be able to extract the secret.
* Any HTTP client that makes a request to an HTTP server is using the `back channel`, even if that client is JavaScript code in a browser.
* Using the browser’s address bar to move data between two other pieces of software is using the `front channel`.
