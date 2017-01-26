<!--TOC-->

# Table of Contents
* [Introduction](#introduction)
* [Package License Headers](#package-license-headers)
* [Including License Headers in Package Header Files](#including-license-headers-in-package-header-files)
* [Using the License Checking Mechanism](#using-the-license-checking-mechanism)

<!--TOC-->

## Introduction
CGAL header files are under LGPL-3+ or GPL-3+. Some users only want to use
LGPL headers, other will get a commercial license for some packages. In order
to make sure that the header used follow what was intended, a license checking
mechanism has been implemented in CGAL. This page describes how it is working, and
what a new header file in an existing or new package must contain.

## Package License Headers
For each package under GPL-3+, a license file is available in 
`Installation/include/CGAL/license`. These files are automatically generated
using the template `Installation/include/CGAL/license/gpl.h.in`. Calling
`cmake -P generate_files.cmake` from `Installation/include/CGAL/license` will
read the file `Installation/include/CGAL/license/gpl_package_list.txt` and will
generate a license file for each entry in the file.
An entry in `gpl_package_list.txt` is a line with the short name of the package
(the name of the directory of the package) followed by the complete name of the
package (the name used in the documentation). For example 
```
Triangulation_2 2D Triangulations
```

## Including License Headers in Package Header Files
All the header files of a package must include the license file of its package.
All the include directives have been added using the script
`Scripts/developer_scripts/add_license_in_pkg_header.py`. It takes two arguments.
The first one is the short name of the package and the second one
(optional if identical) is the name of the directory containing the header files.
The script must be called at the root of a CGAL checkout.

Example calls:
```
# example for Triangulation_2 package
add_license_in_pkg_header.py Triangulation_2
# example for Polyhedron_3_IO that uses Polyhedron_3 license file
add_license_in_pkg_header.py Polyhedron_3  Polyhedron_3_IO

```

The script will update only headers that do not already contain the include
directive. The mechanism is rather simple and expects the include directive to
follow the include guards (that must be something like `CGAL_.*_H`).

Here is an example for the file `Delaunay_triangulation_2.h` in the `Triangulation_2`
package.
```
#ifndef CGAL_DELAUNAY_TRIANGULATION_2_H
#define CGAL_DELAUNAY_TRIANGULATION_2_H

#include <CGAL/license/Triangulation_2.h>
```

## Using the License Checking Mechanism
If you want to test this mechanism yourself, you need to update the file
`Installation/include/CGAL/license.h`. As explained in the file, enable
one of the checking mechanisms (error or warning):
```
// #define CGAL_LICENSE_WARNING  1   // in order to get a warning during compilation
// #define CGAL_LICENSE_ERROR    1   // in order to get an error during compilation
```
Then define a macro for all packages you think you are using:
```
#define CGAL_AABB_TREE_COMMERCIAL_LICENSE  20140101
```
The name of the macro is `CGAL_`+short name of the package uppercased+`_COMMERCIAL_LICENSE`.
The number following corresponds to the release date for which you have a
commercial license.
