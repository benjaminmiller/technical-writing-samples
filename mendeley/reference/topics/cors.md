## CORS

Cross origin resource sharing (CORS) is enabled on *all* API requests. CORS helps web browsers to make API requests directly without using a server.

In almost all situations, your application will not have to take any special action. CORS adds an additional header to API responses enabling *any* client domain to make API requests in the browser:

    Access-Control-Allow-Origin: *

Web browsers are normally unable to make requests to load executable resources from a server at another domain. A page hosted at www.yourapp.com would be unable to make API requests to api.mendeley.com. Browsers' web application security model mandates a same origin policy for sensitive resource load requests but CORS enables exceptions to the same origin policy.

The `Access-Control-Allow-Origin` is included in responses to requests that include an `origin` HTTP header.


### Further Resources

* W3C [Cross-Origin Resource Sharing Recommendation](http://www.w3.org/TR/cors/)