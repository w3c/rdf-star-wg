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
  
- 1. sugar
- 2. sugar+
- 3. triple-terms / descriptors
- 4. edge statements / RDFn

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
  :e rdf:subject   :s .
  :e rdf:predicate :p .
  :e rdf:object    :o .
  ```
- ```
  :e rdf:nameOf [
    rdf:subject   :s ;
    rdf:predicate :p ;
    rdf:object    :o
  ]
  ```
- ```
  :e rdf:nameOf <<( :s :p :o )>> .
  ```
- ```
  :e | :s :p :o .
  ```


- ********************
  `SELECT (COUNT(*) AS ?c)`<br />`WHERE { ?s ?p ?o }`<br/>over<br/>`<<:e | :s :p :o>>`<br />`  :pp :oo .`<br/>results in
  ********************

- 4 (or 5 if `:e rdf:type rdf:Statement` is in the result of the expansion as well)
- 5 (or 6 if `:e rdf:type rdf:Statement` is in the result of the expansion as well)
- 2
- 1?


- ********************
  `SELECT *`<br />`WHERE { ?s ?p ?o }`<br/>over<br/>`<<:e | :s :p :o>>`<br />`  :pp :oo .`<br/>results in
  ********************

- ```
  :e rdf:subject   :s .
  :e rdf:predicate :p .
  :e rdf:object    :o .
  :e :pp           :oo .
  ## possibly also
  ## :e       rdf:type       rdf:Statement .
  ```
- ```
  :e        rdf:nameOf    _:blank1
  _:blank1  rdf:subject   :s .
  _:blank1  rdf:predicate :p .
  _:blank1  rdf:object    :o .
  :e        :pp           :oo .
  ## possibly also
  ## :e        rdf:type       rdf:Statement .
  ```
- `:e :pp :oo .`<br />`:e rdf:nameOf <<( :s :p :o )>> .`
- `:e :pp :oo .`


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
- add new kind of term (`TRIPLE-TERM`):
  ```
  graph       := triple*
  triple      := subject predicate object
  subject     := iri | bnode
  predicate   := iri
  object      := iri | bnode | literal | TRIPLE-TERM
  TRIPLE-TERM := triple
  ```
- add new kind of statement (`EDGE`) in graph:
  ```
  graph     := ( triple | EDGE )*
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
  
  - `IR`: set of resources  
  - `IP`: set of properties  
  - `IS`: maps IRIs to `IR U IP`  
  - `IL`: maps literals to `IR`  
  - `IEXT`: maps properties to subsets of `IRxIR`  

- unchanged:  

  - `IR`, `IP`, `IS`, `IL`, `IEXT` as before

- add set for triple terms:
  
  - `IR`, `IP`, `IS`, `IL`, `IEXT` as before  

  - `IA`: set of reification atoms, `⊆ IRxIPxIR`

- add map for edges
  
  - `IR`, `IP`, `IS`, `IL`, `IEXT` as before  

  - `IO`: binary relation over `IRx(IRxIPxIR)`



- ********************
  Satisfaction
  ********************

- unchanged:

  - `[I+A](iri) = IS(iri)`
  - `[I+A](lit) = IL(lit)`
  - `[I+A](bnode) = A(bnode)`

  - `[I+A](triple)` is true iff
    `([I+A](subj), [I+A](obj))` in `IEXT([I+A](pred))`

  - `[I+A](graph)` is true unless
    `[I+A](x)` is false for some triple `x` in graph

- unchanged:

  - `[I+A]` as before for terms, triples, and graphs

- add interpretation for triple-terms
  
  - `[I+A]` as before for IRIs, literals, bnodes, triples, and graphs  

  - `[I+A](triple-term) = ([I+A](subj), [I+A](pred), [I+A](obj))` in `IA`

- add semantic condition for edges

  - `[I+A]` as before for terms and triples

  - `[I+A](edge)` is true iff
    `([I+A](name), ([I+A](subj), [I+A](pred), [I+A](obj)))` in `IO`

  - `[I+A](graph)` is true unless
    `[I+A](x)` is false for some triple _OR_ EDGE `x` in graph



- ********************
  [Supports one name for several triples?](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0175.html)
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

  <abbr title="Antoine Zimmermann">AZ</abbr>'s proposal of a semantics for the CG's RDF-star
  could also be adopted as is (the abstract syntax is essentially the same).
  See below, rows 'Interpretation AZ' and 'Satisfaction AZ'.

- https://www.emse.fr/~zimmermann/W3C/RDF-star-semantics/

  <abbr title="Antoine Zimermann">AZ</abbr>'s proposal of a semantics for the CG's RDF-star
  could easily be adapted to this abstract syntax.
  See below, rows 'Interpretation AZ' and 'Satisfaction AZ'.

- ********************
  Interpretation variant [AZ](https://www.emse.fr/~zimmermann/W3C/RDF-star-semantics/)
  ********************

- 
- 
- add map and properties for triple terms:  

  - `IR`, `IP`, `IS`, `IL`, `IEXT` as before

  - `IT`: maps ground triple-terms to `IR`  
  - `Ps`, `Pp`, `Po` are elements of `IP`

- add properties for edges  

  - `IR`, `IP`, `IS`, `IL`, `IEXT` as before

  - `Pe`, `Ps`, `Pp`, `Po` are elements of `IP`


- ********************
  Satisfaction variant [AZ](https://www.emse.fr/~zimmermann/W3C/RDF-star-semantics/)
  ********************

-
- 
- add interpretation and semantic condition for triple-terms

  - `[I+A]` as before for IRIs, literals, bnodes, triples, and graphs

  - `A` is defined for blank nodes _AND_ NON-GROUND TRIPLE-TERMS

  - `[I+A](triple-term) = | IT(triple-term)` if ground  
    `                     | A(triple-term)`  otherwise

  - `([I+A](triple-term), [I+A](subj)) ∈ IEXT(Ps)`  
  - `([I+A](triple-term), [I+A](pred)) ∈ IEXT(Pp)`  
  - `([I+A](triple-term), [I+A](obj))  ∈ IEXT(Po)`

- add semantic condition for edges

  - `[I+A]` as before for terms and triples

  - `[I+A](edge)` is true iff `∃t ∈ IR` such that  
    - `([I+A](name), t) ∈ IEXT(Pe)`  
    - `(t, [I+A](subj)) ∈ IEXT(Ps)`  
    - `(t, [I+A](pred)) ∈ IEXT(Pp)`  
    - `(t, [I+A](obj))  ∈ IEXT(Po)`

  - `[I+A](graph)` is true unless  
    - `[I+A](x)` is false for some triple _OR_ EDGE `x` in graph

</div>

Source: https://github.com/w3c/rdf-star-wg/blob/main/docs/seeking-consensus-2024-01.md
