<!--TOC-->

# Table of Contents
* [Linkage](#linkage)
* [Placing images in specific ways](#placing-images-in-specific-ways)
* [ccVar](#ccvar)
* [Groups](#groups)
* [Words in mathmode](#words-in-mathmode)
* [Mixing dummy headers and real documentation](#mixing-dummy-headers-and-real-documentation)
* [Deprecation](#deprecation)

<!--TOC-->

## Linkage

Doxygen uses different ways to link to link symbol names to
documentation pages. Understanding
[auto-linking](http://www.stack.nl/~dimitri/doxygen/autolink.html) saves
a lot of trouble when adapting documentation.

One main difference to the old tools is the way doxygen links to nested
types. In CGAL, a link to nested type would usually be
`\ccc{Foo<Bar>::Nested}` whereas doxygen requires the link to be
`` `Foo::Nested` `` or just `Foo::Nested`.

**Problem**
<br>&nbsp;&nbsp; Missing link for `\cgalHasModel`.<br>
**Solution**
<br>&nbsp;&nbsp; the type has to be backquoted, and the class must be prefixed with `CGAL::` as concepts are not in a namespace.

**Problem**
<br>&nbsp;&nbsp; Missing link to a symbol from another package.
<br>
**Solution**
<br>&nbsp;&nbsp; Add a package dependency in the file Package/doc/Package/dependencies and run `cmake .` before running `make doc_with_postprocessing`

**Problem**
<br>&nbsp;&nbsp; A text like ``A call to `make_surface_mesh(c2t3,surface, criteria, tag)` does...`` does not create a link when you write `` `make_surface_mesh(c2t3,surface, criteria, tag)` ``.
<br>
**Solution**
<br>&nbsp;&nbsp; Use `\link \endlink` and write: ``A call to \link make_surface\_mesh() `make_surface_mesh(c2t3,surface, criteria, tag)` \endlink does...`` instead, because the linking mechanism accepts only empty `()` or the exact API.

**Problem**
<br>&nbsp;&nbsp; How to get a link to a type obtained with a `typedef`?
<br>
**Solution**
<br>&nbsp;&nbsp; backquotes are enough for classes and functions, but such types you have to use `\link \endlink` and write: `` \link CGAL::Tag_true `Tag_true` \endlink ``. Note that there are no backquotes for the link, but backquotes so that what follows the link is typeset like code.

**Problem**
<br>&nbsp;&nbsp; My sentence "Object oriented programming is....." generates a link to the page of `CGAL::Object`. How to get rid of this link?
<br>
**Solution**
<br>&nbsp;&nbsp; Put a `%` in front of the word that you don't want to link. This also works in backquoted text. ``In a polyhedron you call `e->%next()` to get to the next edge ...``

## Placing images in specific ways


**Problem**
<br>&nbsp;&nbsp; I have special requirements how my images should be placed (e.g. next to each other or in-line).
<br>
**Solution**
<br>&nbsp;&nbsp; Use the raw commands of the targeted output mode directly protected by [latexonly](http://www.stack.nl/~dimitri/doxygen/commands.html#cmdlatexonly) and [htmlonly](http://www.stack.nl/~dimitri/doxygen/commands.html#cmdhtmlonly). Sometimes it is easier to combine two images into a single one with some image manipulation tools and just use that instead.

## ccVar


**Problem**
<br>&nbsp;&nbsp; The old CGAL manual tool let you define a variable name for an instance of the class you document, and the member function documentation was presented as a call applied to this variable. This allowed to use the variable name inside the method documentation.
<br>
**Solution**
<br>&nbsp;&nbsp; Using doxygen, you have to write "this object" or `*this`, or introduce an object name that you use consistently in the documentation.

## Groups

**Problem**
<br>&nbsp;&nbsp; `\ingroup` does not work
<br>
**Solution**
<br>&nbsp;&nbsp; Did you forget to `\defgroup` or does the name not match?

**Problem**
<br>&nbsp;&nbsp; I have several `\defgroup` spread over different files. How to get them listed in alphabetical order in the navigation panel?
<br>
**Solution**
<br>&nbsp;&nbsp; Change them to `\addtogroup` and put the `\defgroup` in one central file in the right order.

## Words in mathmode

**Problem**
<br>&nbsp;&nbsp; Words like "\$coefficient(..)\$" in mathmode are ugly.
<br>
**Solution**
<br>&nbsp;&nbsp; Use `$\mathrm{coefficient}(..)\$"`. The previous `latex2html` converter was relatively sloppy with mathmode. Switching to MathJax revealed the problem. The previously generated pdf version of the manual had the problem as well, but it went unnoticed.

## Mixing dummy headers and real documentation

If you want to add a new class and write the documentation in-source
directly to a package, you will need to add the file directly to the
input. In order to make doxygen aware these files should be processed,
you should update the `INPUT` field in the file `Doxyfile.in` of
the package.

**Example:**
```
INPUT += ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/Euler_operations.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/Helpers.h 
``` 


## Deprecation


As described in the
[Coding](Developing-Features-with-Git#coding)
section, use the Doxygen macro `\deprecated` to deprecate a class. The
deprecation should be done consistently in the source code file.
