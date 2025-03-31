## Proposal for a new version directive

This is an outline for a proposed solution to breaking changes to RDF and SPARQL results syntaxes (https://github.com/w3c/rdf-star-wg/issues/141).

This proposal introduces a version directive to RDF and SPARQL results syntaxes,
and version parameters for HTTP requests and responses.

## Version directive

A new version directive can be added for all RDF and SPARQL results syntaxes to specify the processing mode.
All RDF 1.2 and SPARQL 1.2 result documents SHOULD include it.

If set to `1.2`, the document MAY use 1.2-only features, such as base direction and triple terms.
1.1-only parsers will crash on this due to the unsupported version construct.

If no version is set, it is up to the parser to choose the behaviour.
For example, parsers may fallback to 1.1 parsing, or it can default to the latest version (1.2).

## Response parameter

RDF 1.2 and SPARQL 1.2 result documents SHOULD include a `version` parameter in the content type header of responses:

```
HTTP/1.1 200 OK
Content-Type: text/turtle; version=1.2
Location: http://example.com/document.ttl
```

## Content negotiation

When sending requests to a server for an RDF document or SPARQL query results, a `version` parameter MAY be included in the `Accept` header.
If this parameter is included, a server MAY respond with an HTTP 406 response if no 1.2 representation exists.

```
GET /document.ttl HTTP/1.1
Host: example.com
Accept: text/turtle; version=1.2
```

## Behaviour in different specs

### Turtle, TriG

A similar directive to `PREFIX` and `BASE` can be introduced, which takes `1.2` as value.
This directive can be defined anywhere in the document, and applies to all triples defined after the directive.
It MAY be defined multiple times.

```text
VERSION 1.2
BASE <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
```

### N-Triples, N-Quads

These are a bit annoying, every line (except for empty lines, optionally with comments) is supposed to represent a triple.

For this, I see 3 options:

1. Include a `BASE` directive like Turtle and TriG, with a slight increase in complexity for parsers. However, since `VERSION` can be set multiple times, the benefits (line-by-line parsing, easy concatenation of multiple documents by directly appending) of N-Triples should not be lost.
2. Represent the version in a comment.
3. Don't do versioning in N-Triples and N-Quads (and only rely on the optional version parameter in the HTTP response header).

### RDF/XML

The top-level element already requires `rdf:version` to be set to `"1.2"`.
No changes are needed here anymore.

```
<rdf:RDF xmlns:rdf="http://www.w3.org/1999/02/22-rdf-syntax-ns#"
         xmlns="http://example.com/" 
         xml:base="http://example.com/"
         rdf:version="1.2">
```

### SPARQL/XML

Here, we could reuse `rdf:version` element from RDF/XML.

```
<?xml version="1.0"?>
<sparql xmlns="http://www.w3.org/2005/sparql-results#"
  xmlns:its="http://www.w3.org/2005/11/its" 
  its:version="2.0"
  rdf:version="1.2">
```

### SPARQL/JSON

The `head` entry could contain a `version` key.

```json
{
  "head": {
    "vars": [ "book" , "title" ],
	"version": "1.2"
  },
  "results": {}
}
```

### SPARQL/CSV, SPARQL/TSV

I don't see a good way to support a syntactical version directive for these.
For these, I think we can only rely on the optional version parameter in the HTTP response header.
