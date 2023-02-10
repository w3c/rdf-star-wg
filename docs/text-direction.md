According to the [W3C Process Document](https://www.w3.org/2021/Process-20211102/):

> All reports, publications, or other deliverables produced by the group for public consumption should follow best practices for **internationalization** and for accessibility to people with disabilities. Network access to W3C-controlled domains may be assumed.

One thing that wasn't given as much attention in earlier Working Groups (including RDF 1.1) was support for describing the base paragraph direction that text should should use for presentation. Although Unicode provides some information about the directionality of individual code points and a way to estimate base paragraph direction when no other information is available (see [First-string property detection](https://w3c.github.io/string-meta/#firststrong), there are still many cases in which this results in incorrect results. The requirements here are captured  in https://github.com/w3c/rdf-concepts/issues/9 on RDF Concepts, where much of this information has been gathered. Of course, changes potentially will impact other specifications.

The [Internationalization Best Practices for Spec Developers Note](https://www.w3.org/TR/international-specs/) has a whole section devoted to [Text direction](https://www.w3.org/TR/international-specs/#text_direction), and this is a checklist item for the internationalization review all specs need to go through. When this was performed for JSON-LD 1.1, it came up that the underlying data model (RDF) had no way to indicate text direction, which was a shortcoming. There were a number of review items and proposals put forth (See [RDF Literals and Base Directions](https://w3c.github.io/rdf-dir-literal/) by Ivan Herman and Pierre-Antoine Champin, for example and further discussion).

JSON-LD added support by introducing the [`@direction` keyword](https://www.w3.org/TR/json-ld11/#dfn-base-direction) added to a Literal Object. When serializing to and from an RDF Dataset, non-normative solutions were proposed using [The `i18n` Namespace](https://www.w3.org/TR/json-ld11/#the-i18n-namespace) and [The `rdf:CompoundLiteral`](https://www.w3.org/TR/json-ld11/#the-rdf-compoundliteral-class-and-the-rdf-language-and-rdf-direction-properties) along with associated properties. The idea being that these would be interim solutions available before normative behavior could be described in a future version of RDF.

# Potential solutions

Generally, the `i18n` namespace solution seems to have had the most uptake.

## The `i18n` namespace

Summary: The `i18n` namespace `<https://www.w3.org/ns/i18n#>` is used as the basis for a datatype that includes either or both of the `language tag` and `text direction`, separated by an underscore (`_`). In JSON-LD, both values are normalized to lower-case.

Example for Arabic text (`ar-EG`) with `right to left` text direction (`rtl`):

```turtle
[
  ex:title "HTML و CSS: تصميم و إنشاء مواقع الويب"^^i18n:ar-eg_rtl;
  ex:publisher "مكتبة"^^i18n:ar-eg_rtl
] .
```

Advantages:
* Works in every existing RDF serialization format.

Disadvantages:
* As it uses a datatype IRI, without normalization, otherwise equivalent literals may not be considered to be the same.
* It does not actually create a [language-tagged string](https://www.w3.org/TR/rdf11-concepts/#dfn-language-tagged-string), and SPARQL builtins such as [`LANG`](https://www.w3.org/TR/sparql11-query/#func-lang) can't access this.

Also see the [Pros and Cons](https://w3c.github.io/rdf-dir-literal/#pros-and-cons-0) discussion in [RDF Literals and Base Directions](https://w3c.github.io/rdf-dir-literal/),

## The `rdf:CompoundLiteral` class

Summary: The `[i18n` namespace](rdf:CompoundLiteral) uses a blank node to contain the separate components of the "literal":

Example for Arabic text (`ar-EG`) with `right to left` text direction (`rtl`):

```turtle
[
  ex:title [
    rdf:value "HTML و CSS: تصميم و إنشاء مواقع الويب",
    rdf:language "ar-eg",
    rdf:direction "rtl"
  ];
  ex:publisher [
    rdf:value "مكتبة",
    rdf:language "ar-eg",
    rdf:direction "rtl"
  ]
] .
```

**Ednote**: The group considered using a language-tagged string as the value of `rdf:value`, but I can't immediately see the rationale for rejecting this.

Advantages:
* Works in every existing RDF serialization format.

Disadvantages:
* Introduces a new blank node.
* Also same dis-advantages as the `i18n` datatype solution.

Also see the [Pros and Cons](https://w3c.github.io/rdf-dir-literal/#pros-and-cons-2) discussion in [RDF Literals and Base Directions](https://w3c.github.io/rdf-dir-literal/),

## Extend `langString`

As expressed in [RDF Literals and Base Directions](https://w3c.github.io/rdf-dir-literal/) [Extend `langString`](https://w3c.github.io/rdf-dir-literal/#extending-lang-string), the definition for `rdf:langString` could be extended to include _both_ the language tag and the text direction by introducing a separator such as a carat (`^`).

Example for Arabic text (`ar-EG`) with `right to left` text direction (`rtl`):

```turtle
[
  ex:title "HTML و CSS: تصميم و إنشاء مواقع الويب"@ar-EG^rtl;
  ex:publisher "مكتبة"@ar-EG^rtl
] .
```

Advantages:
* Literal is treated as a language-tagged string with an extra "text direction" facet.
* Minimal impact on RDF Semantics.

Disadvantages:
* Requires changes to every RDF serialization format (which we're doing anyway).
* Is not available to specifications which are not updated.
* Depending on your view, it raises compatibility issues with RDF 1.0/1.1 in that systems that see this in RDF 1.2 data will either not see the text direction, or will fail to process the data. Although, a quick check on what this means may suggest otherwise:
  > Backward compatibility is a design that is compatible with previous versions of itself. Forward compatibility is a design that is compatible with future versions of itself.

A related solution would update the grammars to allow _both_ a language tag and a datatype, for specific datatype IRIs derived from `rdf:langString`.

```turtle
[
  ex:title "HTML و CSS: تصميم و إنشاء مواقع الويب"@ar-EG^^rdf:langStringRtl;
  ex:publisher "مكتبة"@ar-EG^^rdf:langStringRtl
] .
```

