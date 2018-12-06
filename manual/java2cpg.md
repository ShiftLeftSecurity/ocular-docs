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

# Usage

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

# Blacklisting and whitelisting of packages

There is no way for `java2cpg` can know which of the classes in a jar you are interested in analyzing, in which ones are just dependencies. By default, `java2cpg` will therefore **recursively unpack** all jars in jars and include all class files in the code property graph. This is often not what you want and leads to out of memory errors. To tackle this problem, you can either specify a whitelist of packages to include or blacklist packages to ensure that they are not included. As an example, consider you would like to only include code in the package `com.customer`. You can do this by running java2cpg as follows:

```bash
java2cpg.sh -nb -w 'com/customer' <customer.jar>
```

Alternatively, you can specify a comma-separated list of packages, which should not be included, for example

```bash
java2cpg.sh -b 'org/springframework,org/apache' <customer.jar>
```
will exclude all code in the packages org,springframework and
org.apache. Finally, it is possible to disable recursive unpacking via
the `-nu` flag. For example, consider an application.jar with the
following content:

```
com/custommer/myClass.class
dependency.jar
    org/foo/Library.class
```

The "dependency.jar" and its archived "Library.class" file can be
excluded from the analysis as follows:

```bash
java2cpg application.jar -nu
```