# Constructing beautiful CPGs from Java Archives

In this tutorial, we learn how code property graphs (CPGs) can be
created from Java archives (JAR or WAR files) using the Ocular full or
trial version. While in principle, it is sufficient to run the
`java2cpg` tool, there are several options to tune CPG construction,
which help you focus subsequent analysis to the code you care about.

The `java2cpg` command line tool, included in Ocular, can be used to
translate JAR and WAR files into CPGs. However, without telling it
which classes one is interested in, the resulting CPGs may contain to
little code, or too much. There is no way for the tool to know which
classes you are interested in, however, the tool does offer
capabilities for the user to quickly select Java packages of
interest. We demonstrate these capabilities in this tutorial.

Let us begin by downloading a sample jar, in this case, the Jenkins
jar:

```
	cd ~/bin/shiftleft-cmd/subjects
	wget http://mirrors.jenkins.io/war/latest/jenkins.war
	cd ..	
```

Instead of simply running `java2cpg` on `subjects/jenkins.war`, we use
`java2cpg`'s *dry-run* feature. This feature allows us to determine
which Java packages `java2cpg` will *include* and *skip* for CPG construction, without actually performing time and memory intense calculation.

```
	$ time ./java2cpg.sh subjects/jenkins.war -dr | egrep '(Including|Skipping)' > packages.txt
	$ wc -l packages.txt
	1283 packages.txt
	$ grep 'Including' packages.txt| wc -l
	240
	$ grep 'Skipping' packages.txt| wc -l
	1043
```

We see that `java2cpg` has recursively unpacked the WAR to detect a
total of 1283, 240 if which were automatically selected for inclusion
in the CPG, while 1043 packages have been discarded via the default
blacklist. The next step is to review the skipped packages to see if any relevant packages have been skipped.

```
grep 'Skipping' packages.txt| less
```

See for yourself that no relevant packages have been excluded, as the default blacklist contains only names of common libraries and frameworks such as `spring` or `log4j`. Next, we examine the list of included packages.

```
grep 'Included' packages.txt| less
```

Among the packages `jenkins`, `lib.jenkins`, `org.jenkins`, and `org.jenkinsci`, libraries such as `hudson`, `gnu.crypt` and `winstone` were apparently not on the blacklist and would therefore be included in the CPG. This is not desirable, as we are interested in finding vulnerabilities in Jenkins, and not in one of its dependencies. In this particular case, a good choice is therefore to simply use a whitelist:

```
time ./java2cpg.sh subjects/jenkins.war -nb -w jenkins,org.jenkins,org.jenkinsci,lib.jenkins
```
The resulting CPG is 9.5 MB in size, as opposed to the CPG generated using the default blacklist, which is 14MB in size. Subsequent analysis of this CPG will now focus on the Jenkins code and skip libraries such as `hudson`, and `gnu.crypt`, which were included originally only due to lack of blacklisting.
