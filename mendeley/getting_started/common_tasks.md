## Common tasks

Initial help for commonly performed tasks using the API or tasks where the API resources may not be immediately obvious.

### Documents

<a name="document_file_in_catalog"></a>
##### Determine if a document for a PDF file exists within the Mendeley catalog

Catalog documents can be fetched from the `/catalog` API resource using a SHA1 hash of the file. Compute a hash of the PDF file and use as the value for the `filehash` HTTP query string field.
	
	openssl sha1 sample.pdf
	SHA1(/path/to/sample.pdf)= b0b75611366f6a5af8dd906c935f38bf3f509751
	
	curl -H "Authorization: Bearer MSwxNDA5…a1BJUTQ" "https://api.mendeley.com/catalog?filehash=b0b75611366f6a5af8dd906c935f38bf3f509751"

If the response contains a document, the file has been included in the catalog. The [Ruby Catalog Search](../code/sample_code.html#ruby_file_in_catalog) sample code is available to illustrate how this may be achieved in a script.

### Files

<a name="file_document_has_file"></a>
### Check if a document has a file attached

To determine if a *single* document has a file attached, request the files for the known document using its identifier in the request's HTTP query string:
    
	GET /files?document_id=1137042a-8c30-3cd6-a7f6-7d5f1f6c450d
	
An empty array in the response indicates the document does not have files attached. 

To determine if documents in a collection may have files attached, inspect the `file_attached` member of a document. Request the documents with a `client` or `all` value for the `view` HTTP query string field.

	{
		"id": "1137042a-8c30-3cd6-a7f6-7d5f1f6c450d",
		…
		"file_attached": true,
		…
	}


<a name="file_download"></a>
### Download a media file

To download a media file, request the individual file resource:

	GET /files/1137042a-8c30-3cd6-a7f6-7d5f1f6c450d

The successful API response will have:

* `303 See Other` HTTP status
* HTTP `Location` header containing a link to the file location
* an empty body

The Location header contains a URL value to download the media file. The URL will only remain valid for a short period of time so your application should not cache these values and *should* only request a location just before the application intends to download the media file. The URL location, format and authorization mechanism are subject to change and your application *must* not attempt to parse the download URL.

	HTTP/1.1 303 See Other
	Location: Location: https://mendeley-files.s3.amazonaws.com/0c/07/0c071302a58db1e471c0923deeb9f47c5cd2f63d?response-content-disposition%3Dattachment%3Bfilename%3D%E2%80%9CReport.pdf%22&response-content-type=application%2Fpdf&AWSAccessKeyId=AKIAJGTIMKBSM3FEA25A&Expires=1409062238&Signature=aLU%2F%2FaqPGYgmiTCgHuKT%2B%2F%2BgWNk%3D
	Content-Length: 0
	
Once the file has completely downloaded to local storage, your application should ensure file integrity by comparing the file size or a SHA1 hash to the API response file metadata.
	
	// API response for file:
	{
		"id":"76d0efcb-2690-507f-07b7-21234c617b03",
		"document_id":"1137042a-8c30-3cd6-a7f6-7d5f1f6c450d",
		"mime_type":"application/pdf",
		"file_name":"Report.pdf",
		"size":6123,
		"filehash":"0c071302a58db1e471c0923deeb9f47c5cd2f63d"
	}
	
	
	stat -f %z report.pdf
	6123
	openssl sha1 report.pdf
	SHA1(/path/to/report.pdf)= 0c071302a58db1e471c0923deeb9f47c5cd2f63d
	

<a name="file_upload"></a>
### Upload a media file

Each file upload request transfers the media data and creates a new object to reference the media file. Files are uploaded using a POST request with HTTP headers containing the metadata and the body containing the media data. Four additional HTTP headers and values 

	POST /files HTTP/1.1
	Authorization: Bearer MSwxNDA5MDU0ODM0MTQy…zFJd0liZHpSMVU
	Link: <https://api.mendeley.com/documents/1137042a-8c30-3cd6-a7f6-7d5f1f6c450d>; rel="document"
	Content-Disposition: attachment; filename="report.pdf"
	Content-Type: application/pdf
	Content-Length: 6123

	[binary data in the request body]

The request HTTP header values should be:

<dl>
	<dt><code>Link</code></dt>
	<dd>The <code>document</code> relationship must contain the URL to the intended parent document.</dd>
	<dt><code>Content-Disposition</code></dt>
	<dd>This must be <code>attachment</code> and your application can use the <code>filename</code> qualifier to suggest a filename.</dd>
	<dt><code>Content-Type</code></dt>
	<dd>The Internet MIME type for the uploaded file. Unrecognized MIME types are persisted and delivered as binary data: <code>application/octet-stream</code>.</dd>
	<dt><code>Content-Length</code></dt>
	<dd>The number of bytes in the request's body.</dd>
</dl>

A successful file upload generates an HTTP response with:

* an HTTP status of `201 Created`.
* an HTTP `Location` header containing the URL of the newly created file.
* a body containing the JSON representation of the newly created file.


<a name="file_update"></a>
### Updating a file object

File objects are immutable and can not be updated. Your client application should replace the document's file in an atomic manner by: 

1. Uploading a new file for the document
* Removing the file your application wished to update


### Groups

<a name="group_get_documents"></a>
### Get documents for a group
Groups documents are listed using the `documents` platform object and supplying the group ID in the HTTP query string:

	GET /documents?group_id=77dd7020-e2f6-3fb3-94ea-3cea11555af2


<a name="group_add_document"></a>
### Add a document to a group

Documents created with a group ID included are inserted into the group. The group must exist and the authorized user must be an owner, administrator or member of the group.
Adding a library document to a group, creates a copy of the original document.

	POST /documents

	{
	  "group_id":"77dd7020-e2f6-3fb3-94ea-3cea11555af2",
	  "title": "Map Reduce: Simplified data processing on large clusters",
	  "type": "journal",
	  "authors": [
		{
		  "first_name": "Jeffrey",
		  "last_name": "Dean"
		},
		{
		  "first_name": "Sanjay",
		  "last_name": "Ghemawat"
		}
	  ],
	  "year": 2008
	} 

<a name="group_find_owner"></a>
### Find the owner of a group
The owner of a group has a profile identifier that can be used to retrieve metadata for the group owner.

1. Request the group using `GET /groups/{id}`.
* Retrieve the identifier value for the member `owning_profile_id` from the response.
* Use the identifier to request the profile information using `GET /profiles/{id}`.




### Folders


<a name="folder_create_in_folder"></a>
### Create a folder inside an existing folder
When creating a new folder, include a value for a parent folder's identifier in the request's body. 

	POST /folders
	
	{
	  "name":"Untitled folder",
	  "parent_id":"95342445-ab75-42ba-b98f-888bbe543c0f"
	}

<a name="folder_create_in_group"></a>
### Create a folder in a group
When creating a new folder, include a value for the group's identifier in the request's body. 

	POST /folders

	{
	  "name":"デジタルコンテンツ",
	  "group_id":"9ab2887c-9b1c-38d7-a64a-3491306362a7"
	}

<a name="folder_move"></a>
### Move a folder into another folder
Update the value of the `parent_id` member to move a folder within a hierarchy. 

    PATCH /folders/9ab2887c-9b1c-38d7-a64a-3491306362a7
    {
      "parent_id":"95342445-ab75-42ba-b98f-888bbe543c0f"
    }

A folder can not be moved into itself or any of it's descendent child folders. 

<a name="folder_list_child"></a>
### List the child folders of a folder
It is not possible to list the child folders of a folder directly using the API. Your application should construct a tree model of folders by parsing the results of `/folders` and using the value of the `parent_id` member to establish a parent relationship.

<a name="folder_to_root"></a>
### Move a folder into the root level
Update the `parent_id` value of a folder to the special value *root* to remove any previous parent folder.

	PATCH /folders/9ab2887c-9b1c-38d7-a64a-3491306362a7
	
	{
	  "parent_id":"root"
	}
	

### Trash

<a name="trash_in"></a>
### Move a document to Trash
Documents are moved to Trash through the `/documents` resource:

	POST /documents/{id}/trash

An HTTP response with a status code 204 and an empty body indicates the move was successful. 

<a name="trash_out"></a>
### Remove a document from Trash
Documents are moved from Trash through the `/trash` resource, using a *restore* action:

	POST /trash/{id}/restore

An HTTP response with a status code 204 and an empty body indicates the move was successful. 


### Document types

<a name="doc_types_populate_ui"></a>
### Populate user interface controls for with document types

Document types are commonly used to populate selection user interface controls. The JavaScript snippet below illustrates the creation of a popup menu with document types and the setting of the chosen value on a `document` object:

	…
	<select id="doc_type_popup" onchange="docTypeDidChange(this);"></select> <!-- the popup menu in the HTML -->
	
	…
	function processDocumentTypesResponse(xmlhttp) {
		// in the successful response to an AJAX request for /document_types
		data = JSON.parse(xmlhttp.responseText);
		
		popup_menu = document.getElementById("doc_type_popup");
		for (i=0;i<types.length;i++) {
		    var option = document.createElement("option");
		    option.textContent = types[i].description; // set the label visible to the user
			option.value = types[i].name; // set the data value
			popup_menu.appendChild(option);
		}
	}
		
	function docTypeDidChange(sender) {
		selectedDocument.type = sender.value; // assume selectedDocument is a JS object
	}
