# Exploring the call graph

In addition to identifying data flows, call chains can be identified
using scripts and the REPL. We illustrate this ability with
`commons-io` as our test subject. You can download commons-io here:
([mvnrepository](http://central.maven.org/maven2/commons-io/commons-io/2.5/commons-io-2.5.jar))

We then generate a CPG as follows:

```
./java2cpg.sh -f protobufzip -o commons-io-2.5.bin.zip commons-io-2.5.jar -nb

```

We start the REPL and load the graph:

```
$ ./ocular.sh 
ocular> loadCpg("commons-io-2.5.bin.zip") 
```

We search for interesting methods in commons-io 2.5 and soon find `java.lang.Runtime.exec` 

```
ocular> cpg.method.fullName(".*exec.*").fullName.p 
java.lang.Runtime.exec:java.lang.Process(java.lang.String[])
```

Now we are interested in answering the questions; where is the data
comming from and can we control it? To answer this questions we can go
the call stack up by using the keyword `caller` as follows:

```
ocular> cpg.method.fullName(".*exec.*").caller.fullName.p 
org.apache.commons.io.FileSystemUtils.openProcess:java.lang.Process(java.lang.String[])


ocular> cpg.method.fullName(".*exec.*").caller.caller.fullName.p 
org.apache.commons.io.FileSystemUtils.performCommand:java.util.List<java.lang.String>(java.lang.String[],int,long)


ocular> cpg.method.fullName(".*exec.*").caller.caller.caller.fullName.p 
org.apache.commons.io.FileSystemUtils.freeSpaceUnix:long(java.lang.String,boolean,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceWindows:long(java.lang.String,long)


ocular> cpg.method.fullName(".*exec.*").caller.caller.caller.caller.fullName.p 
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)

[..]

ocular> cpg.method.fullName(".*exec.*").caller.caller.caller.caller.caller.caller.caller.fullName.p 
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long()
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long()
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long()
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long()


ocular> cpg.method.fullName(".*exec.*").caller.caller.caller.caller.caller.caller.caller.caller.fullName.p
[no results]
```

As we see, even within the small jar, we need seven steps to find the
"beginning" of the call stack, where no caller are present anymore.
We have an idea about where the data is comming from, it has to be
somewhere along the call stack, but we still don't know if we can
control it.

We could look into every method in the call stack and check if it
consumes the right parameter but this can be very time consuming.
Therefor we introduced the `repeat`, `until` and `emit` steps. The
following query starts from a method named `exec` and repeats the
method.caller step untill it finds a method that is public and
consumes a parameter of type `java.lang.String`.

```
ocular>  cpg.method.name("exec").repeat(method=>method.caller).until(method=> method.isPublic.parameter.evalType("java.lang.String")).emit().fullName.p 
org.apache.commons.io.FileSystemUtils.openProcess:java.lang.Process(java.lang.String[])
org.apache.commons.io.FileSystemUtils.performCommand:java.util.List<java.lang.String>(java.lang.String[],int,long)
org.apache.commons.io.FileSystemUtils.freeSpaceUnix:long(java.lang.String,boolean,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceWindows:long(java.lang.String,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceOS:long(java.lang.String,int,boolean,long)
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long(java.lang.String,long)
org.apache.commons.io.FileSystemUtils.freeSpace:long(java.lang.String)
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long(java.lang.String,long)
org.apache.commons.io.FileSystemUtils.freeSpace:long(java.lang.String)
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long(java.lang.String,long)
org.apache.commons.io.FileSystemUtils.freeSpace:long(java.lang.String)
org.apache.commons.io.FileSystemUtils.freeSpaceKb:long(java.lang.String,long)
org.apache.commons.io.FileSystemUtils.freeSpace:long(java.lang.String)
```

This query gives us an answer to the question, where is the data
comming from? And since the methods are public, the data is
potentially controllable by us. Still we need to answer if the data
actually flows from the parameter of the methods to the `exec` method.
For this we can mark the results of the above query as source, we just
need to exchange `.fullName.p` with `.parameter`, while the sink is
our `exec` method.

```
ocular> val source = cpg.method.name("exec").repeat(m=>m.caller).until(m=> m.isPublic.parameter.evalType("java.lang.String")).emit().parameter 

ocular> val sink = cpg.method.name("exec").parameter 

ocular>sink.reachableBy(source).flows.p 
```

Along other flows we find and we know that `org.apache.commons.io.FileSystemUtils.freeSpace:long(java.lang.String)` is publically available and a data flow between this parameter and `exec` exists.
```
------ Flow with 15 elements ------
path 	 142 	 freeSpace 	 org/apache/commons/io/FileSystemUtils.java
path 	 143 	 freeSpace 	 org/apache/commons/io/FileSystemUtils.java
path 	 259 	 freeSpaceOS 	 org/apache/commons/io/FileSystemUtils.java
path 	 269 	 freeSpaceOS 	 org/apache/commons/io/FileSystemUtils.java
path 	 381 	 freeSpaceUnix 	 org/apache/commons/io/FileSystemUtils.java
path 	 401 	 freeSpaceUnix 	 org/apache/commons/io/FileSystemUtils.java
param1 	  	 <operator>.assignment 	 N/A
param0 	  	 <operator>.assignment 	 N/A
cmdAttribs[2] 	 401 	 freeSpaceUnix 	 org/apache/commons/io/FileSystemUtils.java
cmdAttribs 	 398 	 freeSpaceUnix 	 org/apache/commons/io/FileSystemUtils.java
cmdAttribs 	 473 	 performCommand 	 org/apache/commons/io/FileSystemUtils.java
cmdAttribs 	 484 	 performCommand 	 org/apache/commons/io/FileSystemUtils.java
cmdAttribs 	 537 	 openProcess 	 org/apache/commons/io/FileSystemUtils.java
cmdAttribs 	 538 	 openProcess 	 org/apache/commons/io/FileSystemUtils.java
param0 	  	 exec 	 java/lang/Runtime.java
```
