<!--TOC-->

# Table of Contents
* [History](#history)
* [Git Repository Layout](#git-repository-layout)
  * [Branches](#branches)
    * [Feature branches](#feature-branches)
    * [Utility branches](#utility-branches)
    * [Tags and Release Branches](#tags-and-release-branches)
* [Working with Git](#working-with-git)
  * [Workflow](#workflow)
    * [Guidelines](#guidelines)
  * [Pull request](#pull-request)
    * [Update a pull-request](#update-a-pull-request)
    * [Bug-fix branch](#bug-fix-branch)
  * [Commit Messages](#commit-messages)
  * [Branch-build](#branch-build)
  * [Post-commit mails](#post-commit-mails)
  * [Special files in or not in the repository](#special-files-in-or-not-in-the-repository)
* [Git: Working with the local and the remote repository](#git-working-with-the-local-and-the-remote-repository)
* [Further Directions](#further-directions)
* [Commitment](#commitment)
* [Resources](#resources)

<!--TOC-->

## History

CGAL's code is maintained by a **Source Control Management** system. It
keeps track of the complete history of changes of a set of source files.
When several people work on the same project, using a Source Control
Management system becomes vital. At the beginning, CVS was used. At some
point CVS was replaced by the popular SVN (Subversion). This document
describes the current iteration, where Git is enabled as a new version
control system that has better support for branching and also allows
decentralized repositories. As each developer holds a local repository,
branching in Git is much faster.

## Git Repository Layout

Any Git repository for CGAL, in particular your local one, contains
various branches. A branch represents a version of CGAL, that is a set
of source, header and documentation files needed to
[build](Branch-Build) a version of CGAL. Each
branch contains `CMakeLists.txt` file that serves as anchor for
building, and a set of subfolders, so called *packages* (packages have
their own [certain
structure](Directory-Structure-for-Packages)).
Most packages implement a data structure or an algorithm for CGAL (e.g.,
`Convex_hull_2`, or `Triangulation_3`); however some packages serve
special needs:

-   Installation - meta-files and CMake-support
-   Maintenance - infrastructural support
-   Core, CGALimageIO, Qt_widget, GraphicsView - component libraries
-   Scripts - scripts to simplify developer's and user's work
-   Testsuite - infrastructure for testsuite
-   MacOSX - support for writing demos for Mac OS X
-   Documentation - infrastructure for CGAL's manual
-   LEDA - interfacing external libraries
-   STL_Extension - extensions to the standard template library
-   Stream_support - support for iostreams

A branch of CGAL can contain a stable release or a forthcoming release,
a stable version with a collection of new features for the upcoming
release or an extension of a stable version with single new feature
under development; a branch can also exist for adding demos and
examples, or to fix non-trivial bugs. The development of any new code
(except for trivial bugfixes) must be carried out in a dedicated branch
referred to as a *feature-branch* (see [Developing features with branches](Developing-Features-with-Git)
for more details). Development in one branch can happen without
disturbing development in any other branch. A feature branch has a
limited life-time. Upon acceptance and successful integration and
testing of a new feature, the new code in the feature branch can be
integrated into a branch that collects all features for the upcoming
release, referred to as the `master` branch. Once this integration is
complete the feature branch will be removed. Small bug fixes that are
absolutely harmless are committed directly to `master` (though it is
very easy to do this on a feature branch, as its creation is a question
of seconds). A detailed description of the various branches follows:

### Branches

Note that, in contrast to SVN, a Git repository does not organize
branches in a tree. All branches exist next to each other. Each Git
repository (remote or local) contains the following (types of) branches.

We propose a layout that uses three different main branches for
development (ordered by stability):

* The **master** branch collects all features accepted and successfully integrated for the upcoming release. Changes to this branch are merges of tested, mature feature branches or merges of very small bug-fix branches.
* The **integration** branch represents the current ongoing development in CGAL and the test-suite is run from this branch. If the work in a feature branch is completed it is [merged into this branch and tested](Developing-Features-with-Git#integrating-the-feature-branch-and-adding-it-to-the-testsuite).
* a separate branch for each feature, and tags for specific releases.

#### Feature branches

A feature branch is used for a single new feature that is under
development, such as a new package, a new function, or some kind of
optimization. A feature branch starts as a clone of `master`, that is
`master` is its *parent*. When Jenny intends to develop a new feature,
she creates the branch. A feature branch ceases to exist upon its
successful reintegration into `master`, which takes place after
acceptance of the feature by CGAL's Editorial Board and a successful
integration and test period - in particular, after invitation of the
Release Manager.

Larger bug-fixes, demos, or examples have to be developed in branches
and follow the same integration process but do not need to
follow the Guidelines for Features or Small Features and the approval
process.

Developers must follow the rules for [features](https://cgal.geometryfactory.com/CGAL/Members/wiki/Features) and
[small features](https://cgal.geometryfactory.com/CGAL/Members/wiki/Features/Small_Features)
and the [naming recommendations for feature branches](Naming-Recommendations).

#### Utility branches

A set of branches is maintained for things that are not immediately
related to the CGAL source repository. For now the only two candidates
are Debian packaging information and git-hook scripts. Such branches
should not be created lightly and often a separate repository is more
appropriate.

#### Tags and Release Branches

Tags of the form `X.Y.Z` denote releases of CGAL. If a release has to be
maintained even after new feature release (e.g. `3.9.1 -> 3.9.2`, while
the current release is `4.0`) this is done in a branch with the name
`maint-X.Y.(Z-1)`. Other tags may be added but this is not
expected to happen often.

## Working with Git

[Quick Start](Quick-Start)

### Workflow

The main goal of the proposed workflow is to maintain a clean, easy to
understand history of the CGAL repository. General feature development
follows a few general steps. The specific commands to execute those
steps and descriptions are explained in
[Developing features with branches](Developing-Features-with-Git).

1.  To develop a feature, a branch of the current master is created.
    See:
    [Opening a feature branch](Developing-Features-with-Git#opening-a-feature-branch)
2.  When the development is finished and the feature is accepted the
    branch is merged into `integration`.
    [Integrating the feature branch and adding it to the testsuite](Developing-Features-with-Git#integrating-the-feature-branch-and-adding-it-to-the-testsuite)
3.  After the branch has spent some time in integration and all
    necessary adjustments have been made to integrate it successfully,
    the feature branch (not `integration`) can be merged into the
    `master` branch through a [pull request](#pull-request).

#### Guidelines

-   Integration commit should happen in the feature branch, not in the
    integration branch. That is, changes that are required to
    successfully merge a feature into a stable branch, should be first
    committed to the feature branch before merging the feature branch
    into the stable branch (and not: first merge and then commit fixes
    that are further needed to resolve conflicts) - merges of features
    become conflict free this way.

### Pull request

A pull request is a request to merge a feature branch or a bug-fix
branch into master. In order to submit a pull request you need to have
your own fork of the cgal repository. If you don't already have one, go
to <https://github.com> and log in. Then go to
<https://github.com/CGAL/cgal> and click on the *Fork* button in the
upper right part of the page. You need to add another remote (*mine*) in
your git clone:

``` {.bash}
> git remote add mine git@github.com:jenny/cgal.git  #assuming your GitHub ID is jenny
> git fetch mine
```

If I want to merge a feature branch from *cgal-public-dev* or
*cgal-public-dev*, I need to push that branch into *mine*:

``` {.bash}
> git push mine AABB_tree-dimension_2-jenny
```

Then go to your local fork (https://github.com/jenny/cgal), select the
branch `AABB_tree-dimension_2-jenny` and click on the green button left
to the branch selection box. You will be asked to add a comment describing
what your merge is about. This should contain:

-   the reference of the (small) feature corresponding to the branch, if
    there is one,
-   or the fact that the branch is a bug-fix, if that is the case,
-   the internal release number in which the branch was last
    successfully tested (if not a trivial fix).

**Note:** *please* use clickable links to the feature pages, and to the
test result page, to ease the job of the Release Manager.

If your pull request is accepted, you will receive an email from github
and the branch will be removed from your local fork.

Finally, you can update the [features](https://cgal.geometryfactory.com/CGAL/Members/wiki/Features) or [small
features](https://cgal.geometryfactory.com/CGAL/Members/wiki/Features/Small_Features) table indicating that your
feature has been merged into `master`. To do so, move the feature line
from the table "Proposed features" to the table "Accepted/Integrated
into CGAL-x.y" below. This includes, in particular, mentioning the link
to the pull request.

If your feature branch was shared in `cgal-public-dev`, do
not forget to remove it from there once the pull request is accepted:

``` {.bash}
> git push cgal-public-dev --delete  AABB_tree-dimension_2-jenny
```

For more information about pull requests, refer to the official
documentation: <https://help.github.com/articles/about-pull-requests/>

#### Update a pull-request

If, during the tests for integration, you need to update something in the
branch then the pull-request is automatically updated as soon as you push your branch
(with the modifications) in *mine* again:

``` {.bash}
> git push mine AABB_tree-dimension_2-jenny
```

#### Bug-fix branch

If you want to create a short term bug-fix branch, it is sufficient to
create it locally, push it in your *mine* remote, do a pull request and
then forget about it. The Release Manager will deal with the
pull-request and then remove the branch from your *mine* remote.

``` {.bash}
> git checkout -b AABB_tree-bug_fix-jenny --no-track cgal/master
# edit files, use git-add and git-commit to create a commit with the modifications, then:
> git push mine AABB_tree-bug_fix-jenny
# and then create the pull-request in the web interface
```

### Commit Messages

Use the following format for commit messages:

```
<max 79 characters short description>\n
\n
<long description, 79 chars per line>
\n
```

An example from the Git project (commit 2b34e486bc):

```
pack-objects: Fix compilation with NO_PTHREADS
  
It looks like commit 99fb6e04 (pack-objects: convert to use
parse_options(), 2012-02-01) moved the #ifdef NO_PTHREADS around but
hasn't noticed that the 'arg' variable no longer is available.

```

Git commands like log, shortlog, etc support this format and make use of
the short description. Please stick to the format. At some point a push
hook might be installed that will reject pushes containing
non-conforming commit messages.

### Branch-build

CGAL can be built from a branch directly, i.e., there is no need to
create an internal release. The developer Jenny can build the CGAL
libraries from the branch she is currently working on. The [branch build
has its own wiki page](Branch-Build).

### Special files in or not in the repository

-   Do not put automatically generated files into the repository.
-   Files and directories in the repository that you do not want
    included in releases (e.g., literate programming web files) can be
    listed with their names in a file called `dont_submit` (at the top
    of a package, see
    [Directory Structure for Packages](Directory-Structure-for-Packages)).
-   The following files are always excluded from releases: `TODO`,
    `todo`, `TODO.txt`, `todo.txt`, `TO_DO`, `Doxyfile` and `*.dxy` (as
    as long Doxygen is not officially supported). more generally, you
    can have a look at the file
    `Maintenance/release_building/global_dont_submit` for the full list
    of files or file patterns that are ignored globally during the
    creation of release tarballs.

## Git: Working with the local and the remote repository

Git is essentially demanding and build on branches. This section gives a
high-level explanation of Git concepts. It is no replacement for reading
introductory materials.

The following picture is taken from
[1](http://tiebing.blogspot.com/2010/12/git-data-transport-commands.html).
It illustrates the connection between the local workspace, local index,
local repository and a remote repository. For now you can assume, for
simplicity, that the *remote repository* is located on a remote
filesystem. The *single* difference is that one is on a remote location,
the other is on a local file systems.

![](Git-transport.png "Git-transport.png")

To understand the figure, you **first concentrate on its left part**,
that is, forget about the remote repository for a minute. We are faced
with the workspace, the index and the (local) repository. Please observe
that this setup basically matches the situation of a (local)
SVN-repository with a checkout working copy:

-   The workspace represents all files belonging to a certain branch
    that can be **checkout**.
-   Files can be scheduled for **addition** to the (local) repository
    ...
-   ... and finally **committed**.
-   Updates from the (local) repository can be **pulled** into the
    workspace (assuming that someone else alters a branch in the - local
    - repository, we will see what "someone" means).
-   And finally modifications of the workspace can be compared against
    the repository. This **diff** shows changes that can be committed to
    the (local) repository.
-   Similarly to SVN, and not depicted in the figure, one can **merge**
    one branch of the repository into another branch. And, similarly
    again, merge conflicts must be resolved.

We now focus on the **right part of the figure**, which mainly adds two
operations:

-   All changes *committed* to the local repository only can be
    **pushed** into the remote repository. Typically the current branch
    is pushed, but there is also the `--all` option. This is the main
    difference to the SVN setup, where no "local repository" exists.
    That is, Git throws the local repository into the picture!
-   The local repository will be updated with the changes from the
    remote directory by **fetching**. A call to fetch updates the local
    repository from all remote branches.
-   A fetch constitutes the before-mentioned alterations of the local
    repository by *someone*. It updates one or several local branches.
    Then, these changes were pulled from a local branch into an checkout
    workspace.
-   To short-cut these two steps, the branch in the workspace can also
    directly be updated from a remote repository as indicated with the
    **pull** arrow that is originating from the remote repository.
-   There are many, many, many more Git-commands.

Analogy to SVN:

-   commit becomes two-phase: first commit to the local repository, then
    a push to the remote location
-   update becomes two-phase: first fetch from remote location, then
    merge changes to local branch (there is a shortcut to do both in one
    step: pull)

Remarks:

-   Note that the changes in a Git repository are organized as directed
    acyclic graph (DAG), where a node represent a certain change that is
    identified by a hash (replacing revision numbers).
-   **push** and **fetch** simply update the remote (push) or the local
    graph (fetch) accordingly. Be aware that **pull**, the direct
    counterpart to **push**, also updates the workspace (i.e., apply
    changes to the checkout files).

## Further Directions

Git enables some further developments that can be considered:

-   Code review
-   Continuous integration
-   publicly mirror specific parts of the SCM tree

## Commitment

-   CGAL developers learn a new architecture that requires slightly
    changed commands.
-   In contrast to central repository, local repositories are not
    backed-up through server maintenance. To backup your work, push your
    branches onto the central remote repository, or **mirror** your
    local repository - either on any network file system of your choice,
    or in a private directory on CGAL's scm-server.

## Resources

-   [SO Definitive
    Guide](http://stackoverflow.com/questions/315911/git-for-beginners-the-definitive-practical-guide)
-   [Official Git Page](http://git-scm.com/)
-   [learn.github (with Videos for the
    Lazy)](http://learn.github.com/p/intro.html)
