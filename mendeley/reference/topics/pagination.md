## Collections, paged results and ordering

API request for resources potentially may return large result sets. The API provides help for your application to manage and display large result sets:

* results are paginated into manageable batches
* key document API requests support ordering the results set


### Paged results

Resources returning collections are batched into result pages. Each page by default contains 20 items with the exception of `/seach/catalog` results which contain 10. Your application can change the number of results in each page using the `limit` field of an HTTP query string appended to the request URL:

	GET /folders?limit=3

Values for the `limit` argument can range from one to 500 with the exception of `/search/catalog` requests which have a maximum value of 100. Exceeding the limit will result in an `400 Bad Request` error response containing a JSON format error message. The response for the example request above is:

	HTTP/1.1 200 OK
	Content-Type: application/vnd.mendeley-folder.1+json
	Link: <https://api.mendeley.com/folders?limit=3&reverse=false&order=asc&marker=cbefa272-3734-4ea4-8b12-9effc6d948fc>; rel="next"
	Link: <https://api.mendeley.com/folders?limit=3&reverse=true&order=asc>; rel="last"
	Mendeley-Count: 16
	Content-Length: 450

	[
	    {
	        "created": "2014-08-17T20:06:25.000Z",
	        "id": "f67f2842-1088-4dca-8df8-835fce3ad989",
	        "name": "Folder One"
	    },
	    {
	        "created": "2014-08-21T10:05:15.000Z",
	        "id": "98302258-25ff-417a-a5cb-dca7d553251a",
	        "name": "Folder Two"
	    },
	    {
	        "created": "2014-08-21T10:05:15.000Z",
	        "id": "cbefa272-3734-4ea4-8b12-9effc6d948fc",
	        "name": "Folder Three"
	    }
	]
	
If the size of the results collection does not exceed the value specified in the `limit` argument, the results collection is not paginated.



### Collection size
Your application can obtain the size of the result collection using the value in the `Mendeley-Count` HTTP header. This header is only included in the first page of a paged results set so your application should retrieve this value before requesting other result pages.

### Navigating between pages in a collection

Page navigation links are provided by the API to help your application request further pages of results. API responses, for paged results, contain HTTP `Link` headers providing a URL used to request another page of results (*See the sample response above for an example*). Four relationship types are possible:

* **first** describes a URL to request the first page of the collection. Not present if the response is the first page in a collection.
* **previous** describes a URL to request the preceding page of the collection. Not present if the response is the first page in a collection.
* **next** describes a URL to request the following page of the collection. Not present if the response is the last page in a collection.
* **last** describes a URL to request the last page of the collection. Not present if the response is the last page in a collection.

Your application *must* follow the relationship links as a navigation interface. Please do not try to interpret or reverse engineer the URL format as the URLs may change in the future.

**Note**: reference resource collections containing predefined values or controlled vocabularies, e.g., `disciplines`, `identifier_types`, `academic_statuses`, do **not** support paged results and ordering. Responses for `/catalog` requests only return a single document within a collection and are not paginated.


### Ordering results

Document collections in responses for `/documents` and `/trash` API requests can be sorted. Sorting the results changes the order results are returned to your application and is most useful with large, paged result sets. Your application can change the order using two fields, `sort` and `order`, in the HTTP query string of an API request. To display user documents in *A-Z title* order: 

	GET /documents?sort=title&order=asc

<dl>
<dt><code>sort</code></dt>
<dd>The attribute key to sort the document collection on. For <code>/documents</code> requests, possible values are: <code>title</code>, <code>created</code> and <code>last_modified</code>. For <code>/trash</code> requests, possible values are: <code>document_id</code> and <code>deleted_at</code>.</dd>
<dt><code>order</code></dt>
<dd>The arrangement order of results. A value of `asc` arranges the results from the smallest value to the largest. A value of `desc` arranges  the results from the largest value to the smallest. If order is not included in a request, the value `asc` is used.</dd>
</dl>


### Further resources

* HTTP Link headers are specified in [RFC5988 Web Linking](http://tools.ietf.org/html/rfc5988)