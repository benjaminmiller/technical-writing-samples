## Client credentials authorization flow

Client credentials authorization flow is used to obtain an access token to authorize API requests. This authorization flow is best suited to applications that only require access to the read-only Mendeley Catalog of crowd sourced documents. [Other authorization flows](authorization_overview.html) are available to obtain an access token providing more capabilities. 

Access tokens, obtained using client credentials authorization flow, only provide permission for your client application to search for and retrieve Catalog documents. The access tokens can **not** be used to authorize other API requests requesting data on behalf of a Mendeley user. Access tokens have a limited lifetime, and expire after one hour. Your client application simply requests a replacement access token one the current token expires.

Remember, your application must be [registered](application_registration.html) with the API to generate the *client application ID* and *application secret* used in the client credentials authorization flow. Your application must be capable of securely storing the application credentials required for the client credentials authorization flow.


### Example

The flow for client credentials authorization is:

1. Request an access token from the API token endpoint
* Extract the access token from the response

### Request an access token

Your application requests a token from the API. The Mendeley OAuth token exchange endpoint is `https://api.mendeley.com/oauth/token` and the token request is made using an HTTPS request with the conditions:

* the method must be POST
* is authenticated using HTTP Basic authentication
* the body contains a single parameter and values named `grant_type`

The request is authenticated using HTTP Basic authentication:

<dl>
<dt><code>username</code></dt>
<dd>The value for <em>application ID</em> you received when registering your application. You can check the ID of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal.</dd>
<dt><code>password</code></dt>
<dd>The value for the <em>application secret</em> you received when registering your application. If absolutely necessary, you can reset the secret for your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. <strong>Important</strong>: your application must store the application secret value securely and must not expose the value to any users.</dd>
</dl>

The value sent in the body of the request is:

<dl>
<dt><code>grant_type</code></dt>
<dd>The value <em>must</em> be <code>client_credentials</code> indicating your application is requesting an access token for itself.</dd>
</dl>

	curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -u 799:1K2757WBBkLr9DOs -d "grant_type=client_credentials" https://api.mendeley.com/oauth/token


	POST /oauth/token HTTP/1.1
	Authorization: Basic NzczOjFLMjc1N1dCQmtMcjlET3M=
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 29
	
	grant_type=client_credentials
	
If your application is unable to create or change the HTTP Authorization header, the *application ID* and *application secret* credentials can alternatively be supplied in the body of the request using the parameters `client_id` and `client_secret`:

    grant_type=client_credentials&client_id=799&client_secret=1K2757WBBkLr9DOs
	

### Extract the access token

A successful response from the token request in the previous stage is JSON format data containing the access token. Your application parses the JSON to extract the OAuth access token.

	{
	    "access_token": "MSwxNMWRSemRhbTVVeWYwDA4NDMzY2LDsYWxsLCw0TWtrNEFBNFJoLMSw3NzOTAzZQYWdZeEEEwMzczNDM1",
	    "token_type": "bearer",
	    "expires_in": 3600,
	    "refresh_token": null
	}

The JSON data object contains a number of properties in addition to the access token:

<dl>
<dt><code>access_token</code></dt>
<dd>The OAuth access token value.</dd>
<dt><code>token_type</code></dt>
<dd>The Mendeley API issues bearer tokens so this value will always be `bearer`.</dd>
<dt><code>expires_in</code></dt>
<dd>The remaining lifetime of the access token measured in seconds.</dd>
<dt><code>refresh_token</code></dt>
<dd>This value will be <code>null</code> because the client credentials authorization flow does not provide long lived tokens</dd>
</dl>

### Refreshing 

There is no special protocol to refresh an access token using the client credentials authorization flow. Your client application simply requests a new token once the previous token has expired.

### Error handling

If all the request parameters are valid the response contains the access token. If one of the request parameters is invalid, an error condition exists.

Authentication errors, including incorrect or missing *application ID* or *application secret*, result in an HTTP *unauthorized* response with a status of `401 Unauthorized`, a `WWW-Authenticate` HTTP header and a plain text message in the body.

Errors due to incorrect or missing values for `grant_type` result in a HTTP *bad request* response with a status of `400 Bad Request` and a JSON format error code and message:

	HTTP/1.1 400 Bad Request
	Content-Type: application/json
	Content-Length: 82
	
	{"error":"invalid_grant","error_description":"Invalid access code"}
	
A missing *grant type* value generates a response with an `invalid_request` error code. Specifying a *grant type* value other than `client_credentials` generates a response with an `unsupported_grant_type` error code.

### Client software libraries

While the steps outlined above, indicate the tasks required by a client application to obtain an access token, we recommend taking advantage of a library in your application. Many [OAuth 2.0 client libraries](http://oauth.net/2/) are available for a wide range of languages and environments. 

### Further resources

* [Authorization](authorization_overview.html) explains how to use the access token you obtained to authorize API requests.
* [OAuth 2.0](http://oauth.net/2/) protocol introduction and specification.
