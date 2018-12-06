# Ocular Manual

Ocular exposes the complete capabilities of ShiftLeft's static analysis to security consultants and developers. Ocular consists of a set of command line tools. These tools give users the ability to generate and query code property graphs and security profiles, and are designed with extensibility and interoperability in mind.

The command line tools currently operate on Java archives (JAR/WAR format). These archives are passed to the `java2cpg` tool, which generates an intermediate graph representation of code from the JAR, a code property graph (CPG). This graph can be queried using an interactive shell, the REPL, or analyzed automatically to generate a *security profile* with the `cpg2sp` tool, according to security policy. The security profile summarizes security-relevant properties of the application, including identified vulnerabilities and data leaks.

## Core features

* **Generation of code property graphs.** Given a jar, we generate a versatile intermediate graph representation of code called the code property graph (CPG). These graphs can be used as the basis of custom static analysis. Supported output formats are CSV, GraphML, Gryo, and Protobuf.

* **Interactive code analysis via a REPL.** Code property graphs can be interactively explored via a REPL, an interactive shell that supports a custom query language for code analysis. Results can be piped to text files or exported in JSON format. The REPL brings readline support and offers tab-completion to ease interaction.

* **Custom analysis scripts and extensibility.** REPL scripts can be executed non-interactively to perform custom scans for patterns indicating vulnerable code, defined by the consultant. Moreover, the consultant can augment and customize the query language via the "pimp-my-library" pattern.

* **Framework and library support via policies.** ShiftLeft Code Analysis Tools come with annotations for common Java frameworks and libraries. Possibly attacker-controlled data sources and interesting sinks are tagged in the graph automatically, and flow descriptions exist to scan for common vulnerability patterns. Users can provide additional annotations to extend supported frameworks and libraries or encode additional vulnerability patterns.

* **Automatic code scanning.** Policies can be applied to code property graphs to generate *security profiles*. These profiles provide a summary of an application's security-relevant flows, derived with respect to the policy. Like code property graphs, security profiles can be explored and processed via the REPL, and they additionally serve as summaries of vulnerabilities  and data leaks present in the code.

* [Installation](installation.md)
* [Getting Started](getting-started.md)
* [Java Vulnerable Lab](java-vuln.md)
* [Policy Language](policy-language.md)
* [Java2CPG](java2cpg.md)
* [FuzzyC2CPG](fuzzyc2cpg.md)
* [CPG2SP](cpg2sp.md)
* [REPL](repl.md)
* [Analyzing libraries](libanalysis.md)
* [Exploring the call graph](callgraph.md)

