<!--TOC-->

# Table of Contents
* [Introduction](#introduction)
* [Coding conventions](#coding-conventions)
* [The Programs](#the-programs)
* [Create `CMakeLists.txt`](#create-cmakeliststxt)
* [Allow the use of C++11 features (or later version of C++)](#allow-the-use-of-c11-features-or-later-version-of-c)
* [Including programs in documentation](#including-programs-in-documentation)
* [Demo programs on the web](#demo-programs-on-the-web)

<!--TOC-->

## Introduction

The best way to illustrate the functionality provided by the library is
through programs that users can compile, run, copy and modify to their
hearts' content. Thus every package should contain some of these
programs. In CGAL we distinguish between two types of programs: those
that provided graphical output (demos) and those that do not (examples).

In this chapter we provide guidelines for the development of these
programs and their inclusion in the documentation. See Sections
 [example subdirectory](https://github.com/CGAL/cgal/wiki/Directory-Structure-for-Packages#examples-subdirectory) and [demo subdirectory](https://github.com/CGAL/cgal/wiki/Directory-Structure-for-Packages#demo-subdirectory) for a
description of the directory structure required for example and demo
programs, respectively. Note in particular that each directory should
contain a README file that explains what the programs do and how one
interacts with them.

**Requirements**:

-   Follow the [coding conventions](https://doc.cgal.org/latest/Manual/devman_code_format.html)
    (see the [section below](#coding-conventions) for a
    summary).
-   Include all example and demo programs from the documentation in the
    examples/ or demo/ directories.

**Recommendations**:

-   Place a demo of your package on the web site.

## Coding conventions

Remember that these programs are likely to be a user's first
introduction to the library, so you should be careful to follow our
[coding conventions](https://doc.cgal.org/latest/Manual/devman_code_format.html)
and good programming practice in these programs. In particular:

-   Do **not** use the statements
```
using namespace CGAL;
using namespace std;
```
We discourage the use of these as they introduce more names than are
necessary and may lead to more conflicts than are necessary.

-   As of release 2.3, you can include only the kernel include file
    (*e.g.*, Cartesian.h or Homogeneous.h) to get all kernel classes as
    well as the basic.h file. All example and demo programs should do
    this. For example, you should have simply:
``` {.cpp}
#include <CGAL/Cartesian.h>
```
instead of:
``` {.cpp}
#include <CGAL/config.h>
#include <CGAL/Cartesian.h>
#include <CGAL/Point_2.h>
#include <CGAL/Triangle_2.h>
#include <CGAL/Segment_3.h>
```
-   Types should be declared using the following syntax:
```
typedef CGAL::Cartesian<double>     Kernel;
typedef Kernel::Point_2             Point_2;
typedef Kernel::Triangle_3          Triangle_3;
```
instead of this syntax:
```
typedef CGAL::Cartesian<double>     Kernel;
typedef Point_2<Kernel>             Point_2;
typedef Triangle_3<Kernel>          Triangle_3;
```
Although both will work, the former is to be preferred since it
reflects that the types are actually part of the kernel and also
reflects the new (as of release 2.3) kernel design that allows types
to be easily exchanged and adapted.
Note also that the typedef used above is
``` {.cpp}
typedef CGAL::Cartesian<double>     Kernel;
```
instead of
``` {.cpp}
typedef CGAL::Cartesian<double>     R; // for representation
```
This also reflects the new design, where the kernel classes are
kernels containing objects and predicates not just representation
classes for the objects.

## The Programs

The following guidelines should be followed to the greatest extent
possible when writing the example and demo programs.

-   Provide simple programs with which a beginner can get started.
-   Provide more involved programs that illustrate the power of the
    software.
-   Provide programs that truly exercise the data structure. Though you
    may have some canned programs that work on fixed data sets to
    illustrate specific things, you should also have one (or more)
    programs that work on [randomly generated](https://doc.cgal.org/latest/Generator/)
    or user-generated data. This illustrates confidence in the software
    (and can also build confidence by highlighting bugs).
-   Take some care to design a good interface to the program; the
    packaging of a product does make a difference.

## Create `CMakeLists.txt`

Each directory containing examples or a demo (or tests) needs a
`CMakeLists.txt` file. There is a script `cgal_create_CMakeLists`.
(The previous script `cgal_create_cmake_script`
does not support all use-cases and is going to become deprecated soon.)

The file `CMakeLists.txt` also ensures that depending optional
third-party libraries are configured for the demo/examples. Say a
directory requires MPFI, then `CMakeLists.txt` must have been altered
accordingly. There are three ways to do so:

1.  call the script accordingly: `cgal_create_CMakeLists -c MPFI`
2.  replace `find_package(CGAL)` by `find_package(CGAL COMPONENTS MPFI)`
    (not deleting existing components)
3.  add `find_package(MPFI)` and `include(${MPFI_USE_FILE})` to
    `CMakeLists.txt`.

For the demo/example, all three are equivalent. It is to be discussed
which option is the preferred one for CGAL demos and examples (and
tests); in particular for components (like MPFI/RS) that activate
additional code in CGAL's header files, and thus must be (pre)configured
with CGAL (i.e. when and where are `CGAL_USE_<lib>` flags set - config
of CGAL, config of example?).

## Allow the use of C++11 features (or later version of C++)

### For a given example

The CMake function `create_single_source_cgal_program` allows the following syntax:
```CMake
create_single_source_cgal_program( "compute_normals_example.cpp"
                                   CXX_FEATURES cxx_range_for cxx_auto_type )
```
where keywords after the optional `CXX_FEATURES` are [CMake compile features], among the [list of know features].

A user can also opt for all C++11 features, that way:
```CMake
create_single_source_cgal_program( "compute_normals_example.cpp" 
                                   CXX_FEATURES ${CMAKE_CXX11_COMPILE_FEATURES} )
```
but that is discouraged because variables like `CMAKE_CXX11_COMPILE_FEATURES` are [explicitly not documented](https://cmake.org/cmake/help/v3.6/manual/cmake-developer.7.html#adding-compile-features).

[CMake compile features]: https://cmake.org/cmake/help/v3.1/manual/cmake-compile-features.7.html
[list of know features]: https://cmake.org/cmake/help/v3.6/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html#prop_gbl:CMAKE_CXX_KNOWN_FEATURES

This requires CMake 3.1 or later, but there is a compatibility mode: the example will not be configured, with a warning, iif an older version of CMake is used.

### For a whole directory (of examples or of a demo)
To modify the CMakeLists.txt of a demo, to make it use C++11 or C++14, that is even easier: add that line before the definition of any target:
```CMake
set(CMAKE_CXX_STANDARD 14)
```
[This requires CMake version 3.1 or later](https://cmake.org/cmake/help/v3.1/variable/CMAKE_CXX_STANDARD.html?highlight=cmake_cxx_standard).

## Including programs in documentation

All programs included in the documentation should be included in either
the `examples/` or the `demo/` directory to ensure that:

-   (a) the programs will be included in the test suite and
-   (b) the user can more easily use the example program

The reverse statement, that all programs in the `examples/` and `demo/`
directory should be included in the documentation, is not necessarily
true. Ideally the programs included in the documentation will be a
proper (non-empty) subset of the programs included in the distribution.

Please make sure that your example programs do not have too long lines,
this can overflow in the output.

## Demo programs on the web

Demo programs for some packages are accessible from the [package overview](https://doc.cgal.org/latest/Manual/packages.html)
web page. For each demo on the page, we have a precompiled executable
for the Windows platform.

If a specific package has a demo that should be linked in the [package overview](https://doc.cgal.org/latest/Manual/packages.html)
web page, then the header in `<package>/doc/<package>/PackageDescription.txt` should be updated to include a `\cgalPkgDemo{}` line.
For details see [this page](Writing-Documentation#packagedescriptiontxt).

For each demo of the package, please discuss with [@lrineau](https://github.com/lrineau) to agree on the name
`<name_of_the_demo>.zip` of the zip file, so that the automatic scripts
that create the zip files are updated.
