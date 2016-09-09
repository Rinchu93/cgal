<!--TOC-->

# Table of Contents
* [Create a GitHub account](#create-a-github-account)
* [Migration Procedure for Regular Developers](#migration-procedure-for-regular-developers)
* [Migration Procedure for Student Developers](#migration-procedure-for-student-developers)
* [Migration Procedure for Web Developers](#migration-procedure-for-web-developers)
* [New procedure to merge a branch into master](#new-procedure-to-merge-a-branch-into-master)

<!--TOC-->

Since the 5th of March 2015, the Git repositories of the CGAL project
are hosted on GitHub.

## Create a GitHub account

The first step is to create a GitHub account [as described here](Information-for-New-Developers#creating-a-GitHub-account).

## Migration Procedure for Regular Developers

We describe here the procedure to migrate a repository that was using
`ssh://git@scm.cgal.org/var/git/cgal.git` as url for the *origin* remote
and `ssh://gsoc@scm.cgal.org/var/git/cgal-gsoc.git` as url for the
*gsoc* remote. If you have only one remote choose the correct one
(`cgal-public-dev` if you are a student or `cgal-dev` if you are a
*regular* developer).

Rename your *origin* remote to *cgal-dev* and update its url:

``` {.bash}
 cd ~/CGAL/git/cgal #where your git clone is locate on your disk git remote rename origin cgal-dev git remote set-head cgal-dev integration git remote set-url cgal-dev git@github.com:CGAL/cgal-dev.git git fetch --all --prune 
```

Rename your *gsoc* to *cgal-public-dev* and update its url:

``` {.bash}
 git remote rename gsoc cgal-public-dev git remote set-url cgal-public-dev git@github.com:CGAL/cgal-public-dev.git 
```

Master is no longer writable for regular developers. You need to setup a
new remote to access it:

``` {.bash}
 git remote add cgal git@github.com:CGAL/cgal.git 
```

and set the tracking branch of your local master branch:

``` {.bash}
 git fetch cgal git branch --set-upstream-to=cgal/master master # for git &lt; 1.8: git branch --set-upstream master cgal/master 
```

The command `git remote -v` should now show the following:
```{.bash}
    $ git remote -v
    cgal    git@github.com:CGAL/cgal.git (fetch)
    cgal    git@github.com:CGAL/cgal.git (push)
    cgal-dev        git@github.com:CGAL/cgal-dev.git (fetch)
    cgal-dev        git@github.com:CGAL/cgal-dev.git (push)
    cgal-public-dev   git@github.com:CGAL/cgal-public-dev.git (fetch)
    cgal-public-dev   git@github.com:CGAL/cgal-public-dev.git (push)
```
You can now fetch all the remotes at once with:

``` {.bash}
 git fetch --all 
```

## Migration Procedure for Student Developers

Rename your *origin* to *cgal-public-dev* and update its url:

``` {.bash}
 cd ~/CGAL/git/cgal #where your git clone is locate on your disk git remote rename origin cgal-public-dev git remote set-head cgal-public-dev -d git remote set-url cgal-public-dev git@github.com:CGAL/cgal-public-dev.git git fetch --all --prune 
```

Master is not writable. You need to setup a new remote to access it:

``` {.bash}
 git remote add cgal git@github.com:CGAL/cgal.git 
```

and set the tracking branch of your local master branch:

``` {.bash}
 git fetch cgal git branch --set-upstream-to=cgal/master master # for git &lt; 1.8: git branch --set-upstream master cgal/master 
```

The command git remote -v should now show the following:
``` {.bash}
    $ git remote -v
    cgal    git@github.com:CGAL/cgal.git (fetch)
    cgal    git@github.com:CGAL/cgal.git (push)
    cgal-dev        git@github.com:CGAL/cgal-dev.git (fetch)
    cgal-dev        git@github.com:CGAL/cgal-dev.git (push)
    cgal-public-dev   git@github.com:CGAL/cgal-public-dev.git (fetch)
    cgal-public-dev   git@github.com:CGAL/cgal-public-dev.git (push)
```
You can now fetch the two remotes at once with:

``` {.bash}
 git fetch --all 
```

## Migration Procedure for Web Developers

Rename your *origin* to *cgal-web* and update its url:

``` {.bash}
 cd ~/CGAL/git/cgal #where your git clone is locate on your disk git remote rename origin cgal-web git remote set-url cgal-web git@github.com:CGAL/cgal-web.git 
```

Verify all is good:

``` {.bash}
 git fetch --all 
```

## New procedure to merge a branch into master

We now use [pull requests](Source-Code-Management-with-Git#pull-request).
