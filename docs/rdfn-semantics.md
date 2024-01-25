# Enrico's semantics for RDFn

This is a possible semantics for the [abstract syntax proposed by Souri].
It is *not* a new proposal:
* the definition of interpretation is exactly the same as the one [proposed by Enrico here],
* the semantics constraints are slightly modified to be adapted to the new abstract syntax.
The realization that this semamtics could be used for Souri's abstract syntax came during a Semantics Task Force call in december.

[abstract syntax proposed by Souri]: https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0002.html
[proposed by Enrico here]: https://github.com/w3c/rdf-star-wg/wiki/Semantics%3A-Andy%27s-proposal#semantics

## SEMANTICS

**this part is a copy-paste of Enrico's proposal**

An RDF-star simple interpretation `I` is a structure <<code>IR</code>, <code>IP</code>, <code>IS</code>, <code>IL</code>, <code>IEXT</code>, <code>IT</code>, <code>IO</code>> consisting of:
1. A non-empty set <code>IR</code> of _resources_, called the domain or universe of <code>I</code>.
2. A set <code>IP</code>, called the set of _properties_ of <code>I</code>.
3. A mapping <code>IS</code> from IRIs into <code>IR ⋃ IP</code>, called the _interpretation_ of IRIs.
4. A partial mapping <code>IL</code> from _literal_ into <code>IR</code>, called the _interpretation_ of literals.</br>
5. A mapping <code>IEXT</code> from <code>IP</code> into <code>2<sup>IR x IR</sup></code>, called the _extension_ of properties. </br>
6. A binary relation <code>IO</code> over <code>IR x (IR x IP x IR)</code>, called the _occurrence of a triple term_.

<code>A</code> is a mapping from _BlankNode_ to <code>IR</code>.

<p>

Given `I` and `A`, the function <code>\[I+A\](.)</code> is defined over _terms_, _triples_, and _graphs_ as follows.

* <code>\[I+A\](r) = IL(r)</code>    if `r` is a _literal_ <br/>
* <code>\[I+A\](r) = IS(r)</code>    if `r` is a _iri_ <br/>
* <code>\[I+A\](r) = A<sub></sub>(r)</code>     if `r` is a _BlankNode_ <br/>

<p>

**this is the only difference with Enrico's initial proposal**

* `[I+A](t)`, if t is a triple `s p o`,
  is TRUE if `( [I+A](s), [I+A](o) ) ∈ IEXT( [I+A](p) )`,
  FALSE otherwise
* `[I+A](e)`, if e is an edge `s p o n`,
  is TRUE if `( [I+A](n), ( [I+A](s), [I+A](p), [I+A](o) )) ∈ IO`,
  FALSE otherwise
* `[I+A](g)]`, if g is a graph,
  is FALSE if there is a triple or an edge i ∈ g such that `[I+A](i) = FALSE`,
  TRUE otherwise
