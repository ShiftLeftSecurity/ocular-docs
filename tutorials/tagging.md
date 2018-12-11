# Using Ocular's tagging API

Ocular provides a tagging API that allows you to attach key value
pairs to nodes and edges, and retrieve this in subsequent
queries. This is useful, e.g., to mark methods or variables of
interest and later run queries over all selected nodes of this type.

```
 cpg.method.fullName(".*write.*").newTagNodePair("foo").store
 save
```

```
cpg.tag.name("foo").method.l
```
