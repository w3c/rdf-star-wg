# Seeking consensus

<div class=warning>

This document is written in markdown for easy editing and quick preview in GitHub,
but a [better rendering](https://htmlpreview.github.io/?https://github.com/w3c/rdf-star-wg/blob/main/docs/seeking-consensus-2024-01.html)
(as a table) is available.
It is generated with the following command line:
```bash
pandoc seeking-consensus-2024-01.md -f gfm -o seeking-consensus-2024-01.html -s -c seeking-consensus-2024-01.css
```
</div>

<div class=grid-list>

- ********************
  Approaches
  ********************
  
- sugar
- sugar+
- triple-terms / descriptors
- edge statements / RDFn

- ********************
  Source
  ********************
  
- https://github.com/w3c/rdf-star-wg/blob/main/docs/sugar-proposal.md
- https://github.com/w3c/rdf-star-wg/blob/main/docs/sugar-proposal.md#syntax
- https://github.com/afs/rdf-star-notes/blob/main/reif-atoms.md
- https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0002.html (syntax)

  https://github.com/w3c/rdf-star-wg/blob/main/docs/rdfn-semantics.md (semantics)


- ********************
  `<<:e | :s :p :o>>` expands to
  ********************

- ```
  :e rdf:subject :s .
  :e rdf:predicate:p .
  :e rdf:object :o .
  ```
- ```
  :e rdf:nameOf [
    rdf:subject :s ;
    rdf:subject :p ;
    rdf:subject :o
  ]
  ```
- ```
  :e rdf:nameOf <<(:s :p :o)>> .
  ```
- ```
  :e | :s :p :o .
  ```


- ********************
  `SELECT (COUNT(*) AS ?c) { ?s ?p ?o }`<br/>over `<<:e | :s :p :o>> :pp :oo .`<br/>results in
  ********************

- 4 (or 5 if `:e rdf:type rdf:Statement` is in the result of the expansion as well)
- 5 (or 6 if `:e rdf:type rdf:Statement` is in the result of the expansion as well)
- 2
- 1?


- ********************
  Abstract syntax
  ********************

- unchanged:
  ```
  graph     := triple*
  triple    := subject predicate object
  subject   := iri | bnode
  predicate := iri
  object    := iri | bnode | literal
  ```
- unchanged:
  ```
  graph     := triple*
  triple    := subject predicate object
  subject   := iri | bnode
  predicate := iri
  object    := iri | bnode | literal
  ```
- add new kind of term:
  ```
  graph       := triple*
  triple      := subject predicate object
  subject     := iri | bnode | TRIPLE-TERM
  predicate   := iri
  object      := iri | bnode | literal
                 | TRIPLE-TERM
  TRIPLE-TERM := triple
  ```
- add new kind of statement in graph
  ```
  graph     := (triple | EDGE)*
  triple    := subject predicate object
  subject   := iri | bnode
  predicate := iri
  object    := iri | bnode | literal
  EDGE      := NAME subject predicate object
  NAME      := iri | bnode
  ```


- ********************
  Interpretation
  ********************

- unchanged:
  ```
  IR: set of resources
  IP: set of properties
  IS: maps IRIs to IR U IP
  IL: maps literals to IR
  IEXT: maps properties to subsets of IRxIR  
  ```
- unchanged:
  ```
  IR, IP, IS, IL, IEXT as before
  ```
- add map for triple terms:
  ```
  IR, IP, IS, IL, IEXT as before

  IA: maps triple terms to IRxIPxIR
  ```
- add map for edges
  ```
  IR, IP, IS, IL, IEXT as before

  IO: binary relation over IRx(IRxIPxIR)
  ```


- ********************
  Satisfaction
  ********************

- unchanged:
  ```
  [I+A](iri) = IS(iri)
  [I+A](lit) = IL(lit)
  [I+A](bnode) = A(bnode)

  [I+A](triple) is true iff
    ([I+A](subj), [I+A](obj)) in IEXT([I+A](pred))

  [I+A](graph) is true unless
    [I+A](x) is false for some triple x in graph
  ```
- unchanged:
  ```
  [I+A] as before for terms, triples and graphs
  ```
- add interpretation for triple-terms
  ```
  [I+A] as before for IRIs, literals, bnodes, triples and graphs

  [I+A](triple-term) = ([I+A](subj), [I+A](pred), [I+A](obj))
  ```
- add semantic condition for edges
  ```
  [I+A] as before for terms and triples

  [I+A](edge) is true iff
    ([I+A](name), ([I+A](subj), [I+A](pred), [I+A](obj))) in IO

  [I+A](graph) is true unless
    [I+A](x) is false for some triple x in graph
    OR some edge x in graph
  ```


- ********************
  Supports one name for several triples?
  ********************
  
- no (mixes the subjects, predicates and objects)
- yes
- yes
- yes


- ********************
  Atomicity
  ********************
  
- weak (via well-formed-ness)
- weak (via well-formed-ness)
- strong (enforced by abstract syntax)
- strong (enforced by abstract syntax)


- ********************
  Variant
  ********************

- see sugar+
- https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0141.html

  Enrico's proposal includes a stronger version of well-formed-ness
- https://www.emse.fr/~zimmermann/W3C/RDF-star-semantics/

  Antoine's proposal of a semantics for the CG's RDF-star
  could also be adopted.
	(no change to the abstract syntax)
- 

</div>
