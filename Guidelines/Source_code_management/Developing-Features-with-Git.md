<!--TOC-->

# Table of Contents
* [Opening a feature branch](#opening-a-feature-branch)
* [Coding](#coding)
* [Synchronizing the feature branch](#synchronizing-the-feature-branch)
* [Integrating the feature branch and adding it to the testsuite](#integrating-the-feature-branch-and-adding-it-to-the-testsuite)
* [Merging the feature branch to master](#merging-the-feature-branch-to-master)
* [Closing the feature branch](#closing-the-feature-branch)
* [Problem with end-of-line encoding in a feature branch](#problem-with-end-of-line-encoding-in-a-feature-branch)
* [How to develop several features in parallel](#how-to-develop-several-features-in-parallel)
  * [How to work on a different branch?](#how-to-work-on-a-different-branch)
  * [Which branch Jenny is currently working on?](#which-branch-jenny-is-currently-working-on)
  * [Building after switching/updating](#building-after-switchingupdating)
* [How to use another feature in own feature branch](#how-to-use-another-feature-in-own-feature-branch)
* [Developing and committing to several orthogonal feature branches](#developing-and-committing-to-several-orthogonal-feature-branches)
* [Sharing your branches with the outside](#sharing-your-branches-with-the-outside)

<!--TOC-->

Any change to the existing code of CGAL, such as the introduction of a
new function or functor in an existing file, the fixing of a bug in a
package, or the refactoring of some code in various packages, is
classified either as a feature or as a bug fix. Features must be
reviewed by the Editorial Board. The development of a feature should
follow a certain procedure described in this page. However, it is
encouraged to follow the same procedure also for the development of
demos or fixing non-trivial bugs, even though both are not needed to be
reviewed. One advantage, for example, is that such code can be tested as
part of the testsuite in the same way that features are. A bug is
non-trivial if its fix is "beyond a typo". Trivial bugs can be fixed
directly on the `master` branch.

A new feature must be developed in a dedicated branch called the
<i>feature branch</i>. This branch is cloned from the `master` branch.
The feature branch, just as the `master` branch, supports the
[branch-build](Branch-Build), which allows the
building of CGAL from the sources in their package structure. This is
advantageous especially in cases where new features alter the generated
library objects. Once the development of a feature is complete, the
branch with all the modifications is merged back into `master`. The
release manager clones `master` to create the branch for upcoming public
release a short period before the release. This way new features end up
in the upcoming release.

The following assumes that 1) you are familiar with CGAL's SCM-layout
and which branches exists, as described in
[the guidelines](Source-Code-Management-with-Git),
2) you have cloned the remote Git repository, and 3) took a crash course
on git commands (our
[Quickstart](Quick-Start)),
or for example: [1](http://git.or.cz/course/svn.html) (which also
compares with SVN) or [2](http://cworth.org/hgbook-git/tour/) (from
Section 2.4 onwards), as **this documentation is not a general
introduction to GIT. It will only list the most important use-cases for
developing code in CGAL using Git as SCM.**

This page discusses the case of adding a package. The same steps
similarly apply for adding a demo/example or non-trivial bug-fixes. At
the beginning Jenny enters her local Git repository.
```
> cd /path/to/my/cgal_repository
```
## Opening a feature branch


Assume that Jenny would like to develop a new package named
`Kinetic_arrangement_2`.

First, following the [quick start](Quick-Start)
Jenny creates her own feature branch (based on the master branch).
Following the [naming recommendations](Naming-Recommendations),
Jenny calls the branch `Kinetic_arrangement_2-jenny`.

``` {.bash}
 git checkout -b Kinetic_arrangement_2-jenny --no-track cgal/master 
```

This is a local branch, and not visible to anyone besides Jenny until
the first call to the `git push` command (see below).

Note: if you already have made a ["pull
request"](Source-Code-Management-with-Git#pull-request),
your local repository has a remote branch named `mine/master`. That
branch is useless. It is a leftover of the fork. Unless you have
regularly merged `cgal/master` into it, `cgal/mine` branch is probably
obsolete.

## Coding


Now, Jenny can implement the package. She is iterating the following
commands until the package is implemented.

``` {.bash}
> mkdir Kinetic_arrangement_2
# [hack boom bang]
# Rebuild CGAL with branch-build to include the package in the build system
# Use `git add Kinetic_arrangement_2/path/to/file.cpp` to add new files to the source code management.
> git add <all-new-relevant-files>
> git commit <files>
```

This opens an editor to enter the commit message. Please use the
[established format](Source-Code-Management-with-Git#commit-messages)
for Git repositories.

If she wants to share the new feature with others, or have a back-up,
she can push the new commits to the remote repository:

``` {.bash}
 > git push -u cgal-dev Kinetic_arrangement_2-jenny 
```

Now her branch is visible to everyone with access to the remote
*cgal-dev*. Once the first push of the branch has been done, the
following ones no longer need the `-u cgal-dev` option, and are done
simply with

``` {.bash}
 > git push 
```

Otherwise, she could postpone this step until major progress took place.
Note: There are techniques (not explained here) to combine several small
commits into one bigger.

**To deprecate** something, she should make modifications in the code
and in the documentation. \* In the .h code-file she wants to deprecate,
she should add the following :
```
#define CGAL_DEPRECATED_HEADER "<CGAL/old_header_file.h>"
#define CGAL_REPLACEMENT_HEADER "<CGAL/new_header_file.h>"
#include <CGAL/internal/deprecation_warning.h>
```
-   In the .h file that contains the documentation, in the description
    of the class she should add :

```
\deprecated This class is deprecated since N.M. The class `New_class` should be used
instead.
```

**Once the package is ready** she submits the documentation of the
package to the editorial board. She gets some feedback and improves the
package according to the review.

## Synchronizing the feature branch

In contrast to SVN, we now have an **integration** branch that is used
to integrate new features to the recent development that took place on
`master` and by the nightly testsuite. Thus, Jenny delays the
integration until her feature has been accepted.

To remember: In order to avoid unnecessary dependencies and to keep the
shape of history simple, Jenny follows the procedure of section 
["My branch is really old. I would like to update it to get latest update from master."](Git-FAQ#my-branch-is-really-old-i-would-like-to-update-it-to-get-latest-update-from-master)
of [our Git FAQ](Git-FAQ) to update her feature branch.

<b>Under no circumstance Jenny merges master into her feature branch</b>.

## Integrating the feature branch and adding it to the testsuite

Once Jenny got the feature accepted by CGAL's Editorial Board, she gains
the right and obligation to test her feature on various platforms and to
ensure the interoperability with other parts of the library. The
[Testing](Testing) chapter explains how to test
code.

Note that on Saturday nights, the internal releases are usually created
from `master` only. That means that the Saturdays versions of the
`integration` branch are not tested.

Before merging her branch to `integration`, Jenny has to shortly
describe the changes she is making to CGAL in the file
`Installation/changes.html` and commit it.

``` {.bash}
 # be sure to have a checkout of the right branch 
 > git checkout Kinetic_arrangement_2-jenny # commit changes.html
 > git commit 
 # then push this commit to the branch Kinetic_arrangement_2-jenny 
 > git push 
```

**Be careful** that the following commands will erase, in your working
directory, all *non-committed* modifications to files tracked by Git.
The following command:

``` {.bash}
 > git status 
```

must display either `nothing to commit (working directory clean)` or
`nothing added to commit but untracked files present`, but not a list
of files under a section `Changes not staged for commit:`. If you have
uncommited changes to tracked files, you must commit your changes to a
branch, or save them using the `git stash` command.

The inclusion of her feature branch to the testsuite takes place in the
`integration` branch (which she gets as any other branch with
`git checkout -b integration cgal-dev/integration`), and needs a few
steps:

``` {.bash}
# update the local copies of the remote branches cgal-dev/*
> git fetch cgal-dev
 
## update the 'integration' branch, in two steps ##
# First: checkout of the branch
> git checkout integration
# That Git command might displays such a warning:
Switched to branch 'integration'
Your branch and 'cgal-dev/integration' have diverged,
and have 1 and 31 different commit(s) each, respectively.
# Or the following message:
Switched to branch 'integration'
Your branch is ahead of 'cgal-dev/integration' by 285 commits.
  (use "git push" to publish your local commits)
 
# Second: one forces the update of the branch, that way:
> git reset --hard cgal-dev/integration
 
# now, Jenny merges her own feature branch 
> git merge --no-ff --no-commit Kinetic_arrangement_2-jenny
# --no-ff keeps the branch visible in the history
# --no-commit does the merge but does not commit it. 
# You can thus look at the diff and see if all went as expected.
```

The merge can only be aborted with an **empty** commit message! If you
leave the editor *without* saving, the commit message is **not** empty.

At this point, Jenny has to check the integrity of the merge: 
- solve conflicts (hints might be given in
[3](http://public.kitware.com/Wiki/Git/Workflow/Topic/Conflicts#Topic-to-Topic)
and
[4](http://public.kitware.com/Wiki/Git/Workflow/Topic/Conflicts#Resolution_Topic).
Note that they use `next` while we name this branch `integration`)
- build, test, and verify...

Then, she commits locally and pushes the integration onto the remote
location:

``` {.bash}
> git commit
# the commit message is already filled with the merge message.

# push updates 'integration' onto the remote location
> git push
```

If the push is rejected because the update is not fast-forward, that
means that another developer has pushed to the same branch between the
`git reset --hard` and the push. That situation can be fixed with a pull
of the remote updates, and then a new push attempt:

``` {.bash}
> git pull
> git push 
```

Please do not use `git rebase` or `git pull --rebase` in the
`integration` branch.

If Jenny modifies again her feature branch the same day after the merge
in `integration`, she can restart the same procedure to merge again the
feature branch in `integration`. Only the new commits will be considered
by Git.

Notice that a snapshot of the integration branch is taken every day
exactly at 21:00 CEST (local time in France: UTC/GMT + 1 hour + Daylight
saving time when applicable) and the integration branch is reset to
reflect the master branch immediately after.

Finally, Jenny waits for the results reported on [the test results web
page](https://cgal.geometryfactory.com/CGAL/Members/testsuite/) on the
following day (assuming that she completed the steps above before the
snapshot was taken). She can click on the *y/w/n/r* symbols in the table
to see for which commit the test suite was run. If the testsuite is
green, her feature can be merged into the `master` branch upon approval
of the release manager (see [below](#merging-the-feature-branch-to-master)).

If not green, note that `integration` gets thrown away and is reset to
the content of `master` each day, during the daily internal release
creation. As a consequence, if one day Jenny merges her branch into
`integration` so that it is tested during the night, and if the tests
were not fully successful, Jenny will have to remerge again the branch
into `integration` the day after (i.e. redoing the steps above). Anyway,
if the tests were not fully successful (e.g. the feature did not work
correctly on all platforms), she has to modify something in her code and
remerge into `integration`. This strategy shows Jenny that she is
responsible to get her feature properly tested and if successful to
quickly merge into in the stable `master` branch.

In order to check previous runs of the testsuite, the Git repository
will keep a sort of "cyclic backup" of all branches that were tested
during the last week, as branches: 
- `testsuite-Monday`
- `testsuite-Tuesday` 
- and so on (seven such branches)

## Merging the feature branch to master

Once a feature branch has been successfully tested 
([went through integration](#integrating-the-feature-branch-and-adding-it-to-the-testsuite)),
then Jenny can ask her branch to be merged into master by opening a
[Pull Request](Source-Code-Management-with-Git#pull-request).

## Closing the feature branch

At the end of the reintegration Jenny removes her feature branch, which
has reached the end of its life-time with the merge into `master`.

``` {.bash}
# change to the master-branch
> git checkout master 
# delete the local branch
> git branch -d Kinetic_arrangement_2-jenny 
# delete the branch on cgal-dev
> git push cgal-dev --delete Kinetic_arrangement_2-jenny
```

Remarks:
- If the last command does not work because your version of
Git does not know `git push --delete`, then you can use the old syntax:
"`git push cgal-dev :Kinetic_arrangement_2-jenny`".
- Removing the local branch is actually not a requirement. What happens in your own
repository is your responsibility.
- Once a branch is merged into
another, even if all the references to the branch has been removed, the
branch itself still exist in the history of the branch where it has been
merged. Thus, it is possible to revive old feature branches, using for
example [the procedure described on the Kitware wiki](http://public.kitware.com/Wiki/Git/Workflow/Topic#Old_Topic).

## Problem with end-of-line encoding in a feature branch

The CGAL repository used to include a few files with `crlf`, and
`crlfcr` end-of-line encoding. These have been fixed in master by
updating the .gitattributes (`6b43b44e642a560cf63f6dac3438298cbd3f4de4`)
and problematic files (`1adf441b18227f2e064abe59173a6fb7a6e48d65` and
`7d20531b1def82fe7f8bcc1bb557c3a350b23270`). In your feature branch
created before those fixes, it might happen that you need to modify the
re-encoded files. In order to avoid a problem while merging your branch
to master, it is advised to do the following:

``` {.bash}
# stash current work
> git stash
# update the .gitattributes
> git cherry-pick -x 6b43b44e642a560cf63f6dac3438298cbd3f4de4
# resolve the conflict by accepting the new version
# -x write the picked hash to the commit message, -ff tries a fast-forward
> git add .gitattributes
> git commit
# force re-encoding of eol
> rm .git/index
> git reset
> git commit -a -m "Convert all CRLF files to LF"
# then pop the stash to retrieve the modifications you have stashed
> git stash pop
```

(An alternative to be checked could be to set
`git config merge.renormalize true` as written in this thread
[5](http://stackoverflow.com/questions/861995/is-it-possible-for-git-merge-to-ignore-line-ending-differences)

## How to develop several features in parallel

### How to work on a different branch?

Assume Jenny is developing several features in different branches. With
Git it is very easy to deal with different (local) branches. Before she
checks out another branch, she ensures that her workspace has no
modifications, that is, `git status` reports no alterations. If there
are, she either commits them to her local repository (see above) or
*stash* them onto a stack (not explained here).

To checkout her feature branch `Convex_hull_2-make_it_faster-jenny` she
uses the following command:

``` {.bash}
 > git checkout Convex_hull_2-make_it_faster-jenny 
```

**NOTE: In contrast to SVN, Jenny is not required to be in the root of
the branch to switch them.** Git-commands likes `status` or `checkout`
can be called in any subdirectory and still work on the whole branch.

Future commits from that working copy will be made to the branch
`Convex_hull_2-make_it_faster-jenny` (until the next use of
`git checkout ` command).

Then, she can continue developing the new feature and commit changes
into the right branch. If she adds a new package, a new
[branch-build](Branch-Build) may be required to
include the new package in CGAL.

### Which branch Jenny is currently working on?

When Jenny develops more than one feature in several feature branches
she may frequently checkout various branches. If at any point Jenny is
uncertain which feature branch is currently checked out she can issue
the following command:

``` {.bash}
 > git branch Kinetic_arrangement_2-jenny 
```

At this point, the repository's workspace contains a full checkout of
CGAL, with all the packages, representing the current state of a branch.

Some shells can be extended to give the branch name in the prompt. See,
e.g., [6](http://aaroncrane.co.uk/2009/03/git_branch_prompt/)
(untested). Ask a search engine if you want to have this gimmick. There
is also tab-completion for branch names available.

### Building after switching/updating

For now, it is compulsory to
[rebuild](Branch-Build) CGAL each time a working
copy either is switched from one branch to another or if the branch is
simply updated.

Remarks: 
- In fact, rebuilding CGAL is only needed if a cpp file has
been added, removed or altered or include dirs have been added or
deleted during the switch/update 
- As CGAL mainly consists of
header files, actual rebuilds are often not needed. 
- For the same
reason, it is actually not a big harm to rebuild, as this is done within
less than a minute. 
- There is a chance that in the future a
warning/error is reported if build and working copy do not match.

**Comment:** Jenny can also maintain [several working directories](Git-FAQ#What_if_I_want_to_work_on_multiple_branches_in_parallel.3F)
in parallel from one repository. This is recommended if switching
branches happens quite often. On the other hand, Git encourages to often
commit (to the local repository, while pushes to the central repository
can be less often), and such a single checkout should usually not be
dirty (and even if: `git stash` offers a nice way to temporarily clean
up - and to get it *dirty* again.

## How to use another feature in own feature branch

At some point Jenny notices that she needs, in her branch
`Convex_hull_2-make_it_faster-jenny`, a feature that has been
implemented by Adam in the `STL_extension-new_iterator-adam` branch. It
is required that Adam's feature is already integrated. Then, Jenny has
to revive his feature branch and merge it into her branch; she never
merges the branches `integration`, `master` or `maint` into her feature
branch! We follow roughly the ideas in
[7](http://public.kitware.com/Wiki/Git/Workflow/Topic#Dependent_Topic)
with details from
[8](http://public.kitware.com/Wiki/Git/Workflow/Topic/Conflicts#Topic-to-Topic)
and
[9](http://public.kitware.com/Wiki/Git/Workflow/Topic/Conflicts#Resolution_Topic)
(note that their `next` is our `integration` branch):

``` {.bash}
 # update the local copies of the remote branches cgal-dev/* &gt; git fetch cgal-dev 
```

Use

``` {.bash}
 > git log --first-parent cgal-dev/integration 
```

to find the merge-commit for Adam's feature. The commit message gives
you the name of Adam's branch. The second parent of the commit (see the
message) is the end of Adam's feature branch, say 0a398e5. Next Jenny
creates a branch from this commit

``` {.bash}
 > git branch STL_extension-new_iterator-adam 0a398e5 
```

Now Jenny can merge that branch into her feature branch

``` {.bash}
> git checkout Convex_hull_2-make_it_faster-jenny
> git merge STL_extension-new_iterator-adam
# resolved conflicts!
# and delete Adam's revived branch
> git branch -d STL_extension-new_iterator-adam
```

This way, only Adam's feature is merged into Jenny's branch, while all
other new features on `integration` are excluded. Thus Jenny's feature
has no dependencies to those features. Furthermore there is a crisp
commit message for the merge "Merge branch
'STL\_extension-new\_iterator-adam' into 'Mesh\_3-jenny'" which is much
more appealing than the merge for the whole `integration` branch.

Downside: Jenny's feature is now dependent on Adam's.

## Developing and committing to several orthogonal feature branches

There are scenarios where Jenny wants to compile programs with sources
from several branches, like 
- preparing some experiments for a paper
- providing a demo or a web service with experimental code (e.g. for
reviewers of papers)

There are two ways: 
- Checkout [several
branches](Git-FAQ#What_if_I_want_to_work_on_multiple_branches_in_parallel.3F)
and tweak `CMakeLists.txt` of your executable to collect the right
includes from several checkouts. 
- Create a new local branch
`Combined-jenny` and merge all relevant feature branches into it:
`git merge branchA branchB [... branchG]`

The former feels more dirty and less *gitty*. The latter has the
problems that commits are expected on the individual feature branches
and in contrast to `Combined-jenny`. In the following Jenny decides for
the second way. Her rationale is to submit the features indepently! But
she is aware of the fact that this is only recommended if her commits
are rather orthogonal across the branches (e.g. adding GPU support to an
algebraic kernel in one branch, and implementing a new point location in
arrangements in another branch). As soon as a commit in one branch
requires a commit in the other (e.g. a change in the interface) the
following method is not recommended.

[Combining orthogonal feature branches](#developing-and-committing-to-several-orthogonal-feature-branches)

## Sharing your branches with the outside

If Jenny wants to share a branch with a person who does not have access to
`cgal-dev`, she has two solutions:
* The branch is not sensitive and
can be made public: she can push it into her own `cgal` fork (`mine`).
The procedure is the same as when she prepares a ["pull request"](Source-Code-Management-with-Git#pull-request).
* The branch is sensitive and cannot be made public yet: If not already
done, create a fork of the repository `private-fork` by visiting
[<https://www.github.com/CGAL/private-fork>](https://www.github.com/CGAL/private-fork)
and clicking on the <i>fork</i> button. she can now visit
[<https://www.github.com/jenny/private-fork>](https://www.github.com/jenny/private-fork),
click on <i>Settings</i> and add <i>Collaborators</i> to which she wants
to give access to the private repository. She has to update her git
config to add this new repository as a remote:

```
> git remote add private-mine git@github.com:jenny/private-fork.git  #assuming your GitHub ID is jenny
> git fetch private-mine
```

Then pushing a branch `Pkg-modif-jenny` can be done using:

```
> git checkout Pkg-modif-jenny # switch to the branch from cgal-dev you want to share
> git push mine Pkg-modif-jenny # public option
> git push private-mine Pkg-modif-jenny # private option
```

Note that if Adam or Jenny did a modification that (s)he pushed into cgal-dev, when
ready the other remotes can be updated by simply pushing the branch into them.
