Ocular Documentation
====================

Ocular is a set of command line tools that expose static analysis
primitives via a flexible query language. The distribution currently
supports Java, C# and C/C++ (based on
[Joern](https://github.com/octopus-platform/joern)), and is aimed
towards developers and security researchers interested in exploring
code for vulnerabilities.

A trial version (Java-only) with a limited feature set can be downloaded here:

https://go.shiftleft.io/ocular-free-trial

Ocular provides the following core features.

* **Generation of code property graphs.** Given a jar, we generate a
  versatile intermediate graph representation of code called the code
  property graph (CPG). These graphs can be used as the basis of
  custom static analysis. Supported output formats are CSV, GraphML,
  Gryo, and Protobuf (included in trial).

* **Interactive code analysis via a REPL.** Code property graphs can
  be interactively explored via a REPL, an interactive shell that
  supports a custom query language for code analysis. Results can be
  piped to text files or exported in JSON format. The REPL brings
  readline support and offers tab-completion to ease interaction
  (included in trial).

* **Custom analysis scripts and extensibility.** REPL scripts can be
  executed non-interactively to perform custom scans for patterns
  indicating vulnerable code, defined by the consultant. Moreover, the
  consultant can augment and customize the query language via the
  "pimp-my-library" pattern (included in trial).

* **Framework and library support via policies.** ShiftLeft Code
  Analysis Tools come with annotations for common Java frameworks and
  libraries. Possibly attacker-controlled data sources and interesting
  sinks are tagged in the graph automatically, and flow descriptions
  exist to scan for common vulnerability patterns. Users can provide
  additional annotations to extend supported frameworks and libraries
  or encode additional vulnerability patterns.

* **Automatic code scanning.** Policies can be applied to code
  property graphs to generate *security profiles*. These profiles
  provide a summary of an application's security-relevant flows,
  derived with respect to the policy. Like code property graphs,
  security profiles can be explored and processed via the REPL, and
  they additionally serve as summaries of vulnerabilities  and data
  leaks present in the code.

This is Ocular's official documentation. Please feel free to suggest
fixes or provide new tutorials by sending in a pull request.

Tutorials
---------

* [Installation](tutorials/installation.md)
* [Getting Started with the CPG](tutorials/getting-started-cpg.md)
* [Beautiful CPGs with Java2CPG](tutorials/beautifulcpgs.md)
* [Interfacing with Ocular](tutorials/interfacing.md)
* [Auditing for deserialization](tutorials/deserialization.md)
* [Getting Started with the SP](tutorials/getting-started-sp.md)
* [Java Vulnerable Lab](tutorials/java-vuln.md)
* [Analyzing libraries](tutorials/libanalysis.md)
* [Exploring the call graph](tutorials/callgraph.md)
* [Finding real world vulnerabilities by Example](tutorials/CVE-2018-19859.md)

Reference Manual
-----------------

* [Java2CPG](manual/java2cpg.md)
* [FuzzyC2CPG](manual/fuzzyc2cpg.md)
* [CPG2SP](manual/cpg2sp.md)
* [REPL](manual/repl.md)
* [Policy Language](manual/policy-language.md)

Videos
-------
* [Ocular Demo by Chetan Conikee](https://www.youtube.com/watch?v=bB2C-FzC1Yw&feature=youtu.be)


