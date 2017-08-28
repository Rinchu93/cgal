<!--TOC-->

# Table of Contents
* [Getting and Installing the Tools](#getting-and-installing-the-tools)
  * [doxygen](#doxygen)
    * [Syntax Highlighting](#syntax-highlighting)
  * [MathJax](#mathjax)
* [Building The Documentation](#building-the-documentation)
  * [Building Only a Single Package](#building-only-a-single-package)
  * [Looking at the Result](#looking-at-the-result)
  * [Html output postprocessing: `html_output_post_processing.py`](#html-output-postprocessing-html_output_post_processingpy)
    * [Getting it to work on Windows](#getting-it-to-work-on-windows)
  * [Testing the Documentation](#testing-the-documentation)
  * [Releasing the Documentation](#releasing-the-documentation)
  * [Changing things](#changing-things)
  * [PDF Output](#pdf-output)
    * [Using Doxygen](#using-doxygen)
    * [Alternative (linux,windows)](#alternative-linuxwindows)

<!--TOC-->

## Getting and Installing the Tools


To build the CGAL documentation, we use two tools. In the following
documentation, we use `/path/to/branch` to refer to the path where you
have a checkout of your git branch on your disk.

### doxygen

`doxygen` generates documentation for individual CGAL packages.
We have added a few features and fixes to doxygen, which we
communicate to its author to get them included upstream. The official
documentation is built with this patched version while all the
patches are not integrated in the official release. You may try
it if you wish (see `https://github.com/CGAL/doxygen`),
but it is not required to build a correct version of your
documentation. A windows executable is available
[here](doxygen-1.8.4-patched.gz).

- doxygen 1.8.13 works almost well. [This branch](https://github.com/CGAL/doxygen/tree/release_1_8_13_patched)
  contains the patch for having less output when `EXTRACT_ALL=ON`.

- doxygen 1.8.11 (+1.8.12) generates an incomplete website: the treeview is missing.
Maybe our hackery is incompatible? Also too many things are exported in
tags. Only two consecutive runs doxygen can be done. After that the tag
files must be deleted to avoid many warnings.

- doxygen 1.8.10 generates an incomplete website: the treeview is missing.

- doxygen 1.8.8 gives a segmentation fault.

- doxygen 1.8.5 has a bug and cannot build correctly the target `BGL_doc`
after some tag files exists (it will fill the memory before crashing).


The only remaining extra patch corresponds to this bug report: [`502d15f`](https://bugzilla.gnome.org/show_bug.cgi?id=684978) Bug 684978 - `EXTRACT_ALL` creates superfluous detail sections. See also this [PR](https://github.com/doxygen/doxygen/pull/452).

There is also the current [bug report](https://bugzilla.gnome.org/show_bug.cgi?id=770973)
about a blocking issue found in the 1.8.12 version.

#### Syntax Highlighting

-   Microsoft provides a
    [plugin](https://visualstudiogallery.msdn.microsoft.com/11a30c1c-593b-4399-a702-f23a56dd8548)
    for Visual Studio for syntax highlighting.
-   On Emacs there is [doxymacs](http://doxymacs.sourceforge.net/).

### MathJax

MathJax is required to be installed if you need to have typeset formulas
without an internet connection.

-   get MathJax:
    `git clone git://github.com/mathjax/MathJax.git /path/to/MathJax`
-   to configure the documentation to build with your local MathJax,
    change the CMake variable `CGAL_DOC_MATHJAX_LOCATION` to point to
    your locale MathJax

<b>Note:</b>

-   For alternative methods to get MathJax, see
    [here](https://docs.mathjax.org/en/latest/installation.html).
-   You have the possibility to disable MathJax and use fixed size
    bitmap by setting `USE_MATHJAX = NO` in the config file
    `/path/to/branch/Documentation/BaseDoxyfile.in`.

## Building The Documentation


To build the documentation on Unix:

``` {.bash}
cd /path/to/cgal/build/dir
cmake -DBUILD_DOC:BOOL=ON /path/to/cgal/ # if not already done
make doc
```

Alternatively, if your branch is fresh enough and contains commit [`96c64d48`](https://github.com/CGAL/cgal/commit/96c64d4866546c7a843a41485eb25e877f931bd6)
(merge of PR [`#1444`](https://github.com/CGAL/cgal/pull/1444)), you can directly call cmake on the doc `CMakeLists.txt`,
which will skip the `CGAL` configuration steps

``` {.bash}
cd /path/to/cgal/build/dir
cmake /path/to/cgal/Documentation/doc # if you use a CGAL branch
# or
cmake /path/to/cgal/doc # if you use a CGAL release
make doc
```

If `make` does not find target "doc", it is probably because your
branch is too old. Try: `make Documentation_doc`.

To build the documentation with Visual Studio, build the `doc` target in
Visual Studio.

With a self-built Doxygen, or with Doxygen from MacPorts
(`/opt/local/bin/doxygen`) you will probably need to adjust the CMake
variable `DOXYGEN_EXECUTABLE` to point to the right location.

After that, a single run suffices.

This will generate the output for each package and a master package with
the Introduction, Preliminaries, and Installation manuals and a complete
index.

### Building Only a Single Package

Often you only want to work on one package and building the whole
documentation would take longer than you would like. To do this:

``` {.bash}
# in /path/to/cgal/build/dir
make My_package_doc
```

in Visual Studio build the target `My_package_doc`.

Doing this requires that the `doc` target has been built twice before.

In case you are working on a new package, make sure that you followed
the steps discussed
[here](Writing-Documentation#new-packages-and-doxygen-configuration).

### Looking at the Result

The result will be stored in `/path/to/cgal/build/dir/doc_output/`. The
main entry point is:
`/path/to/cgal/build/dir/doc_output/Manual/index.html`. The
documentation of a respective package can be found under
`/path/to/cgal/build/dir/doc_output/My_package/index.html`.

### Html output postprocessing: `html_output_post_processing.py`

The postprocessing of the html output is triggered by building the
target <b>`doc_with_postprocessing`</b>:

``` {.bash}
# in /path/to/cgal/build/dir
make doc_with_postprocessing
```

Internally, the script `html_output_post_processing.py` is used to do
some output cleaning in the html output of doxygen. This script:

-   turns specific classes into concepts.
-   performs some scrubbing of the documentation to remove duplicate
    files, small glitches and the like.
-   uses special icons in `Concept and class list` pages.
-   numbers the figures using CGAL doxygen macros.

This script requires at least Python 2 and the library
[PyQuery](http://pypi.python.org/pypi/pyquery), and
[python-lxml](http://lxml.de/installation.html) v2.3.2 (or higher). To
check which python executable is used, you can check in your
`CMakeCache.txt` the value of `PYTHON_EXECUTABLE`.

Note that [pip](http://www.pip-installer.org/en/latest/) is a solution
to install python dependencies on machine you do not administrate.

#### Getting it to work on Windows

Getting the postprocessing working on Windows, we have not checked with `pip`
yet, but we used `easy_install` instead.

-   *The recommended way to install setuptools on Windows is to download
    `ez_setup.py` and run it. The script will download the appropriate
    .egg file and install it for you.*
    [1](https://pypi.python.org/pypi/setuptools#windows)
    [2](https://bootstrap.pypa.io/ez_setup.py)
-   Install the C++ libraries `libxml` and `libxslt`. As written on
    [3](http://www.xmlsoft.org/downloads.html) Igor Zlatkovic is now the
    maintainer of the Windows port, he provides binaries.
    [4](http://www.zlatkovic.com/libxml.en.html)
-   Add in `PATH` `C:\Python27`, `C:\Python27\Scripts`, and the `bin`
    directory of `libxml/libxslt`
-   In a `cmd.exe`
    -   run `easy_install lxml==2.3` to install the python bindings of
        libxml (the version is fixed to `2.3` as it is broken for the
        latest version)
    -   run `easy_install pyquery` to install the pyquery package

To check that everything is fine, you can run in a `cmd`

``` {.bash}
> python
>>> import lxml
>>> import pyquery
```

### Testing the Documentation

To create a small HTML overview detailing errors and warnings when
building the documentation use:

``` {.bash}
cd /path/to/cgal/build/dir
cmake -DBUILD_DOC:BOOL=ON -DCGAL_DOC_CREATE_LOGS:BOOL=TRUE /path/to/cgal/
make doc_and_publish_testsuite
```

### Releasing the Documentation

Use
`cmake -DBUILD_DOC:BOOL=ON  -DCGAL_DOC_RELEASE:BOOL=TRUE .. -DCGAL_DOC_MATHJAX_LOCATION:STRING=../../MathJax `
if you intend to make public the documentation.

-   The flag `CGAL_DOC_RELEASE` removes TODO-list, bug-list,...
-   The path related to MathJax indicates the relative location from a
    package directory (for example <https://doc.cgal.org/4.3/Kernel_23/>,
    or `doc_output/Manual` in your local build) to a local MathJax
    checkout on the server. This is required in particular when using
    `https`. Modern browsers protect users by disabling any `http` (like
    MathJax CDN) included in a `https` page.

### Changing things

The file `Documentation/Customizations.txt` contains a detailed list of
the customizations that are applied to doxygen. It should be maintained
if things are changed.

### PDF Output

Please note that the pdf output is not yet bug-free and largely a work
in progress.

#### Using Doxygen

In case you want the PDF output of your package:

1.  in `Documentation/BaseDoxyfile.in`, set the option `GENERATE_LATEX`
    to `YES`
2.  build targets as before
3.  `cd doc_output/Package_name/latex`
4.  `make`

Please note that the pdf output is not yet bug-free and largely a work
in progress.

#### Alternative (linux,windows)

Another option is to use the html to create a pdf.

Open in a new firefox window the user manual and the classified ref man.
Open in different tabs, all the pages you want to print.

You can then use the [firefox extension "Print pages to PDF"](https://addons.mozilla.org/fr/firefox/addon/print-pages-to-pdf/)
to print all the tabs into one pdf. In the preferences of that firefox
extension, make sure that the checkbox `PrintMedia` of the [tab `Pdf (webpage)->Styling`](http://printpdf.pf-control.de/index.php/en/styling/articles/styleing-preferences.html)
is checked. That option instructs the extension to print using the CSS
style dedicated to "print" media (in Doxygen, that tweaks several
things, and particularly the navigation box is hidden).

An example of the result can be found
[here](https://cgal.geometryfactory.com/CGAL/Members/wiki-images/1/17/Deformation-man-v3.pdf).

-   Issue with MathJax Formulas:
    -   There are issues with missing MathJax formulas on some machines
        and we have not figured the reason for this yet.
    -   Solution 1: disable MathJax globally or in your package. Just
        modifying `PackageName/doc/PackageName/Doxyfile.in`, i.e.,
        adding line
```
Don't commit this change
USE_MATHJAX = NO
```

   -   Solution 2: Ask somebody else to print for you and get the nicer
        formulas.
