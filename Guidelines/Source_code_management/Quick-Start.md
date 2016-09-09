<!--TOC-->

# Table of Contents
* [Obtain Git repository](#obtain-git-repository)
  * [General Setup](#general-setup)
  * [Additional Setup for Adding Another Remote](#additional-setup-for-adding-another-remote)
* [Status](#status)
* [New feature branch](#new-feature-branch)
* [Switching branches](#switching-branches)
* [Other branches](#other-branches)
* [diff/log](#difflog)
* [Revert last commit](#revert-last-commit)
* [Comparing Branches](#comparing-branches)
* [Branch completion](#branch-completion)

<!--TOC-->

This guide gives a quick introduction to using Git with `CGAL`.

Beyond, it is valuable to read more "getting started" information at
[Git - SVN Crash Course](http://git-scm.com/course/svn.html). There is
also plenty of nice documentation on the web, e.g. the [Git community
Book](http://book.git-scm.com/). Some fundamental concepts are nicely
explained
[here](http://eagain.net/articles/git-for-computer-scientists/), and,
about Git and Tortoise Git [there (nice course notes, in
french)](http://marc.chevaldonne.free.fr/ens_rech/Csharp_XML_GI_2010_2011_files/Quick%20Guide%20Git%20TortoiseGit.pdf).

In the following commands of the command-line client of Git (`git` on
Linux/Mac OS, `git.exe` on Windows cygwin) are given. They naturally
translate to GUI-guided actions. Intuitive GUIs exists. For instance,
for **Windows** (TortoiseGit, which is similar to TortoiseSVN, or
[GitExtensions](http://gitextensions.github.io/), that integrate
into Visual Studio) and **Mac OSX** (Sourcetree) or **both** (SmartGit)
- and many others. Feel free to explore the web and choose a GUI to your
preference and wallet (or stay with the command-line client). Cloning,
branching, committing, merging is equivalent (or even simpler) in GUIs.
Please refer to tutorials on the web how to install them (e.g. for
[TortoiseGit](http://robertgreiner.com/2010/02/getting-started-with-git-and-tortoisegit-on-windows/)).
See also our [FAQ](Git-FAQ#which-clients).

Beyond the following, and for reference reasons, check the
[http://byte.kde.org/\~zrusin/git/git-cheat-sheet-medium.png Git Cheat Sheet](http://byte.kde.org/~zrusin/git/git-cheat-sheet-medium.png Git Cheat Sheet)

## Obtain Git repository

### General Setup

Jenny has Git installed. Next Jenny needs to set **once** some basic
variables. She uses the following commands (while replacing personal
information!):

``` {.bash}
# required variables
> git config --global user.name "Jenny Doe" 
> git config --global user.email "jenny.doe@example.com"
# useful variables
## pretty
> git config --global color.branch auto
> git config --global color.diff auto
> git config --global color.interactive auto
> git config --global color.status auto
## religious statement about editor preference
> git config --global core.editor "emacs"
```

`git config --global` stores the values for these variables in a Jenny's
`.gitconfig` file.

Now Jenny is ready to get her own local repository by **cloning** the
remote repository on our server:

``` {.bash}
> git clone -o cgal-dev git@github.com:CGAL/cgal-dev.git my_cgal_folder  #If you are NOT a CGAL student
> # or
> git clone -o cgal-public-dev git@github.com:CGAL/cgal-public-dev.git my_cgal_folder  #If you are a CGAL student
```

Master and release branches are not writable by regular developers.
Jenny needs to add another remote and add a local master branch:

``` {.bash}
> cd my_cgal_folder
> git remote add cgal git@github.com:CGAL/cgal.git
> git fetch --all
> git checkout -b master cgal/master
```

The local repository is a full clone of the remote repository which
includes all branches and tags, and is stored in very efficient
compressed fashion. Its size is about 850 MB. Cloning also checks out
the integration branch.

At this point we also *strongly recommend* to run

``` {.bash}
> cd my_cgal_folder
> ./Scripts/developer_scripts/cgal_git_update_hooks_for_client
```

which installs a pre-commit hook to Jenny's local repository (Windows
users: there is a git-bash in the context menu of Git in which you can
run the script). The hook gives you *early alerts* for commit that will
be rejected by the central repo when you try to push your changes to it.

With

``` {.bash}
> git branch
* master # the star marks the current branch
integration
```

Jenny sees on which branch she is currently working on. Here she is
working on the **local** `master`:

``` {.bash}
> ls
AABB_tree
Algebraic_foundations
Algebraic_kernel_d
...
iostream
kdtree
wininst
```

### Additional Setup for Adding Another Remote

You can add another remote like this:

``` {.bash}
# go into your cgal git repository folder and then
> git remote add cgal-public-dev git@github.com:CGAL/cgal-public-dev.git
# After that
>  git fetch cgal-public-dev 
#brings in all branches from the cgal-public-dev repository.
>  git branch -a 
#will show the branches:
  remotes/cgal-public-dev/hooks-for-clients
  remotes/cgal-public-dev/hooks-on-server
  remotes/cgal-public-dev/gsoc2013-XXXX-student
 
#Starting to work on such a branch works as for branches on "cgal-dev" - see the guidelines, and replace "cgal-dev" with "cgal-public-dev" - or add "cgal-public-dev" to pushes
  git push cgal-public-dev gsoc2013-XXXX-student
 
#If you want to push a branch from gsoc to the main repository, use
  git push cgal-dev gsoc2013-XXXX-student
```

## Status

To check the status of the current branch she uses

``` {.bash}
> git status
# On branch master
nothing to commit (working directory clean)
```

`git status` also shows the status of all files (modified, staged,
deleted, etc) in the repository - no matter whether Jenny is the root
level of the repository or in a subdirectory.

## New feature branch

In order to start a feature development she comes up with a name for a
branch following our <b>[naming conventions](Naming-Recommendations)</b>,
here `Mesh_9-jenny`, and then uses

``` {.bash}
> git checkout -b Mesh_9-jenny --no-track cgal/master
Switched to a new branch 'Mesh_9-jenny'
> git branch
master
* Mesh_9-jenny
```

This command creates a local branch `Mesh_9-jenny` from the master
branch and checks it out. It is shorthand for:

``` {.bash}
> git checkout master
> git branch Mesh_9-jenny
> git checkout Mesh_9-jenny
```

Next, Jenny can alter files and commit changes and check the status
again with `git status`. Before commiting she uses

``` {.bash}
> git add [myfiles.hpp]
> git commit
[Mesh_9-jenny 6341737c37db] Message
1 file changed, 1 insertion(+)
```

`git add` to *add* a file to the index (list of files scheduled for next
commit), while `commit` really executes the commitment (of all indexed
files - in the version they have been added to the index!)

'''Remarks: ''' \* Note that every "commit" is only done to the
**local** repository. It stores all previous and new commit information.
\* Each commit *gets assigned a SHA-1 hash* (a cryptographically
tamper-proof signature of your file contents), here
`6341737c37dbcd932e6646eac2347d9402f12b07`. For convenience these hashes
can be abbreviated with the first chars only (seven is usually
sufficient to uniquely identify a commit). It is also mentioned to which
branch Jenny has committed (here "Mesh\_9-jenny"). \* In case you want
to *abort* the commit, you just don't write a **commit message**!

The next step is synchronize the local changes with the remote location.

In order to avoid wrong pushes to `master` Jenny adds this to her
configuration

``` {.bash}
> git config --add push.default simple
# In even older Git version (<1.7.11) 'simple' must be replaced with 'current' or 'tracking' (for even older versions)
```

This refuses a push if the local and remote branchnames do not match
(check whether other repositories also need this option - or whether you
want to add it to you global `.gitconfig` - can be done by adding
`--global` option). Though this should now happen if she **initially
pushes with** the `-u` flag. Jenny sends the branch onto the remote
repository she cloned from ("cgal-dev"):

``` {.bash}
 > git push -u cgal-dev Mesh_9-jenny 
```

Let's explain this in more detail: The `push` mirrors the local branch
`Mesh_9-jenny` into the remote repository "cgal-dev". This way her
branch becomes available to everybody with access to the "cgal-dev"
repository - and her commits get also backup'ed in the remote location.
The `-u` option not only publishes the branch on the remote, but also
sets it as a *tracking branch* of her local branch. Every future

``` {.bash}
 > git push 
```

will publish her local changes onto *cgal-dev*.

## Switching branches

Jenny now has two local branches, she is in Mesh\_9-jenny and she can
switch back to master with

``` {.bash}
> git checkout master 
```

## Other branches

By using

``` {.bash}
> git branch -a
* Mesh_9-jenny
  master
  remotes/cgal/master
  remotes/cgal-dev/Mesh_9-jenny
  remotes/cgal-dev/Kernel-rewrite_functors-adam
```

can see a list of all local branches and all branches on the configured
*remotes*.

If Jenny wants to work on one of those remote branches she uses

``` {.bash}
> git checkout -b Kernel-rewrite_functors-adam cgal-dev/Kernel-rewrite_functors-adam 
```

That command creates a *local* branch named
`Kernel-rewrite_functors-adam` as a copy of the current state of the
*remote* branch `cgal-dev/Kernel-rewrite_functors-adam`. Be careful that
the branch name appears twice: after `-b`, and after `cgal-dev/`. The
branch name after `-b` is actually a free name you can choose, where as
`cgal-dev/some_name` refers to an existing referenced branch on the
remote repository `cgal-dev`.

Again she alters files:

``` {.bash}
> git add [otherfiles.hpp]
> git commit
```

And finally she pushes her changes back to the remote location.

As some Git versions (e.g. 1.7.9.5) set no default upstream branch for
push, Jenny adds this to her configuration

``` {.bash}
> git config --add push.default simple
# In even older Git version 'simple' must be replaced with 'current' or 'tracking' (for even older versions)
```

If this is set (or the Git version is new enough) the following suffice
to push changes to `cgal-dev`

``` {.bash}
 > git push 
```

Note that here the tracking of the upstream branch
`cgal-dev/Kernel-rewrite_functors-adam` is correct, thus a `git push`
suffices.

In order to update her branch from the remote repository, Jenny gets the
changes into her local repository with:

``` {.bash}
> git fetch 
> git merge 
```

We refer Jenny for full details on how to work with branches to [the
section on how to use branching in Git for code
development](Developing-Features-with-Git#how-to-develop-several-features-in-parallel).

## diff/log

`git log` is used to inspect the revision history of a file, repository
or branch.

``` {.bash}
> git log # plain log of current repository
> git log --follow -- filename # file history, including before renames
> git log -p # add diffs
> git log --since=2.weeks # commits of the last 2 weeks
```

`git diff` is used to inspect the changes between commits

``` {.bash}
> git diff # the difference between the working copy and the index
> git diff --cached # the difference between the index and the last commit
> git diff HEAD HEAD^ --stat # the changed files between the last commit and the commit that came before
> git diff HEAD HEAD^ -- my_file.h # same as above, but just for my_file.h
> git diff master..my_branch # diff between master and my_branch
```

## Revert last commit

There are several scenarios: \* the bad commit wasn't pushed yet \* the
bad commit was already pushed to a remote \* the commit is not really
that bad, wasn't pushed yet and can be fixed easily

In the first case `git reset --hard HEAD^` resets your repository to the
commit just before the last one. All changes in the last commit will be
lost.

In the second case, `git revert commit_name` followed by a `git push` is
one of the less intrusive options. It will create a new commit that
reverts the old one.

In the third option `git commit --amend` can be used to modify the
current HEAD commit. This is useful if something has been forgotten in
the last commit.

## Comparing Branches

Sometimes it is useful to see if a branch is actually a subset of
another, e.g. if all commits of a local branch have been pushed to the
remote.

``` {.bash}
 > git log cgal-dev/my_branch..my_branch
 # if empty, everything is pushed 
```

Similarily,

``` {.bash}
 > git log --branches --not --remotes 
```

will show all commits on local branches that are not present on remote
branches. The options `--decorate --graph` can be added to decorate the
result of the `git log` command with the name of the branches involved,
and the drawing (in ASCII) of the graph of commits.

The above commands are also useful to check if a release branch is a
subset of an integration branch and which commits are still candidates
to be picked.

## Branch completion

The Bash completion system offers completion for branches and prompt
modifications (to display current branch in prompt). Google is your
friend.
