# A draft proposal for satisfying the main requirement of the working group

This is a proposal to satisfy the main requirement of the working group by **adding syntactic shorthands to concrete syntaxes for RDF** and **making no changes to either RDF graphs or the core semantics of RDF**. The proposal moves away from the "quoted triples" of the CG report to the idea of named occurrences of triples.  It started out as https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0095.html 

## Named occurrences of triples

The proposal is to add a new syntactic construct to Turtle and also other complex syntaxes for RDF (not including N-Triples, for example) for named occurrences of triples.

A named occurrence of a triple can appear as the subject or object of a triple as shown here *(details of the syntax not important for now)*:

    << :e | :s :p :o >> :a :b .

and its variants:

    << :s :p :o >> :a :b . # syntactic sugar for << [] | :s :p :o >> :a :b .
    :s :p :o {| :e | :a :b |}.  # syntactic sugar for :s :p :o. << :e | :s :p :o >> :a :b .
    :s :p :o {| :a :b |}.  # syntactic sugar for :s :p :o  {| [] | :a :b |}.

## Named occurrences of triples as RDF reification

This syntax is simply a shorthand for RDF reification (or some variation thereof). In other words,

    << :e | :s :p :o >> :a :b .

would be syntactic sugar for the following triples (or some variation of them)

    :e a rdf:Statement .
    :e rdf:subject :s .
    :e rdf:predicate :p .
    :e rdf:object :o .
    :e :a :b .

and the abstract syntax would not need to be extended.

### Adding an edge between the name and the reification.

A significant option (see the second syntax option below) would instead result in something like:

    :e rdf:nameOf [rdf:subject :s; rdf:predicate :p ; rdf:object :o ] .
    :e :a :b .

## Criticisms and responses

This proposal has to handle the usual criticisms raised against standard reification :
1. It is verbose
2. It can be inefficient
3. It can be messy (nodes with missing or duplicate `rdf:subject` / `rdf:predicate` / `rdf:object`)

Issue 1 is alleviated by the double-pointy bracket syntactic sugar in Turtle, TrIg and SPARQL. This can be extended to other concrete syntaxes (in fact RDF/XML already has some syntactic sugar for reification, and the efforts on JSON-LD-star (https://json-ld.github.io/json-ld-star/) can also be leveraged).

Issues 2 and 3 can be alleviated by introducing a notion of "well-formed" RDF (again, a better term can maybe be found). 

**Definition: An RDF graph is reification well-formed iff any node in it that is the subject of a triple in the graph with predicate `rdf:subject`, `rdf:predicate`, or `rdf:object` must be the subject of exactly one triple in the graph with each of these predicates.**

Systems would not be required to detect or reject ill-formed RDF, but they would not be required to accept it either. In other words, ill-formed RDF is not forbidden nor semantically inconsistent, but it has no guarantee in terms of interoperability. Existing systems (which accept ill-formed RDF) would then still be compliant, but new systems could reject ill-formed RDF and use this assumption to optimize storage for reifications.  Well-formedness is a syntactic notion.

For document collections that don't use the reification vocabulary explicitly well-formedness can be guaranteed if no name is used more than once in a double-pointy bracket construct.  (If the optional expansion is chosen then this second condition is not needed.)

Note that the notion of well-formedness already exists in RDF for Semantic Extensions (they are called "syntactic conditions or restrictions" in https://www.w3.org/TR/rdf11-semantics/#dfn-semantic-extension). 

## Options

Here are some options but the core proposal is just the above.  The only option that has dramatic consquences is the second syntax option.

### Syntax:

* Should there be the ability to have independent creation of named occurences of triples, i.e., allow `<< :e | :a :b :c >>` by itself in concrete syntaxes?
* Should the expansion be instead to something like `:e rdf:isNameOf <<< :s :p :o >>> .` and `<<< :s :p :o >>>` itself expand to something like `[ rdf:subject :s; rdf:predicate :p; rdf:object :o ]`?  (Instead of a fresh blank node the node might be determined by :s, :p, and :o.)  This option has decided benefits because it separates the reification from the name but at the expense of adding an extra triple.

### Vocabulary:

1. Use the RDF reification vocabulary (`rdf:Statement`, `rdf:subject`, `rdf:predicate`, `rdf:object`) in toto and as it is used for RDF reification.
2. As above but don't use an `rdf:Statement` triple.
3. Use a completely new vocabulary, unrelated to the RDF reification vocabulary.
4. Use specializations of (some of) the RDF reification vocabulary.

### Well-formedness:

* Implementations may/mustnot reject non-well-formed RDF graphs.

### Semantics:

* Modify the section on RDF reification to make the intended meaning of RDF reification more specific to named triples vs keeping it roughly the same.
* Potentially include intended meaning of any specializations of the RDF reification vocabulary or even actual implications of some specializations.


## Necessary Aspects of the Proposal

### Concrete Syntaxes:

* New concrete syntax is *just* shorthand, and expands to (something like) RDF reification
* There is no intended meaning of the new concrete syntax beyond what it expands to.

### Concepts:

* No change whatsoever to the normative definitions of RDF graphs, triples, etc.
    * So no notion of "edge" or "triple occurrence" or "named triple" in the normative description of RDF graphs.

### Semantics:

* No changes to the normative parts of the current RDF semantics.



## Implications of the Proposal

### SPARQL:

* No changes to how SPARQL works, so there are five (or four) results from `SELECT ?p WHERE { ?s ?p ?o . }` when querying `<< :a :b :c >> :d :e .`

### RDF Graph Operations:

* An RDF graph data structure, even if internally optimized, can be written in N-Triples with no extra space and in (quasi-)linear time on the size of the resulting N-triples file.
* Creating an optimized RDF graph data structure may require some extra storage.
* Maintaining an optimized RDF graph data structure requires checking whether changed nodes are complete reifications.

### Names of Triple Occurrences:

The name of a triple occurrence is important.  Creators of RDF content need to realize this.   The syntax is biased towards anonymous blank nodes as names so this should not be too much of a problem.

### One Name for Multiple Triple Occurrences:

In some earlier proposals it made sense to use the same name in multiple double-pointy bracket constructs because it would then be the name of more than one quoted triple.  This proposal, as presented above, does not allow this possibility.  However, the syntactic variation with `rdf:isNameOf` does support the use of the same name in multiple double-pointy bracket constructs.


