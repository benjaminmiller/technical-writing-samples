## Hello Mendeley

Welcome to a short tutorial designed to get you connected to the Mendeley API for the first time. The tutorial is suitable for anyone new to the Mendeley API and should take around 45 minutes to complete. Some technical knowledge is required but you do not have to be a professional software developer. During this tutorial you will:

1. Create a Mendeley user account
* Register an application in the developer portal
* Retrieve an access token from the interactive console
* Use the API to perform a search and fetch metadata for a document


### 1. Create a Mendeley user account

You will need a Mendeley user account to use the Mendeley API. If you do not have an account, follow the steps below to create a free account.

* Open [www.mendeley.com/join/](https://www.mendeley.com/join/) in your web browser.
* Follow the instructions displayed in the web page to create the account.

### 2. Register an application

Your application must be registered for Mendeley API authorization to be granted. Application registration provides you with OAuth credentials required for user authorization. To register your application using the *My Applications* page in the *Developer Portal*:

* Open [dev.mendeley.com/yourapps.html](http://dev.mendeley.com/yourapps.html) in your web browser and sign in with your Mendeley account's email address and password.
* Complete the *Register a new app* form and make a note of the *application ID* and the *secret* as you will not be able to retrieve it later.

See [Application Registration](../reference/topics/application_registration.html) for further information about application registration and guidance for each of the requested values.

### 3. Retrieve an access token

Mendeley uses OAuth access tokens to provide authorization for API requests. A token is a long sequence of characters that contains security credentials. Each token has a limited lifetime and once expired, a replacement token must be generated. 
Normally tokens are generated using the Mendeley API OAuth authentication service but for this tutorial you will use the web browser to create a token.

1. Open <a href="https://api.mendeley.com/oauth/authorize?response_type=token&scope=all&client_id=777" target="_blank">https://api.mendeley.com/oauth/authorize?response\_type=token&scope=all&client\_id=777</a> in your web browser to display an authorization panel.
* Authorize the page by entering your Mendeley account email address and password credentials.
* The access token is displayed in the result panel of a successfully authorized page. Copy the long sequence of characters, that form the access token, to use in the next stage. 

If your access token expires, repeat the steps to generate a replacement token. Access tokens currently have a lifetime of one hour but this may change.


### 4. Fetch a document from the Mendeley Catalog

With an access token, you can now use the API to fetch document metadata from the millions of documents available in the Mendeley catalog. In this tutorial, you will search for a document using the Digital Object Identifier (DOI). A DOI is an external unique reference that identifies a digital document regardless of publishing location.

You will use a utility called curl to send HTTP requests to the Mendeley API. curl is pre-installed on many operating systems and can be downloaded from [curl.haxx.se/download.html](http://curl.haxx.se/download.html). In further tutorials you will write programs to make API requests.

Now fetch metadata about the document *"Laser cooling of atoms"* using the DOI `10.1103/PhysRevA.20.1521`. The command you will use to fetch the metadata is:

	curl --request GET --header "Authorization: Bearer <TOKEN>" "https://api.mendeley.com/catalog?doi=10.1103/PhysRevA.20.1521"

* Open a shell console or terminal window and check curl is installed by typing `curl --version` at the prompt and pressing return. If curl is installed correctly you will see version information displayed in the console.
* Type the command listed above, replacing `<TOKEN>` with the characters from your recently obtained access token.
* Press the enter or return key to send the HTTP request over the Internet to the Mendeley API.
* The response data, in this case JSON formatted metadata, is displayed in the console.

Examine the JSON response data to identify useful attributes provided. The Mendeley API has included the title, authors, abstract and a web link among many other useful attributes.

If the response generates a token expiry error, simply create a new token and resend the request.


### Curl command in detail
Now we will examine the curl command in more detail and see which parts affect the Mendeley API behavior. The command can be broken into separate components:

<dl>
<dt><code>curl</code></dt>
<dd>invokes the curl utility to send the request</dd>
<dt><code>--request GET</code></dt>
<dd>command-line argument sets the HTTP method GET to request the document from the API. The GET method is used to read data from the API. Other methods are used to create, edit and remove resources.</dd>
<dt><code>--header "Authorization: Bearer <TOKEN>"</code></dt>
<dd>command-line argument adds an HTTP header to the request containing the access token. OAuth, the authorization protocol used by the Mendeley API, expects the access token to be supplied in an HTTP header named `Authorization`.</dd>
<dt><code>https://api.mendeley.com/catalog?doi=10.1103/PhysRevA.20.1521</code></dt>
<dd>the Mendeley API URL the request is sent to.</dd>
</dl>

The URL you used to request the catalog document has a particular structure and can also be examined as separate components:

<dl>
<dt><code>https://api.mendeley.com/</code></dt>
<dd>the Mendeley API host name. All requests to the API are directed at this server.</dd>
<dt><code>catalog</code></dt>
<dd>the type of resource to fetch – in REST APIs, requests are directed to a particular type of resource. The Mendeley API exposes Catalog Documents through the <em>catalog</em> resource.</dd>
<dt><code>?doi=10.1103/PhysRevA.20.1521</code></dt>
<dd>an additional qualifier to filter the result set. In this case you only wish to receive documents with the specified DOI. Additional qualifiers for Mendeley API requests are supplied in an HTTP query string.</dd>
</dl>

Now that you understand how to search for and retrieve metadata from the Mendeley API, try changing the command to retrieve metadata about the document *"Starch mobilization in leaves"* with the DOI `10.1093/jxb/erg036`.


### What you've learned

Now that you have completed the tutorial you have learned how to:

* create a Mendeley account
* register an API application with Mendeley
* generate, retrieve and refresh an access token from the OAuth endpoint
* incorporate an OAuth access\_token into a HTTP request
* make a catalog search request against the Mendeley API and display the response
* use curl to quickly demonstrate using API methods
* modify the results of a request using an HTTP query string


### What next?

Now that you have successfully made an API request to retrieve document metadata you may want to:

* explore other [platform objects](../overview/platform_objects.html) available and learn more about [documents](../overview/platform_objects.html#documents)
* learn more by working through a more complex app in a [step-by-step tutorial](walk_through.html)
* learn more about [authorizing API requests](../reference/) using OAuth and choose the right authorization workflow for your application
