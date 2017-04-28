<!--TOC-->

# Table of Contents
* [Writing Doxygen documentation in CGAL](#writing-doxygen-documentation-in-cgal)
  * [Directory Layout](#directory-layout)
  * [PackageDescription.txt](#packagedescriptiontxt)
  * [User Manuals](#user-manuals)
    * [Adding Figures](#adding-figures)
    * [Automatic Equation Numbering](#automatic-equation-numbering)
    * [How to Write a Good User Manual](#how-to-write-a-good-user-manual)
  * [Reference Manual](#reference-manual)
    * [Class Documentation](#class-documentation)
    * [Function Documentation](#function-documentation)
    * [Global function overloads](#global-function-overloads)
    * [Concept Documentation](#concept-documentation)
    * [Hiding part of the documentation](#hiding-part-of-the-documentation)
  * [CGAL Specific Macro Reference](#cgal-specific-macro-reference)
    * [Acronyms (User and Reference Manual)](#acronyms-user-and-reference-manual)
    * [Examples and Figures (User Manual)](#examples-and-figures-user-manual)
    * [Concepts and Models (Reference Manual)](#concepts-and-models-reference-manual)
    * [Package Descriptions (used in PackageDescription.txt)](#package-descriptions-used-in-packagedescriptiontxt)
    * [Miscellaneous Macros](#miscellaneous-macros)
  * [New Packages and Doxygen Configuration](#new-packages-and-doxygen-configuration)
    * [Package Overview](#package-overview)
    * [Doxyfile.in](#doxyfilein)
      * [Doxyfile.in defaults](#doxyfilein-defaults)
    * [Adding citations](#adding-citations)
    * [Adding package in biblio (since 5d62213cf)](#adding-package-in-biblio-since-5d62213cf)
  * [Standalone version of your package documentation](#standalone-version-of-your-package-documentation)
* [Resources](#resources)
* [Recommendations](#recommendations)

<!--TOC-->

## Writing Doxygen documentation in CGAL

Doxygen is a documentation tool that allows to put documentation in C++
comments in the source code. This can either be the real or fake header
files for CGAL classes and functions. We currently have these fake
header files as they were generated from the .tex files we had before
switching to doxygen.

This web page is not about doxygen itself, but only explains how the
CGAL project makes use of it. So this page will not explain that
`\tparam` is a doxygen macro that should be used to explain a
<b>t</b>emplate <b>param</b>eter. See the Section
[Resources](#resources) at the bottom of this page.

CGAL is physically structured into packages, which is reflected in the
repository's structure. Generally, each package in the library will
contribute some chapters to the manuals, some might only contribute a
section to some larger chapter.

The details on how to add the documentation of a **new package** are
given in [this section](#new-packages-and-doxygen-configuration).

### Directory Layout

``` {.cpp}
Triangulation_2/doc/Triangulation_2/
  CGAL/
    Delaunay_triangulation_2.h
  Concepts/
    TriangulationVertexBase_2.h // concept headers
  fig/
    infinite.gif
  PackageDescription.txt        // the package overview
  Triangulation_2.txt           // the user manual
  examples.txt
  Doxyfile.in                   // the Doxygen configuration
  dependencies                  // a file listing the dependencies
```

The doxygen documentation can be either in dummy headers or in the real
source code.

There is a one-to-one correspondence between chapters in the
documentation and source code management (SCM) CGAL packages.

-   Directory structure documentation summary (detailed in the following
    subsections):
    -   `PackageDescription.txt` is the only mandatory file.
    -   `Concepts` contains the *fake* `.h` files describing the
        concepts
    -   `CGAL` contains the public interface of CGAL classes and
        function that we want to document. Note that this can be
        directly done in the original source files.
    -   `fig` contains all figures used in the package
    -   `examples.txt` contains the list of all examples of the package.
        Using the `\example` command
        [1](http://www.stack.nl/~dimitri/doxygen/commands.html#cmdexample),
        this generates a list of all examples in the package and all
        examples used in the manual (through `\cgalExample{1}` command)
        refers to them.
    -   `Triangulation_2.txt` should contain the user manual

### PackageDescription.txt

This file defines general information that is displayed in the Package
Overview and has to follow a certain structure.

``` {.cpp}

/// \defgroup PkgTriangulation2 2D Triangulation Reference
/// \defgroup PkgTriangulation2Concepts Concepts
/// \ingroup PkgTriangulation2

/// \defgroup PkgTriangulation2TriangulationClasses Triangulation Classes
/// \ingroup PkgTriangulation2

/// \defgroup PkgTriangulation2TraitsClasses Traits Classes
/// \ingroup PkgTriangulation2

/// \defgroup PkgTriangulation2VertexFaceClasses Vertex and Face Classes
/// \ingroup PkgTriangulation2

/// \defgroup PkgTriangulation2Miscellaneous Miscellaneous
/// \ingroup PkgTriangulation2

/*!
\addtogroup PkgTriangulation2
\todo check generated documentation

\cgalPkgDescriptionBegin{2D Triangulation,PkgTriangulation2Summary}
\cgalPkgPicture{cdt2d-small.png}

\cgalPkgSummaryBegin
\cgalPkgAuthor{Mariette Yvinec}
\cgalPkgDesc{This package  allows to build and handle various triangulations for point sets two dimensions. 
Any \cgal triangulation covers the convex hull of its vertices. 
Triangulations are build incrementally  and can be modified by insertion  or removal  of vertices.  
They offer point location facilities.  The package provides ... }
\cgalPkgManuals{Chapter_2D_Triangulations,PkgTriangulation2}
\cgalPkgSummaryEnd

\cgalPkgShortInfoBegin
\cgalPkgSince{0.9}
\cgalPkgDependsOn{\ref PkgTDS2Summary}
\cgalPkgBib{cgal:y-t2}
\cgalPkgLicense{\ref licensesGPL "GPL"}
\cgalPkgDemo{Delaunay Triangulation,delaunay_triangulation_2.zip,Regular Triangulation,regular_triangulation_2.zip,Constrained Delaunay Triangulation,constrained_delaunay_triangulation_2.zip}
\cgalPkgShortInfoEnd

\cgalPkgDescriptionEnd

*/
```

where `PkgTriangulation2` is the top-level group of the package and
`Chapter_2D_Triangulations` an anchor in the User Manual. This makes the
same block that can be seen in the Package Overview show up in the
documentation of the group.

In order to link to the package description in the Package Overview
page, you add the suffix `Summary` to the package name, in our example:
`PkgTriangulation2Summary`. This is useful for expressing package
dependencies. As the triangulation package depends on the triangulation
data structure package we write
`\cgalPkgDependsOn{\ref PkgTDS2Summary}`.

The macro `\cgalPkgManuals{..}` expects the anchor of the user and
reference manual. The macro `\cgalPkgBib` is there for the bibtex entry
of this package.

Doxygen allows to regroup classes and organize groups hierarchically.
The `\defgroup` and `\ingroup` commands defining such a hierarchy should
be at the top of the package description to define the whole group
hierarchy in one place.

### User Manuals

The set-up allows a fully free form doxygen documentation. To keep with
the general style in CGAL some rules should be followed. The User Manual
should be the Doxygen mainpage of the package.

A typical example is (file: `Triangulation_2.txt`):

``` {.cpp}
namespace CGAL { // provide a scope for references inside our text to reduce verbosity

/*!
\mainpage 2D Triangulations
\anchor Chapter_2D_Triangulations

\cgalAutoToc
\author Mariette Yvinec

\image html tr1dt1.png

This chapter describes the two dimensional triangulations of \cgal. 
Section \ref Section_2D_Triangulations_Definitions recalls the
main definitions about triangulations.


\section Section_2D_Triangulations_Definitions Definitions

An introductory section.

\subsection Subsection_2D_Triangulations_Basic_Description Description 

The class `Triangulation_2<Traits,Tds>` serves as a base class for the other  2D triangulations classes and ....   
Figure \ref Triangulation_2D_Fig_infinite_vertex  illustrates the *infinite vertex*, which serves as....

\anchor Triangulation_2D_Fig_infinite_vertex
\image html infinite.gif "Infinite vertex and infinite faces"

As we have seen in Section \ref Subsection_2D_Triangulations_Basic_Description ....

The empty circle property (see \cite d-vrtdd-09) guarantees that... 


\subsection Subsection_2D_Triangulations_Delaunay_Terrain Example: a Delaunay Terrain 

The following code creates a Delaunay triangulation with 
the usual Euclidean metric for the vertical projection of a 
terrain model. The points have elevation, that is they are 3D points,
but the predicates used to build the Delaunay triangulation
are computed using only the `x` and `y` coordinates 
of these points.

The class `Projection_traits_xy_3<K>` is part of the 2D and 3D Linear Geometric Kernel,

\cgalExample{Triangulation_2/terrain.cpp}

*/

}
```

The manual can also be split into multiple pages and grouped together
with the command `\subpage`.

``` {.cpp}
/*!
\mainpage My Package
\author Alice and Bob

Introductory text.

Read the following sections:
  - \subpage intro Introduction
  - \subpage stuff Stuff
  - \subpage more_stuff More Stuff
*/

/*!
\page intro Introduction
*/

/*!
\page stuff Stuff
*/

/*!
\page more_stuff More Stuff
*/
```

#### Adding Figures

The inclusion of a figure with captions, by opposition to a figure
without caption that does not need to be referenced (like package
illustration for example) is eased using CGAL custom macros. These
macros must be used if the automatic numbering of figures is expected in
the page. Note that the numbering of figures is done using the
post-processing script `html_output_post_processing.py`. Note also that
as shown in the example above, a figure without caption should be
included using the doxygen command `\image` or any specific html code
in-between `\htmlonly` and `\endhtmlonly`.

If you want to display horizontally aligned images on the page (with no
resizing), use the macro
`\cgalFigureBegin{ref_id,image1.png,image2.png,...}` where `ref_id` is
the name of the figure anchor. Overloads of this macro from 1 to 4
images are provided (more can be added into Documentation/Doxyfile, see
`ALIASES` section). The caption must then follow and the end must be
indicated by using the macro `\cgalFigureEnd`. `Figure i` will be added
to your caption, `i` begin the number assigned to the figure in the
page. To reference a figure, use the macro `\cgalFigureRef{ref_id}`.
This will be replaced by a link with name `Figure i` where `i` is the
number assigned to the figure in the page.

``` {.cpp}
/*!
\cgalFigureBegin{pkgiceref,icecream.png}
This is a nice ice cream.
\cgalFigureEnd

As you can see a dessert on \cgalFigureRef{pkgiceref}.
*/
```

In case you want to have more complex assembly or resizing of images,
you need to directly use html code. The macro
`\cgalFigureAnchor{ref_id}` that creates the anchor for the figure must
be put before the html code for the image. The caption must be enclosed
by `\cgalFigureCaptionBegin{ref_id}` and `\cgalFigureCaptionEnd`.

``` {.cpp}
/*!
\cgalFigureAnchor{mink_figpgn_offset}
<center>
<table border=0>
<tr>
<td>\image html convex_offset.gif</td>
<td>\image html offset_decomp.gif</td>
<td>\image html offset_conv.gif</td>
<tr align="center"><td>(a)</td><td>(b)</td><td>(c)</td></tr>
</table>
</center>
\cgalFigureCaptionBegin{mink_figpgn_offset}
(a) Offsetting a convex polygon. 
(b) Computing the offset of a non-convex polygon by decomposing it to convex sub-polygons. 
(c) Offsetting a non-convex polygon by computing its convolution with a disc. 
The convolution cycle induces an arrangement with three faces, whose winding numbers are shown enclosed in dashed circles.
\cgalFigureCaptionEnd
*/
```

Using the aforementioned macros in both cases put the caption in a
division (`div` html tag) with `class="cgal_figure_caption"`. Thus the
formatting of figure captions is globally controlled in the main css
file (`cgal_stylesheet.css`).

Here is an example for the Nef\_S2 package:

```
@INCLUDE = ${CGAL_DOC_PACKAGE_DEFAULTS}

PROJECT_NAME = "CGAL ${CGAL_DOC_VERSION} - 2D Boolean Operations on Nef Polygons Embedded on the Sphere"
HTML_EXTRA_FILES           =  ${CGAL_PACKAGE_DOC_DIR}/fig/halfspace.png \
                              ${CGAL_PACKAGE_DOC_DIR}/fig/complex.png

```

#### Automatic Equation Numbering

We use the MathJax [equation
numbering](http://docs.mathjax.org/en/latest/tex.html#automatic-equation-numbering)
for all user manual pages (AMSmath configuration). This mean that an
equation without label is not numbered.

The following equation will not be numbered:

``` {.cpp}
/*!
\f[
\begin{equation}
   E = mc^2
\end{equation}
\f]
*/
```

while this one will:

``` {.cpp}
/*!
In equation \f$ \eqref{eq:sample} \f$, we find the value of an
interesting integral:

\f[
\begin{equation}
  \int_0^\infty \frac{x^3}{e^x-1}\,dx = \frac{\pi^4}{15}
  \label{eq:sample}
\end{equation}
\f]
*/
```

#### How to Write a Good User Manual

In contrast to the reference manual, the users’ manual is intended to be
read by users who are first considering whether to use part of the
library. Thus the descriptions provided here should give sufficient
information for a user to understand the functionality provided by the
packages, but this does not mean, for example, that all functions and
classes need to be described in great detail. Descriptions should be
given in a more general way than in the reference manual. For example,
one might mention that there is a function called `ch_jarvis` that
implements the Jarvis march convex hull algorithm, but one need not
explicitly state what the arguments of this function are, what the
traits class requirements are, or the template parameters. The users’
manual should also provide examples that are more lengthy than the ones
provided in the reference manual. The examples should naturally be
accompanied by sufficient explanation of the code in order for them to
be understandable, and one should aim for examples that illustrate the
most interesting features of a package and not necessarily the most
complicated ones.

You should mention the precise name of the class(es) or function(s)
being described (although NOT in the table of contents) so if the user
wants the functionality being described it will be easy to find the
thing that provides it. If the precise name is given (*i.e.*, the name
given at the top of the reference page), then a hyperlink can be created
automatically.

As a rule, each chapter should include:

-   definitions necessary to describe the functionality (usually at the
    beginning of the chapter);
-   a section describing the software design, where applicable. When
    there are no real design issues to explain, then a section isn’t
    really warranted but at the very least the interface to the class or
    function should be briefly explained (*e.g.*, ``the class
    `CGAL::Cone<T, DS>` has two template parameters; the first
    represents the traits class and the second the data structure``, or
    ``the function `flavor(b, e, r)` computes the value 42 from an
    iterator range `[b, e)` over a set of points. The computed value is
    stored in the output iterator `r` ``);
-   one or more examples, which are included in the `examples` or `demo`
    directory of the package and inserted into the documentation using
    the `\cgalExample{}` command.
-   a section (or paragraph) with information about the implementation,
    which includes:
    -   references to the papers describing the algorithms/DS’s
        implemented;
    -   comments on the running times;
    -   and perhaps a short description of how the algorithm works or
        the data structure is built.

-   pictures of resulting output are good too.

The table of contents reflects the organization of the manual, and it
should be a concise but informative listing. These specific guidelines
regarding the organization are provided:

-   There should be an introductory section that actually introduces the
    chapter, perhaps providing definitions of relevant terms. It should
    not be followed immediately by examples, as this implies that the
    entire description was contained in the introduction.
-   The section describing software design should be labeled (you
    guessed it) “Software Design.”
-   Example programs should have entries in the table of contents and
    the user should be able to figure out quite easily what this example
    illustrated from the table of contents. This means, examples should
    be in sections of their own and the sections should have descriptive
    names (*i.e.*, “Example Constructing a Vanilla Cone” instead of just
    “Example”, unless this is a subsection of a section entitled
    “Vanilla Cone”).
-   The examples should appear near the things of which they are
    examples. So for chapters describing more than one class (such as
    Triangulations) or one class that achieves different things by using
    different traits classes (such as Arrangements) or more than one
    global function, the examples should appear in the sections
    corresponding to each class. For example, if you had a section
    labeled, say, "Vanilla Cones" and then you do some explanations
    about what vanilla cones are and finally show an example of
    constructing one, the expected organization would be
```
\section vanilla_cones Vanilla Cones
\subsection vanilla_cones_defs Definition
\subsection vanilla_cones_more More
\subsection vanilla_cones_example Example
```
or
```
\subsection vanilla_cones_ex_example Example with Extra Stuff
```

    For chapters describing a single class (such as HalfedgeDS), the
    examples can appear in a section of their own.

-   Examples should generally not span more than a page. Advanced
    examples are a possible exception.

In general, one should describe things at a level of detail that gives
people enough information to determine if they want to refer to the
reference page for the full details and one should use the precise name
of a reference manual item so that cross-linking (by humans or the HTML
converter) is possible. For example, you might say:

```
The fact that points are required to be chocolate in order to compute
a Hundink cone means that the traits class for a Hundink cone must
provide flavored predicates. The precise description of the
requirements is given by the concept `HundinkConeTraits_6`. The class
`Hundink_cone_traits_6` is a model of this concept.
```


### Reference Manual

The Reference Manual documents classes, concepts and functions.

#### Class Documentation

A few additional macros are provided to make documentation more uniform
and documenting hassle-free.

``` {.cpp}
namespace CGAL{

/*!
\ingroup PkgTriangulation2TriangulationClasses

\brief The class `Triangulation_2` is the basic class 
designed to handle triangulations 
of set of points \f$ { A}\f$ in the plane. 

\details Such a triangulation has vertices at the points of \f$ { A}\f$ 
and its domain covers the convex hull of \f$ { A}\f$. 
It can be viewed as a planar partition of the plane 
whose bounded faces are triangular and cover 
the convex hull of \f$ { A}\f$. The single unbounded face of this partition 
is the complementary of the convex hull of \f$ { A}\f$. 

\tparam Traits is the geometric traits, it is to be instantiated by 
a model of the concept `TriangulationTraits_2`. 

\tparam Tds is the triangulation data structure, 
it has to be instantiated by a model of the concept `TriangulationDataStructure_2`. 
By default, the triangulation data structure is instantiated by 
`CGAL::Triangulation_data_structure_2 < CGAL::Triangulation_vertex_base_2<Gt>, CGAL::Triangulation_face_base_2<Gt> >`. 
 */

template< typename Traits, typename Tds >
class Triangulation_2 : public Triangulation_cw_ccw_2 {

public:
// This is a comment with only two slashes. 
// Those as well as /* .. */ are ignored by doxygen. 
// Doxygen parses ///  and /*!   See the doxygen manual for details.
// Doxygen provides default grouping by types, constructors and access right, but member groups are often helpful.

/// \name Types 
/// @{

/*! 
the traits class. 
*/ 
typedef Traits Geom_traits; 

/*! 
iterator over finite vertices. 
*/ 
typedef unspecified_type Finite_vertices_iterator; 

/// @}

  /// \name Accessors and Getters
  /// An important sentence goes here.
  /// @{

  /// Documentation.
  int& get() {}

  /// @}
  
  /// \name Operations
  /// The triangulation offers several functions that allow to ...

  /// @{
  
  /*!
     \brief writes the vertices incident to `v` into `out`.
     
     \details This function is a convenience function for people who do not feel comfortable with circulators.
     
     \tparam OutputIterator  must be a model of `CopyConstructible`.
  */
  template<typename OutputIterator>
  void incident_vertices(Vertex_handle vh, OutputIterator out);


#ifdef DOXYGEN_RUNNING
  typedef unspecified_type iterator;
#else 
  typedef boost::boing_iterator<Gnus,Gnats> iterator;
#endif
  
  /// @}
};
}
```

What is CGAL specific is the `unspecified_type`. As doxygen expects
legal C++ code, we have to provide something in a typedef, even if we
consider it as an implementation detail. In case it is not a fake header
file, but a real one, we need conditional doxygenization/compilation. We
use the C++ preprocessor for that: `DOXYGEN_RUNNING` is defined when we
generate the documentation in order that it takes the
`unspecified_type`.

#### Function Documentation

When mentioning a function you must write an opening and closing
parenthesis at the end, to increase readability. For example write ``The
function `convex\_hull_2()` allows to ...``.

#### Global function overloads

If you want to document all overloads of a given function into the same
page, the way it is currently done in the manual is to create a group
with the name of the function. Then all overloads are added into this
group. Then each time you want to refer to the set of overloads, you can
directly refer to the group. Use back-ticks around the group name if you
want typewriter mode for the group name. It is advised to use another
name that the function name for the group name. In the Kernel, we use
the suffix `_grp` appended to the function name.

Example:

``` {.cpp}

/// \defgroup y_equal_grp CGAL::y_equal()
/// \ingroup kernel_global_function
/// \sa `compare_y_grp`
/// \sa `x_equal_grp`
/// \sa `z_equal_grp`

/// @{

/*!
returns `true`, iff `p` and `q`
have the same `y`-coordinate.
*/
template <typename Kernel>
bool y_equal(const CGAL::Point_2<Kernel> &p,
const CGAL::Point_2<Kernel> &q);

/*!
returns `true`, iff `p` and `q`
have the same `y`-coordinate.
*/
template <typename Kernel>
bool y_equal(const CGAL::Point_3<Kernel> &p,
const CGAL::Point_3<Kernel> &q);

/// @}
```

#### Concept Documentation

Because doxygen has no notion of concepts, they are documented just as
classes. Concepts must be in the Concepts directory and you must add the
`\cgalConcept` macro which allows a postprocessing script to replace,
where necessary, occurrences of the word \*class\* with \*concept\* in
the generated html.

We further provide macros to express relationships like `\cgalHasModel`
or `\cgalRefines`.

``` {.cpp}

/*!
\ingroup PkgTriangulation2Concepts
\cgalConcept

The concept `TriangulationVertexBase_2` describes the requirements for the 
vertex base class of a triangulation data structure  to be plugged in a basic, 
Delaunay or constrained triangulation. 

The concept `TriangulationVertexBase_2` refines the concept 
`TriangulationDSVertexBase_2` adding geometric information: 
the vertex base of a triangulation stores a point. 

\cgalRefines `TriangulationDSVertexBase_2` 

\cgalHasModel `CGAL::Triangulation_vertex_base_2<Traits,Vb>`

\sa `TriangulationDataStructure_2` 
\sa `TriangulationDataStructure_2::Vertex` 
\sa `CGAL::Triangulation_vertex_base_2<Traits>` 

*/

class TriangulationVertexBase_2 {
public:

/// \name Types 
/// @{

/*! 
Must be the same as the point type `TriangulationTraits_2::Point_2` 
defined by the geometric traits class of the triangulation. 
*/ 
typedef unspecified_type Point; 

/// @} 

.
.
.

}; /* end TriangulationVertexBase_2 */
```

Because concepts are not in the CGAL namespace we must prefix classes
with their namespace for `\cgalHasModel`.

#### Hiding part of the documentation

If you have some functions that are public but you do not want them to
appear in the reference manual, the way [recommended by
doxygen](http://www.stack.nl/~dimitri/doxygen/manual/commands.html#cmdcond)
to ignore parts of a file is to use the `\cond` and `\endcond` commands.
An alternative to hide details to users but not to developers is to use
the
[`\internal`](http://www.stack.nl/~dimitri/doxygen/manual/commands.html#cmdinternal)
command.

If you want to expose a different portion of the code to doxygen (to
hide an implementation detail for example), for convenience the macro
`DOXYGEN_RUNNING` is defined during doxygen run.

``` {.cpp}

#ifndef DOXYGEN_RUNNING
typedef internal::terrible_name<TINTIN, MILOU> type;
#else
typedef unspecified_type type;
#endif
```

### CGAL Specific Macro Reference

#### Acronyms (User and Reference Manual)

- `\cgal`
- `\protocgal`
- `\plageo`
- `\stl`
- `\leda`
- `\gcc`
- `\cpp`
- `\cpp11`
- `\CC`:

#### Examples and Figures (User Manual)

- `\cgalExample{1}`: The argument is an example file prefixed with the package path (`Triangulation_2/example1.cpp` for example) that is verbatim pasted onto the page.
- `\cgalFigureRef{1}`: The argument is an anchor. It creates a link with the text "Figure" followed by the automatically generated number. This one can also be used in the reference manual.
- `\cgalFigureBegin{2}`
- `\cgalFigureBegin{3}`
- `...`
- `\cgalFigureBegin{10}`
:   The first argument is an anchor. Each extra argument is the filename
    of an image to be added to the figure. The text on the next line is
    the beginning of the caption of the figure.
- `\cgalFigureEnd`: Indicates the end of the caption of the figure.
- `\cgalFigureAnchor{1}`: The argument is an anchor. Only use this if `cgalFigureBegin/End` are not general enough.
- `\cgalFigureCaptionBegin{1}` : The argument is the anchor used in `\cgalFigureAnchor{1}`. Begin marker for the caption. Only use this if `cgalFigureBegin/End` are not general enough.
- `\cgalFigureCaptionEnd` : End marker for the caption. Only use this if `cgalFigureBegin/End` are not general enough.

#### Concepts and Models (Reference Manual)

- `\cgalConcept` : Must be put in the comment of a concept, as doxygen does not natively support concepts.
- `\cgalConceptNamespace` : Use to indicate a concept namespace. It must precede the concept namespace declaration in `PackageDescription.txt`.

example:
```
`\cgalConceptNamespace`
` namespace Kernel{}`
```

- `\cgalRefines` : Name a concept that this concept refines. Adjacent commands will be merged under one heading.
- `\cgalModels` : Name a concept that is modeled by this class. Adjacent commands will be merged under one heading.
- `\cgalGeneralizes` : Name a concept that this concept generalizes. Adjacent commands will be merged under one heading.
- `\cgalHasModel` : Name a model of this concept. Adjacent commands will be merged under one heading.

- `\cgalRequires` : Name a requirement. Adjacent commands will be merged under one heading. In the old manual tool ccRequires was often used to express that a template argument must be model of a certain concept. With doxygen it is usually better to use `\tparam` or `\param` for this purpose.
- `\cgalDebugFunction` : This adds a brief description to tell that this function is for debugging purpose. This is needed if the description of your function only contains a paragraph enclosed by `\cgalDebugBegin` and `\cgalDebugEnd`.
- `\cgalDebugBegin` : Starts a debugging related documentation part.
- `\cgalDebugEnd` : Ends a debugging related documentation part.

- `\cgalAdvancedFunction` : This adds a brief description to tell that this function is for advanced uses. This is needed if the description of your function only contains a paragraph enclosed by `\cgalAdvancedBegin` and `\cgalAdvancedEnd`.
- `\cgalAdvancedClass` : Same as previous but for a class.
- `\cgalAdvancedBegin` : Starts an advanced feature documentation part.
- `\cgalAdvancedEnd` : Ends an advanced feature documentation part.

#### Package Descriptions (used in PackageDescription.txt)

The following commands should be used in the order presented here.

- `\cgalPkgDescriptionBegin` : Begin of the package description
- `\cgalPkgPicture{1}` : Figure for the package description.
- `\cgalPkgSummaryBegin` : Begin of the summary
- `\cgalPkgAuthors{1}` : The argument is a comma separated list of first name and name.
- `\cgalPkgAuthor{1}` : The argument is a first name and a name.
- `\cgalPkgDesc{1}` : The argument is the textblock of a package description.
- `\cgalPkgSummaryEnd` : End of the summary.
- `\cgalPkgShortInfoBegin` : Begin of the short info section.
- `\cgalPkgSince{1}` : The argument is the release number when the package was introduced.
- `\cgalPkgDependsOn{1}` : The argument is a sentence with references to other packages.
- `\cgalPkgLicense{1}` : The argument is a text and a reference to the license.
- `\cgalPkgBib` : The argument is the bibtex entry for this package.
- `\cgalPkgDemo{2}` : (optional) The two arguments are the text and the zip file containing the demo.
- `\cgalPkgDemo{4}` : (optional) Same as the 2 argument version but when 2 demos are available
- `\cgalPkgDemo{6}` : (optional) Same as the 2 argument version but when 3 demos are available
- `\cgalPkgShortInfoEnd` : End of the short info section.
- `\cgalPkgDescriptionEnd` : End of the package description
- `\cgalPkgManuals{2}` : The arguments are the anchors of the user and reference manual.

#### Miscellaneous Macros

- `\cgalFootnote{1}` : The argument is the footnote text.
- `\cgalAutoToc` : Create a full (all levels) table of contents.
- `\cgalTagTrue` : Creates a link for `TagTrue`.
- `\cgalTagFalse` : Creates a link for `TagFalse`.
- `\cgalHeading{1}`: Create a heading for a section in the user manual of a function or a class (same formatting as specific doxygen macros such as - `\pre, \param, ...- `
- `\cgalModifBegin` : Begin of a paragraph modified (used only during the submission/reviewing process).
- `\cgalModifEnd` : End of a paragraph modified.
- `\cgalReleaseNumber` : The release version (available only in the `Documentation` package)

### New Packages and Doxygen Configuration

#### Package Overview

The package overview page is a doxygen page where each package is placed
inside the corresponding section using the macro
- `\package_listing{package_directory_name}`.

In `Documentation/doc/Documentation/packages.txt`:

``` {.cpp}
...
...
\package_listing{Envelope_2}
\package_listing{Envelope_3}

\section PartTriangulationsAndDelaunayTriangulations Triangulations and Delaunay Triangulations

\package_listing{Triangulation_2}
\package_listing{Triangulation_2/TDS_2}
\package_listing{Triangulation_3}
\package_listing{Triangulation_3/TDS_3}
\package_listing{Periodic_3_triangulation_3}
\package_listing{Alpha_shapes_2}
\package_listing{Alpha_shapes_3}
...
...
```

#### Doxyfile.in

The Doxygen configurations of each package are maintained in the file
`My_package/doc/My_package/Doxyfile.in`. This provides a common
configuration base for all packages and enables to tweak the output to
the package requirements.

The `Doxyfile.in` needs to contain a line referring to the base
configuration, the name of the package, and the place of the doxygen
input.

If the package depends on the documentation of other packages (e.g.
links to reference pages of another package) a
`My_package/doc/My_package/dependencies` file has to be provided as
well. This file lists the code name of each dependency on a line.

Here is the example of the AABB-tree package where the documentation is
directly inside header files:

``` {.bash}
@INCLUDE = ${CGAL_DOC_PACKAGE_DEFAULTS}

PROJECT_NAME =  "CGAL ${CGAL_CREATED_VERSION_NUM} - 3D Fast Intersection and Distance Computation (AABB Tree)"

# custom options for this package
EXTRACT_ALL                =  false
HIDE_UNDOC_MEMBERS         =  true
HIDE_UNDOC_CLASSES         =  true
```

and the `dependencies` file:

``` {.bash}
Manual
Kernel_23
STL_Extension
Algebraic_foundations
Circulator
Stream_support
Box_intersection_d
Polyhedron
```

Note that "Manual" is required for every package as it provides the link
for licensing etc.

Here is the example of the Triangulation\_2 package where the
documentation is inside fake header files:

``` {.bash}
@INCLUDE = ${CGAL_DOC_PACKAGE_DEFAULTS}

PROJECT_NAME = "CGAL ${CGAL_CREATED_VERSION_NUM} - 2D Triangulation Data Structure"
IMAGE_PATH  +=  ${CMAKE_SOURCE_DIR}/Triangulation_2/doc/Triangulation_2/fig # not the default image path
```

`INPUT` is set automatically by cmake. If a `CGAL` directory does not exist
in the `doc` directory, we assume that the doc is inline and the path to
the include directory of the package is automatically appended to `INPUT`.

In the case of mixed documented headers, you need to set `INPUT` by hand. You
specify headers one by one like in the BGL package:
```{.bash}
INPUT += ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/Euler_operations.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/iterator.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/helpers.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/selection.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/split_graph_into_polylines.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/copy_face_graph.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/Graph_with_descriptor_with_graph.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/Dual.h \
         ${CGAL_PACKAGE_INCLUDE_DIR}/CGAL/boost/graph/convert_nef_polyhedron_to_polygon_mesh.h
```
or simply
```{.bash}
INPUT += ${CGAL_PACKAGE_INCLUDE_DIR} 
```
if all headers must be considered.
The `doc` directory is always added in `INPUT`.

##### Doxyfile.in defaults

The base configuration provides defaults that will work if the package
sticks to normal CGAL conventions.

* `IMAGE_PATH` : `My_package/doc/My_package/fig`
* `EXAMPLE_PATH` : `My_package/examples` 
  * if you need to add the example path of another package you must use `CGAL_PKG_EXAMPLE_DIR` like in the BGL package:
```
EXAMPLE_PATH            =  ${CGAL_Surface_mesh_skeletonization_EXAMPLE_DIR} \
                              ${CGAL_BGL_EXAMPLE_DIR}
```
* `STRIP_FROM_PATH` : `My_package/doc/My_package/` and `My_package/include`
* `STRIP_FROM_INC_PATH` : `My_package/doc/My_package/` and `My_package/include`

Some options **should** not be overwritten by a package:

-   `OUTPUT_DIRECTORY`
-   `GENERATE_TAGFILE`
-   `TAGFILES`
-   `ALIASES` (append only with `+=`)

All other defaults can be found in the file
`Documentation/BaseDoxyfile.in`

#### Adding citations

All new citations need to be added in
`Documentation/biblio/cgal_manual.bib`. Use the `\cgalCite` macro to
link to them.

#### Adding package in biblio (since 5d62213cf)

There is no need to update any file now to get your package added in the
file `how_to_cite_cgal.bib`. It will be automatically generated by cmake
and the script `Documentation/doc/scripts/generate_how_to_cite.py`.

### Standalone version of your package documentation

If you need a stand alone version of the documentation for a submission
for example, there are two options:

-   Compile the latex documentation generated by doxygen (when
    `GENERATE_LATEX = YES` in your package doxyfile). Note that the
    latex generation might issue a few errors that do not prevent the
    generation

of the manual. For now the figure are not included, it only requires a
handling of the cgal doxygen macros (and was not done because no correct
solution has been found for now to handle the size of the pictures).

-   Extract the minimal html documentation needed: you will need to ship
    the folder `Manual` and the folder `Your_package`. In case you
    want to be able to read it offline (without internet connection),
    you will need to remove the `MathJax` dependency by setting
    `USE_MATHJAX = NO` in your package doxyfile.

Generating a PDF of the documentation with modifications between two
versions highlighted ("diff") can be done using latexdiff. However,
latexdiff finds "false positive" differences in the links (because link
"target" names contain a different unique ID each time); this can be
solved setting PDF\_HYPERLINKS to NO in the Doxyfile, at the expense of
loosing the links.

For some problems with the current output, please see here 
**[Standalone Problems](Standalone-Problems)**

## Resources


The manual and some of the more important pages when writing doxygen
documentation:

-   Doxygen Manual: <http://www.stack.nl/~dimitri/doxygen/manual.html>
-   Doxygen Commands:
  <http://www.stack.nl/~dimitri/doxygen/commands.html>
-   Supported HTML commands:
  <http://www.stack.nl/~dimitri/doxygen/htmlcmds.html>
-   Doxygen Grouping:
  <http://www.stack.nl/~dimitri/doxygen/grouping.html>

## Recommendations


-   Use grouping aggressively.
-   Add common documentation to groups and use them as guidance pages
    through your reference manual.
-   There is no preference for any comment block style, but it should be
    consistent within a package.
-   Prefer `\section` commands over markdown. This forces you to add a
    label to a section and makes it show up on the left. Only use the
    markdown style if you don't want a heading to appear in the
    navigation tree.
-   Doxygen will try to add a link to each word you write. You want to
    prevent a word to be a link to some documentation page, add `%`
    before it. This does hold for nested types. See the [doxygen page](http://www.stack.nl/~dimitri/doxygen/manual/autolink.html) for
    more details.
-   Use macro `\cgalModifBegin` and `\cgalModifEnd` to indicate the
    modifications made in an existing package. This will create a page
    containing links through all the modified sections which will
    simplify the reviewing process.
