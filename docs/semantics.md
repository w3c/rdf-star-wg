# RDF-star Semantics Options

There have been several different semantics proposals for quoted triples, some with multiple semantics.  The proposed semantics differ in technical details but also differ in the entailments they support.  The major differences between semantics can be summarized in whether they are sensitive to the precise syntax of quoted triples or only to the semantics of the components of quoted triples.   The first kind of semantics have been called opaque and the second kind transparent.  There is also a semi-opaque (or semi-transparent) semantics, as in the community report, where the syntax of IRIs and literals are important but only the meaning of blank nodes is.

The difference between the semantics can be seen in the following examples.

Note that, although we use the Turtle syntax for convenience, we interpret it in a subtly different way: each blank node label is interpreted as a specific and fixed element of the set of blank nodes defined by the RDF abstract syntax.

In RDF (extended to include `owl:sameAs` as equality to provide a way of requiring that two different IRIs mean the same thing)
```
_:a                           :b         "4"^^xsd:int .
:b                            owl:sameAs :c .
```
entails
```
_:f                           :b         "4"^^xsd:int .
```
and 
```
_:a                           :b         "4"^^xsd:integer .
```
and 
```
_:a                           :c         "4"^^xsd:integer .
```


## Transparent Semantics

In the _transparent_ semantics
```
<< _:a :b "4"^^xsd:int     >> :d         :e .
:b                            owl:sameAs :c .
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
<< _:a :b "4"^^xsd:int     >> :d         :e .
:b                            owl:sameAs :c .
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
<< _:a :b "4"^^xsd:int     >> :d         :e .
:b                            owl:sameAs :c .
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

One could also create other semi-opaque semantics where either IRIs or literals were transparent.

