# Introduction

The purpose of this document is to introduce well-defined terminology to talk about different syntactic properties that an RDF graph with triple terms may possess. These properties are independent of one another and capture different types of restrictions that the WG may combine into a notion of well-formedness, either by including all of these properties or only a selected subset.

# Preliminaries

The definitions in this document consider a version of the abstract data model of RDF that is extended with triple terms in the following way.

**Definition:** An **RDF graph** is a set of RDF triples.

**Definition:** An **RDF triple** (usually called "triple") is a 3-tuple that is defined recursively as follows:

* If *s* is an IRI or a blank node, *p* is an IRI, and *o* is an IRI, a blank node, or a literal, then (*s*, *p*, *o*) is an RDF triple.
* If *s* is an IRI or a blank node, *p* is an IRI, and *o* is an RDF triple, then (*s*, *p*, *o*) is an RDF triple.
* If *s* is an RDF triple, *p* is an IRI, and *o* is an IRI, a blank node, or a literal, then (*s*, *p*, *o*) is an RDF triple.
* If *s* is an RDF triple, *p* is an IRI, and *o* is an RDF triple, then (*s*, *p*, *o*) is an RDF triple.

**Definition:** Let *t* be an RDF triple (*s*, *p*, *o*). If *s* or *o* is an RDF triple *t'*, then we say that *t'* is a **triple term** in *t*.


# Triple-Term-Subject Well-Formedness

The first restriction focuses on where an RDF triple may be used as a triple term within another RDF triple. Informally, the restriction is that RDF triples may only be used as objects of other triples. RDF graphs whose triples abide by this restriction are said to be *triple-term-subject well-formed*. Notice that this notion of well-formedness may be only one of several conditions combined into the notion of well-formedness that the WG is going to define. Alternatively, it may also be an option to integrate the restriction captured by triple-term-subject well-formedness directly into the definition of RDF triples (i.e., changing this definition by removing the option for a triple in the subject position altogether).

The following definitions capture the notion of triple-term-subject well-formedness formally (first within the context of individual RDF triples, and thereafter for whole RDF graphs).

**Definition:** An RDF triple (*s*, *p*, *o*) is **triple-term-subject well-formed** if it has both of the following two properties:

* *s* is a not an RDF triple
* either *o* is not an RDF triple or *o* is an RDF triple that is itself triple-term-subject well-formed

**Definition:** An RDF Graph *G* is **triple-term-subject well-formed** if every triple in *G* is triple-term-subject well-formed.


# Triple-Term-Object Well-Formedness

Another possible restriction regarding the usage of triples as triple terms is that a triple may be used as an object of another triple only in combination with the IRI `rdf:reifies` as predicate. RDF graphs that abide by this restriction are said to be *triple-term-object well-formed* and, again, this notion of well-formedness may be only one of several conditions combined into the notion of well-formedness that the WG is going to define.

Formally, triple-term-object well-formedness is defined recursively as follows (again, first for individual triple terms and individual triples, and thereafter for whole graphs).

**Definition:** An RDF triple (*s*, *p*, *o*) is **triple-term-object well-formed** if it has either of the following two properties:

* *o* is a not an RDF triple
* *o* is an RDF triple that is triple-term-object well-formed and *p* is the IRI `rdf:reifies`

**Definition:** An RDF Graph *G* is **triple-term-object well-formed** if every triple in *G* is triple-term-object well-formed.


# Reifies-Predicate Well-Formedness

Notice that both, triple-term-subject well-formedness and triple-term-object well-formedness, focus solely on triple terms and do not restrict the usage of the `rdf:reifies` as an arbitrary predicate. The restriction in this section can be seen as a counterpart to these two notions of well-formedness; that is, it does not restrict the usage of triple terms but, instead, it restricts the usage of `rdf:reifies` as a predicate.

Informally, the restriction is that `rdf:reifies` may be used as a predicate only in triples in which the object is a triple and the subject is not. Formally, this restriction is defined recursively as follows:

**Definition:** An RDF triple (*s*, *p*, *o*) is **reifies-predicate well-formed** if it has either of the following two properties:

* *p* is a not the IRI `rdf:reifies`
* *p* is the IRI `rdf:reifies`, *s* is a not an RDF triple, and *o* is an RDF triple that is reifies-predicate well-formed

**Definition:** An RDF Graph *G* is **reifies-predicate well-formed** if every triple in *G* is reifies-predicate well-formed.


# Reifier Minimality

The previous three notions of well-formedness focus on how triple terms and `rdf:reifies` predicates can be used in RDF graphs, respectively. They do not disallow RDF graphs that contain a triple of the form

(*s*, `rdf:reifies`, (*s'*, *p'*, *o'*) )

without any other triple that talks about *s*. I believe that some members of the WG consider a notion of well-formedness in which such graphs are indeed disallowed. As a means to talk about this particular aspect of well-formedness, I introduce the notion of *reifier minimality* as another (syntactic) property of RDF graphs. Informally, an RDF graph is reifier minimal if, for every triple of the aforementioned form, the graph contains another triple that talks about *s*. Formally, this property is defined as follows.

**Definition:** An RDF Graph *G* is **reifier minimal** if, for every triple (*s*, *p*, *o*) in *G*, it holds that, if *p* is the IRI `rdf:reifies` and *o* is a triple, then there is another triple (*s2*, *p2*, *o2*) in *G* such that *p2* is not the IRI `rdf:reifies` and either *s* and *s2*, or *s* and *o2*, are the same RDF term.


# No Multi-Term Reification

The last syntactic property focuses on the question of whether a reifier may be associated with multiple triples. We may refer to cases in which a reifier is indeed associated with multiple triples as *multi-term reification*. The following two triples illustrate such a multi-term reification (assuming (*s*, *p*, *o*) and (*s'*, *p'*, *o'*) are two different triples):

* (*x*, `rdf:reifies`, (*s*, *p*, *o*) )
* (*x*, `rdf:reifies`, (*s'*, *p'*, *o'*) )

Some members of the WG argue for a notion of well-formedness in which RDF graphs are free of multi-term reifications. Formally, this property is defined as follows.

**Definition:** An RDF Graph *G* is **multi-term-reification free** if no pair of triples (*s1*, *p1*, *o1*) and (*s2*, *p2*, *o2*) in *G* has all of the following properties:

* *s1* and *s2* are the same RDF term
* *p1* and *p2* both are the IRI `rdf:reifies`
* *o1* and *o2* are two different triples
