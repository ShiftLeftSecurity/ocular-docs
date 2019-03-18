## Introduction

This tutorial teaches the basics of navigating in the **Code Property
Graph (CPG)**. Ocular can also be used to navigate Security Profile (SP)
which is generated on top of CPG. The tutorial focuses on analysis using
CPG and demonstrates how the tooling can be used to:

* Interactively query the code property graph to uncover attack surface
* Formulate ad-hoc queries to identify vulnerabilities

The philosophy behind our tooling is that, while creating a
"one-fits-all" vulnerability scanner borders on the impossible, you
can certainly provide the tooling that vulnerability researchers
require to explore code bases in order to determine vulnerability
patterns, formulate these patterns in concise and expressive
languages, and persist them, such that code can be automatically
scanned for these patterns in the future. Instead of only showcasing
the tooling's default capabilities, in this tutorial we also
demonstrate the many ways in which the tooling can be adapted and
extended to suit your specific needs.

## **Step 1:** Install Ocular

### Prerequisites

Ocular runs on top of the Java virtual machine. Please make sure you have a Java Runtime Environment >= 1.8 installed.

### Instructions

Begin by decompressing the provided ZIP file `ocular-distribution.zip`. This will create the directory `ocular-distribution`.

```bash
unzip ocular-distribution.zip
cd ocular-distribution
```

Run the installer and follow the prompts:

```bash
bash ./install.sh
```

The install script will:

* ask you where you want to install it to (defaults to `~/bin/ocular`)
* check if there is an existing installation and offer to delete it
* unpack the ShiftLeft dynamic policy to `~/.shiftleft/policy/dynamic` and offer to delete it, if it already exists
* unpack the ShiftLeft static policy to `~/.shiftleft/policy/static` and offer to delete it, if it already exists.
* not touch anything outside these directories (installation and policy)

### Additional Configuration for Large Projects

Code analysis can require lots of memory, and unfortunately, the Java Virtual Machine (JVM) does not pick up the available amount of memory by itself. While tuning Java memory usage is a discipline in its own right, it is usually sufficient to specify the maximum available amount of heap memory via the Java virtual machine's `-Xmx` flag. The easiest way to achieve this globally is by setting the environment variable `_JAVA_OPTS` as follows:

```bash
export _JAVA_OPTS="-Xmx$NG"
```
where `$N` is the amount of memory in gigabytes. You can add this line to your shell startup script, e.g., `~/.bashrc` or `~/.zshrc`.

> Note: The above option will affect all JVM instances that are started henceforth on the machine.

In order to restrict the JVM option to only the parts of Ocular tools that you intend to use, you can provide it individually as well (such as in Step 3) For example to set 12 GB available heap memory to `java2cpg`, `cpg2sp` or `ocular`, we can just provide this option directly:

```bash
./ocular.sh -J-Xmx12g
```

## **Step 2:** A quick look at the target application

This tutorial is based on the sample application "Hello-ShiftLeft"
which you can find in the directory `subjects` provided with the
ShiftLeft Command Line Tools distribution.

Hello-Shiftleft is a Spring-based Web application which contains
different sample vulnerabilities, including typical injection
vulnerabilities and leakages of sensitive information. Throughout this
guide, we focus on an object deserialization vulnerability in the
`AdminController` shown in the listing below.

```java
...
@Controller
public class AdminController {
...
@RequestMapping(value = "/admin/login", method = RequestMethod.POST)
public String doPostLogin(
  @CookieValue(value = "auth", defaultValue = "notset") String auth,
  @RequestBody String password, HttpServletResponse response,
  HttpServletRequest request) throws Exception {
...
if (!auth.equals("notset")) {
   if(isAdmin(auth)) {
     request.getSession().setAttribute("auth",auth);
     return succ;
   }
 }
 ...
}
...
private boolean isAdmin(String auth) {
try {
	ByteArrayInputStream bis = new ByteArrayInputStream(
  	Base64.getDecoder().decode(auth));
	ObjectInputStream objectInputStream = new ObjectInputStream(bis);
	Object authToken = objectInputStream.readObject();
  return ((AuthToken) authToken).isAdmin();
	} catch (Exception ex) {
   	System.out.println(" cookie cannot be deserialized: "
                      +ex.getMessage());
   	return false;
	}
}
...
```

In this code fragment, a cookie is received via HTTP and eventually
deserialized to create a Java object, an optimistic practice that can
often be exploited by attackers for arbitrary code execution.

## **Step 3:** Uncovering Attack Surface

The code property graph contains information about the processed code
on different levels of abstraction, from dependencies, to type
hierarchies, control flow, data flow, and instruction-level
information. The CPG can be queried interactively via Ocular or via
non-interactive scripts. We now illustrate interactive querying,
however, all queries can also be used as-in in interactive scripts.

The CPG is created via the `createCpg` command:

```scala
createCpg("subjects/hello-shiftleft-0.0.1-SNAPSHOT.jar")
```

This creates an object named `cpg`, which provides access to the code
property graph. We begin by exploring the program dependencies:

```scala
cpg.dependency.name.l
```

This provides a list of all dependency names. We support functional
combinators. For example, to output (name, version) pairs, we can use
the following expression:

```scala
cpg.dependency.map(x => (x.name, x.version)).l
```

which yields
```scala
List[(String, String)] = List(
  ("zt-exec", "1.9"),
  ("httpclient", "4.3.4"),
  ("lombok", "1.16.6"),
  ("commons-io", "2.5"),
  ("joda-time", "unknown"),
  ("jasypt", "1.9.2"),
  ("jackson-databind", "unknown"),
  ("spring-boot-starter-web", "unknown"),
  ("jasypt-spring-boot-starter", "1.11"),
  ("spring-boot-starter-test", "unknown"),
  ("spring-web", "unknown"),
  ("hsqldb", "unknown"),
  ("jackson-mapper-asl", "1.5.6"),
  ("spring-boot-starter-actuator", "unknown"),
  ("spring-boot-starter-data-jpa", "unknown"),
  ("logback-core", "1.1.9"),
  ("spring-web", "4.3.6.RELEASE"),
  ("tomcat-embed-websocket", "8.5.11"),
  ...
)
```

It is also possible to process CPG sub graphs via external programs by
exporting them to JSON. For example,

```scala
cpg.dependency.toJson |> "/tmp/dependencies.json"
```

dumps dependency information into the file "/tmp/dependencies.json" is
JSON format. Fields of the CPG can be queried using regular
expressions. For example, to determine whether an application uses the
spring framework, a quick query could be

```scala
cpg.dependency.name(".*spring.*").l.nonEmpty
=> true
```

Since the application uses Spring, it makes sense to look for the
typical Java annotations that indicate attacker-controlled variables.

```scala
cpg.annotation.name(".*(CookieValue|PathVariable).*").l
```

From annotations, we can jump to parameters using these annotations:

```scala
cpg.annotation.name(".*(CookieValue|PathVariable).*").parameter.name.l
```

which yields

```scala
List[String] = List("customerId", "customerId", "customerId", "accountId", "accountId", "accountId", "accountId", "auth", "auth")
```

We can now track these attacker-controlled variables to see all data flows originating at them. To do this, we first define the set of sinks to be all parameters annotated by CookieValue or PathVariable:

```scala
val sources = cpg.annotation.name(".*(CookieValue|PathVariable).*").parameter
```

We then define the set of sinks to be all parameters:

```scala
val sinks = cpg.method.parameter
```

Finally, we enumerate all flows from sources to sinks:

```scala
sinks.reachableBy(sources).flows.p
```

The flows can be examined manually or automatically. For example, we can determine parameters we control as a result of data flows as follows:

```scala
sinks.reachableBy(sources).flows.sink.parameter.l
```

The query determines sinks reachable by sources and examines the corresponding data flows. The last flow element is extracted of each flow via the `pathElemens.last` directive, and the corresponding parameter is retrieved. The result of the query can be stored in a variable for further processing, which comes in handy when determining a large number of data flows:

```scala
val controlled = sinks.reachableBy(sources).flows.sink.parameter.l
```

We can now retrieve the parameter index ("ast child number" and method full name):

```scala
controlled.map(x => s"Controlling parameter ${x.order} of ${x.start.method.fullName.l.head}")
```

yielding

```scala
"Controlling parameter 1 of java.lang.Long.valueOf:java.lang.Long(long)",
"Controlling parameter 1 of java.lang.Long.valueOf:java.lang.Long(long)",
"Controlling parameter 1 of java.lang.Long.valueOf:java.lang.Long(long)",
"Controlling parameter 1 of java.lang.Long.valueOf:java.lang.Long(long)",
"Controlling parameter 0 of java.lang.String.equals:boolean(java.lang.Object)",
"Controlling parameter 1 of java.io.ObjectInputStream.<init>:void(java.io.InputStream)",
<b>"Controlling parameter 0 of java.io.ObjectInputStream.readObject:java.lang.Object()",</b>
"Controlling parameter 0 of io.shiftleft.model.AuthToken.isAdmin:boolean()",
"Controlling parameter 1 of io.shiftleft.repository.AccountRepository.findOne:java.lang.Object(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.AccountRepository.findOne:java.lang.Object(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.AccountRepository.findOne:java.lang.Object(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.AccountRepository.findOne:java.lang.Object(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.CustomerRepository.findOne:java.lang.Object(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.CustomerRepository.exists:boolean(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.CustomerRepository.exists:boolean(java.io.Serializable)",
"Controlling parameter 1 of io.shiftleft.repository.CustomerRepository.delete:void(java.io.Serializable)",
"Controlling parameter 1 of java.util.Base64$Decoder.decode:byte[](java.lang.String)",
"Controlling parameter 1 of io.shiftleft.controller.AdminController.isAdmin:boolean(java.lang.String)",
"Controlling parameter 1 of java.io.ByteArrayInputStream.<init>:void(byte[])",
"Controlling parameter 2 of javax.servlet.http.HttpSession.setAttribute:void(java.lang.String,java.lang.Object)",
...
```

In particular, we see that the instance parameter (with an index of 0)
of the method `ObjectInputStream.readObject` is controlled, that is,
the deserialization vulnerability exists. This shows a more
exploratory way of identifying the vulnerability.