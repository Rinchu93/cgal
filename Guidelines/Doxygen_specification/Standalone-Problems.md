## List of possible/actual problems with the standalone version of the documentation

* Examples are titled `File <package_name>/<filename.cpp>` instead of the actual path, `<package_name>/examples/<package_name>`
* Figure names have to be corrected by hand since output is not filtered through `html_output_post_processing.py` like html is.
* A small text is prepended to the Class Index for all packages that reads "Here is the list of all concepts and classes of this package. Classes are inside the namespace CGAL. Concepts are in the global namespace". What exactly does the last sentence mean - especially since Concepts are not `C++` entities.
* The Reference Manual is included as a chapter titled "Module Documentation". This is a bit strange because the authors are re-included here while they have already been mentioned in the first chapter.
* A chapter titled "Example Documentation" is included in the end. I'm not sure this is useful, since all examples and their code have all been included in the previous chapters, so it's just repeating information.
* The underscore in `Is Model` and `Has Model` has been removed.
