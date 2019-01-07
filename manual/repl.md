# Ocular Query Language (Oh-Kewl!)

ShiftLeft Ocular, is a REPL (Read-Eval-Print-Loop). It lets you perform code analysis on code property graphs and security profiles, both interactively or with non-interactive scripts. This document provides an overview of the Ocular features.

The underlying shell is an interactive scala shell which includes useful things like:
* <TAB> for autocomplete
* <UP> and <DOWN> for going back in the history of commands
* <CTRL-r> to search in the command history
* `helpMsg` and `status` are your friends

# Using Ocular

Ocular is started as follows: 

```bash
./ocular.sh
```

Once fired up, Ocular provides the following commands.

* ***loadCpg(filename).*** Loads the code propert graph (CPG) stored
     at `filename`. The format is inferred from the file
     extension. Valid extensions are ".xml" for GraphML and ".bin.zip"
     for the default binary format (recommended). The CPG is made
     available via the object `cpg`.
     
* ***loadSp(filename, isJson=true).*** Loads the security profile (SP)
     stored at `filename`. The SP is expected to be in the default
     binary format, or in its JSON version if `isJson` is explicitly
     set to true. The SP is made available via the object `sp`.

# Unix powertools

The below only work with `List` at this stage. We can extend/change that if needed.

* pipe output to outfile: `cpg.namespace.name.l |> "out.txt"`
* append to outfile: `cpg.namespace.name.l |>> "out.txt"`

# Ammonite tricks

* don't use .p, rather use .l, and ammonite will pretty print our data structures
* use `browse(yourquery)` to open a less-like application

# Scripting

You can execute the below script with 

```bash
ocular.sh --script /home/fullpath/to/testscript.sc --params jarFile=/fullpath/to/file.jar,outFile=out.cpg`
```

testscript.sc:
```scala
@main def exec(jarFile: String, outFile: String) = {
  loadCpg(jarFile)
  cpg.namespace.nalme.l |> outFile
}
```

# Debug Ocular with jdb
```bash
export JAVA_OPTS='-Xdebug -Xrunjdwp:transport=dt_socket,address=8002,server=y,suspend=n'
./ocular.sh
```
The first line of output will be `Listening for transport dt_socket at address: 8002`.  You can now connect to remote debug with your favorite debugger, e.g. Intellij or jdb command line.
