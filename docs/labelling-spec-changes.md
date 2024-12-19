# Labelling issues and PR that affect specifications

The [W3C Process] defines 5 classes of change for specifications.
This document is a summary of those classes,
and provides guidelines on which github label to use on issues and pull-request,
depending on changes they involve.

* [Class 1]: No changes to text content

  > These changes include fixing broken links, style sheets, or invalid markup. 

  Label to use: `spec:editorial`


* [Class 2]: Changes that do not functionally affect interpretation of the document

  > this constitutes changes that do not affect conformance

  Labels to use:
  * `spec:editorial` for minor changes in informative text
  * `spec:enhancement` for bigger changes that do not change the normative content substantively

* [Class 3]: Other changes that do not add new features

  > 	these changes may affect conformance to the specification

  Labels to use:

  * `spec:bug` for changes that fix a mistake in normative text
  * `spec:substantive` for other substantive changes in normative text


* [Class 4]: New features

  > Changes that add new functionality

  Label to use: `spec:new-feature`


* [Class 5]: Changes to the contents of a registry table

  Not applicable: this Working Group has not registry.

  

[W3C Process]: https://www.w3.org/policies/process/#correction-classesA
[Class 1]: https://www.w3.org/policies/process/#class-1
[Class 2]: https://www.w3.org/policies/process/#class-2
[Class 3]: https://www.w3.org/policies/process/#class-3
[Class 4]: https://www.w3.org/policies/process/#class-4
[Class 5]: https://www.w3.org/policies/process/#class-5
