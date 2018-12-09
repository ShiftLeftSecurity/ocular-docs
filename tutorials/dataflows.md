# Finding and processing data flows with Ocular

Generate a CPG for jenkins

```
$ cd ~/bin/shiftleft-cmd/subjects
$ wget http://mirrors.jenkins.io/war/latest/jenkins.war
$ time ./java2cpg.sh ~/bin/shiftleft-cmd/subjects/jenkins.war -nb -w jenkins,org.jenkins,org.jenkinsci,lib.jenkins
```

Start ocular and load the CPG

```
$ ./ocular.sh
ocular > loadCpg("cpg.bin.zip")
```
