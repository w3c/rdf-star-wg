# A Minimal Core and Clearly Defined Extension Points

This proposal aims to define a minimal core and sensible extension points.


## Consensus on Annotation Syntax

December 2023 we found a possible position around the annotation syntax (sometimes also called shorthand syntax) 
with added naming to support multi-edges:

```turtle
:s :p :o {| :u :v |}          # an `spo` edge, implicitly named by a blank node
:s :p :o {| :A | :x :y |}     # a different `spo` edge, explicitly named
```

### Use Cases

This option is not just about some syntax, but is about the most important use cases:

- annotating a statement with administrative or qualifying metadata
- support for multiplicity in LPG-style edge annotation
- intuitive and easy to use syntax

This option is missing support for annotating multiple triples in one go (i.e., support for graphs), 
but let's ignore that for now.

### Intuitive Semantics

Crucially, the annotation/shorthand syntax also fixes in intuitive ways what I find the most  
important semantic aspects:

- different annotations on the same type of triple are disambiguated by default, as the syntax 
  centers around occurrences of triples, not their type.
- annotated occurrence and annotations both are referentially transparent, which is the only  
  semantics that RDF knows of (and that its users expect)
- the annotated triple is asserted
- there *seems* to be a clear connection between the statement and its annotation.


## Mapping to RDF Triples

So far, everything looks pretty good to this writer (modulo support for graphs). The devil lies in the mapping to
N-Triples, where we have to make explicit what is so conveniently implied by the annotation syntax.

### Basic Problems and Abstractions — Un-intuitive Semantics

A recent mail discussed some of 
[the not so obvious underlying issues](https://lists.w3.org/Archives/Public/public-rdf-star-wg/2024Jan/0062.html)
that the annotation syntax so favoirably glosses over in more detail, namely:

- asserted vs unasserted statements
- referential transparency vs referential opacity
- fact vs claim

The WG has to have those discussions, although they are tedious, to come to an informed decision.
This will probably lead to some vocabulary to disambiguate annotations on asserted triples from 
those on unasserted ones and those from annotations on quotes, etc. However, for reasons of time 
the reference to that mail has to do for now.


## Mapping the Annotation Syntax

Mapping to RDF triples only what is intuitively provided by the annotation syntax is much easier, and 
indeed pretty easy IMHO.

### RDF standard reification

RDF since 1999 provides a reification vocabulary which is well disliked for its syntactic verbosity, 
e.g. (here with implicitly naming blank node):

```turtle
:s :p :o .
[] a rdf:Statement ;
    rdf:subject :s ;
    rdf:predicate :p ;
    rdf:object :o ;
    :u :v .
:A a rdf:Statement ;
    rdf:subject :s ;
    rdf:predicate :p ;
    rdf:object :o ;
    :x :y .
```
The reification vocabulary might be verbose, but it is part of the RDF specification and therefore 
it is ubiquituous. And happily, its semantics as specified in RDF 1.x capture quite exactly the  
intuitive semantics of the annotation syntax.

### RDF-star Triple Terms

Different versions of mapping  to RDF-star have been discussed:

- named occurrences `<< :A | :s :p :o >>`
- implicitly named occurrences `<< :s :p :o >> `
- triple term types `<<( :s :p :o )>>`
- edge sets `<<[ :s :p :o ]>>`

I won't go into these in detail because I think they are all too convoluted and some of them seem to 
require substantial effort to implement. I'd rather propose a very bare bones approach that explictly
defines each occurrence, like so:
```turtle
:s :p :o .
[] rdf-star:occurrenceOf << :s :p :o >> ;
     :u :v .
:A rdf-star:occurrenceOf << :s :p :o >> ;
     :x :y .
```
The triple term `<< :s :p :o >> ` here is not an implicitly named occurrence but a type, similar to 
how it's defined in the RDF-star CG report.
The property `rdf-star:occurrenceOf` is just a placeholder. As discussed above, RDF-star will probably
have to provide different properties for different semantics: asserted or not, referentially transparent
or opaque. However, there can be only one property to map the annotation syntax to triples, and that has
to express assertedness and referential transparency of the occurrence, just like the semantics of RDF 
standard reification.

### Nested Named Graphs
My heart is somewhere else, namely with a graph based approach called [Nested Named Graph](https://github.com/rat10/nng). 
I don't want to go into more detail to not annoy anybody, but I will say this much: among other benefits 
it caters for the fundamental problem that neither RDF standard reification nor RDF-star are able to 
solve. It bridges the gap between a statement and its annotation unambiguously, and effortlessly, e.g.:
```turtle
# serialized as NNG
[] { :s :p :o } :u :v .
:A { :s :p :o } :x :y .
# serialized as TriG
_:NG  :u :v .
:X  :x :y .
_:NG { :s :p :o }
:A { :s :p :o }
```
There is not separation between an assertion and its annotation. There is at most, in the TriG mapping, 
an indirection. For more about nesting, compatability to installed uses of named graphs, ease of querying, 
etc see the ilink above.


## Standardizing RDF 1.2

### Minimal Core

I propose to include into RDF 1.2 only the annotation syntax (for Turtle, TriG, maybe an equivalent 
in JSON-LD), together with support in SPARQL. The mapping to RDF standard reification normatively 
describes the semantics. Supporting that mapping provides complete RDF 1.2 support.

The reification quad is well disliked, but it provides a common denominator since RDF 1999.
For stores that don’t plan to support lots of annotations or heavy loads of LPG-style data this still 
offers an economical path to full RDF 1.2 compatability (of course, some stores even support RDF 
standard reification *very* efficiently, despite its syntactic verbosity, so there’s no judgement 
involved at all). For stores that implement RDF-star as named graphs this arrangement provides 
security that their implementation won’t be invalidated by some unforseen usage of a new term type
in RDF like the proposed RDF-star triple term.

Explicit support for un-asserted triples has to go somewhere. RDF standard reification already is
misused to support it. We could strengthen and rectify that misuse by supporting it with a proper
subtype of `rdf:Statement`. That would again require a (pretty trivial) mapping to triple terms in 
RDF-star (and is provided in NNG already).

According to this proposal it would be NORMATIVE that a system claiming to support RDF 1.2 has to 
support the annotation shorthand syntax *at least* via RDF standard reification. However, any 
implementation would be free to do whatever it likes under the hood to more efficiently store 
the annotation syntax. Proper extensions to that effect may be defined.


### Extensions

#### RDF-star

Of course getting rid of the RDF standard reification quad was *the* motivation for the RDF-star effort. 
However, it requires a considerable extension to the core of RDF and is still struggling in a lot
of crucial details. I propose therefore to make it a semantic extension to RDF - endorsed by this WG,
but still separate and optional. A store deciding to support RDF-star would be welcome to map the 
annotation syntax to triple terms, not to standard reification quads. 

This arrangement has several benefits:

- it frees implementors that are not convinced of RDF-star from having to implement the annotation
  syntax in exactly this way,
- it gives RDF-star more wiggle room to evolve further and
- it provides some prominence to the semantic extension mechanism, which I think it deserves, 
encouraging more such efforts as RDF-star (and supporting a pretty practical approach to the
"living standard" idea)

Since broad support for the annotation syntax core can be assumed (especially if implementors are not 
*forced* to support it via RDF-star triple terms), even this status as "only" semantic extension  
might very well rather ensure than hinder broader adoption.


#### Nested Named Graphs (NNG)

Naturally I'm listing NNG here as well. It provides everything that RDF standard reification and
RDF-star provide, and quite a bit more. Contrary to RDF-star it doesn't even seem to require an 
extension to RDF's model and abstract syntax (of course, in that respect real scrutiny hasn't been 
applied yet by experts in the field, at least not that I'm aware). It avoids all the problems that 
lead to the misuses of RDF standard reification in practice and those contortions in RDF-star 
described above. It also provides the most succinct syntax. Nonetheless it has been met with little 
interest in this WG.
