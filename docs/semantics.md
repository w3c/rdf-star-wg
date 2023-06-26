# RDF-star Semantics Options

There have been several different proposals of semantics for quoted triples, some with multiple semantics.  The proposed semantics differ in technical details as well as in the entailments they support.  The major differences between the proposed semantics can be summarized in whether they are sensitive to the precise syntax of quoted triples or only to the semantics of the components of quoted triples.   The first kind of semantics have been called "opaque" and the second kind "transparent".  There is also a "semi-opaque" (or "semi-transparent") semantics, as found in the Community Group report, where the syntax of both IRIs and literals is important, but only the meaning (and not the syntax) of blank nodes is.

The differences between the semantics can be seen in the following examples.

Note that, although we use the Turtle syntax for convenience, we interpret it in a subtly different way: each blank node label is interpreted as a specific and fixed element of the set of blank nodes defined by the RDF abstract syntax.

In RDF (extended to include `owl:sameAs` as equality to provide a way of requiring that two different IRIs mean the same thing) —
```
:b                            owl:sameAs :c .
_:a                           :b         "4"^^xsd:int .
```
— entails —
```
_:f                           :b         "4"^^xsd:int .
```
— (because any blank node can be replaced by any other blank node without changing their meanings), and —
```
_:a                           :b         "4"^^xsd:integer .
```
— (because `xsd:int` and `xsd:integer` mean the same datatype, according to the RDF specification), and —
```
_:a                           :c         "4"^^xsd:int .
```
— (because `:b`and `:c` mean the same thing, according to the `owl:sameAs` declaration above).


## Transparent Semantics

In the _transparent_ semantics —
```
:b                            owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
— entails —
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
— and —
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
— and —
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

## Opaque Semantics

In the _opaque_ semantics —
```
:b owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
— does not entail —
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
— nor —
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
— nor —
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

## Semi-transparent Semantics

In the _semi-transparent_ (also known as _semi-opaque_) semantics, as proposed in [the RDF-star final community group report](https://www.w3.org/2021/12/rdf-star.html) —
```
:b owl:sameAs :c .
<< _:a :b "4"^^xsd:int     >> :d         :e .
```
— does entail —
```
<< _:f :b "4"^^xsd:int     >> :d         :e .
```
— but does not entail —
```
<< _:a :b "4"^^xsd:integer >> :d         :e .
```
— nor —
```
<< _:a :c "4"^^xsd:int     >> :d         :e .
```

In this semantics, the IRI `:b` inside the quoted triple denotes (i.e., refers to, or means) something in the real world just like it does in "standard", un-quoted RDF triples. According to the `owl:sameAs` statement, that meaning can also be referred to by `:c`. However, this semantics aims to faithfully record that the IRI `:b` was used to refer to that meaning, and therefore doesn't support entailments that, for instance, entail the usage of `:c` from the usage of of `:b`, although they would mean the same thing.

One could also create other semi-opaque semantics where either IRIs or literals were transparent.

