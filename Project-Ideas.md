# CGAL Projects Page for the Google Summer of Code 2018


The CGAL Project is applying as a mentoring organization of the [Google Summer of Code](https://summerofcode.withgoogle.com) 2018.
On this page we present some project ideas as well the [information](#information-candidates-should-supply) applicants have to provide us.
GSoC applicants are welcome to propose other ideas and check if a mentor is interested in supervising it. For new project
proposals, contact us at [gsoc-cgal@inria.fr](mailto:gsoc-cgal@inria.fr).

## Projects
### Reworking Intersection Functions
**Mentor(s)**: Sebastien Loriot

**Project description:**
The goal of this project is to rewrite all the intersection predicates (`do_intersect_2/3`) to provide more information, but at the same time staying backward compatible. For example, say you want to know whether two segments intersect but also where lies the intersection, in the interior or at an endpoint of each segment. If the student is fast enough, an extension of the project is to rewrite intersection computation functions to take into account the result of the do-intersect predicate to speed up the computation.

**Required Skills:** C++, generic programming, basic geometry

**Contact:** sebastien.loriot@cgal.org

### Enhancing the 2D Arrangement Demo (1) ### 

**Mentor(s)**: Efi Fogel (Tel Aviv University)

**Project description:**
Currently the demo supports (linear) segments, polylines, conic arcs, linear curves, circular arcs, and algebraic curves to some extent. The goal of this project is to enhance the 2D arrangement demo to support additional types of curves, namely, Bezier curves and algebraic curves.

This is a perfect project for GSoC. A developer that commits to pursue the goals of this project will learn to use the 2D arrangement package, other components of CGAL, and other libraries, such as QT. The purpose of the CGAL demos is to demonstrate the potential of the various components of CGAL using visual effects. The excitement and satisfaction, a developer of an application experience, are always enhances when visual effects are exploited. Demo programs of CGAL show the capabilities of the library and help the community evaluating it. A potential user can quickly determine whether a specific component of CGAL can be used to solve a problem she or he may have and how to go about it.

**Required Skills:** C++, generic programming, geometry

**Contact:** efifogel@gmail.com

### Enhancing the 2D Arrangement Demo (2) ###

**Mentor(s):** Efi Fogel (Tel Aviv University)

**Project description:**
Currently the demo supports only arrangements in the plane. The goal of this project is to enhance the 2D arrangement demo to support 2D arrangements not only embedded in the plane, but also embedded in certain surfaces in space, e.g., arrangements of arcs of great circles embedded in the sphere. This is an upcoming feature of the "2D Arrangements" package.

Like the project above, this is also a perfect project for GSoC. A developer that commits to pursue the goals of this project will learn to use a significant upcoming feature of the "2D Arrangements" package that supports 2D arrangements on 3D surfaces. Similar to the project above, the developer will learn to use other components of CGAL, and other libraries, such as QT. The purpose of the CGAL demos is to demonstrate the potential of the various components of CGAL using visual effects---an enjoyable side effect of developing such applications especially when 3D graphics is involved. Demo programs of CGAL show the capabilities of the library and help the community evaluating it. A potential user can quickly determine whether a specific component of CGAL can be used to solve a problem she or he may have and perhaps how to go about it.

**Required Skills:** C++, generic programming, basic geometry, basic 3D graphics

**Contact:** efifogel@gmail.com

### Develop the 2D Regularized Boolean Set Operations Demo (2) ###

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
The goal of this project is to generalize the CGAL package [Point Set Shape Detection](https://doc.cgal.org/latest/Point_set_shape_detection_3/index.html#Chapter_Point_Set_Shape_Detection). The student needs to make several changes to the package, for example, generalize 3D Ransac and Region Growing algorithms and make them work both in 2D and 3D (now they are available only in 3D); make these algorithms work with other than points input elements, e.g. region growing can grow regions using connected polygons or segments as an input; make region growing detect other than lines and planes primitive shapes; any other ideas are also welcome. As a starting point for the student, I already have the 2D working and quite well-tested versions of the region growing on points and triangles (though they must be adapted). Concerning me as a mentor, I have some experience with applying both ransac and region growing to real use cases and I implemented the 2D versions of the algorithms above. By implementing this change, the student will make a great impact onto current geometry processing pipelines and, in particular, it will be very useful in the urban reconstruction topic.

**Required Skills:** C++, generic programming, basic geometry

**Contact:** dmitry.anisimov@inria.fr

### Extending 2D Generalized Barycentric Coordinates
**Mentor(s)**: Dmitry Anisimov (Inria)

**Project description:**
The goal of this project is to extend the CGAL package [2D Generalized Barycentric Coordinates](https://doc.cgal.org/latest/Barycentric_coordinates_2/index.html#Chapter_2D_Generalized_Barycentric_Coordinates). Now the package has Discrete Harmonic (work well on convex polygons, but can be negative), Wachspress (work well and positive on strictly convex polygons), and Mean Value (work well on any simple polygon, but can be negative) coordinates. All coordinates have simple analytic formulations. I suggest adding to this package Harmonic and Maximum Entropy coordinates (both work well and positive on any simple polygon). Harmonic coordinates need discretezation of the domain and can be computed by solving a sparse linear system, while maximum entropy coordinates can be computed at any point by the Newton's method. I also have a few ideas how to improve the API of the package. As a starting point for the student, I already have well-implemented and tested versions of both harmonic and maximum entropy coordinates (though they must be adapted). Concerning me as a mentor, I am one of the authors of the original package and so I know exactly how it works. In addition, I have very good understanding of barycentric coordinates. By implementing this change, the package with 2D generalized barycentric coordinates will be able to handle the largest part of all real use cases for these coordinates, while now it is less complete.

**Required Skills:** C++, generic programming, basic calculus, linear algebra, and probability

**Contact:** dmitry.anisimov@inria.fr

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

## Previous year project ideas pages
* [2017](https://www.cgal.org/gsoc/2017.html)
* [2016](https://www.cgal.org/gsoc/2016.html) (not accepted)
* [2015](https://www.cgal.org/gsoc/2015.html) (not accepted)
* [2014](https://www.cgal.org/gsoc/2014.html)
* [2013](https://www.cgal.org/gsoc/2013.html)
* [2012](https://www.cgal.org/gsoc/2012.html)
* [2011](https://www.cgal.org/gsoc/2011.html)
* [2010](https://www.cgal.org/gsoc/2010.html)