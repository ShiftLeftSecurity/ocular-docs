# Installation

This section describes how to install Ocular. Once you have installed it, proceed with [Getting started](https://docs.shiftleft.io/ocular/getting-started).

# Prerequisites

Ocular runs on top of the Java virtual machine. Please make sure you have a Java Runtime Environment >= 1.8 installed.

# Instructions

Begin by decompressing the provided ZIP file `ocular-distribution.zip`. This will create the directory `ocular-distribution`.

```bash
unzip ocular-distribution.zip
cd ocular-distribution
```

Run the installer and follow the prompts:

```bash
bash ./install.sh
```

The install script will:

* ask you where you want to install it to (defaults to `~/bin/ocular`)
* check if there is an existing installation and offer to delete it
* unpack the ShiftLeft dynamic policy to `~/.shiftleft/policy/dynamic` and offer to delete it, if it already exists
* unpack the ShiftLeft static policy to `~/.shiftleft/policy/static` and offer to delete it, if it already exists.
* not touch anything outside these directories (installation and policy)

# Additional configuration to deal with large projects

Code analysis can require lots of memory, and unfortunately, the Java virtual machine does not pick up the available amount of memory by itself. While tuning Java memory usage is a discipline in its own right, it is usually sufficient to specify the maximum available amount of heap memory via the Java virtual machine's `-Xmx` flag. The easiest way to achieve this globally is by setting the environment variable `_JAVA_OPTS` as follows:

```bash
export _JAVA_OPTS="-Xmx$NG"
```
where `$N` is the amount of memory in gigabytes. You can add this line to your shell startup script, e.g., `~/.bashrc` or `~/.zshrc`.
