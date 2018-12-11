# Interfacing with Ocular

Showing our results to the outside world is a key to collaboration and thus success! We want to make it as easy as possible for you! The following commands show you how to talk and represent your results, to the outside world. 

## Writing 

```
cpg.method.toJson |> "/tmp/foo" 
```

```
cpg.method.toPrettyJson |> "/tmp/foo"
```

## Appending

```
cpg.method.toJson ||> "/tmp/foo" 
```

```
cpg.method.toPrettyJson ||> "/tmp/foo"
```
