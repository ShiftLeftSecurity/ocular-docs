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
