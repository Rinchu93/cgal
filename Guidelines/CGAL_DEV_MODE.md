Since CGAL-4.12, the behavior of CGAL CMake scripts have changed.

The CMake option `CGAL_DEV_MODE` currently has two effects:

 - the CMake variable [`CMAKE_NO_SYSTEM_FROM_IMPORTED`] is set to `TRUE`,

 - the old behavior is used: the `CMAKE_BUILD_TYPE` and compilation flags
   configured in `CGAL_DIR` are copied to the current CMake project.

[`CMAKE_NO_SYSTEM_FROM_IMPORTED`]: https://cmake.org/cmake/help/v3.10/variable/CMAKE_NO_SYSTEM_FROM_IMPORTED.html
