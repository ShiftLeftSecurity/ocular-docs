# Constructing beautiful CPGs from Java Archives

In this tutorial, we show how to create code property graphs (CPGs) from 
Java archives (JAR or WAR files). To be more precise, we want to use the Ocular
toolset to create efficient CPGs to improve your results and speed up 
the analysis, no matter if you use the trial or the full version of Ocular. 

Ocular is shipped with our java frontend tool named `java2cpg`. While java2cpg, 
with default settings is doing a great job at creating CPGs from java projects, 
it is still easily customizable to adjust it to your needs. 

We want to show you the impact of parameterizing java2cpg, by getting 
our hands dirty on a sample war, the jenkins project war. The shell-commands 
below are downloading the war project from the jenkins site, which has a size 
of around 73M at the time of writing this tutorial. 

```
	cd ~/bin/ocular/subjects
	wget http://mirrors.jenkins.io/war/latest/jenkins.war
	cd ..	
```

Now we are ready do a first analysis on the war file but instead 
of simply running `java2cpg` on `subjects/jenkins.war`, we use
`java2cpg`'s *dry-run* feature. This feature allows us to determine
which Java packages `java2cpg` will *include* (Including) and *skip* (Skipping) 
for CPG construction, without actually performing time and memory intense calculation.

`-dr` is the flag we use to make java2cpg perform a dry-run. 

```
	$ time ./java2cpg.sh subjects/jenkins.war -dr | egrep '(Including|Skipping)' > packages.txt
	$ wc -l packages.txt
	1303 packages.txt
	$ grep 'Including' packages.txt| wc -l
	240
	$ grep 'Skipping' packages.txt| wc -l
	1063
```

We see that `java2cpg` has recursively unpacked the WAR to detect a
total of 1303, 240 if which were automatically selected for inclusion
in the CPG, while 1063 packages have been discarded via the default
blacklist. The next step is to review the skipped packages to see if any relevant packages have been skipped.

```
grep 'Skipping' packages.txt| less
```

See for yourself that no relevant packages have been excluded, as the default blacklist contains only names of common libraries and frameworks such as `spring` or `log4j`. Next, we examine the list of included packages.

```
grep 'Included' packages.txt| less
```

Among the packages `jenkins`, `lib.jenkins`, `org.jenkins`, and `org.jenkinsci`, libraries such as `gnu.crypt` and `winstone` were apparently not on the blacklist and would therefore be included in the CPG. This is not desirable, as we are interested in finding vulnerabilities in Jenkins, and not in one of its dependencies. In this particular case, a good choice is therefore to simply use a whitelist that includes jenkins and hudson packages (hudson is Jenkin's predecessor).

```
time ./java2cpg.sh subjects/jenkins.war -nb -w jenkins,org.jenkins,org.jenkinsci,lib.jenkins,hudson
```

The resulting CPG is 44 MB in size, as opposed to the CPG generated using the default blacklist, which is 70MB in size. Subsequent analysis of this CPG will now focus on the Jenkins code and skip libraries such as `hudson`, and `gnu.crypt`, which were included originally only due to lack of blacklisting.


However, without telling it which classes one is interested in, the resulting 
CPGs may contain to little code, or too much. There is no way for the tool 
to know which classes you are interested in, however, the tool does offer
capabilities for the user to quickly select Java packages of
interest. We demonstrate these capabilities in this tutorial.


It is important to note that blacklisting a package is *not* removing the calls to this package. Blacklisting just avoids an analysis of the package and thus add the content of the package to the CPG. Meaning, if we blacklist hudson we still see the calls to this dependency but not what is inside of these calls. 
