<!--TOC-->

# Table of Contents
* [Requirements](#requirements)
* [Generating a Standalone Toolchain](#generating-a-standalone-toolchain)
* [Building GMP](#building-gmp)
* [Building MPFR](#building-mpfr)
* [Configuring CGAL](#configuring-cgal)
* [Build an Example](#build-an-example)

<!--TOC-->

## Requirements

- [GMP] [sources][GMP sources]
- [MPFR] [sources][MPFR sources]
- [Boost] headers
- [Android NDK]
- [CGAL] [sources][CGAL sources]

[GMP]: https://gmplib.org/
[MPFR]: http://www.mpfr.org/
[Boost]: http://www.boost.org/
[Android NDK]: https://developer.android.com/ndk/downloads/index.html
[CGAL]: https://www.cgal.org/

[GMP sources]: https://gmplib.org/#DOWNLOAD
[MPFR sources]: http://www.mpfr.org/mpfr-current/#download
[CGAL sources]: https://www.cgal.org/download/last

## Generating a Standalone Toolchain
The first step for cross-compiling a library to Android is to create a toolchain from the NDK. 
A python script is given to do so:
```bash
> ${ANDROID_NDK}/build/tools/make_standalone_toolchain.py --arch ${ARCH} --install-dir ${TOOLCHAIN_PATH}
```
- `${ANDROID_NDK}` is the root directory of your NDK.
- `${ARCH}` is the architecture you are targeting. For example: arm, arm64, ...
- `${TOOLCHAIN_PATH}` is the directory where the toolchain will be installed.


This will generate all the files you need in `${TOOLCHAIN_PATH}`.

## Building GMP
We will now build a version of GMP for Android, using the precedently generated toolchain.
In the GMP sources dir, do the following:
```bash
> export CC=${TOOLCHAIN_PATH}/bin/clang
> export PATH="${TOOLCHAIN_PATH}:${PATH}"
> ./configure --prefix=${GMP_PATH} --enable-shared --host=${TARGET_TRIPLET}

> make
> make install
```
- `${TARGET_TRIPLET}` represents the machine you are targeting. It is of the form cpu-company-system. For example, `aarch64-linux-android` for a 64bit arm Android device, or `arm-linux-androideabi` for a 32bit arm Android device.

## Building MPFR
This is very similar to building GMP. 
In the MPFR sources dir, do the following:

```bash
> export CC=${TOOLCHAIN_PATH}/bin/clang
> export PATH="${TOOLCHAIN_PATH}:${PATH}"
> ./configure --prefix=${MPFR_PATH} --enable-shared--host=${TARGET_TRIPLET} --with-gmp=${GMP_PATH}

> make
> make install
```

## Configuring CGAL
Now that we have the dependencies built, we can configure CGAL.
First we will create a `toolchain.cmake` file, containing the paths needed by CMake.
Create this file at ${TOOLCHAIN_FILE_PATH}:
```cmake
#Target system
set(CMAKE_SYSTEM_NAME  Android)

set(CMAKE_SYSTEM_VERSION 21)

# Compiler to build for the target
set(CMAKE_C_COMPILER ${TOOLCHAIN_PATH}/bin/aarch64-linux-android-clang)
set(CMAKE_CXX_COMPILER${TOOLCHAIN_PATH}/bin/aarch64-linux-android-clang++)
```

Once we have this, we call cmake:
```bash
> cmake -DCMAKE_TOOLCHAIN_FILE=${TOOLCHAIN_FILE_PATH} -DWITH_CGAL_Core=FALSE  -DCMAKE_CXX_FLAGS=-std=c++11 -DCGAL_test_cpp_version_RUN_RES=0 -DGMP_INCLUDE_DIR=${GMP_PATH}/include -DGMP_LIBRARIES=${GMP_PATH}/lib/libgmp.so -DMPFR_INCLUDE_DIR=${MPFR_PATH}/include -DMPFR_LIBRARIES=${MPFR_PATH}/lib/libgmp.so -DBOOST_INCLUDE_DIR=${BOOST_INCLUDE_PATH} -DCGAL_HEADER_ONLY=TRUE
```
CGAL is now ready to be used with an Android Application. 

Notes:
- The options `-DCMAKE_CXX_FLAGS=-std=c++11 -DCGAL_test_cpp_version_RUN_RES=0` allow to use the C++11 standard. When CGAL is compiled with a C++11 compiler, the library Boost.Thread is no longer required, and Boost libraries can be used header-only. There is one exception: `CGAL_Core` still requires Boost.Thread. That is why we use the cmake option `-DWITH_CGAL_Core=FALSE`, to disable `CGAL_Core`. If you need `CGAL_Core`, then you will have to cross-compile Boost, and then set the appropriate CMake variables. Some packages have more dependencies. If you wish to use them, you will have to cross-compile them too.
- With the option `-DCGAL_HEADER_ONLY=TRUE`, CGAL is used header-only.

## Build an Example

Since Android 5.0 (API level 21), an executable needs to be PIE (Position independant executable) to work. To make your executable PIE, you need to add `-fPIE` to the CXX flags, and `-fPIE -pie` to the linker flags

```bash
> cmake -DCMAKE_CXX_FLAGS=-std=c++11 -fPIE -DCMAKE_EXE_LINKER_FLAGS="-fPIE -pie" .
```