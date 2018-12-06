# Java2CPG: Generating Code Property Graphs from Java Archives

Java2CPG is a command line tool used tool to convert Java archives
(JAR/WAR files) to Code Property Graphs (CPG).

# Usage

```bash
java2cpg [-o <outputformat>] [-n <outputlocation>] <jar>
```

# Description

Java2CPG converts Java Archives (JAR files) and Java Web Archives (WAR
files) into Code Property Graphs, an intermediate graph representation
of code. Multiple different output formats are supported, including
standard formats such as graphml and gyro, as well as a the
space-efficient binary format Protobuf. Java2CPG recursively unpacks
jars to obtain all class files directly and indirectly stored in the
archive. A package blacklist or whitelist can subsequently be applied
for package selection.

# Examples

A code property graph named "cpg.bin.zip" suitable to be processed
with other ShiftLeft Command Line Tools can be generated as follows:

```bash
java2cpg application.jar
```

To write the CPG to a different file, the output location can be controlled via the parameter "-n":

```bash
java2cpg application.jar -n foo.bin.zip
```

CPGs can also be generated in standard formats to enable importing into external tools. For example, a graph can be generated in graphml format as follows:

```bash
java2cpg application.jar -n foo.xml -o graphml
```

By default, java2cpg will unpack jars recursively, that is, if the
application jar bundles dependencies as jars, those will be unpacked
and their corresponding graphs will be included in the output. It is
possible to disable this recursive unpacking via the `-nu` flag. For
example, consider an application.jar with the following content:

```
com/custommer/myClass.class
dependency.jar
    org/foo/Library.class
```

The "dependency.jar" and its archived "Library.class" file can be excluded from the analysis as follows:

```bash
java2cpg application.jar -nu
```

More fine grained control over the classes to exclude from analysis is supported via the "-b" flag, which accepts a comma-separated list of path names to exclude. 

For example, the following command will explicitly blacklist any classes defined in packages beneath "org/foo" and "org/bar".

```bash
java2cpg application.jar -b org/foo,org/bar
```

Alternatively, whitelisting can be performed on packages. For example,
code property graph construction can be limited to classes in the
package "org.project" as follows.

```bash
java2cpg -nb -w org/project
```
