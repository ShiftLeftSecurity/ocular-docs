# Analyzing libraries

By default, `java2cpg` recursively unpacks jars to create one code
property graph that captures all components of the application. In
order to exclude libraries and frameworks, `java2cpg` applies a
blacklist of common libraries (e.g., Apache-commons libraries). This
means that, by default, `java2cpg` will return an empty CPG when run
on a blacklisted dependency.

To analyze a library, we can disable blacklisting with the `nb` flag.
As an example, we can create a CPG for commons-io
([mvnrepository](http://central.maven.org/maven2/commons-io/commons-io/2.5/commons-io-2.5.jar))
as follows:

```
./java2cpg.sh -f protobufzip -o commons-io-2.5.bin.zip commons-io-2.5.jar -nb

```

# Attack surface for libraries

For applications, we can automatically mark I/O sources and sinks via
the policy, e.g., we mark HTTP parameters, input read from files, or
data doing into sockets. For libraries, we want to additionally allow
marking all public method parameters as potential sources of
attacker-controlled data. This can be achieved via cpg2sp's `-m` flag:

```
 time ./cpg2sp.sh --cpg commons-io-2.5.bin.zip -o commons-io-2.5.sp -m 
```

This flag marks every parameter of `public` functions as source, to be
more specific as `attacker-controlled`. This allows you to define
conclusions such as the following to detect attacker-controlled data
that reaches deserializers.

```
CONCLUSION attacker-to-deserializer = FLOW DATA (attacker-controlled) -> IO (deserializer)
WHEN CONCLUSION attacker-to-deserializer => EMIT {
    title: "Attacker controlled data to deserialization",
    description: "Attacker controlled data is deserialized in this flow. ...",
    category: "a1-injection",
    score: "8.0"
}
```

This rule is already included in the default policy.

