## Requirements

- [GMP] [sources][GMP sources]
- [MPFR] [sources][MPFR sources]
- [Boost] headers
- [Android NDK]
- [CGAL] [sources][CGAL sources]

[GMP]: https://gmplib.org/
[MPFR]: http://www.mpfr.org/mpfr-current/#download
[Boost]: http://www.boost.org/
[Android NDK]: https://developer.android.com/ndk/downloads/index.html
[CGAL]: https://www.cgal.org/

[GMP sources]: https://gmplib.org/#DOWNLOAD
[MPFR sources]: http://www.mpfr.org/mpfr-current/#download
[CGAL sources]: https://www.cgal.org/download

## Generating a Standalone Toolchain
The first step for cross-compiling a library to Android is to create a toolchain from the NDK. 
A python script is given to do so:
``` {.bash}
> ${ANDROID-NDK}/build/tools/make_standalone_toolchain.py --arch ${ARCH} --install-dir ${TOOLCHAIN_PATH}
```
- `${ANDROID-NDK}` is the root directory of your NDK.
- `${ARCH}` is the architecture you are targeting. For example: arm, arm64, ...
- `${TOOLCHAIN_PATH}` is the directory where the toolchain will be installed.


This will generate all the files you need in `${TOOLCHAIN_PATH}`.

## Building GMP
We will now build a version of GMP for Android, using the precedently generated toolchain.
In the GMP sources dir, do the following:
``` {.bash}
> export CC=${TOOLCHAIN_PATH}/bin/clang
> export CXX=${TOOLCHAIN_PATH}/bin/clang++
> export PATH="${TOOLCHAIN_PATH}:${PATH}"
> export LIBGMP_LDFLAGS='-avoid-version'
> export LIBGMPXX_LDFLAGS='-avoid-version'
> export BASE_CFLAGS='-O2'
> export LDFLAGS=' -Wl,-z,now'
> export CFLAGS="${BASE_CFLAGS} -fstack-protector-strong"
> ./configure --prefix=${GMP_PATH} --enable-shared --disable-static --enable-cxx -- build=${LOCAL_TRIPLET} --host=${TARGET_TRIPLET} MPN_PATH="${ARCH} generic"

> make
> make check TESTS=''
> make install
```
${LOCAL_TRIPLET} represents the machine you are building on.  It is of the form cpu-company-system. For example, x86_64-pc-linux-gnu for a 64bit Desktop Linux.
${TARGET_TRIPLET} represents the machine you are targeting. For example, aarch64-linux-android for a 64bit arm Android device.

```{.bash}
> make check TESTS=''
```
allows to build GMP tests without running them. As we are cross-compiling, tests cannot be performed, but not building them will keep us from configuring MPFR.

## Building MPFR
This is very similar to building GMP. 
In the MPFR sources dir, do the following:

``` {.bash}
> export CC=${TOOLCHAIN_PATH}/bin/clang
> export CXX=${TOOLCHAIN_PATH}/bin/clang++
> export PATH="${TOOLCHAIN_PATH}:${PATH}"
> export LIBGMP_LDFLAGS='-avoid-version'
> export LIBGMPXX_LDFLAGS='-avoid-version'
> export BASE_CFLAGS='-O2'
> export LDFLAGS=' -Wl,-z,now'
> export CFLAGS="${BASE_CFLAGS} -fstack-protector-strong"
> ./configure --prefix=${MPFR_PATH} --enable-shared --disable-static  --build=${LOCAL_TRIPLET} --host=${TARGET_TRIPLET} MPN_PATH="${ARCH} generic" --with-gmp=${GMP_PATH}

> make
> make install
```

## Building CGAL
Now that we have the dependencies built, we can configure CGAL.
First we will create a toolchain.cmake file, containing the paths needed by CMake.
Create this file at ${TOOLCHAIN_FILE_PATH}:
```
#Target system
set(CMAKE_SYSTEM_NAME  Android)

set(CMAKE_SYSTEM_VERSION 1)

# Compiler to build for the target
set(CMAKE_C_COMPILER ${TOOLCHAIN_PATH}/bin/aarch64-linux-android-clang)
set(CMAKE_CXX_COMPILER${TOOLCHAIN_PATH}/bin/aarch64-linux-android-clang++)
```

Once we have this, we call cmake:
```{.bash}
cmake -DCMAKE_TOOLCHAIN_FILE=${TOOLCHAIN_FILE_PATH} -DBUILD_SHARED_LIBS=true -DWITH_CGAL_Core=false -DWITH_CGAL_Qt5=false -DCMAKE_CXX_FLAGS=-std=c++11 -DCGAL_test_cpp_version_RUN_RES=0
-DCGAL_test_cpp_version_RUN_RES__TRYRUN_OUTPUT=0 -DGMP_INCLUDE_DIR=${GMP_PATH}/include -DGMP_LIBRARIES=${GMP_PATH}/lib/libgmp.so -DMPFR_INCLUDE_DIR=${MPFR_PATH}/include -DMPFR_LIBRARIES=${MPFR_PATH}/lib/libgmp.so -DBOOST_INCLUDE_DIR=${BOOST_INCLUDE_PATH} -DCGAL_HEADER_ONLY=true
```

CGAL is now ready to be used with an Android Application. 

NB: Some packages have more dependencies. If you wish to use them, you will have to cross-compile them too.
