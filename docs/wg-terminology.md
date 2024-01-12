# Working Group Terminology

Clarifications and additions.

### RDF Triple
[RDF Triples are defined in RDF 1.2](https://www.w3.org/TR/rdf12-concepts/#section-triples).

Sometimes informally called: "Triple Type", "Abstract Triple".

### Asserted Triple

A triple is asserted with respect to a graph, when it is an element of a graph.

### RDF Statement

[RDF Statements are defined in RDF 1.2](https://www.w3.org/TR/rdf12-concepts/#dfn-rdf-statement).

(See also [N-quads statement](https://www.w3.org/TR/rdf12-n-quads/#grammar-production-statement).)

The statement is the meaning of the triple.

The triple is the representation of the statement.

### Triple Occurrence

*[TODO: Pending rough draft.]*

Sometimes informally called: "Triple Instance".

An *occurrence* of a triple is a *possible use* of it (presumably *through* but not *as* [tokens](https://plato.stanford.edu/entries/types-tokens/#Occ)), on the level of the abstract syntax.

This is closely related to the (non-normative) definition of a [reified RDF statement](https://www.w3.org/TR/rdf11-mt/#reification):

> The subject of a reification is intended to refer to a concrete realization of an RDF triple, such as a document in a surface syntax, rather than a triple considered as an abstract object. This supports use cases where properties such as dates of composition or provenance information are applied to the reified triple, which are meaningful only when thought of as referring to a particular instance or token of a triple.

Also [from RDF 1.2 Semantics](https://www.w3.org/TR/rdf12-semantics/#Reif):

> The reification only says that the triple token exists and what it is about, not that it is true, so it does not entail the triple.

