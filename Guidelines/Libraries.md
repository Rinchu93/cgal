<!--TOC-->

# Table of Contents
* [Altering the libraries](#altering-the-libraries)
  * [Remark for Qt5](#remark-for-qt5)
* [Adding a library](#adding-a-library)
  * [Layout of directories and files](#layout-of-directories-and-files)
  * [The `CMakeLists.txt` of the new library](#the-cmakeliststxt-of-the-new-library)
  * [Additions to `CGALConfig.cmake`](#additions-to-cgalconfigcmake)
  * [Build](#build)
  * [Remark](#remark)
* [Support for Windows DLL, and ELF visibility](#support-for-windows-dll-and-elf-visibility)
  * [Definition of the export macro](#definition-of-the-export-macro)
  * [Use of the export macro](#use-of-the-export-macro)
  * [Test of the visibility feature with GNU/g++](#test-of-the-visibility-feature-with-gnug)

<!--TOC-->

## Altering the libraries

In order to compile and build CGAL's libraries
`CGAL, CGAL_Core, CGAL_ImageIO, CGAL_Qt5` from the packages of
a branch, the branch-build collects all sources that are required to
build by calling
``` {.bash}
> ls */src/CGAL<name-of-the-library>/*.cpp
```
on the parent of the branch. That is, `*` is replaced by all packages
contained in branch. More detailed: the source files of a library can
spread over various packages. Though, for most libraries, only a single
package collects all source files.

Jenny can alter or extend a library by simply modifying or adding a
`.cpp` file in `<whateverpackage>/src/CGAL<name-of-the-library>/`. If
the modifications of the library need new third-party libraries support,
then the `CMakeLists.txt` of the library (located in a package as
`*/src/CGAL<<name-of-the-library>/CMakeLists.txt`) needs to be adapted.

### Remark for Qt5

Building the Qt5 library also requires to call `qtmoc` for some
files. This task is half-automated: to support the 'qtmoc', Jenny
is required to add a file to each package that contributes sources to
`CGAL_Qt5`, respectively. Such a file should be located in
`<whateverpackage>/src/CGALQt5/` and needs to a have unique filename
over all packages, because of flattening of packages during release
creation. A recommended choice is `<name-of-package>.qtmoc.cmake`. The
file's content gives commands needed to run `qtmoc` for files related to
the package. For instance, the file `GraphicsView.qtmoc.cmake` looks like:

``` {.cmake}
# moc files that are compiled directly as cpp files
qt5_wrap_cpp(mocfiles ../../include/CGAL/Qt/GraphicsViewNavigation.h
                      ../../include/CGAL/Qt/DemosMainWindow.h
                      ../../include/CGAL/Qt/GraphicsItem.h
                      ../../include/CGAL/Qt/GraphicsViewInput.h)

# qrc files (resources files, that contain icons, at least)
qt5_add_resources ( RESOURCE_FILES ../../demo/resources/CGAL.qrc ../../demo/icons/Input.qrc ../../demo/icons/File.qrc ../../demo/icons/Triangulation_2.qrc)
```
Modifying this example should suffice for most of Jenny's use cases.

## Adding a library

**Disclaimer**: *This is an initial version of
this section. There are plans to improve the handling of libraries in
CGAL in a more automatic way with CMake. For now the following steps are
known, although slight modifications of the steps should be expected. Note
that adding a library is a very seldom task as a CGAL developer.*

### Layout of directories and files

Suppose Jenny wants to add a new component library to CGAL, e.g., to
speed up parallel computations. She chooses the new library to be called
`CGAL_Parallel`. The new development takes place in a new package (with
some subdirectories)

``` {.bash}
> mkdir Parallel
> mkdir -p Parallel/package_info/Parallel
> mkdir -p Parallel/src/CGALParallel
```

She adds the proper information in the directory
`Parallel/package_info/Parallel`. The sources of the new library have to
be put under `Parallel/src/CGALParallel`. The naming of this directory
`CGALParallel` is crucial. The new library will be named
`CGAL_Parallel`, that is the name of the directory where the prefix
`CGAL` has been replaced by `CGAL_`.

### The `CMakeLists.txt` of the new library

In order to include the new library to the build-process, she adds a
`CMakeLists.txt` file to the same directory.

The simplest `CMakeLists.txt` is a single call to the CMake macro
**`build_cgal_library`**:
``` {CMakeLists.txt}
build_cgal_library(CGAL_Parallel CGALParallel "")
```

The macro `build_cgal_library` declares a new library to CMake, using
`add_library`. It does extra work for name-mangling support on Windows,
and sets the VERSION and SOVERSION properties of the library. Here are
the arguments of the macro:

-   The first argument is the name of the library.
-   The second argument is the name of the directory, under `src/` that
    contains the .cpp files of the library.
-   The third argument may list extra files to be included in the build,
    here an empty list.

Usually, the new library needs third-party libraries, and for that
reason the `CMakeLists.txt` is usually more complicated. See e.g. `CGALimageIO/src/CGALImageIO/CMakeLists.txt`.

### Additions to `CGALConfig.cmake`

Jenny also has to announce the new library with the help of CGAL's
configurations file. To do so, she alters two template files: first, she
adds to `Installation/cmake/modules/CGALConfig_binary.cmake.in` (for the
CGAL in out-of-source-build):
``` {.cmake}
set(WITH_CGAL_Parallel "@WITH_CGAL_Parallel@")
 
set(CGAL_Parallel_LIBRARY "@CGAL_Parallel_LIBRARY@")

set(CGAL_Parallel_3RD_PARTY_INCLUDE_DIRS   "@CGAL_Parallel_3RD_PARTY_INCLUDE_DIRS@" )
set(CGAL_Parallel_3RD_PARTY_DEFINITIONS    "@CGAL_Parallel_3RD_PARTY_DEFINITIONS@" )
set(CGAL_Parallel_3RD_PARTY_LIBRARIES_DIRS "@CGAL_Parallel_3RD_PARTY_LIBRARIES_DIRS@" )
set(CGAL_Parallel_3RD_PARTY_LIBRARIES      "@CGAL_Parallel_3RD_PARTY_LIBRARIES@" )`
```
and similarly adds the following lines to
` Installation/cmake/modules/CGALConfig_install.cmake.in` (for installed
CGAL):
``` {.cmake}
set(WITH_CGAL_Parallel "@WITH_CGAL_Parallel@")
set(CGAL_Parallel_LIBRARY "@CMAKE_INSTALL_PREFIX@/@CGAL_INSTALL_LIB_DIR@/@CGAL_Parallel_LIBRARY_NAME@")

set(CGAL_Parallel_3RD_PARTY_INCLUDE_DIRS   "@CGAL_Parallel_3RD_PARTY_INCLUDE_DIRS@" )
set(CGAL_Parallel_3RD_PARTY_DEFINITIONS    "@CGAL_Parallel_3RD_PARTY_DEFINITIONS@" )
set(CGAL_Parallel_3RD_PARTY_LIBRARIES_DIRS "@CGAL_Parallel_3RD_PARTY_LIBRARIES_DIRS@" )
set(CGAL_Parallel_3RD_PARTY_LIBRARIES      "@CGAL_Parallel_3RD_PARTY_LIBRARIES@" )`
```
Lines like:
``` {.cmake}
set(CGAL_Parallel_USE_ZLIB                  "@CGAL_Parallel_USE_ZLIB@" )
```
might be required, too. And if so, in both files.

### Build

Then, after calling
``` {.bash}
> cmake ~/CGAL/workingcopy
> make
```
she will find the library `CGAL_Parallel` compiled from the sources in
`*/src/CGALParallel` in the `lib` directory of her build-path.

### Remark

-   The sources for the library can be spread over several packages, as
    indicated by the `*` in `*/src/CGALParallel`. However, only one of
    the packages is allowed to have the corresponding `CMakeLists.txt`
    file. It is recommended not to spread the files too much; it is best
    if all source files of one library are parts of one package.

## Support for Windows DLL, and ELF visibility

On Windows, shared libraries are called DLLs (dynamic-link library). The
implementation of DLLs differs from the implementation of shared
libraries on other systems by the way public symbols are exported from a
DLL and imported into an application (see [the Visual Studio reference
about DLLs](http://msdn.microsoft.com/en-us/library/1ez7dh12.aspx)). For
that reason, when one creates a library that can be compiled as shared
library, all the classes or symbols that must be accessible from outside
the library need to use special macros to declare symbols to export.
*Note that private functions or private data members of a class can be a
symbol that must be export, if the class contains inline public
functions that uses those internal symbols*.

### Definition of the export macro

First you need to create a file `include/CGAL/export/Parallel.h`, to
define the macros:

``` {.cpp}
// Copyright (c) 2011 GeometryFactory (France). All rights reserved.
//
// This file is part of CGAL (www.cgal.org); you can redistribute it and/or
// modify it under the terms of the GNU Lesser General Public License as
// published by the Free Software Foundation; either version 3 of the License,
// or (at your option) any later version.
//
// Licensees holding a valid commercial license may use this file in
// accordance with the commercial license agreement provided with the software.
//
// This file is provided AS IS with NO WARRANTY OF ANY KIND, INCLUDING THE
// WARRANTY OF DESIGN, MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.
//
// $URL$
// $Id$
// 
//
// Author(s)     : Laurent Rineau

#ifndef CGAL_PARALLEL_EXPORT_H
#define CGAL_PARALLEL_EXPORT_H

#include <CGAL/config.h>
#include <CGAL/export/helpers.h>

#if defined(CGAL_BUILD_SHARED_LIBS)

#  if defined(CGAL_Parallel_EXPORTS) // defined by CMake or in cpp files of the dll

#    define CGAL_PARALLEL_EXPORT CGAL_DLL_EXPORT
#    define CGAL_PARALLEL_EXPIMP_TEMPLATE

#  else // not CGAL_Parallel_EXPORTS

#    define CGAL_PARALLEL_EXPORT CGAL_DLL_IMPORT
#    define CGAL_PARALLEL_EXPIMP_TEMPLATE extern

#  endif // not CGAL_PARALLEL_EXPORTS

#else // not CGAL_BUILD_SHARED_LIBS

#  define CGAL_PARALLEL_EXPORT
#  define CGAL_PARALLEL_EXPIMP_TEMPLATE

#endif // not CGAL_BUILD_SHARED_LIBS

#endif //  CGAL_PARALLEL_EXPORT_H
```

You can copy, for example, the file
`GraphicsView/include/CGAL/export/Qt.h`, and adapt it. Note that the
macro `CGAL_Parallel_EXPORTS` used at line 28 is defined by the build
system generated by CMake. The name of that macro is
`''<libname>''_EXPORTS`: as here the library is named CGAL\_Parallel,
the macro is named `CGAL_Parallel_EXPORTS` (attention to the case).

The important macro that is defined by this file is:
`CGAL_PARALLEL_EXPORT` (all uppercase to respect the naming convention
of macros in CGAL).

### Use of the export macro

The export macro must be used in the header file that corresponds to the
.cpp file build in the library.

More precisely, the export macro must be inserted in the **declaration**
of any:

-   class that has parts of its implementation defined in a .cpp file in
    the library,
-   free function that has its definition in a .cpp file,
-   global variable that is declared in a header file but initialized in
    a .cpp file.

Example:

``` {.cpp}
// Do not forget to include the header that defines the export macros
#include <CGAL/export/Parallel.h>

// for a non-inline and non-template function:
CGAL_PARALLEL_EXPORT int parallel_do_overlap(Bbox_3, Bbox_3);

// for a non-template class that has part of its
// implementation in a .cpp file of the library:
class CGAL_PARALLEL_EXPORT Foobar {
  int private_stuff;
public:
  Foobar();
  void parallel_compute(Something input);
};
```

Note that at line 9, the export macro is inserted between the `class`
and the name of the class.

You must not insert export macro in the **definition** of a symbol: it
means that .cpp files should not contain uses of export macros.

See also:

-   <http://gcc.gnu.org/wiki/Visibility>

### Test of the visibility feature with GNU/g++

You can add `-fvisibility-ms-compat` to the `CMAKE_CXX_FLAGS` to check
the visibility feature with a non-Windows compiler. That works with:

-   llvm/clang,
-   the Linux version of the Intel compiler,
-   g++ versions 4.0 and after.
