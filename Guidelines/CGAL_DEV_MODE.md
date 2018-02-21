Since CGAL-4.12, the behavior of CGAL CMake scripts have changed.

The CMake option `CGAL_DEV_MODE` currently has two effects:

 - the CMake variable [`CMAKE_NO_SYSTEM_FROM_IMPORTED`] is set to `TRUE`, so that the CGAL include paths are setup using `-I` instead of `-isystem`, so that internal warnings of the CGAL headers are not disabled,

 - and the old behavior of our CMake sciprts is used: the `CMAKE_BUILD_TYPE` and compilation flags
   configured in `CGAL_DIR` are copied to the current CMake project.

[`CMAKE_NO_SYSTEM_FROM_IMPORTED`]: https://cmake.org/cmake/help/v3.10/variable/CMAKE_NO_SYSTEM_FROM_IMPORTED.html
