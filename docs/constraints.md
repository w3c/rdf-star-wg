# Introduction

The purpose of this document is to introduce well-defined terminology to talk about different syntactic properties that an RDF graph with triple terms may possess. These properties are independent of one another and capture different types of restrictions that the WG may combine into a notion of well-formedness, either by including all of these properties or only a selected subset.

# Preliminaries

The definitions in this document consider a version of the abstract data model of RDF that is extended with triple terms in the following way.

**Definition:** An **RDF graph** is a set of RDF triples.

**Definition:** An **RDF triple** (usually called "triple") is a 3-tuple (*s*, *p*, *o*) where:

* *s* is an IRI, a blank node, or a triple term,
* *p* is an IRI, and
* *o* is an IRI, a blank node, a literal, or a triple term.

**Definition:** An **triple term** is a 3-tuple that is defined recursively as follows:

* If *s* is an IRI or a blank node, *p* is an IRI, and *o* is an IRI, a blank node, or a literal, then (*s*, *p*, *o*) is a triple term.
* If *s* is an IRI or a blank node, *p* is an IRI, and *o* is a triple term, then (*s*, *p*, *o*) is a triple term.
* If *s* is a triple term, *p* is an IRI, and *o* is an IRI, a blank node, or a literal, then (*s*, *p*, *o*) is a triple term.
* If *s* is a triple term, *p* is an IRI, and *o* is a triple term, then (*s*, *p*, *o*) is a triple term.

**Note:** While, syntactically, the notion of an RDF triple and the notion of a triple term are the same, they represent different concepts. RDF triples are the members of RDF graphs, whereas triple terms can be used as components of RDF triples.


# Triple-Term-Placement Well-Formedness

The first restriction focuses on where triple terms may be used within an RDF graph and, by extension, within one another. Informally, by this restriction, triple terms may only be used as objects, in combination with the IRI `rdf:reifies` as predicate. RDF graphs that abide by this restriction are said to be *triple-term-placement well-formed*. Notice that this notion of well-formedness may be only one of several conditions combined into the notion of well-formedness that the WG is going to define.

The following definitions capture the notion of triple-term-placement well-formedness formally (first within the context of individual triple terms and individual RDF triples, and thereafter for whole RDF graphs).

**Definition:** A triple term (*s*, *p*, *o*) is **triple-term-placement well-formed** if it has one of the following two properties:

1. *s* is a not a triple term, and neither is *o*,
2. *s* is a not a triple term, *p* is the IRI `rdf:reifies`, and *o* is a triple term that is triple-term-placement well-formed.

**Definition:** An RDF triple (*s*, *p*, *o*) is **triple-term-placement well-formed** if it has one of the following two properties:

1. *s* is a not a triple term, and neither is *o*,
2. *s* is a not a triple term, *p* is the IRI `rdf:reifies`, and *o* is a triple term that is triple-term-placement well-formed.

**Definition:** An RDF Graph *G* is **triple-term-placement well-formed** if every triple in *G* is triple-term-placement well-formed.


# Reifies-Predicate Well-Formedness

Notice that the notion of triple-term-placement well-formedness focuses solely on triple terms and does not restrict the usage of the `rdf:reifies` as an arbitrary predicate. The restriction in this section can be seen as a counterpart to triple-term-placement well-formedness; that is, it does not restrict the usage of triple terms but, instead, it restricts the usage of `rdf:reifies` as a predicate.

Informally, the restriction is that `rdf:reifies` may be used as a predicate only in triples in which the object is a triple term and the subject is not. Formally, this restriction is defined recursively as follows.

**Definition:** A triple term (*s*, *p*, *o*) is **reifies-predicate well-formed** if it has one of the following two properties:

1. *p* is a not the IRI `rdf:reifies`,
2. *p* is the IRI `rdf:reifies`, *s* is a not a triple term, and *o* is a triple term that is reifies-predicate well-formed.

**Definition:** An RDF triple (*s*, *p*, *o*) is **reifies-predicate well-formed** if it has one of the following two properties:

1. *p* is a not the IRI `rdf:reifies`,
2. *p* is the IRI `rdf:reifies`, *s* is a not a triple term, and *o* is a triple term that is reifies-predicate well-formed.

**Definition:** An RDF Graph *G* is **reifies-predicate well-formed** if every triple in *G* is reifies-predicate well-formed.


# Reifier Minimality

The previous two notions of well-formedness focus on how triple terms and `rdf:reifies` predicates can be used in RDF graphs, respectively. They do not disallow RDF graphs that contain a triple of the form

(*s*, `rdf:reifies`, (*s'*, *p'*, *o'*) )

without any other triple that talks about *s*. I believe that some members of the WG consider a notion of well-formedness in which such graphs are indeed disallowed. As a means to talk about this particular aspect of well-formedness, I introduce the notion of *reifier minimality* as another (syntactic) property of RDF graphs. Informally, an RDF graph is reifier minimal if, for every triple of the aforementioned form, the graph contains another triple that talks about *s*. Formally, this property is defined as follows.

**Definition:** An RDF Graph *G* is **reifier minimal** if, for every triple (*s*, *p*, *o*) in *G*, it holds that, if *p* is the IRI `rdf:reifies` and *o* is a triple term, then there is another triple (*s2*, *p2*, *o2*) in *G* such that *p2* is not the IRI `rdf:reifies` and either *s* and *s2* are the same RDF term or *s* and *o2* are the same RDF term.


# No Multi-Term Reification

The last syntactic property focuses on the question whether a reifier may be associated with multiple triple terms. We may refer to cases in which a reifier is indeed associated with multiple triple terms as *multi-term reification*. The following two triples illustrate such a multi-term reification (assuming (*s*, *p*, *o*) and (*s'*, *p'*, *o'*) are two different triple terms).

* (*x*, `rdf:reifies`, (*s*, *p*, *o*) )
* (*x*, `rdf:reifies`, (*s'*, *p'*, *o'*) )

Some members of the WG argue for a notion of well-formedness in which RDF graphs are free of multi-term reifications. Formally, this property is defined as follows.

**Definition:** An RDF Graph *G* is **multi-term-reification free** if no pair of triples (*s1*, *p1*, *o1*) and (*s2*, *p2*, *o2*) in *G* has all of the following properties.

1. *s1* and *s2* are the same RDF term
2. *p1* and *p2* both are the IRI `rdf:reifies`
3. *o1* and *o2* are two different triple terms
