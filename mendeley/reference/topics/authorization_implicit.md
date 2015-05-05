## Implicit authorization flow

Implicit authorization flow is used to obtain an access token to authorize API requests. This authorization flow is best suited to applications running in environments that do not provide secure storage. Implicit authorization is often used for client side applications running in the browser using JavaScript or Flash. [Other authorization flows](authorization_overview.html) are available to obtain an access token.

Access tokens, obtained using implicit authorization flow, provide permissions for your client application to manipulate documents and other resources on behalf of a Mendeley user and make requests for all API resources. Access tokens have a limited lifetime, and expire after one hour. The user must be prompted to authenticate again once the token expires.

Your application should warn users they will be asked to authorize using their Mendeley account before initiating an action that requires authorization because users' web browsers will redirect to another site.

Remember, your application must be [registered](application_registration.html) with the API to generate the *client application ID* and *redirection URL* used in the implicit authorization flow.

### Example

The flow for implicit authorization is simple:

1. Create a URL to the OAuth authorization service 
* Display the authorization user interface
* Extract the access token from the URL of redirection page

### Create a URL

Your client application loads the authorization user interface into a web browser window. The Mendeley OAuth authorization endpoint is `https://api.mendeley.com/oauth/authorize`. Four values must be passed, contained in an HTTP query string, with the request:

<dl>
<dt><code>client_id</code></dt>
<dd>The value for <em>application ID</em> you received when registering your application. You can check the ID of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal.</dd>
<dt><code>redirect_uri</code></dt>
<dd>The URL value for <em>Redirection URL</em> you set when registering your application. You can check or change the redirection URL of your application using the <a href="http://dev.mendeley.com/yourapps.html">My Applications</a> page in the Mendeley Developer Portal. Remember to URL encode this value.</dd>
<dt><code>response_type</code></dt>
<dd>The value <em>must</em> be <code>token</code> indicating the implicit grant type is required and an access token is desired in the response.</dd>
<dt><code>scope</code></dt>
<dd>The value <em>must</em> be <code>all</code> permitting the manipulation of all API resources.</dd>
</dl>

An optional parameter *should* be included in all requests to protect against Cross-site request forgery (CSRF) attacks.

<dl>
<dt><code>state</code></dt>
<dd>A random value, unique for every access token request. Used in the <em>Extract the access token</em> stage below.</dd>
</dl>

The completed URL will appear similar to:

    https://api.mendeley.com/oauth/authorize?client_id=777&redirect_uri=http:%2F%2Flocalhost%2Fmendeley%2Fsample.html&response_type=token&scope=allstate=692055100728.984

### Display the authorization user interface

Use the URL, constructed in the previous step, to ask the user to authorize using their Mendeley credentials. Your application can load, using a normal GET request, the Authorization page into an existing browser window because the page will be redirected back your application in the final stage of the process. Alternatively, your application can open a new pop-up window or panel to display the authorization process but extracting and transferring the access token between web browser windows requires a little more work.

### Extract the access token

Once the user has authorized, they are redirected to the page specified in the request's `redirect_uri` query argument. The OAuth service will append a  fragment to the URL containing values for `access_token` and `state` keys. 

    http://localhost/mendeley/sample.html#access_token=MSwxNDA4MzcwNDA2NzIxLDEwMzczNDM1MSw3NzcsYWxsLCxUUkJaQnpjSUlwYlZaSW9KbHlQNFJtVGdaalk&state=arandomvalue


Parse the URL fragment and extract values for an access token and state. In a web browser application, this is best achieved using JavaScript as  browsers do not include URL fragments in requests sent to servers.

	var params = {}, queryString = location.hash.substring(1),
	    regex = /([^&=]+)=([^&]*)/g, m;
	while (m = regex.exec(queryString)) {
	  params[decodeURIComponent(m[1])] = decodeURIComponent(m[2]);
	}
	console.log(params.state); // always compare this value
	console.log(params.access_token);

**Important**: before attempting to use the access token, first check the value of the the `state` key. This value *must* match the value your application supplied in the URL creation step to protect against [CSRF](http://en.wikipedia.org/wiki/Cross-site_request_forgery) attacks.

### Refreshing 

There is no special protocol to refresh an access token using the implicit authorization flow. The user must be prompted to authorize your application again using the same process. Unlike other authorization flows, your application does not receive the expiry time of the supplied token.

### Error handling

If all the request parameters are valid and the user authorizes the API request, the user is redirected back to your application and the URL you specified. If one of the request parameters is invalid, an error condition exists.

User authentication errors, including incorrect email address or password, are resolved within the authorization process and your application does not have to handle errors for the user. The user can reset their password from the authorization page, solving forgotten password issues.

Incorrect values supplied for `client_id` and `redirect_uri` do not display the authentication page and return a response with a 400 Bad Request status code and a JSON object containing an explanatory message. The user is **not** redirected back to your application.

    HTTP/1.1 400 Bad Request
    Content-Type: application/json
    Content-Length: 90
    
    {"error_message":"Redirection URI does not match the one registered for this application"}

Invalid values for `scope` and `response_type` are sent to your application's redirect page. An HTTP query string contains the fields `error` and `error_description` indicating the nature of the error.

    http://localhost/mendeley/sample.html?error=unsupported_grant_type&error_description=Invalid+response+type

Your application should report the authorization failure to the user, explain why an error may have occurred and suggest possible solutions.

### Client software libraries

While the steps outlined above, indicate the tasks required by a client application to authorize an API request, we recommend taking advantage of a library in your application. Many [OAuth 2.0 client libraries](http://oauth.net/2/) are available for a wide range of languages and environments. 

### Further resources

* [Authorization](authorization_overview.html) explains how to use the access token you obtained to authorize API requests.
* [Readership Locations](../../getting_started/walk_through.html) is a tutorial stepping through implicit authorization in a JavaScript client application.
* [OAuth 2.0](http://oauth.net/2/) protocol introduction and specification.
