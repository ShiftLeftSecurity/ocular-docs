# CSharp2CPG

CSharp2CPG is a ShiftLeft Command Line Tool used tool to convert C# sources to Code Property Graphs (CPG).

# Usage

```bash
csharp2cpg -i <csprojfilepath> [-o <outputlocation>]
```

# Description

CSharp2CPG converts C# source-code into Code Property Graphs, an intermediate graph representation of code. Currently, only the Protobuf format is supported.

# Examples

A code property graph named "cpg.bin.zip" suitable to be processed with other ShiftLeft Command Line Tools can be generated as follows:

```bash
csharp2cpg application.csproj
```

To write the CPG to a different file, the output location can be controlled via the parameter "-o":

```bash
csharp2cpg application.csproj -o cpg.bin.zip
```
