# RDF-star Semantics Options

There have been several different semantics proposals for quoted triples, some with multiple semantics.  The proposed semantics differ in technical details but also differ in the entailments they support.  The major differences between semantics can be summarized in whether they are sensitive to the precise syntax of quoted triples or only to the semantics of the components of quoted triples.   The first kind of semantics have been called opaque and the second kind transparent.  There is also a semi-opaque (or semi-transparent) semantics, as in the community report, where the syntax of IRIs and literals are important but only the meaning of blank nodes is.

The difference between the semantics can be seen in the following examples.

Note that, although we use the Turtle syntax for convenience, we interpret it in a subtly different way: each blank node label is interpreted as a specific and fixed element of the set of blank nodes defined by the RDF abstract syntax.

In RDF (extended to include `owl:sameAs` as equality to provide a way of requiring that two different IRIs mean the same thing)
```
:b                            owl:sameAs :c .
_:a                           :b         "4"^^xsd:int .
```
entails
```
_:f                           :b         "4"^^xsd:int .
```
(because blank nodes can be replaced by blank nodes without changing their meaning)

and 
```
_:a                           :b         "4"^^xsd:integer .
```
(because `xsd:int` and `xsd:integer` mean the same datatype as per the RDF specification)

and 
```
_:a                           :c         "4"^^xsd:int .
```
(because `:b`and `:c` mean the same thing as per the `owl:sameAs` declaration above).


## Transparent Semantics

In the _transparent_ semantics
```
:b                            owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
entails
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
and 
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
and
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

## Opaque Semantics

In the _opaque_ semantics
```
:b owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
does not entail
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
or 
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
or
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

## Semi-transparent Semantics

In the _semi-transparent_ (also known as _semi-opaque_) semantics, as proposed in [the RDF-star final community group report](https://www.w3.org/2021/12/rdf-star.html)
```
:b owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
does entail
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
but does not entail
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
or
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

In this semantics, the IRI `:b` inside the quoted triple denotes (i.e., refers to, or means) something in the real world just like it does in "standard", un-quoted RDF triples. According to the `owl:sameAs` statement, that meaning can also be referred to by `:c`. However, this semantics aims to faithfully record that the IRI `:b` was used to refer to that meaning, and therefore doesn't support entailments that, for instance, entail the usage of `:c` from the usage of of `:b`, although they would mean the same thing.

One could also create other semi-opaque semantics where either IRIs or literals were transparent.

