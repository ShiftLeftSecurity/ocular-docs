# FuzzyC2CPG

`fuzzyc2CPG` is a fuzzy C parser that generates Code Property Graphs (CPG).

# Usage

```bash
fuzzyc2cpg <dir 1> ... <dir n>
```

`fuzzyc2CPG` converts C source-code into Code Property Graphs, an intermediate graph representation of code.

# Example

The following command generates the CPG file `cpg.bin.zip` from the C project located in `/tmp/project` and places 
it in the current working directory.

```bash
./fuzzyc2cpg.sh /tmp/project
```

