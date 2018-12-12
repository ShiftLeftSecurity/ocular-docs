# Scanning for deserialization sinks

OWASP provides a cheat sheet for typical deserialization sources.

https://www.owasp.org/index.php/Deserialization_Cheat_Sheet#Java

We can search for references of these methods as follows.

```
val sinkMethods = cpg.method.or(
      _.fullName(".*(XMLdecoder|ObjectInputStream).*readObject.*"),
      _.fullName(".*XStream.*fromXML.*"),
      _.fullName(".*readObjectNodData|readResolve|readExternal.*"),
      _.fullName(".*ObjectInputStream.*readUnshared.*"))

sinkMethods.calledBy(cpg.method).newCallChain.p

```

The OWASP guide also suggests to harden classes that derive from
`Serializable`. You can use Ocular to identify classes that directly
inherit from `Serializable` as follows.

```
cpg.typeDecl.name("Serializable").derivedTypeDecl.fullName.l
```

For classes that inherit from `Serializable` either directly or
indirectly, you can use the following query:
```
cpg.typeDecl.name("Serializable").derivedTypeDeclTransitive.fullName.l
```
