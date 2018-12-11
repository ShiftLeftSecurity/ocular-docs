# FuzzyC2CPG

`fuzzyc2CPG` is a fuzzy C parser that generates Code Property Graphs (CPG).

# Usage

```bash
usage: ./fuzzyc2cpg.sh
 -i,--input <arg>    comma-separated list of input directories
 -o,--output <arg>   path to output file (default is cpg.bin.zip in your
                     current working dir)
```

`fuzzyc2CPG` converts C source-code into Code Property Graphs, an intermediate graph representation of code.

# Example

The following command generates the CPG file `cpg.bin.zip` from the C project located in `/tmp/c-project` and places 
it in the current working directory.

```bash
./fuzzyc2cpg.sh -i /tmp/c-project
```

