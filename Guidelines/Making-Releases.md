## Internal Releases

Internal releases are currently created daily from a script run at
GeometryFactory. This script packages together the current versions of
all packages into a tar file and publish a tarball at
<https://cgal.geometryfactory.com/CGAL/Members/Releases/>. People
responsible for running the test suite can pick it up automatically
using the [`autotest_cgal`](Scripts#autotest_cgal) script.

## Public Releases

A public release can be created from an internal release by following
the steps detailed in the `README` file in the package `Release` in the
SCM repository. This file describes how to create the code, the
documentation and the updated web pages for a new release.

Each public release is tagged in the [`cgal`](https://github.com/CGAL/cgal)
repository with a tag in the following format
`releases/CGAL-N`, where `N` is the release
number. There is also a branch created for each release
with the name `releases/CGAL-N-branch`, where `N` is the release number as
before. This facilitates the creation of bug fix releases.
