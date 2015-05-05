## Sample code
Sample tools and applications illustrating API requests and authorization flow.

### JavaScript

<a name="javascript_readership_locations"></a>
### JavaScript Readership Locations
A JavaScript example page showing document reader locations are plotted onto a world map. Illustrates implicit OAuth flow, fetching user documents and requesting readership statistics for catalog documents. <a href="https://github.com/Mendeley/mendeley-api-javascript-readership_map" target="_blank">GitHub source code repository</a>

### Python

<a name="python_example"></a>
### Python Example
A Python web application, written using Flask, to show documents in the user's Mendeley library together with the document's  annotations. Catalog documents can also be retrieved using a DOI. <a href="https://github.com/Mendeley/mendeley-api-python-example" target="_blank">GitHub source code repository</a>

<a name="python_catalog_example"></a>
### Python Catalog Example
A command-line Python script that retrieves the number of Mendeley users that have read a document. The script uses an OAuth client credentials flow, so users don't have to log in to use the application. <a href="https://github.com/Mendeley/mendeley-api-python-catalog-example" target="_blank">GitHub source code repository</a>

### R

<a name="r_example"></a>
### R Example
A command-line R script that retrieves the number of Mendeley users that have read a document and produces a bar-plot of the readers by academic status. <a href="https://github.com/Mendeley/mendeley-api-r-example" target="_blank">GitHub source code repository</a>

### Ruby

<a name="identifier_catalog_search"></a>
### Ruby Identifier Catalog Search
A Ruby example script that populates a command-line user interface with identifier_types and then performs a search for a catalog document using the chosen identifier type. <a href="https://github.com/Mendeley/mendeley-api-ruby-identifier-catalog-search" target="_blank">GitHub source code repository</a>

<a name="ruby_file_in_catalog"></a>
### Ruby Catalog Search
A command-line Ruby script, taking a file argument, that searches for a Catalog document referencing the same file. Illustrates client credentials OAuth flow and requesting a catalog document using a SHA1 hash of a file. <a href="https://github.com/Mendeley/mendeley-api-ruby-is-file-in-catalog" target="_blank">GitHub source code repository</a>