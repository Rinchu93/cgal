<!--TOC-->

# Table of Contents
* [Which clients?](#which-clients)
* [How much space do I need on my disk?](#how-much-space-do-i-need-on-my-disk)
* [How can I see which branch I am on?](#how-can-i-see-which-branch-i-am-on)
* [What if I want to work on multiple branches in parallel?](#what-if-i-want-to-work-on-multiple-branches-in-parallel)
* [How to remove old branches that are already merged](#how-to-remove-old-branches-that-are-already-merged)
* [How can I disable/change the pager used by some git commands?](#how-can-i-disablechange-the-pager-used-by-some-git-commands)
* [How can Git ignore files globally?](#how-can-git-ignore-files-globally)
* [How to create aliases for Git commands?](#how-to-create-aliases-for-git-commands)
* [How to search for the commits of a certain author?](#how-to-search-for-the-commits-of-a-certain-author)
* [My branch is really old. I would like to update it to get latest update from master.](#my-branch-is-really-old-i-would-like-to-update-it-to-get-latest-update-from-master)
  * [Simpler alternative if your have not shared your branch with others](#simpler-alternative-if-your-have-not-shared-your-branch-with-others)
* [I want to rename a local branch, without renaming the upstream branch (in cgal-dev)](#i-want-to-rename-a-local-branch-without-renaming-the-upstream-branch-in-cgal-dev)
* [I see remote branches in my repository that have been removed on the server. How do I update my local clone?](#i-see-remote-branches-in-my-repository-that-have-been-removed-on-the-server-how-do-i-update-my-local-clone)

<!--TOC-->

We here collect questions already raised on the cgal-dev mailing list or
stated below.

Note that the Git project already has a huge FAQ:
<https://git.wiki.kernel.org/index.php/GitFaq>

## Which clients?

On Linux/Mac OS git comes as command line tool. Please refer to your
package manager to install it. You can also install git from sources
(http://www.git-scm.org/downloads). For windows there is also a command
line (http://code.google.com/p/msysgit/)

There are also many GUI available, **for instance**

-   Windows: TortoiseGit (which is similar to TortoiseSVN), SmartGit,
    GitEye
-   Mac OSX: Sourcetree, Tower, SmartGit, GitEye

... and many others. Please explore the web (eg here
<http://git-scm.com/downloads/guis>) to find your preferred one.

## How much space do I need on my disk?

With `git clone <url>` you receive a full repository with all branches
as existing on the server. In addition the branch `master` will be
checked out. A clone of `cgal.git` needs about 0.75 GB.
A clone of `cgal-web.git` also needs about 0.75 GB.


## How can I see which branch I am on?

``` {.bash}
 git branch 
```

displays all the local branches, and display a '\*' in front of the
current branch.

The first line of the 'status' command also displays the branch:

``` {.bash}
 git status 
```

There is also a shell prompt, see
<http://nuclearsquid.com/writings/git-new-workdir/>

## What if I want to work on multiple branches in parallel?

If you are working on several branches at a time and for several reasons
you don't want to switch all the time, Bash (or git-shell) users can use
the script `git-new-workdir` on the
[https://github.com/git/git/tree/master/contrib/workdir git
repository](https://github.com/git/git/tree/master/contrib/workdir git repository).
It's creating a copy of the repo but the git config files are not
duplicated (they are symlinked).

``` {.bash}
 git clone cgal cgal-other 
```

create another local clone (with hardlinks even very fastly). However,
it extends the tree of git repositories by one more child. There is

``` {.bash}
 git new-workdir <repo>
<dir>
```

that does not come with all distributions, but check
<http://nuclearsquid.com/writings/git-new-workdir/> how to get it.

Suppose that your cgal clone is in `/home/jenny/CGAL/Git/cgal` do:

``` {.bash}
> cd /home/jenny/CGAL/Git/
> git-new-workdir cgal"ng"A 
```

**Recommendation:** Jenny can add as third parameter the name of the
branch you want to checkout. It looks convenient to name the new working
directory as the branch, that is:

``` {.bash}
 > git-new-workdir cgal Convex_hull_3-make_it_faster-jenny Convex_hull_3-make_it_faster
```

**Note for Windows users:** `git-new-workdir` needs symbolic links. Do
not try to use it on Windows unless you only use git.exe from Cygwin.
Git.exe from msysgit or Tortoize will not work with such a working
directory.

We also discuss (for advanced users) [how to combine several orthogonal features in one branch](Developing-Features-with-Git#developing-and-committing-to-several-orthogonal-feature-branches)

## How to remove old branches that are already merged

The command `git branch --merged cgal/master` lists all local branches
that are already merged into `cgal/master`. (Note that `integration` and
`master` are usually seen as merged into `master`.) The following
command remove all such branches, but `integration` and `master`:

``` {.bash}
 git branch --merged cgal/master | grep -vE 'master|integration' | xargs -n 1 git branch -d<br />
```

(adaptation from [a StackOverflow answer](http://stackoverflow.com/a/6127884/1728537))

## How can I disable/change the pager used by some git commands?

git sends the output of some commands to a pager (like `less` or
`more`). If you don't like that, e.g., because the used pager does not
understand the escape sequences for colors, the pager can be disabled or
changed with the command

``` {.bash}
 git config --global core.pager cat 
```

`cat` effectively disables paging. A different pager can be set by
supplying the command name there.

## How can Git ignore files globally?

See this nice
[tutorial](https://help.github.com/articles/ignoring-files)

## How to create aliases for Git commands?

See that page: <https://git.wiki.kernel.org/index.php/Aliases>

## How to search for the commits of a certain author?

``` {.bash}
 > git log -p --author="Jenny"; 
```

## My branch is really old. I would like to update it to get latest update from master.

Assuming you are currently working on a branch called
`my-ancient-feature`.

``` {.bash}
git branch -m my-ancient-feature my-ancient-feature-old 
# Rename your branch to a temporary name.
# Please use the same name with an -old suffix, because that temporary name
# will be seen in the commit log message later.
 
git fetch cgal
git checkout -b my-ancient-feature --no-track cgal/master
# Creates a branch from master with the same old name and switch to it.
# The --no-track option is in order to avoid accidental pushes to master. 
# It tells git that the new branch must not track 
# the remote branch cgal/master
 
git merge --no-commit my-ancient-feature-old
# Merge the old branch into the newly created one.
 
# Then resolve conflicts, if there are any. You can also review the changes
# made by the merge with the following command:
git diff --staged
 
# Once you resolved everything, you can commit that merge:
git commit
 
# Now you are ready to specify the -u cgal-dev option
# which is needed because the branch, even if it has the same name as before, 
# is a new branch that does not track any remote branch.
# (If your branch is an old GSoC branch that you want to keep in the dedicated
# cgal-public-dev.git repository, then replace "cgal-dev" by "cgal-public-dev", in the following command.)
git push -u cgal-dev my-ancient-feature
# That update is a regular fast-forward push, that other developers will be able
# to pull without issues.
# note -u is equivalent to --set-upstream
 
# Then delete your old local branch
git branch -D my-ancient-feature-old
```

Note that if you have a [special working directory for this branch](#what-if-i-want-to-work-on-multiple-branches-in-parallel),
you will need to erase it and recreate it.

### Simpler alternative if your have not shared your branch with others

If your branch hasn't been shared with others (e.g. push it to a remote
or you are absolutely sure that no one has done any work based on your
branch) you can forgo the above procedure and rebase your branch from an
updated master. Rebasing is the process of replaying your commits on top
of an updated upstream.

This should not be done if you simply intend to merge your branch to
master because it hides the actual integration changes that would
usually be represented by the differences of the merge commit.

``` {.bash}
# switch to your ancient feature
git checkout my-ancient-feature
git rebase cgal/master
# follow the instructions of the output of rebase, fix conflicts
```

Be warned that, if you use `git-rebase` while other developers also work
on the same branch, their next `git-pull` will show a *lot* of
conflicts!

## I want to rename a local branch, without renaming the upstream branch (in cgal-dev)

Just do:

``` {.bash}
 git branch -m <oldname> <newname> 
```

## I see remote branches in my repository that have been removed on the server. How do I update my local clone?

``` {.bash}
 git fetch -p 
```

will do the job

If you have more than one remote, you can add the option `--all` to
apply it to all of them.

``` {.bash}
 git fetch --all -p 
```
