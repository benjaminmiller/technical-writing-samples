## Versioning

APIs have change to bring your application new features and include improvements. To insulate against change, while still allowing improvements, the API is versioned.

API versions are implemented using HTTP's content negotiation mechanism. Your application requests a known version of a resource using an HTTP `Accept` header:

	GET /documents?limit=125 HTTP/1.1
	Accept: application/vnd.mendeley-document.1+json
	Authorization: Bearer MSwxNDA5N…T1I5dnhPa0U

The value for the `Accept` header is an Internet media type (MIME type). Each response confirms the version, and type, of resource returned using an HTTP `Content-Type` header:

	HTTP/1.1 200 OK
	Content-Type: application/vnd.mendeley-document.1+json
	Content-Length: 882
	
	[content not listed]

All Mendeley API resource versions are created from a format:
    
    application/vnd.mendeley-<RESOURCE_TYPE>.<VERSION>+json

### Versioning is important 

You are **very strongly** encouraged to include version information in your application's requests. Requesting a specific version of a resource is the only way to ensure predictable and stable API responses. API requests for a specific version of the resource: 

* will return consistent results
* will be maintained by Mendeley into the future
* mean you will not have to have to retest your application each time a new version of the API is released

If a version is not included in the request, the version returned in the response is undocumented – *another reason to request a specific version*.