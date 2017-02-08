<!--TOC-->

# Table of Contents
* [Directory structure](#directory-structure)
  * [Obligatory](#obligatory)
  * [`test` subdirectory](#test-subdirectory)
  * [`examples` subdirectory](#examples-subdirectory)
  * [`demo` subdirectory](#demo-subdirectory)
  * [`benchmark` subdirectory](#benchmark-subdirectory)
* [Requirements and recommendations](#requirements-and-recommendations)

<!--TOC-->

## Directory structure

In order for code, demos, documentation, etc. developed for CGAL to be
included in an automated way into the internal (and public) releases of
the library, the files must be organized in a specific directory
structure, which we describe here. We describe the entire directory
structure for a package. Not every package will have all the parts
described here. Only the files `copyright`, `description.txt`,
`license.txt` and `maintainer` are obligatory, see
[below](#obligatory).

Submissions should not contain files in other places than described
here. It is possible to have some under SCM, but they need to be listed
in the `dont_submit` file to avoid being submitted. Make sure your
package does not have any file clashing with any other packages (for
example two files named `simple_example.cpp` in the example directories
of two different packages will clash in the target names of cmake).

The directory structure of a package named Package should be as follows:
```
+--- dont_submit
|
+--- include/CGAL/
|
+--- src/{CGAL|CGALQt|...}/
|
+--- test/<testdir>/
|
+--- doc/
|
+--- examples/<exampledir>/
|
+--- demo/<demodir>/
|
+--- benchmark/<benchmarkdir>/
|
+--- auxiliary/
|
+--- scripts/
|
+--- developer_scripts/
|
+--- package_info/Package/
                         +- copyright
                         |
                         +- description.txt
                         |
                         +- license.txt
                         |
                         +- long_description.txt
                         |
                         +- maintainer
```
-   `include/CGAL`
     contains the `.h` files for the package.
-   `src/`
     contains the `.cpp` files (if any) for the package. They are
    gathered in subdirectories corresponding to each library (be it
    static or shared).
-   `test/`
     contains the test suite code for the package. See Section [`test`
    subdirectory](#test-subdirectory) for a detailed
    description.
-   `doc/`
     contains the documentation for the user and reference manuals.
    These are the files used to produce the manuals for public releases
    and to run the internal release reference manual test suite. See the
    [Documentation Guidelines](Documentation-Guidelines)
    for a detailed description of this subdirectory.
-   `examples/`
     contains the example programs for the package. See Section
    [`examples` subdirectory](#examples-subdirectory) for a
    detailed description of this subdirectory.
-   `demo/`
     contains the demo programs for the package. Contrary to example
    programs, demo programs are not expected to be usable on all
    platforms. Demo programs may depend on platform-specific software
    and may require user interaction. They are compiled but not run in
    the test suite. See Section [`demo` subdirectory](#demo-subdirectory) for a more detailed
    description of this subdirectory.
-   `benchmark/`
     contains the benchmark programs for the package. See Section
    [`benchmark` subdirectory](#benchmark-subdirectory) for a
    detailed description of this subdirectory.
-   `auxiliary/`
     contains auxiliary software for CGAL. For instance, GMP goes here
    when using the Windows installer. The average package won't have
    this directory.
-   `scripts/`
     contains scripts that are of interest to CGAL users.
-   `developer_scripts/`
     contains scripts that are of interest to CGAL developers. This
    directory is included in internal releases only, not in public
    releases.
-   `dont_submit`
     specifies files and directories that are to be excluded from the
    release, for example:
-   `TODO`
-   `include/CGAL/my_internal_file.h`
-   `benchmark/`

### Obligatory

-   `package_info/Package/copyright`
     Indicates who is holding the copyright of the package
-   `package_info/Package/description.txt`
     should give a very short description of the contents of the
    package.
-   `package_info/Package/license.txt`
     indicates the license of the package.
-   `package_info/Package/long_description.txt`
     may be added with more detailed information about the package.
-   `package_info/Package/maintainer`
     should be used to name the maintainers of the package. The file
    should have for each maintainer a line with the following format
    `name <email address>`. For example:
```
  Jenny Doe `<jenny@mpi-sb.mpg.de>
  Adam Smithee `<adam.smith@inria.fr>
```

A package has a name, which identifies it. This name should obey the
same rules as for C identifiers: it consists of letters, digits and
underscores and it does not start with a digit. Choose a name that is
descriptive, yet not too long (under 25 characters). If a package deals
with objects of a particular dimension, then use the suffixes `_2`,
`_3`, and `_d`, especially if there exists (or may exist in the future)
a package with similar functionality in different dimensions. Examples
of good package names are `Triangulation_2` for a package dealing with
triangulations of points in the plane and `Min_ellipse_2`, which
contains an algorithm that finds the minimal enclosing ellipse of a set
of points in the plane. The package names `pm` and `arr` are a bit too
terse. `Planar_map` and `Arrangement` (or `Arrangement_2`) are better.

### `test` subdirectory

This directory contains the test suite for the package. Here we just
briefly list the files contained in such a directory. For more detailed
information about testsuites for CGAL packages refer to Chapter
[Testing](Testing)
```
test/<testdir>/
              +--- data/
              |
              +--- include/
              |
              |- CMakeLists.txt
              |
              |- cgal_test
              |
              |- *.cpp
              |
              |- *.cin
```
where the directory `<testdir>` has a name that corresponds to the
package name.

-   `data/`
     is a subdirectory that contains local data files for the test
    suite.
-   `include/`
     is a subdirectory that contains local include files for the test
    suite.
-   `cgal_test`
     is the script that will be called when the entire test suite is
    run. As this file is created automatically during the release
    process, submitting it is error-prone and thus **strongly
    discouraged**. For testing purposes, such a script can be created
    using the `create_cgal_test` script (see
    [here](Scripts#create_cgal_test_with_cmake)).
-   `CMakeLists.txt`
     is the CMake configuration file for the test programs. It is
    created automatically, just like `cgal_test`.
    Such a `CMakeLists.txt` can be created using the
    script `cgal_create_cmake_script` with the argument `-t` (see
    [here](Scripts#cgal_create_cmake_script-same-as-cgal_create_cmakelists)).

-   `*.cpp` source code for the test programs. When a test program
    runs correctly, it should return the value zero, or, more precisely,
    the value `EXIT_SUCCESS` defined in `<cstdlib>`. Test programs may
    not be graphical and they may not require any user interaction.
-   `*.cin` files containing command-line input for test programs.
    These files are necessary for only those programs that require
    command-line input (which can usually be avoided). If a file
    `program.cin` is present in the test directory, then, when the test
    suite is run, `program` will be executed using the command

`       ./program < program.cin`

### `examples` subdirectory

Example programs (see [Examples and Demo
Programs](Examples-and-Demo-Programs)) for a
package should be placed in a subdirectory of the directory `examples`.
The subdirectory name, `<exampledir>`, will usually correspond to the
package name, but this is not a requirement. To make sure that the
examples will be tested, a directory with examples should be submitted
in exactly the same way as a test directory (Section [`test`
subdirectory](#test-subdirectory)).

The structure of an example directory should be as follows:
```
examples/<exampledir>/
                     +--- data/
                     |
                     +--- include/
                     |
                     |- README
                     |
                     |- cgal_test
                     |
                     |- CMakeLists.txt
                     |
                     |- *.cpp
```
The file `README` should contain information about what the programs do
and how to compile them. See the rules for a test directory for an
explanation of the other files and subdirectories.

### `demo` subdirectory


The `demo` directory (see [Examples and Demo
Programs](Examples-and-Demo-Programs)) contains
programs with graphical interfaces or programs requiring user input.
These programs will be compiled but not run by the test suite. The
structure of this directory should be as follows:

```
demo/<demodir>/
              +--- data/
              |
              +--- include/
              |
              |- README
              |
              |- CMakeLists.txt
              |
              |- *.cpp
```

where `<demodir>` is a name that corresponds (at least in part) to the
package for which it is a demo.

Note that if 3D packages have their own `demo` directory, 2D packages have theirs located in GraphicsView/demo/.

The file `README` should contain information about what the program
does, and how to compile it (i.e., what graphical libraries are needed,
etc...). Note that, in contrast to example and test programs, demo
programs more often need to submit a `CMakeLists.txt` since different
demos will require different libraries and thus the `CMakeLists.txt` for
these programs will be somewhat dissimilar.

### `benchmark` subdirectory

Benchmark programs for a package should be placed in a subdirectory of
the directory `benchmark`. The subdirectory name, `<benchmarkdir>`, will
usually correspond to the package name, but this is not a requirement.
Benchmark programs are currently not subject to any automatic treatment
in the test-suite, although they could evolve into an automatic
benchmark-suite at some point. We suggest at directory organization
similar to the one of `<exampledir>`.

Some data sets can be placed under the `data` subdirectory of
`<benchmarkdir>`, but we recommend that only a small number of small
files go there. Extensive benchmarks data sets should probably be
gathered elsewhere, so as not to bloat the SCM repository.

## Requirements and recommendations

Requirements:

-   The directory structure outlined here must be followed exactly.

Recommendations:

-   Do not submit `CMakeLists.txt`s for example programs and test suite
    programs.
-   Do not submit the script `cgal_test` used to compile and test your
    test suite programs.
