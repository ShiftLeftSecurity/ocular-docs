# Interfacing with Ocular

Showing our results to the outside world is a key to collaboration and
thus success! We want to make it as easy as possible for you! The
following commands show you how to talk and represent your results, to
the outside world.

## Passing scripts to ocular

Ocular can be used non-interactively, that is, you can pass scripts to
Ocular, it will run those scripts and exit. To try this out, place the
following into `test.sc`:

```
@main def exec(jarFile: String, outFile: String) = {
  loadCpg(jarFile)
  cpg.namespace.l |> outFile
}

```

You can place arbitrary Scala code into `test.sc` and use the `|>`
operator to pipe output into files. The script can be run as follows.

```
	./ocular.sh --script script.sc --params jarFile=/fullpath/to/file.jar,outFile=out.cpg`
```

## Writing JSON and pretty-printed JSON

```
cpg.method.toJson |> "/tmp/foo" 
```

```
cpg.method.toPrettyJson |> "/tmp/foo"
```

## Appending to files

```
cpg.method.toJson ||> "/tmp/foo" 
```

```
cpg.method.toPrettyJson ||> "/tmp/foo"
```
