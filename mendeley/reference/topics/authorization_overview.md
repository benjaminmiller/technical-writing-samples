## Authorization

All Mendeley API requests require authorization credentials to be included with each request. **OAuth** provides user authentication and authorizes requests.

Remember, your application must be [registered](application_registration.html) with the API to receive values for your OAuth configuration and to be authorized to make API requests.


### OAuth 2.0

[OAuth 2.0](http://oauth.net) is used to authorize requests to the Mendeley API. OAuth is a widely adopted, open protocol for users to delegate authorization in a secure manner. OAuth reduces the security risks associated with the more traditional username and password authentication. OAuth is adaptable to many different API client types and defines a number of different client profiles: 

* *web application*: an application running on a web application server
* *web browser application*: an application, normally Javascript or Flash, running in a web browser on the user's device
* *native application*: an application, running on the user's device, that may not have the ability to display a web content 

OAuth 2.0 client applications can be defined as either *confidential* or *public*:

* *confidential*: clients able to store credentials confidentially
* *public*: clients unable to store credentials confidentially

Mendeley uses OAuth *access tokens* to provide authorization for API requests. A token is a long sequence of characters that contains security credentials. Each token has a limited lifetime and once expired, a replacement token must be generated. Your application will manage the lifecycle of its access tokens and, in series, will:

1. obtain a new access token through an authorization flow
* use the access token to authorize API requests
* replace the access\_token when it becomes invalid

The API provides three different *authorization flows*, sometimes referred as *grant types*, to obtain an access token to suit different client profiles and confidentially abilities.

### Authorization flows

Each of the three different OAuth authorization flows creates an access token with different abilities and characteristics.

 * **Authorization Code** grant type is suitable for confidential client applications, particularly web applications, that require use of the entire Mendeley API. Authorization code is the recommended authorization flow for most applications. See [Implementing Authorization Code](authorization_auth_code.html) flow for further information.
 * **Implicit** grant type is suitable for public client applications, particularly Javascript in the web browser, that require use of the entire Mendeley API. See [Implementing Implicit](authorization_implicit.html) flow for further information.
 * **Client Credentials** grant type is suitable for confidential client applications that only require access to the read-only Mendeley Catalog of crowd sourced documents.  See [Implementing Client Credentials](authorization_client_credentials.html) flow for further information.

### Selecting a suitable authorization flow

The table below summarizes the abilities and responsibilities of the three provided authorization flows to help you select an appropriate authorization flow for your application.

<table>
<tr><th>Grant type:</th><th>Authorization Code</th><th>Implicit</th><th>Client Credentials</th></th>
<tr><th>Delegate for:</th><td>User</td><td>User</td><td>Application</td></tr>
<tr><th>Client confidentiality required:</th><td>Y</td><td></td><td>Y</td></tr>
<tr><th>Log in user interface required:</th><td>Y</td><td>Y</td><td></td></tr>
<tr><th>Long-lived access tokens:</th><td>Y</td><td></td><td></td></tr>
</table>

**Delegation** scope determines how much access to Mendeley API resources is permitted. Requests, acting as the delegate for an authenticated Mendeley user, have access to the entire API with read and write manipulation of many key resources. API requests, acting as the delegate for a registered client application, are limited to the read-only Mendeley catalog.

**Client confidentiality** is not available to all client applications. Most web applications have a private, secure storage location. Secure storage for secrets in  desktop and mobile operating systems is often provided to installed native applications. Always ensure your application stores authorization secrets in a secure manner.

**Log in user interface** must be displayed by client applications when performing API requests on behalf of user. The Mendeley API provides an HTML user interface, optimized for desktop and iOS devices, your application can display in a web panel. 

**Long-lived access tokens** improve the user experience by only requiring the user to authenticate *once* and provide the ability for your client application to perform scheduled tasks using the API, even when the user is not using the client application.


### Authorizing API requests

Once your application has been granted a valid access token, the client can begin authorizing Mendeley API requests. Every request must include the access token and client applications *should* include the access token value in the HTTP Authorization header. 

If your application's environment does not permit modification of the request's Authorization HTTP header, you may include the token value in an HTTP query string appended to the URL. Using the Authorization header is much preferred by Mendeley because HTTP headers are:

* generally not logged by web servers or proxies
* very rarely cached by proxy servers or web browsers


### HTTP Authorization header

Include the token value in the Authorization header field as an HTTP bearer authorization scheme.

    GET /files HTTP/1.1
    Authorization: Bearer MSwxNDA3Nzc5NzY4ODY5LDEwMzczNDM1MSw3MTQsYWxsLCxpcHB5Q25yMnZtalNTbVpwUEJCVjd1N1VpeWc
    Accept: application/vnd.mendeley-file.1+json


### HTTP query string 

If your client application can not modify the request's Authorization header, then append the access token value to the URL using an HTTP query string. Use the field label `access_token` include the token's value.

    https://api.mendeley.com/documents?access_token=MSwxNDA4MTE2NDQwMDYxLDEwMzczNDM1MSw3MTQsYWxsLCxnbmd6N1NHN2ZMb0lRaWpJX3NMYWJwbE84bkE

Your application should URL encode the access token value to protect against any future change away from the current alphanumeric tokens.

### Errors

Authorization errors can occur for many reasons but common causes are access tokens that are missing, expired, revoked or corrupted. Token error responses include:

* a *401 Unauthorized* HTTP status code 
* a `WWW-Authenticate` HTTP header indicating the class of error
* JSON format body content containing a human readable error message

Sample response when using an expired token:

    HTTP/1.1 401 Unauthorized
    WWW-Authenticate: Bearer realm="mendeley" error="invalid_token"
	
    {
        "message": "Could not access resource because: Token has expired"
    }

Scope errors occur when the access token does not provide the permissions required for the request. A scope error response includes:

* a *403 Forbidden* HTTP status code 
* a plain text description of the error in the response body

A sample response when using a *client credential* grant type access token to request a user document:

    HTTP/1.1 403 Forbidden
    Content-Type: application/vnd.mendeley-document.1+json
    Content-Length: 60
	 
    You do not have the required scopes [all] for this operation


### Client software libraries

While the steps outlined above, indicate the tasks required by a client application to obtain an access token, we recommend taking advantage of a library in your application. Many [OAuth 2.0 client libraries](http://oauth.net/2/) are available for a wide range of languages and environments. 
