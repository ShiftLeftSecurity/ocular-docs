# Installation

This section describes how to install the ShiftLeft Code Analysis Tools. Once you have installed the tools, proceed with [Getting started](getting-started.html).

# Prerequisites

The code analysis tools run on top of the Java virtual machine. Please make sure you have a Java Runtime Environment >= 1.8 installed.

# Instructions

Begin by decompressing the provided ZIP file `shiftleft-cmd-distribution.zip`. This will create the directory `shiftleft-cmd-distribution`.

```bash
unzip shiftleft-cmd-distribution.zip
cd shiftleft-cmd-distribution
```

Run the installer and follow the prompts:

```bash
sh ./install.sh
```

The install script will:

* ask you where you want to install it to (defaults to `~/bin/shiftleft-cmd`)
* check if there is an existing installation and offer to delete it
* unpack the ShiftLeft dynamic policy to `~/.shiftleft/policy/dynamic` and offer to delete it, if it already exists
* unpack the ShiftLeft static policy to `~/.shiftleft/policy/static` and offer to delete it, if it already exists.
* not touch anything outside these directories (installation and policy)

# Additional configuration to deal with large projects

Code analysis can require lots of memory, and unfortunately, the Java virtual machine does not necessarily pick up the available amount of memory by itself. While tuning Java memory usage is a discipline in its own right, it is usually sufficient to specify the maximum available amount of heap memory via the Java virtual machine's `-Xmx` flag. The easiest way to achieve this globally is by setting the environment variable `_JAVA_OPTS` as follows:

```bash
export _JAVA_OPTS="-Xmx$NG"
```
where `$N` is the amount of memory in gigabytes. You can add this line to your shell startup script, e.g., `~/.bashrc` or `~/.zshrc`.

# Warning about recursive unpacking and out of memory errors

There is no way for `java2cpg` can know which of the classes in a jar you are interested in analyzing, in which ones are just dependencies. By default, `java2cpg` will therefore **recursively unpack** all jars in jars and include all class files in the code property graph. This is often not what you want and leads to out of memory errors. To tackle this problem, you can either specify a whitelist of packages to include or blacklist packages to ensure that they are not included. As an example, consider you would like to only include code in the package `com.customer`. You can do this by running java2cpg as follows:

```bash
java2cpg.sh -nb -w 'com/customer' <customer.jar>
```

Alternatively, you can specify a comma-separated list of packages, which should not be included, for example

```bash
java2cpg.sh -b 'org/springframework,org/apache' <customer.jar>
```
will exclude all code in the packages org,springframework and org.apache.
