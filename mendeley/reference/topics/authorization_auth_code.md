## Authorization code flow

Authorization code flow is used to obtain an access token to authorize API requests. Authorization code flow is the most flexible of the three supported authorization flows and is the recommended method of obtaining an access token for the API. This authorization flow is best suited to applications that have access to secure, private storage such as web applications deployed on a server. [Other authorization flows](authorization_overview.html) are available to obtain an access token that may be suitable for your application.

Access tokens, obtained using authorization code flow, provide permissions for your application to manipulate documents and other resources on behalf of a Mendeley user and make requests for all API resources. Access tokens while having a limited lifetime, can be renewed with a *refresh* token. A refresh token is valid indefinitely and provides ability for your application to schedule tasks on behalf of a user without their interaction.

Your application should warn users they will be asked to authorize using their Mendeley account before initiating an action that requires authorization because users' web browsers will redirect to another site.

Remember, your application must be [registered](application_registration.html) with the API to generate the *client application ID*, *application secret* and *redirection URL* used in the authorization code flow.


### Example

The flow for authorization code is:

1. Create a URL to the OAuth authorization service 
* Display the authorization user interface
* Capture the authorization code from the user interface response
* Exchange the authorization code for an access token
* Extract the access token from the exchange response

### 1. Create a URL

Your application loads the authorization user interface into a web browser window. The Mendeley OAuth authorization endpoint is `https://api.mendeley.com/oauth/authorize`. Four values must be passed, contained in an HTTP query string, with the request:

<dl>
<dt><code>client_id</code></dt>
<dd>The value for <em>application ID</em> you received when registering your application. You can check the ID of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal.</dd>
<dt><code>redirect_uri</code></dt>
<dd>The URL value for <em>Redirection URL</em> you set when registering your application. You can check or change the redirection URL of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. Remember to URL encode this value.</dd>
<dt><code>response_type</code></dt>
<dd>The value <em>must</em> be <code>code</code> indicating the code authorization grant type is required and an authorization code is desired in the response.</dd>
<dt><code>scope</code></dt>
<dd>The value <em>must</em> be <code>all</code> permitting the manipulation of all API resources.</dd>
</dl>

An optional `state` parameter *should* be included in all requests to protect against Cross-site request forgery (CSRF) attacks. The state value must be kept secret from the client and is required later in the access code exchange stage so your application should persist the value, perhaps in server-side session storage.

<dl>
<dt><code>state</code></dt>
<dd>A random value, unique for every access token request.</dd>
</dl>

The completed URL will appear similar to:

    https://api.mendeley.com/oauth/authorize?client_id=773&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fserver_sample.php&response_type=code&scope=all&state=213653957730.97845


### 2. Display the authorization user interface

Use the URL, constructed in the previous step, to ask the user to authorize using their Mendeley credentials. Your application can load, using a GET request, the Authorization page into an existing browser window because the page will be redirected back your application.

##### Error handling
User authentication errors, including incorrect email address or password, are resolved within the authorization process and your application does not have to handle errors for the user. The user can reset their password from the authorization page, solving forgotten password issues.

Incorrect values supplied for `client_id` and `redirect_uri` do not display the authentication page and return a response with a 400 Bad Request status code and a JSON object containing an explanatory message. The user is **not** redirected back to your application.

    HTTP/1.1 400 Bad Request
    Content-Type: application/json
    Content-Length: 90
    
    {"error_message":"Redirection URI does not match the one registered for this application"}

Invalid values for `scope` and `response_type` are sent to your application's redirect page. An HTTP query string contains the fields `error` and `error_description` indicating the nature of the error.

	http://localhost/mendeley/server_sample.php?error=invalid_scope&error_description=Invalid+scope

Your application should report the authorization failure to the user, explain why an error may have occurred and suggest possible solutions.


### 3. Capture the authorization code

Once the user has authorized, they are redirected to the page specified in the request's `redirect_uri` query argument. The OAuth service will append an HTTP query string to the URL containing values for `code` and `state` (if supplied in the requesting URL) fields. 

    http://localhost/mendeley/server_sample.php?state=213653957730.97845&code=zNlyssMxdc8XcKeLdfHvtxmApe

**Important**: before attempting to use the authorization code, first check the value of the the `state` field. This value *must* match the value your application supplied in the URL creation step to protect against [CSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery) attacks.

Your application now has an authorization code but this can not be used to authorize API requests: the code must be exchanged for an access token. Extract the value of the `code` field and for use in the next stage. 


### 4. Exchange the authorization code

Your application needs to exchange the authorization code, extracted in the previous stage, for an OAuth access token to make API requests. The Mendeley OAuth token exchange endpoint is `https://api.mendeley.com/oauth/token` and the exchange is made using an HTTPS request with the conditions:

* the method must be POST
* is authenticated using HTTP Basic authentication
* the body contains three parameters and values named `grant_type`, `code` and `redirect_uri`

The request is authenticated using HTTP Basic authentication:

<dl>
<dt><code>username</code></dt>
<dd>The value for <em>application ID</em> you received when registering your application. You can check the ID of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal.</dd>
<dt><code>password</code></dt>
<dd>The value for the <em>application secret</em> you received when registering your application. If absolutely necessary, you can reset the secret for your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. <strong>Important</strong>: your application must store the application secret value securely and must not expose the value to any users.</dd>
</dl>

The values sent in the body of the request are:

<dl>
<dt><code>grant_type</code></dt>
<dd>The value <em>must</em> be <code>authorization_code</code> indicating your application is exchanging an authorization code for an access token.</dd>
<dt><code>code</code></dt>
<dd>The value for the <em>authorization code</em> your application extracted in the previous stage.</dd>
<dt><code>redirect_uri</code></dt>
<dd>The URL value for <em>Redirection URL</em> you set when registering your application. You can check or change the redirection URL of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. Remember to URL encode this value.</dd>
</dl>

    curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -u "773:xzcdoG8wmRrf7Npm" -d "grant_type=authorization_code&code=zNlyssMxdc88XcKeLdfHvtxmApe&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fserver_sample.php" https://api.mendeley.com/oauth/token


    POST /oauth/token HTTP/1.1
    Host: api.mendeley.com
	Authorization: Basic NzczOnh6Y2RvRzh3bVJyZjdOcG0=
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 127
	
	grant_type=authorization_code&code=zNlyssMxdc88XcKeLdfHvtxmApe&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fserver_sample.php
	
If your application is unable to create or change the HTTP Authorization header, the *application ID* and *application secret* credentials can alternatively be supplied in the body of the request using the parameters `client_id` and `client_secret`:

    grant_type=authorization_code&code=zNlyssMxdc88XcKeLdfHvtxmApe&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fserver_sample.php&client_id=&773client_secret=zNlyssMxdc88XcKeLdfHvtxmApex

An authorization code can only be exchanged once. Attempting to re-exchange a code will generate a bad request response, outlined below in *Error handling*.

##### Error handling

Authentication errors, including duplicate, incorrect or missing *application ID* or *application secret*, result in an HTTP *unauthorized* response with a status of `401 Unauthorized`, a `WWW-Authenticate` HTTP header and a plain text message in the body.

Errors due to incorrect or missing values for `grant_type`, `code` and `redirect_uri` result in a HTTP *bad request* response with a status of `400 Bad Request` and a JSON format error code and message:

	HTTP/1.1 400 Bad Request
	Content-Type: application/json
	Content-Length: 82
	
	{"error":"invalid_grant","error_description":"Invalid access code"}
	
Missing values generate a response with an `invalid_request` error code. Invalid values (including previously used codes) generate a response with an `invalid_grant` error code.  Specifying a value other than `authorization_code` (or `refresh_token`) generate a response with an `unsupported_grant_type` error code.


### 5. Extract the access token

A successful response from the code exchange request in the previous stage is JSON format data containing the access token. Your application parses the JSON to extract the OAuth access token.

	{
	    "access_token": "MSwxNMWRSemRhbTVVeWYwDA4NDMzY2LDsYWxsLCw0TWtrNEFBNFJoLMSw3NzOTAzZQYWdZeEEEwMzczNDM1",
	    "expires_in": 3600,
	    "refresh_token": "MSwxMDM3MzRU3OUMktdmTsZpCDveWT5XMxQOG1SQTtNzczLVUcHOzNADEsbwGFV",
	    "token_type": "bearer"
	}

The JSON data object contains a number of properties in addition to the access token:

<dl>
<dt><code>access_token</code></dt>
<dd>The OAuth access token value.</dd>
<dt><code>expires_in</code></dt>
<dd>The remaining lifetime of the access token measured in seconds.</dd>
<dt><code>refresh_token</code></dt>
<dd>A token that used to acquire future replacement access tokens without asking the user to re-authorize.</dd>
<dt><code>token_type</code></dt>
<dd>The Mendeley API issues bearer tokens so this value will always be `bearer`.</dd>
</dl>

**Important**: your application must keep the access token and the refresh token secret at all times. Access tokens and refresh tokens should not be visible to any user, including the Mendeley account user.


### Refreshing access tokens

Access tokens have a lifetime limited to one hour. Using the authorization code flow, access tokens can be renewed without repeated user authorization. The refresh token, obtained in the final step of the access token generation process, encapsulates the user authorization to create new tokens. To obtain a new access token a *refresh token* request is made. 

The request is *similar* to the authorization code exchange and is made to the same endpoint: `https://api.mendeley.com/oauth/token`. The request:

* method must be POST
* is authenticated using HTTP Basic authentication using the *application ID* and *application secret*
* body contains three parameters and values named `grant_type`, `code` and `redirect_uri`

*Unlike* the code exchange request the body parameters are:

<dl>
<dt><code>grant_type</code></dt>
<dd>The value <em>must</em> be <code>refresh_token</code> indicating your application is refreshing an access token.</dd>
<dt><code>refresh_token</code></dt>
<dd>The value for the <em>refresh token</em> your application previously extracted.</dd>
<dt><code>redirect_uri</code></dt>
<dd>The URL value for <em>Redirection URL</em> you set when registering your application. You can check or change the redirection URL of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. Remember to URL encode this value.</dd>
</dl>


    POST /oauth/token HTTP/1.1
    Host: api.mendeley.com
	Authorization: Basic NzczOnh6Y2RvRzh3bVJyZjdOcG0=
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 167
	
	grant_type=refresh_token&refresh_token=MSwxMDM3MzRU3OUMktdmTsZpCDveWT5XMxQOG1SQTtNzczLVUcHOzNADEsbwGFV&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fserver_sample.php

A successful refresh response body is JSON data containing the replacement access token. The JSON object contains the same properties as the result of the original access token response.

	{
	    "access_token": "MSwxN1zZPUxsLIzDM1wMJWEdDaTZiNNzMsYLDEdzcXNDY4NzaSzDA4Sw3QWCxFYFBZ0ZWzN3NzMZ3MWp5GM",
	    "expires_in": 3600,
	    "refresh_token": "MSwxMDM3MzRU3OUMktdmTsZpCDveWT5XMxQOG1SQTtNzczLVUcHOzNADEsbwGFV",
	    "token_type": "bearer"
	}

Potential errors and responses are the same as the code exchange request and are described above.
	
A new refresh token may be issued during the process to obtain a new access token. Your application should discard the previous refresh token and store the new value. Ensure your application stores the new value securely.


### Client software libraries

While the steps outlined above, indicate the tasks required by a client application to authorize an API request, we recommend taking advantage of a library in your application. Many [OAuth 2.0 client libraries](http://oauth.net/2/) are available for a wide range of languages and environments. 

### Further resources

* [Authorization](authorization_overview.html) explains how to use the access token you obtained to authorize API requests.
* [OAuth 2.0](http://oauth.net/2/) protocol introduction and specification.

