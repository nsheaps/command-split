# command-split
An easily extendible script to enable you to chain commands together that get executed as scripts. The best use of this is to split commands for an app into small logical segments so that it enforces an easy to maintain structure.

### How to use
Below you can find some examples of use. This is a very simple script so it's uses are minimal, but through some creative naming you can make it link a lot of components together.

#### Simplest use
Executes ~/command-split mylittlepony
```bash
$ ls -lha
total 0
drwxr-xr-x   3 nsheaps nsheaps   102B Mar  5 00:32 .
drwxr-xr-x+ 71 nsheaps nsheaps   2.4K Mar  5 00:32 ..
-rwxr-xr-x   1 nsheaps nsheaps     0B Mar  5 00:32 mylittlepony

... insert code into mylittlepony ...

$ command-split mylittlepony
```

It passes arguments to the next command, too!
```bash
$ ls -lha
total 0
drwxr-xr-x   3 nsheaps nsheaps   102B Mar  5 00:32 .
drwxr-xr-x+ 71 nsheaps nsheaps   2.4K Mar  5 00:32 ..
-rwxr-xr-x   1 nsheaps nsheaps     0B Mar  5 00:32 tell-my-mom

... insert code into tell-my-mom ...

$ command-split tell-my-mom "I Really Like Pie"
```

#### Secondary commands
Secondary commands give you the capabilty to chain commands together with a variety of supported directory structures.

TODO: Insert more verbose examples
Executes `./download/meowmers` with the working directory `./`.
```bash
$ ls
download
$ cd download; ls
meowmers
$ command-split download meowmers
```
Executes `./launch/launch` chrome. Arguments work here too! The script here gets executed with the working directory as `./launch/`.
```bash
$ ls
launch
$ cd launch; ls
launch
$ command-split launch chrome 

```
Executes `./launch-chrome`.
```bash
$ ls
launch-chrome
$ command-split launch chrome 

```

### How it works
The script first sees how long the arguments are. If they are 2 or longer it treats the first two as a possible command. Otherwise, it just attempts to execute the first argument in the current working directory.

If it's two or longer it checks for and executes the scripts in the following order, assuming that you run `./command-split <argument1> <argument2> <argument3> ... <argumentN>`

1. `cd ./<argument1>/ && ./<argument2> <argument3> ... <argumentN>`
2. `cd ./<argument1>/ && ./<argument1> <argument2> <argument3> ... <argumentN>`
3. `./<argument1>-<argument2> <argument3> ... <argumentN>`
4. `./<argument1> <argument2> <argument3> ... <argumentN>`

The check only goes 2 arguments deep to ensure that you don't get files named `push-github-cleopatra-origin-master`.

It's designed to be aliased and symlinked to from /usr/local/bin to let you do neat fancy stuff like:
```bash
$> ls
command-split
git -> ./command-split
git-add
git-branch
git-help
git-push
$> git push origin master
```
or stuff like
```bash
# Directory structure
# |- ./ci  #shell script that changes working dir to ./bin and executes ./command-split
# |-+ ./bin/
# | |- ./command-split
# | |- ./download
# | |-+ ./build/
# | | | ./build
# | | | ./django
# | | | ./static-assets
# | |
# | |-+ ./test/
# | | | ./test
# | | | ./django
# | | | ./smoke
# | |
# | |-+ ./deploy/
# | | | ./deploy
# | | | ./django
# | | | ./static-assets
# |
# |-+ 
$> ci download static-assets-project # this project's type is a static-assets project
Downloading 'static-assets-project' to '~/dev/static-assets-project'...
50 MB downloaded.
$> ci build static-assets-project
> Building 'static-assets-project' as project type 'static-assets'
26 new assets generated.
$> ci test static-assets-project
Error: No test executable defined for project type 'static-assets'
$> ci deploy static-assets-project
> Deploying 'static-assets-project' as project type 'static-assets'
> Connecting to CDN...
Connected to Akamai
> Uploading new assets...
[==========] 100%
Bytes copied: 49 MB
> Changing pointer folder to pull from new static assets.
> Verifying new configuration.
Verified.
> Deploying changes to Akamai.
Done. Old version: 492, New version: 493
> Verifying new build info at http://static.example.com/build-info
Build number verified: 493.

Deployed 'static-assets-project' to Akamai.
```


=== Possible future features
==== Unknown extensions
This is a low priority because you're most likely on a *nix machine that can run executable files without an extension.

