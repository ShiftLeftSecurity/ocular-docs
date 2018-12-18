Ocular Documentation
====================

Ocular is a set of command line tools that provide a static analysis framework
in the context of security; the distribution currently supports Java, C# and
C/C++ (based on [Joern](https://github.com/octopus-platform/joern)), and is
aimed towards developers and security researchers interested in exploring code
for vulnerabilities.

A trial version (Java-only) with a limited feature set can be downloaded here:

https://go.shiftleft.io/ocular-free-trial

Ocular provides the following core features.

* **Generation of code property graphs.** Based on the application code, which
  is provided as JAR for JAVA or as source code for C/C++, we generate a
  versatile, intermediate graph representation called the code property graph
  (CPG). CPGs can be used as the basis of custom static analysis and are
  serializable to various output/exchange formats such as CSV, GraphML, Gryo,
  and Protobuf (included in trial).

* **Interactive code analysis via a REPL.** CPGs can be interactively explored
  via our Read-Eval-Print-Loop (REPL), i.e., an interactive shell with support
  for our custom query language. Among other features, the REPL offers utilities
  for exporting security analysis results as plain text or in JSON format,
  readline support, and tab-completion (included in trial).

* **Custom analysis scripts and extensibility.** User-provided REPL scripts can
  be executed non-interactively to perform automated custom scans for security
  issues. Moreover, the Ocular users can even augment and customize the query
  language via the "pimp-my-library" pattern (included in trial).

* **Framework and library support via policies.** ShiftLeft Code Analysis Tools
  comes with annotations for common Java frameworks and libraries. Possibly
  attacker-controlled data sources and interesting sinks are tagged in the graph
  automatically, and flow descriptions exist to scan for common vulnerability
  patterns. Users can provide additional annotations to extend supported
  frameworks and libraries or encode additional vulnerability patterns.

* **Automatic code scanning.** Policies can be applied to code property graphs
  to generate *security profiles* which can be considered as summaries of
  vulnerabilities and data leaks present in the code. Similar to CPGs, security
  profiles can be also explored and processed via the REPL.

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
* [Finding real world vulnerabilities by example](tutorials/CVE-2018-19859.md)

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


