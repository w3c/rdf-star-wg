# RDF-star semantics: option 3
# (DRAFT 2024.02.16) 
<p>
**How to read** <br/>
The following without the blue and red parts is RDF 1.1.<br/>
The following without the red parts is _option 3_.<br/>
The red parts introduce the direct semantics of the `tripleOccurrence` macro. <br/>
The well-formedness condition guarantees the equivalence of `rdf:nameOf` triples with the `tripleOccurrence` macro.

## ABSTRACT SYNTAX:

    graph            ::= (triple)* 
    triple           ::= subject predicate object 
    subject          ::= iri | BlankNode | 
                         tripleTerm | tripleOccurrence 
    predicate        ::= iri 
    object           ::= term 
    term             ::= iri | BlankNode | literal | 
                         tripleTerm | tripleOccurrence 
    tripleTerm       ::= triple
    tripleOccurrence ::= identifier triple
    identifier       ::= iri | BlankNode  

**Note:**<br/>
A _term_ is denoted by `r`, a _triple_ by `t`, and a _graph_ by `g`.<br/>
Given a _tripleOccurrence_ `r`, we denote the identifier of `r` as `r.id`, and the subject, predicate, object of `r` as `r.s`, `r.p`, `r.o`, respectively.<br/>
Given a _triple_ `t`, we denote the subject, predicate, object of `t` as `t.s`, `t.p`, `t.o`, respectively.<br/>
RDF 1.1 syntax is the above without the _tripleTerm_ and _tripleOccurrence_ categories.

**Proposed well-formedness condition** <br/>
A _tripleTerm_ can only appear as an object of a `rdf:nameOf` triple.

## SEMANTICS:

An RDF-star simple interpretation `I` is a structure <<code>IR</code>, <code>IP</code>, <code>IS</code>, <code>IL</code>, <code>IEXT</code>, <code>IT</code><span style="color:blue;">, <code>ID</code></span>> consisting of:
1. A non-empty set <code>IR</code> of _resources_, called the domain or universe of <code>I</code>.
2. A set <code>IP</code>, called the set of _properties_ of <code>I</code>.
3. A mapping <code>IS</code> from IRIs into <code>IR ⋃ IP</code>, called the _interpretation_ of IRIs.
4. A partial mapping <code>IL</code> from _literal_ into <code>IR</code>, called the _interpretation_ of literals.
5. A mapping <code>IEXT</code> from <code>IP</code> into <code>2<sup>IR x IR</sup></code>, called the _extension_ of properties.
6. <span style="color:blue;">A mapping <code>ID</code> from <code>(IR x IP x IR)</code> into <code>IR</code>, called the _description_ of triple terms.</span>

<code>A</code> is a mapping from _BlankNode_ to <code>IR</code>.

<p>

Given `I` and `A`, the function <code>\[I+A\](.)</code> is defined over _terms_, _triples_, and _graphs_ as follows.

* <code>\[I+A\](r) = IS(r)</code>    if `r` is a _iri_
* <code>\[I+A\](r) = IL(r)</code>    if `r` is a _literal_
* <span style="color:blue;"><code>\[I+A\](r) = ID(<\[I+A\](t.s).\[I+A\](t.p),\[I+A\](t.o)>)</code>    if `r` is a _tripleTerm_ </span>
* <span style="color:red;"><code>\[I+A\](r) = \[I+A\](r\.id)</code>    if `r` is a _tripleOccurrence_</span>
* <code>\[I+A\](r) = A<sub></sub>(r)</code>     if `r` is a _BlankNode_

<p>

* <code>\[I+A\](t) = TRUE</code> if and only if </br>
	<code><\[I+A\](t.s),\[I+A\](t.o)> ∈ IEXT([I+A\](t.p))</code> <span style="color:red;">and</span>
	* <span style="color:red;"><code><\[I+A\](ts\.id), <\[I+A\](ts.s),\[I+A\](ts.p),\[I+A\](ts.o)>> ∈ \[I+A\](rdf:nameOf)</code> <br/>
    if <code>t.s</code> is a _tripleOccurrence_ <code>ts</code></span>
	* <span style="color:red;"><code><\[I+A\](to\.id), <\[I+A\](to.s),\[I+A\](to.p),\[I+A\](to.o)>> ∈ \[I+A\](rdf:nameOf)</code>
    <br/> if <code>t.o</code> is a _tripleOccurrence_ <code>to</code></span>

<p>

* <code>\[I+A\](g) = TRUE</code> if and only if <code> ∀ t ∈ g . \[I+A\](t) = TRUE</code>

An interpretation `I` is called a _model_ of a graph `g` if there exists <code>A</code> such that <code>\[I+A\](g) = TRUE</code>.<br/>
The set of all models of a graph `g` is called `models(g)`.

Simple entailment: `g ⊨ g'` if and only if `models(g) ⊆ models(g')`.

**Note:**<br/>
RDF 1.1 semantics is exactly the above without the <span style="color:blue;">blue</span> and <span style="color:red;">red</span> parts.<br/>
A review of the standard semantics of RDF 1.0: [semantics of RDF.pdf](https://github.com/w3c/rdf-star-wg/files/11849881/semantics.of.RDF.pdf).

## EXAMPLE CASES IN CONCRETE SYNTAX:

    << :wed-1 | :liz :spouse :richard >>
     :starts 1964 .

The above entails:

    :wed-1 rdf:nameOf <<(:liz :spouse :richard)>> .
    :wed-1 :starts 1964 .

More examples:

    << _:bp1-23 | :book1 :datePublished 2023 >> 
      a :PublicationEvent .
    << _:bp1-23 | :book1 :publisher :p1 >> 
      :location :London .

&nbsp;

    :john :believes 
     << :s1 | << [] | :liz :spouse :richard >> 
               :starts 1964 >> .
    :s1 :certified-by :us-census .
    :paul :believes 
     << :s2 | << [] | :liz :spouse :richard >> 
               :starts 1965 >> .

### Shortcuts in concrete syntax:
* Triple term shortcut: <code><< :s :p :o >></code>  ➡️ <br/>
  <code><< [] | :s :p :o >></code>
* Annotation shortcut: 
<code>:s :p :o {| :id | :p1 o1; :p2 o2 |} .</code>  ➡️ <br/>
<code><< :id | :s :p :o >> :p1 o1; :p2 o2 .</code><br/>
<code>:s :p :o .</code>
