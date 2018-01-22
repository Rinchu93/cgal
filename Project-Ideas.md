<!--TOC-->

# Table of Contents
* [Projects](#projects)
  * [Approximate Convex Decomposition of Volumes Bounded by Triangle Meshes](#approximate-convex-decomposition-of-volumes-bounded-by-triangle-meshes)
  * [Enhancing the 2D Arrangement Demo (1)](#enhancing-the-2d-arrangement-demo-1)
  * [Enhancing the 2D Arrangement Demo (2)](#enhancing-the-2d-arrangement-demo-2)
  * [Develop the 2D Regularized Boolean Set Operations Demo](#develop-the-2d-regularized-boolean-set-operations-demo)
  * [Generalized Shape Detection](#generalized-shape-detection)
  * [Extending 2D Generalized Barycentric Coordinates](#extending-2d-generalized-barycentric-coordinates)
  * [Removal of Almost Degenerate Triangles in Surface Meshes](#removal-of-almost-degenerate-triangles-in-surface-meshes)
  * [Smooth Surface Reconstruction](#smooth-surface-reconstruction)
  * [Spectral Surface Reconstruction](#spectral-surface-reconstruction)
  * [Create a Bridge between the Available Github webhooks, and the Usual Git Hook Scripts](#create-a-bridge-between-the-available-github-webhooks-and-the-usual-git-hook-scripts)
  * [Combinatorial Maps Dynamic Properties](#combinatorial-maps-dynamic-properties)
  * [The Heat Method](#the-heat-method-for-distance-computation)
* [Information Candidates Should Supply](#information-candidates-should-supply)
* [Previous Year Project Ideas Pages](#previous-year-project-ideas-pages)

<!--TOC-->

The CGAL Project is applying as a mentoring organization of the [Google Summer of Code](https://summerofcode.withgoogle.com) 2018.
On this page we present some project ideas as well the [information](#information-candidates-should-supply) applicants have to provide us.
GSoC applicants are welcome to propose other ideas and check if a mentor is interested in supervising it. For new project
proposals, contact us at [gsoc-cgal@inria.fr](mailto:gsoc-cgal@inria.fr).

## Projects
### Approximate Convex Decomposition of Volumes Bounded by Triangle Meshes
**Mentor(s)**: Sebastien Loriot

**Project description:**
Convex decomposition of volumes bounded by triangles meshes is a particularly interesting tool to speed up some algorithm that are expensive or that requires to give an answer very quickly (like real time collision detection for example). Exact convex decomposition is in practice too costly and generate too many small convex pieces. Mamou and Ghorbel has proposed an algorithm to compute an approximate convex decomposition:

 - [Mamou, K., & Ghorbel, F. (2009, November). A simple and efficient approach for 3D mesh approximate convex decomposition. In Image Processing (ICIP), 2009 16th IEEE International Conference on (pp. 3501-3504). IEEE.
](https://pdfs.semanticscholar.org/6542/47f3aa425c6b455c953b45b110d009832626.pdf)

The goal of this project is to provide a CGAL implementation of this algorithm based on the generic [Boost Graph Library concepts](https://doc.cgal.org/latest/BGL/index.html) extended by CGAL.

**Required Skills:** C++, generic programming, geometry processing

**Contact:** sebastien.loriot@cgal.org

### Enhancing the 2D Arrangement Demo (1)

**Mentor(s)**: Efi Fogel (Tel Aviv University)

**Project description:**
Currently the demo supports (linear) segments, polylines, conic arcs, linear curves, circular arcs, and algebraic curves to some extent. The goal of this project is to enhance the 2D arrangement demo to support additional types of curves, namely, Bezier curves and algebraic curves.

This is a perfect project for GSoC. A developer that commits to pursue the goals of this project will learn to use the 2D arrangement package, other components of CGAL, and other libraries, such as QT. The purpose of the CGAL demos is to demonstrate the potential of the various components of CGAL using visual effects. The excitement and satisfaction, a developer of an application experience, are always enhances when visual effects are exploited. Demo programs of CGAL show the capabilities of the library and help the community evaluating it. A potential user can quickly determine whether a specific component of CGAL can be used to solve a problem she or he may have and how to go about it.

**Required Skills:** C++, generic programming, geometry

**Contact:** efifogel@gmail.com

### Enhancing the 2D Arrangement Demo (2)

**Mentor(s):** Efi Fogel (Tel Aviv University)

**Project description:**
Currently the demo supports only arrangements in the plane. The goal of this project is to enhance the 2D arrangement demo to support 2D arrangements not only embedded in the plane, but also embedded in certain surfaces in space, e.g., arrangements of arcs of great circles embedded in the sphere. This is an upcoming feature of the "2D Arrangements" package.

Like the project above, this is also a perfect project for GSoC. A developer that commits to pursue the goals of this project will learn to use a significant upcoming feature of the "2D Arrangements" package that supports 2D arrangements on 3D surfaces. Similar to the project above, the developer will learn to use other components of CGAL, and other libraries, such as QT. The purpose of the CGAL demos is to demonstrate the potential of the various components of CGAL using visual effects---an enjoyable side effect of developing such applications especially when 3D graphics is involved. Demo programs of CGAL show the capabilities of the library and help the community evaluating it. A potential user can quickly determine whether a specific component of CGAL can be used to solve a problem she or he may have and perhaps how to go about it.

**Required Skills:** C++, generic programming, basic geometry, basic 3D graphics

**Contact:** efifogel@gmail.com

### Develop the 2D Regularized Boolean Set Operations Demo

**Mentor(s):** Efi Fogel (Tel Aviv University)

**Project description:**
The 2D Regulazied Boolean Set Operations consists of the implementation of Boolean set-operations on point sets bounded by x-monotone curves in 2-dimensional Euclidean space. In particular, it contains the implementation of regularized Boolean set-operations, intersection predicates, and point containment predicates. The demo program should demonstrates all operations of this package and later on of the 2D Minkowski sum package as well.

This project is of large scale and requires complete dedication and also a bit more background and experience than other projects. It is an excellent project for a brilliant student who wishes to gain experience with developing applications from scratch using CGAL, Boost, and Qt5 (among the other).

There used to be a demo based on an old version of Qt that demonstrated a limited number of features of the 2D Regularized Boolean Operation package. However, the basic design of this old demo is outdated, and so there is hardly nothing should be carried over.

**Required Skills:** C++, generic programming, geometry

**Contact:** efifogel@gmail.com

### Generalized Shape Detection
**Mentor(s)**: Dmitry Anisimov (Inria)

**Project description:**
The goal of this project is to generalize the CGAL package [Point Set Shape Detection](https://doc.cgal.org/latest/Point_set_shape_detection_3/index.html#Chapter_Point_Set_Shape_Detection). The student needs to make several changes to the package, for example, generalize 3D Ransac and Region Growing algorithms and make them work both in 2D and 3D (now they are available only in 3D); make these algorithms work with other than points input elements, e.g. region growing can grow regions using connected polygons or segments as an input; make region growing detect other than lines and planes primitive shapes; any other ideas are also welcome.

_To begin with:_ As a starting point for the student, I already have the 2D working and quite well-tested versions of the region growing on points and triangles (though they must be adapted).

_Help:_ Concerning me as a mentor, I have some experience with applying both ransac and region growing to real use cases and I implemented the 2D versions of the algorithms above.

_Impact:_ By implementing this change, the student will make a great impact onto current geometry processing pipelines and, in particular, it will be very useful in the urban reconstruction topic.

**Required Skills:** C++, generic programming, basic geometry

**Contact:** dmitry.anisimov@inria.fr

### Extending 2D Generalized Barycentric Coordinates
**Mentor(s)**: Dmitry Anisimov (Inria)

**Project description:**
The goal of this project is to extend the CGAL package [2D Generalized Barycentric Coordinates](https://doc.cgal.org/latest/Barycentric_coordinates_2/index.html#Chapter_2D_Generalized_Barycentric_Coordinates). Now the package has Discrete Harmonic (work well on convex polygons, but can be negative), Wachspress (work well and positive on strictly convex polygons), and Mean Value (work well on any simple polygon, but can be negative) coordinates. All coordinates have simple analytic formulations. I suggest adding to this package Harmonic and Maximum Entropy coordinates (both work well and positive on any simple polygon). Harmonic coordinates need discretezation of the domain and can be computed by solving a sparse linear system, while maximum entropy coordinates can be computed at any point by the Newton's method. I also have a few ideas on how to improve the API of the package.

_To begin with:_ As a starting point for the student, I already have well-implemented and tested versions of both harmonic and maximum entropy coordinates (though they must be adapted).

_Help:_ Concerning me as a mentor, I am one of the authors of the original package and so I know exactly how it works. In addition, I have very good understanding of barycentric coordinates.

_Impact:_ By implementing this change, the package with 2D generalized barycentric coordinates will be able to handle the largest part of all real use cases for these coordinates, while now it is less complete.

**Required Skills:** C++, generic programming, basic calculus, linear algebra, and probability

**Contact:** dmitry.anisimov@inria.fr

### Removal of Almost Degenerate Triangles in Surface Meshes
**Mentor(s)**: Jane Tournois and Sebastien Loriot

**Project description:**
The goal of this project is to provide an implementation of a research article about a surface mesh cleaning algorithm. Almost degenerate triangles are present in many 3D surface models and eliminating them is a required preprocessing step of many algorithms and is also useful to improve the robustness and stability of some computation methods. The algorithm we propose the student to implement is described in this article:
* [`BOTSCH, Mario et KOBBELT, Leif. A Robust Procedure to Eliminate Degenerate Faces from Triangle Meshes. In : VMV. 2001. p. 283-290.`](http://www-i8.informatik.rwth-aachen.de/publication/134/slicing1.pdf).

The implementation must be generic and must use the generalization of the [BGL API](https://doc.cgal.org/latest/BGL/index.html#BGLExtensions) introduced by CGAL.

**Required Skills:** C++, generic programming, mesh processing

**Contact:** sebastien.loriot@cgal.org

### Smooth Surface Reconstruction

**Mentor(s)**: Pierre Alliez (Inria), Gael Guennebaud (Inria) and Simon Giraudot (GeometryFactory)

**Project description**:
CGAL offers an implicit approach for smooth surface reconstruction from unorganized point sets with oriented normals: http://doc.cgal.org/latest/Manual/packages.html#PkgPoissonSurfaceReconstructionSummary
The key idea is to solve for a scalar function, such that the gradient of the functions approaches the oriented normals at the input points. On smoothness of the output surfaces, the current issue is that the current implicit surface is piecewise linear, hence only C0-continuous, as computed on a piecewise linear 3D function. The first objective of the project is to implement a smooth C1-continuous interpolation approach that yields smooth surfaces at all scales. The second objective is to generate smooth curves as boundaries, using graph cut optimization and local mesh refinement.

**Required Skills**: C++, geometric data structures, applied maths.

**Contact**: pierre.alliez@inria.fr

### Spectral Surface Reconstruction

**Mentor(s)**: Pierre Alliez, David Cohen-Steiner (Inria) and Simon Giraudot (GeometryFactory)

**Project description**:
CGAL already offers an implicit approach for surface reconstruction from unorganized point sets with oriented normals: http://doc.cgal.org/latest/Manual/packages.html#PkgPoissonSurfaceReconstructionSummary
In several cases however the orientation of normals is not given as input, and is very difficult to estimate.
The following article introduced an approach that requires only unoriented normals, given in the form of 3x3 tensors:
ftp://ftp-sop.inria.fr/prisme/dcohen/Papers/ACTD07.pdf
The key idea is to solve for a scalar function, such that the gradient of the function best aligns to the principal component of the tensors. The objective of the project is to implement this approach using the eigen library, combined with spectra, a recent lightweight library for computing eigenvalues and eigenvectors.

**Required Skills**: C++, geometric data structures, applied maths.
**Contact**: pierre.alliez@inria.fr


### Create a Bridge between the Available Github webhooks, and the Usual Git Hook Scripts

**Mentor(s)**: Laurent Rineau

**Project description:**
The CGAL project has moved from a dedicated Git repository to the Github
service. One issue is that Github no-longer support the server Git hooks,
but only its own instance named "webhook". The student will implement a
bridge between Github webhooks and usual Git hook scripts, so that one can
for example use [`git-multimail`](https://github.com/mhagger/git-multimail)
with Github.

**Required Skills:** shell programming, web services

**Contact:** laurent.rineau@geometryfactory.com

### Combinatorial Maps Dynamic Properties

**Mentor(s)**: Guillaume Damiand (CNRS/LIRIS/Lyon university)

**Project description**:
A Combinatorial map is a combinatorial object modeling topological structures with subdivided objects. Combinatorial maps are used as efficient data structures in image representation and processing, in geometrical modeling. The current implementation of Combinatorial map ad Linear Cell Complex package supports only static properties, i.e. users must define all the attributes they want to use at compile time.

The goal of this project is to improve this mechanism by adding the possibility to o dynamically associate some information to a given Combinatorial map. We will use for that boost property_map (as in several other CGAL packages) and provide functions allowing to create/destroy a dynamic property map.

**Required Skills:** C++, generic programming

**Contact:** guillaume.damiand@liris.cnrs.fr


### The Heat Method for Distance Computation

**Mentor(s)**: Andreas Fabri (GeometryFactory) and Keenan Crane (CMU)

**Project description**:

The objective of this project is to offer the [Heat Method](http://www.cs.cmu.edu/~kmcrane/Projects/HeatMethod/index.html) as a CGAL package. The code has not to be written from scratch as it already exists, but it must be adapted to the CGAL design ideas (notion of FaceGraph, usage of property maps,..).  The goal is to have the submission of a completed package at the end of summer, that is the student has to take care of proper handling of additional dependencies (cmake), of writing a the documentation (doxygen), providing a test suite, examples, and to integrate it in the 3D demo.

This project is co-mentored by Keenan Crane who covers the algorithm as such, and by Andreas Fabri who covers the CGAL development process, and the software design. 

**Required Skills**: C++, geometry processing, first user experience with CGAL.
**Contact**: andreas.fabri@geometryfactory.com


## Information Candidates Should Supply
The application process has several steps. Before contacting anybody verify that you are eligible, that is
that you are enrolled as student, don't get a tuition fee, etc. The next step is to contact the mentor
of the project you are interested in. You have to convince him that you are the right person to get the job
done. The next step is to work out more details and to contact the mentoring organization by providing
the following information by email to [gsoc-cgal@inria.fr](mailto:gsoc-cgal@inria.fr):


* Project:
  * Select a project in the list and provide your personal and detailed description. If you wish to work on another idea of your own, we are pretty open as long as this serves the goal of consolidating CGAL as a whole.
  * Provide a proposal of a technical solution with your envisioned methodology. The more detailed the better.
  * Explain how the solution will be available to the user, in which form. Do not forget the documentation, unitary tests and cross-platform aspects.
  * Provide a realistic schedule with objectives (one every two weeks for example) and deadlines. Focus on mid-term objectives as well as on the final evaluation.
  * Provide a formal commitment that you will be involved full time on the GSoC. This is absolutely mandatory.


* Personal data:
  * First name, last name, affiliation and geographical location.
  * A brief list of the main studies and programming courses attended, with ranking.
  * List of the most important software projects contributed and success.
  * Which are your best skills in terms of programming and scientific computing?
  * In general what is your taste in terms of programming? language, methodology, team work, etc.
  * Is there anything that prevents you from working full time on the project during the program period?
  * How do you see your involvement after the program ends? Do you see yourself pushing the project further, or do you see yourself contributing to other CGAL projects?
  * Are you more interested in the theory/scientific aspect of CGAL, or do you feel more like a hacker?
  * What are your long-term wishes in terms of job?

## Previous Year Project Ideas Pages
* [2017](https://www.cgal.org/gsoc/2017.html)
* [2016](https://www.cgal.org/gsoc/2016.html) (not accepted)
* [2015](https://www.cgal.org/gsoc/2015.html) (not accepted)
* [2014](https://www.cgal.org/gsoc/2014.html)
* [2013](https://www.cgal.org/gsoc/2013.html)
* [2012](https://www.cgal.org/gsoc/2012.html)
* [2011](https://www.cgal.org/gsoc/2011.html)
* [2010](https://www.cgal.org/gsoc/2010.html)
