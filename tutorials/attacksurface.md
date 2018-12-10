# Exploring program structure with Ocular

List all files

```
	cpg.file.name.l.sorted.distinct.size
```

List files linked to internal namespaces, that is, Java packages that
are internal, meaning that we have the code for them in this code
property graph.

```
	cpg.file.filter(_.typeDecl.internal).name.l.sorted.distinct 
```

Get classes with largest number of direct derived classes

```
	cpg.typeDecl.map(x => (x.start.derivedTypeDecl.l.size, x.fullName)).l.sorted 
```

Traversing type hierarchies: classes that inherit from `SimpleBuildStep`

```
cpg.typeDecl.name(".*SimpleBuildStep.*").derivedTypeDecl.fullName.l.size
```

Identify libraries used by the application

```
	browse(cpg.method.external.namespace.name.l.sorted.distinct)
```

By taking into account Java naming conventions, we can obtain a better
overview. Typicallythe first two namespace levels are the domain of
the package provider, while the third is the name of the component. We
therefore implement a short method named `trimpack` that shortens
package names to 3, and evaluate this method on each of the namespaces
returned by our query:

```
	def trimpack = { x :String => val parts = x.split("\\."); if(parts.size > 2) { parts.slice(0,3).mkString(".") } else { x } }
	cpg.method.external.namespace.name.l.map(trimpack(_)).distinct.sorted
```


We can filter out namespaces we are not interested in. For example, to
filter out the `java` namespace, we can issue the following command.

```
	cpg.method.external.namespace.nameNot("java.*").name.l.map(trimpack(_)).distinct.sorted
```

In particular, we see that the rather non-standard library Stapler is
used to implement Web routes. Trivia: there is a typo in a package
name.

"org.kohsuke.stapler",
"org.koshuke.stapler",

Methods that receive StaplerRequests and have no callers.

```
	cpg.parameter.evalType(".*StaplerRequest.*").method.filterNot(_.caller).name.l
```

Methods that retrieve parameters from Stapler requests.

```
	cpg.method.fullName(".*Stapler.*getParameter.*").caller.fullName.l
```

Calls to get parameter

```
	cpg.method.fullName(".*Stapler.*getParameter.*").callIn.code.l
```

// Call chains to "set" calls on stapler response

```
	cpg.method.fullName("org.kohsuke.stapler.StaplerResponse.*set.*").calledBy(cpg.method).newCallChain.l.map(x => x.methods.map(_.fullName))
```

Most called methods

```
	cpg.method.map(x => (x.start.callIn.l.size, x.fullName)).l.sorted
```

All methods that perform permission checks
```
	cpg.method.fullName(".*AccessControlled.checkPermission.*").caller.fullName.l
```

Let's use this to draw a map.
