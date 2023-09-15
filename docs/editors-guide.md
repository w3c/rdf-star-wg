# Guide for editors of RDF-star specs

* We use [ReSpec](https://github.com/w3c/respec) as the format for all documents, and locate them in the /spec directory of each repository, along with related files (images, grammars, etc.) The [ReSpec Editor's Guide](https://github.com/w3c/respec/wiki/ReSpec-Editor's-Guide) and [wiki](https://github.com/w3c/respec/wiki) contain useful information about working with ReSpec documents.

## Publication Rules and W3C Process

The tools used here are intended to implement the requirements for conforming to [Publication Rules](https://www.w3.org/2003/05/27-pubrules) and conform to the requirements set forth in the [W3C Process document](https://www.w3.org/2021/Process-20211102/).

## Contributing to the Repository

Generally, contributors with write access to the repository should create Pull Requests from branches on the main repository, rather than from forks of that repository. That makes it easier for other contributors to add changes directly into that branch.

Working Group members that do not have write access can either request to be added to the rdf-star-editors group to gain access, or can work from forks of the repository. In this case, be sure to work off of a branch other than "main".

Contributions from outside the working group are best left as comments, due to patent policy considerations. However, small editorial changes may be considered at the descretion of the working group chairs and staff.

### Pull Requests

See [GitHubs page on pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests) for more information.

The [commenting system](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/commenting-on-a-pull-request) provides other ways to contribute to a PR, notably the `suggestion` mechanism.

After review, unless general review from the working group is deemed necessary, PRs can be merged. Subject, to [Echidna](https://github.com/w3c/echidna) configuration, this may result in the automatic publication of a new Working Draft. [Merging](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges) can be done with either Merge Commits, Rebase, or Squash. Squash and Rebase leave cleaner commit trails in the "main" branch.

### Reviews

For substantive changes consider asking specific individuals for [review](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/reviewing-proposed-changes-in-a-pull-request) (generally, co-editors). To be able to request a review of someone in particular, they need to have some form of access to the repository which can be facilitated by the staff contact. Otherwise, `@` mention them in a comment.

Note that if a reviewer requests changes, they may be asked to re-review and approve before the PR can be merged (this is driven by repository settings).

At some point adding [CODEOWNERS](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners) to the repository can be used to automate review requests for editors of the spec.

## Local editing

Because the specs include files, running locally requires using a local web server. One simply way to do this is to run `python -m http.server` in the repository, or something in the directory tree including the repository clone. Then, the spec page can be opened through `localhost` on whatever port it runs on. For example, `http://localhost:8000/spec/`.

## Common configuration options

Include the following scripts:

```javascript
  <script src="https://www.w3.org/Tools/respec/respec-w3c" class="remove"></script>
  <script src="./common/local-biblio.js" class="remove"></script>
  <script src="./common/fixup.js" class="remove"></script>
```

Do not defer loading of `respec-w3c`, as `fixup.js` needs access to some of the things it loads.

Common files are contained in the [rdf-common]() repository and should be copied to /spec/common directory.
There is a step in a GitHub Action that can check to verify that these files are consistent.

Generally, `common/local-biblio.js` should contain common bibliographic references. If they are widely used, they can be added permanently to [specRef](https://github.com/tobie/specref).

Entries are in `local-biblio.js` for the 1.2 specs, but shouldn't be necessary after FPWD, when they'll automatically be added to specRef.

`common/fixup.js` includes an `end-all` event to correct entries such as `<a data-cite="RDF12-CONCEPTS#triple">...</a>` such that, when used in a document with the same short-name, it will un-anchor it and add `(this document)`. Used for localizing `common/related.html`.

### respecConfig

* Add `localBiblio: localBibliograph`, which is defined in `common/local-biblio.js`.
* Remember that `edDraftURI` should end in `/spec/`, so that referencing the editors draft doesn't take you to the repository, but to the rendered draft.
* `previousPublishDate`, `previousMaturity`, and `prefRecShortname` should be set up for the 1.1 recommendations.
* Use [`formerEditors`](https://github.com/w3c/respec/wiki/formerEditors) to recognize previous editors.

We might consider creating a common CSS file to help maintain consistency between the different specs.

### sotd

The `sotd` section (which automatically gets a heading) should have something that includes `common/related.html` using [`data-include`](https://github.com/w3c/respec/wiki/data-include). We need to decide if each document should list all related RDF and SPARQL documents, or we create subsets for RDF and SPARQL.

## Definitions

* Define terms using `<dfn>`, which can take some modifiers. Note that `data-lt` can be used to define aliases for that term, but also affects which is actually exported from the spec (see the "index" section). Use `class="export"` or `class="noexport"` to control its visibility.
* If changing a previous definition (e.g., because it was errantly exported as plural, or duplicately defined), add a span element with an `id` attribute so that any dangling references go someplace. For example: `<dfn data-lt="inconsistent">Inconsistency</dfn><span id="dfn-inconsistency"><!-- obsolete term --></span>`.

## Citing other documents and definitions.

ReSpec provides a number of different ways to reference definitions from different documents.

* Bare anchors will automatically be filled in if there is a match with a local (or cross-referenced) definition. E.g., `<a>triple</a>` would be styled and updated to `<a href="#dfn-triple">triple</a>`.
  * This automatically handles most plural forms, but can be made explicit using [`data-lt`](https://github.com/w3c/respec/wiki/data-lt).
  * To include definitions from select other documents or document sets, use [`xref`](https://github.com/w3c/respec/wiki/xref) in `respecConfig`. Some document use `xref: ["RDF11-CONCEPTS"]`, which can change to "RDF12-CONCEPTS" after FPWD.
* Anchors can use [`data-cite`](https://github.com/w3c/respec/wiki/data-cite) to explicitly get a term (or other fragment) from another document, which has the added attribute of adding a citation to that document. E.g., `<a data-cite="RDF12-CONCEPTS#dfn-triple">triple</a>`. (Note that they've marked this as obsolete, and there's a [guide for xref](https://github.com/w3c/respec/wiki/Auto-linking-external-references), but I've resisted doing this).
* Normal `[[]]` references can add citations (e.g., `[[RDF12-CONCEPTS]]`), and the form with three brackets `[[[RDF12-CONCEPTS]]]` will expand the document name.

## Examples

Use either `<pre class="example">` or `<aside class="example">` for example blocks. Examples can have titles, and we should consider a policy on using this.
  
Blocks within an example do not need to be left justified, as ReSpec does this automatically now.

Consider using `data-transform="updateExample"` with `<!-- .. -->` comments to avoid entity escapes. This also allows for adding comments (`####`) and highlighting (`****`) for those examples with some CSS styling. See `common/fixup.js`. Automatic example highlighting may mess this up, in which case `class="nohighlight"` may be necessary.

```
  <pre class="example nquads" data-transform="updateExample">
    <!--
    <http://one.example/subject1> <http://one.example/predicate1> <http://one.example/object1> <http://example.org/graph3> . # comments here
    # or on a line by themselves
    _:subject1 <http://an.example/predicate1> "object1" <http://example.org/graph1> .
    _:subject2 <http://an.example/predicate2> "object2" <http://example.org/graph5> .
    -->
  </pre>
```

### PREFIX/BASE use in examples

Turtle and TriG examples should prefer the use of `PREFIX` and `BASE` to `@prefix` and `@base`. This creates a uniform style between Turtle/TriG and SPARQL and facilitates copy/paste.

Doing this, [Example 1](https://www.w3.org/TR/rdf12-turtle/#ex-intro) in the Turtle Spec becomes the following:

```turtle
BASE <http://example.org/>
PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX foaf: <http://xmlns.com/foaf/0.1/>
PREFIX rel: <http://www.perceive.net/schemas/relationship/>

<#green-goblin>
  rel:enemyOf <#spiderman> ;
  a foaf:Person ;    # in the context of the Marvel universe
  foaf:name "Green Goblin" .

<#spiderman>
  rel:enemyOf <#green-goblin> ;
  a foaf:Person ;
  foaf:name "Spiderman", "Человек-паук"@ru .
```

(There was some preference for using the lower-case `prefix` and `base` instead, which is allowed by the grammar, as such tokens are case-insensitive, but consistency is also important).

Exceptions to this rule is where the grammar is discussed explicitly, and examples using `@prefix` and `@base` are appropriate. Also, possibly `prefix` and `base` to clarify the case-insensitive nature of such tokens.

## Contributors

### Configuration fields
Direct contributors to the specification can be classified as [`editors`](https://github.com/w3c/respec/wiki/editors), [`formerEditors`](https://github.com/w3c/respec/wiki/formerEditors), or [`authors`](https://github.com/w3c/respec/wiki/authors).

Entries can use fields from a [`Person`](https://github.com/w3c/respec/wiki/person) entry.

All editors **MUST** have a w3cid field, other uses may include it as well.

The use of `url` field for personal URLs is discouraged.

Consider using the `note` field to describe a particular type of contribution (e.g., "Chair", "Contributor", etc.)

### Acknowledgements

ReSpec has special treatment for an element with `id` `gh-contributors`, which is automatically loaded and formatted with the names of people committing to the repository (excluding editors). For example:

```html
<section id="section-Acknowledgments" class="informative appendix">
  <h2>Acknowledgments</h2>

  <p>The following people have contributed to this specification:
    <span id="gh-contributors"></span>
  </p>

  <p>In addition to the <a href="https://www.w3.org/groups/wg/rdf-star/participants">members of the RDF-star Working Group</a>.</p>
</section>
```

## Manually preparing documents for publication

Normal updates to Working Drafts are typically handled by [Echidna](https://github.com/w3c/echidna), but specific milestones need to be generated manually.

Generally, the FPWD docs will be prepared with a planned publication date and saved in the repository (for example see the [snapshot for rdf-canon](https://github.com/w3c/rdf-canon/tree/main/publication-snapshots/FPWD)). This can be done by adding `?specStatus=FPWD&publishDate=YYYY-MM-DD` to the URL in the browser address bar and using the control in the upper right to save the document as HTML. The resulting file can be saved as `Overview.html` (because of tradition?), along with files used directly by the finished document in a repository folder, for example `publication-snapshots/FPWD/Overview.html`. Once uploaded in a branch the the repository, pass it through the [pubrules checker](https://www.w3.org/pubrules/) and [link checker](https://validator.w3.org/checklink). This sometimes finds minor problems that will need to be corrected. Generally, you can get a URL for a formatted version of the spec after it’s been uploaded using the raw file viewer for the Overview.html in GitHack.

Most of the technical Pubrules requirements are handled by ReSpec automatically based on specStatus.

## Other

* Include a [definition index](https://github.com/w3c/respec/wiki/index) to show all locally defined and imported definitions.
* Include an [issue summary](https://github.com/w3c/respec/wiki/issue-summary) to show issues from the repo.
* Use [`data-number`](https://github.com/w3c/respec/wiki/data-number) to reference a specific issue.
* Consider hiding details in `<details>`.
* Significant changes should be recorded in the change log at the bottom.
* Use the ReSpec features for linking to documents and definitions. (class=“sectionRef” useful for internal links).
* Validate HTML before checking in.

## Working with GitHub

* Please perform a timely review of PRs for which you share responsibility.
  * The "suggestion" commenting mechanism can help.
* Make effective use of pull requests. Be sure to ask co-editors, and anyone else interested for reviews.
* Consider using squash commits if there are too many little changes along they way; otherwise, consider using Rebase to keep the longs clean.
* Remove branches after merging.
* Make issues and refer to them either/both within the doc, or in the PR comments. `“Fixes #nnn”` or `“Relates to #nnn”` links the PR to the issue.
* You can run ReSpec locally (with ?specStatus=WD) to pre-flight the GH Action.
* Don’t make changes in spec/common directly, but we should coordinate on changes to the rdf-common repo. If they’re out of sync, the GH Action build may fail.
