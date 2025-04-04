## Proposal for a new version directive

This is an outline for a proposed solution to breaking changes to RDF and SPARQL results syntaxes (https://github.com/w3c/rdf-star-wg/issues/141).

This proposal introduces a version directive to RDF and SPARQL results syntaxes,
and version parameters for HTTP requests and responses.

The main purpose of this directive is to let parsers only supporting 1.1 quickly error while keeping all existing media types,
and offers an upgrade path for future RDF/SPARQL versions.

## Version directive

A new version directive can be added for all RDF and SPARQL results syntaxes to specify the processing mode.
All RDF 1.2 and SPARQL 1.2 result documents SHOULD (or MUST?) include it.

If set to `1.2`, the document MAY use 1.2-only features, such as base direction and triple terms.
1.1-only parsers will crash on this due to the unsupported version construct.

If [naming RDF 1.2 without triple terms](https://github.com/w3c/rdf-star-wg/issues/135) is desired,
the version value `1.2-basic` can be used.

We do not define `1.1` and `1.0` as valid version values, since this directive did not exist yet in 1.1 and below.

## Response parameter

RDF 1.2 and SPARQL 1.2 result documents SHOULD include a `version` parameter in the content type header of responses:

```
HTTP/1.1 200 OK
Content-Type: text/turtle; version=1.2
Location: http://example.com/document.ttl
```

## Content negotiation

When sending requests to a server for an RDF document or SPARQL query results, a `version` parameter MAY be included in the `Accept` header.
If this parameter is included, the server MAY respond with an HTTP 406 response if no 1.2 representation exists.
If this parameter is not included, the server SHOULD respond with a representation of the latest RDF/SPARQL version (1.2).

```
GET /document.ttl HTTP/1.1
Host: example.com
Accept: text/turtle; version=1.2
```

## Behaviour of documents without version

A version can be defined inline with a version directive, or via the `Content-Type` header.
If both are defined, they MUST be equal.

Some options that are possible if the version was not defined inline and via `Content-Type`:
1. A version MUST be set in RDF/SPARQL 1.2. Documents without it will be considered 1.1. Parsers MAY error if they encounter 1.2 features without the version being set to 1.2.
2. A version SHOULD be set in RDF/SPARQL 1.2. If no version is set, it is up to the parser to choose the behaviour. For example, parsers may fallback to 1.1 parsing, or it can default to the latest version (1.2).

## Behaviour in different specs

### Turtle, TriG

A similar directive to `PREFIX` and `BASE` can be introduced, which takes `1.2` as value.
This directive can be defined anywhere in the document.

It MAY be defined multiple times.
If different values are set for these multiple occurrences, the highest value is taken must be considered by the parser. (this is important for parsing of N-Triples/Quads where parsing of chunks can happen in parallel)

```text
VERSION "1.2"
BASE <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
```

Optional shorthand: `VERSION 1.2`

`@version` may be used instead of `@VERSION`.

### N-Triples, N-Quads

These are a bit annoying, every line (except for empty lines, optionally with comments) is supposed to represent a triple.

For this, I see 3 options:

1. Include a `VERSION` directive like Turtle and TriG, with a slight increase in complexity for parsers.
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

Here, we can use `version` in the default namespace.

```
<?xml version="1.0"?>
<sparql xmlns="http://www.w3.org/2005/sparql-results#"
  xmlns:its="http://www.w3.org/2005/11/its" 
  its:version="2.0"
  version="1.2">
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

### SPARQL

The `VERSION` (and `@version`) directive could be used in SPARQL queries as well.


