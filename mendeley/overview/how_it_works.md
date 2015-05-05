## How it Works

We built the Mendeley API to feel familiar and be easy-to-use. Using *REST* principles, formatting data as *JSON*, securing connections with *HTTPS* and authorizing access with *OAuth 2.0* makes an API that is quick and simple to get started with.

### REST

Mendeley's API is a RESTful API promoting the manipulation of resources using predictable URLs. Individual resource records are located using the base URL, the resource type and a persistent identifier, such as `https://api.mendeley.com/files/{id}` The base URL for all API requests shown in the documentation is:

    https://api.mendeley.com

The HTTP request's method determines the operation on the resource: GET fetches a resource; POST creates a new resource; PATCH updates an existing resource and DELETE removes a resource. 

To learn more about about REST, see [Representational state transfer](http://en.wikipedia.org/wiki/Representational_state_transfer#Applied_to_web_services)

### JSON

API response data is delivered in [JSON](http://json.org) format. JSON data is easily read by humans and computers. JSON parsers are available for almost all commonly used languages and many languages include built-in JSON parsers. To learn more about JSON visit [www.json.org](http://json.org).

### HTTPS

All API requests must be sent using TLS/SSL connections. Connections attempted with plain HTTP are refused. 

### OAuth 2.0

[OAuth 2.0](http://oauth.net) is used to authenticate and authorize all Mendeley API requests. OAuth is a widely adopted, open protocol for users to delegate authorization. The API's OAuth service provides three authorization flows: *Authorization Code* and *Implicit* to access user's data and *Client Credentials* if you only need access to the read-only Catalog. See [Authorization](../reference/topics/authorization_overview.html) for further information about OAuth support in the API.

### CORS

The Mendeley API supports CORS enabling you to make requests direct from Javascript. See [CORS](../reference/topics/cors.html) for further information about CORS support in the API.


### Principle Mendeley Resources

Mendeley's API gives the developer access to the data visible in the desktop and mobile applications. Using the REST principles, each resource is identified by a URL path. There are many types of data available to you but the principle resources include:

<dl>
	<dt><code>/document</code></dt> 
	<dd><a href="platform_objects.html#documents">User Documents</a> created by Mendeley users and assigned to either Libraries or Groups</dd>
	<dt><code>/catalog</code></dt>
	<dd><a href="platform_objects.html#documents">Catalog Documents</a> from Mendeley’s crowdsourced collection of papers</dd>
	<dt><code>/files</code></dt>
	<dd><a href="platform_objects.html#files">File</a> attachments associated with User Documents</dd>
	<dt><code>/groups</code></dt>
	<dd>Collaborative users form a <a href="platform_objects.html#documents">group</a> to sharing documents and ideas</dd>
	<dt><code>/annotations</code></dt>
	<dd>Users mark up and comment in documents and files using <a href="platform_objects.html#annotationss">annotations</a></dd>
</dl>

See [Platform Objects](../overview/platform_objects.html) for more information on the on the principle resources and the [API Reference](/slate/) for the entire range of resources available to you as a developer.
