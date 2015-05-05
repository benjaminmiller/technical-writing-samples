## Frequently asked questions

These FAQs are designed to answer common questions and provide a better understanding of the Mendeley API and techniques for interacting with the API. The answers provide basic information but will link to further information when required.


### API responses

<a name="api_responses_null_in_json"></a>
### Why can't see an attribute included in the response JSON when the documentation states it should be present?

Attributes with `null` values are omitted from the API response. Your application should check for the existence of a any property on the JSON object before attempting to use the value.


### Interactively exploring the API

<a name="interactively_exploring_faster_than_curl"></a>
### Is there a faster way than curl to experiment with the API?

Use the [Interactive Console](https://api.mendeley.com/apidocs) to see all the resources available, easily login and avoid copying and pasting access tokens. Use the interactive console to submit parameters with requests and see the results output within the page.

<a name="interactively_exploring_curl_show_headers"></a>
### How do I see request and response HTTP headers when using curl?

Use the `-v` argument to display both request and response headers in the console output. Request headers are preceded with a greater-than symbol ( > ) and response headers are preceded by a less-than symbol ( < ).

	curl -v https://api.mendeley.com/documents?access_token=MSwxNDA…4bkG

	> GET /documents?access_token=MSwxNDA…bkG HTTP/1.1
	> User-Agent: curl/7.30.0
	> Host: api.mendeley.com
	> Accept: */*
	
	< Content-Type: application/vnd.mendeley-document.1+json
	< Date: Fri, 15 Aug 2014 14:52:53 GMT
	< Vary: Accept-Encoding
	< Vary: Accept-Encoding
	< X-Mendeley-Trace-Id: PbBmO4yljeY
	< Content-Length: 1924
	< Connection: keep-alive
	< 
	{ [data not shown] }
	

<a name="interactively_exploring_curl_format_json"></a>
### How do I format the JSON output when using curl?

Pipe the response of the curl request through Python's `mjson` formatting tool by appending `| python -mjson.tool` to the curl command.

	curl https://api.mendeley.com/documents?access_token=MSwxNDA4…4bkF | python -mjson.tool
	
	100  1924  100  1924    0     0   5688      0 --:--:-- --:--:-- --:--:--  5692
	[
	    {
	        "abstract": "Barthes's essay argues against traditional literary criticism's practice of incorporating the intentions and biographical context of an author in an interpretation of a text, and instead argues that writing and creator are unrelated.",
	        "authors": [
	            {
	                "first_name": "T.",
	                "last_name": "Dalrymple"
	            }
	        ],
	        …
	        "title": "The death of the author",
	        "type": "generic",
	        "year": 2008
	    }
	]
    