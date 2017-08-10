<!--TOC-->

# Table of Contents
* [` cgal_create_cmake_script` (same as `cgal_create_CMakeLists`)](#cgal_create_cmake_script-same-as-cgal_create_cmakelists)
* [`cgal_create_package_dir.py`](#cgal_create_package_dirpy)
* [`create_assertions.sh`](#create_assertionssh)
* [`check_licenses`](#check_licenses)
* [`create_new_release`](#create_new_release)
* [`create_cgal_test_with_cmake`](#create_cgal_test_with_cmake)
* [`autotest_cgal_with_cmake`](#autotest_cgal_with_cmake)
* [`autotest_cgal`](#autotest_cgal)
  * [How to set up your local testing](#how-to-set-up-your-local-testing)
  * [Command line options for the initial set up](#command-line-options-for-the-initial-set-up)
  * [Sending results](#sending-results)
  * [List of miscellaneous configurable variables](#list-of-miscellaneous-configurable-variables)
  * [Testing with Visual Studio on cygwin](#testing-with-visual-studio-on-cygwin)
* [`add_toc_to_github_wiki_page.py`](#add_toc_to_github_wiki_pagepy)

<!--TOC-->

There are a number of tools that automate some of the tedious and
error-prone tasks that are unavoidable when developing for CGAL. We
distinguish

-   scripts for users located in `Package/scripts`, most of them in
    `Scripts/scripts` and
-   scripts for developers located in `Package/developer_scripts`,
    typically `Scripts/developer_scripts`.

They are available in any branch of CGAL, thus the developer Jenny adds
the directories she needs the most (typically the two from the `Scripts`
package to her `PATH` variable.

```
PATH=$HOME/CGAL/workingcopy/Scripts/scripts:$HOME/CGAL/workingcopy/Scripts/developer_scripts:$PATH
```

## ` cgal_create_cmake_script` (same as `cgal_create_CMakeLists`)


The shell script `cgal_create_cmake_script`
creates a CGAL cmake script, that is, a ` CMakeLists.txt` file, in the
current working directory for configuring a particular CGAL application
(such as an example, demo or test).

The cmake script will have commands to make an executable from every
`.C` and `.cpp` file which contains the word `main` in the directory.
The following options are available to indicate the kind of cmake script
to create:

The `CMakeLists.txt` created by this script has the following
properties:

If the directory where the cmake script is created contains a directory
named `include`, that directory will be on the header search path before
any other files. This provides a means to override header files.

The CGAL library is required (must have been already configured) and
cmake must be called with `-DCGAL_DIR=''<path>''`, where *<path>*
corresponds to the binary directory of the CGAL installation (where the
file `CGALConfig.cmake` is).

While cmake itself takes care automatically of most compiler and linker
flags based on the target compiler and debug/release configuration,
additional flags might have been specified when building CGAL and will
be used here (as defined in `CGALConfig.cmake`). However, if you need
*additional* compiler or linker flags specifically here, you can define
any of the following cmake variables at the command line when running
cmake (via the -D option):

- ` CGAL_CXX_FLAGS`
- ` CGAL_CXX_FLAGS_RELEASE`
- ` CGAL_CXX_FLAGS_DEBUG`
- ` CGAL_EXE_LINKER_FLAGS`
- ` CGAL_EXE_LINKER_FLAGS_RELEASE`
- ` CGAL_EXE_LINKER_FLAGS_DEBUG`

The `_RELEASE` and `_DEBUG` variables are *appended* to the general
variable, so for each build type, two sets of flags are used. For
example: `CGAL_CXX_FLAGS` *then* `CGAL_CXX_FLAGS_DEBUG`

## `cgal_create_package_dir.py`

This python script creates the directory structure for a new package,
according to the [package directory structure guidelines](Directory-Structure-for-Packages).
This script is provided so that the writer of a new package does not
have to build the structure from scratch. The command

```
cgal_create_package_dir.py Minkowski_sum_2
```

creates the structure of the new package in the current directory, in a
single directory named `Minkowski_sum_2`. The command

```
cgal_create_package_dir.py Minkowski_sum_2 creation_path
```

creates the single directory with the structure under the given
`creation_path`.

The structure and the files created are just a template and inspection
by the package writer is required. The package writer might decide to
even delete some files or directories created by the script. See the
[package directory structure guidelines](Directory-Structure-for-Packages) for
more details.

Package-specific assertions are not created. If you want to create
package-specific assertions, use the `create_assertions.sh` script.

## `create_assertions.sh`

This shell script generates assertion header files, to be used for
[invariant/pre-/post-condition checks on package level](http://doc.cgal.org/latest/Manual/devman_checks.html).
The command

```
create_assertions.sh Minkowski_sum_2
```

creates a file `Minkowski_sum_2_assertions.h`

This file is usually placed in directory `include/CGAL` and included in
files that implement the `Minkowsk_sum_2` package. The file contains
several macros for adding checks (preconditions, postconditions, etc.)
that can be switched on or off with compile flags, and can be used in
the following fashion:

```
#include <CGAL/optimisation_assertions.h>

void optimisation_foo( int i)
{
  CGAL_Minkowski_sum_2_precondition_msg( i == 42, "Only 42 allowed!");
  // ...
}
```

The documentation of your new package has to name the term chosen to be
part of the package-specific macros in order to enable the user to
selectively turn off and on the checks of your package. For example, in
the documentation of the package you should find a sentence similar to
the following:

```
    The code of this package uses the term `MINKOWSKI_SUM_2` for the
    checks; e.g., setting the compile time flag
    `CGAL_MINKOWSKI_SUM_2_NO_PRECONDITIONS` switches off
    precondition checking in the Minkowski sum code.
```

## `check_licenses`

This script can be used to check all files in an internal or external
release for proper license notices. It reports all files without a
proper notice. Observe that the check is fairly simple, we just grep
through the files looking for a fixed string. Additionally, there might
be provisions in the top-level LICENSE file that are not taken into
account by this script.

Note that there might be license errors that are not detected by this
script. For example, we do not check that files under LGPL and GPL are
not mixed in one library.

That script does not work on SCM branches. It needs a CGAL release.

## `create_new_release`

The script `create_new_release`
builds a tarball of CGAL from a branch of a Git non-bare repository The
command ` create_new_release ` creates a directory ` tmp ` which
contains the tarball, as well as its public and SRPM versions.

This script takes as argument the root of a Git repository (checked out
to a certain branch TODO @LR: Or is there a way to specify the branch?)
and it default to `master`. It also supports the following options:

-   `--rpm` create SRPM files as well
-   `--public` create a public version as well
-   `--do-it` (reserved to the release manager) moves it on the web
    server, tags...
-   `--do-not-tag` option that allows to create a new release with
    `--do-it` without creating the tag.

## `create_cgal_test_with_cmake`

In every subdirectory of ` test` there must be a shell script called
` cgal_test_with_cmake`. This script is called from the
` run_testsuite_with_cmake` script when the test suite is run. The
script ` create_cgal_test_with_cmake`
will create the shell script ` cgal_test_with_cmake` in the current
directory.

This file will be generated
automatically for each subdirectory of ` test` if it is not supplied by
the package. This is also the preferred way, so you should use
` create_cgal_test_with_cmake` to test your package before submitting
but generally should avoid submitting the file ` cgal_test_with_cmake`.
The generated file contains a header that lists the requirements a
` cgal_test_with_cmake` program should fulfill.

Files which are considered by ` create_cgal_test_with_cmake` are those
with ` .c` or ` .cpp` extension, and which content match the word
` main`. This way, no attempt is made at linking programs which are made
of several object files.

The generated script calls ` cmake` with the following definitions

` -DCGAL_DIR=${CGAL_DIR}`\
` -DCGAL_CXX_FLAGS=${TESTSUITE_CXXFLAGS} `\
` -DCGAL_EXE_LINKER_FLAGS=${TESTSUITE_LDFLAGS}`\
` -DCMAKE_BUILD_TYPE=NOTFOUND`

The environment variable ` CGAL_DIR` is *required* and must contain the
path to the binary directory of the CGAL installation to use (the
testsuite defines this automatically), while the environment variables
` TESTSUITE_CXXFLAGS` and `TESTSUITE_LDFLAGS` are optional and are used
to *add* flags which have not been used when building the library. These
TESTSUITE variables are kept for backward compatibility with the
non-cmake testsuite (the preferred way is to define all the flags when
building CGAL rather than using these).

` CMAKE_BUILD_TYPE` is set to ` NOTFOUND` in order to use the same build
type specified when building CGAL (as defined in ` CGALConfig.cmake`).

## `autotest_cgal_with_cmake`

That script has been renamed `autotest_cgal` and a wrapper script is
kept for compatibility with existing setups.

## `autotest_cgal`

The shell script ` autotest_cgal` is used to run automated test suites
for the internal releases of the library. The script need no
command-line arguments, and it is configurable using a ` .autocgalrc`
file stored in ` $HOME`. It is meant to be run regularly by a cron job
run by the testers.

This script allows you to run the testsuite on multiple hosts (remotely
or locally) and multiple compilers/platforms. It is also possible to
specify how many processors the host has and the script will use this
information to run the testsuite process on several processors at the
same time. By default, the number of processors assigned to every host
is one.

In a couple of words, the script first downloads a file named `LATEST`
that contains the current release file name. It compares it with the
file `RELEASE_NR` in the ` $CGAL_ROOT` directory and if it is different it
starts the testsuite process : it downloads the internal release,
unzips, untars, copies the configuration files, and starts the
testsuite. When everything is done, it puts the results on some ftp
server.

### How to set up your local testing

The first step is to create a directory where you will store the
releases. This directory is known in the script as ` $CGAL_ROOT`. By
default ` $CGAL_ROOT` is the directory where you put the script `autotest_cgal`,
but you may change it in ` .autocgalrc`.

The next step is to download an internal release of your choice, say
`CGAL-4.2-Ic-xx`, and unzip then untar it under ` $CGAL_ROOT`. This
release will serve as a *fixed reference release* for configuring and
building each current release to test from now on.

This internal release is used as a reference which defines the
configurations to test (it is not itself tested), thus is better to
rename it accordingly, say as ` CGAL-I-REF`.

The next step is to create a new directory for each wanted configuration
(architecture+platform+compiler) to test under the directory
` $CGAL_ROOT/CGAL-I-REF/cmake/platforms`. Each directory in there,
called *configuration folder*, will contain the testsuite for that
configuration and its name will identify the configuration in the test
results.

Now cd into each such configuration folder and configure CGAL using
cmake (either the command line or the GUI). With the command-line cmake,
use the -D option to define cmake variables and -G to define a generator
if the default is not right for you.

The cmake variables
` WITH_CGAL_Core, WITH_CGAL_ImageIO and WITHD_CGAL_Qt5
are used to enable or disable those CGAL components. These are all ON by
default. You should you define them with OFF if any of those components
do not work in your platform. For *these and only these* cmake variables
you can define them as environment variables rather than cmake
variables.

The cmake variable ` CMAKE_BUILD_TYPE` is used to specify a ` Debug` or
`Release` configuration. For the testsuite, you should always define
this variable since the default varies on different generators (and use
only Debug or Release since other values are not currently supported)

CMake itself takes care automatically of most compiler and linker flags
via the following cmake variables:

- `CMAKE_CXX_FLAGS`
- `CMAKE_CXX_FLAGS_RELEASE`
- `CMAKE_CXX_FLAGS_DEBUG`
- `CMAKE_EXE_LINKER_FLAGS`
- `CMAKE_EXE_LINKER_FLAGS_RELEASE`
- `CMAKE_EXE_LINKER_FLAGS_DEBUG`

The `_RELEASE` and `_DEBUG` variables are *appended* to the general
variable, so for each build type, two sets of flags are used. For
example: `CMAKE_CXX_FLAGS` *then* `CMAKE_CXX_FLAGS_DEBUG`.

You can define any of these variables on the command-line (with the -D
switch) or the GUI (CMakeSetup|ccmake), but doing so *overrides* the
values that cmake would automatically compute.

If you just want to *add* flags, you can define any of the following
variables instead:

- `CMAKE_CXX_FLAGS`
- `CMAKE_CXX_FLAGS_RELEASE`
- `CMAKE_CXX_FLAGS_DEBUG`
- `CMAKE_EXE_LINKER_FLAGS`
- `CMAKE_EXE_LINKER_FLAGS_RELEASE`
- `CMAKE_EXE_LINKER_FLAGS_DEBUG`

which are named with `CGAL` rather than `CMAKE`.

CMake has trouble parsing in the command line variable definitions that
contain the '=' sign in the value, thus, you cannot add a flag like:
`-DCGAL_CXX_FLAGS_RELEASE=' -std=c++11 '` . On the other hand, cmake
uses the *environment* variable `CXXFLAGS` to initialize
`CMAKE_CXX_FLAGS`, so you can use that in this case.

The cmake variable `CMAKE_CXX_COMPILER` can be used to specify the full
path of the C++ compiler to use. Unlike most cmake variables, this one
is handled specially by cmake, thus, while manually defining it works
fine when building the reference release for the testsuite, its value is
not propagated to the releases being tested (recall that at this point
we are manually building the reference release which the testsuite uses
to automatically build and test the others). Therefore, if you need to
specify the compiler (because cmake autodetects the wrong one), you need
to define the *environment* variable `CXX`.

The following example illustrates how to configure and build the
reference release. Notice the ../../.. path to the CGAL sources from the
platform directory (where cmake is invoked).

```
export CXX=`<some compiler not detected by cmake>
export CXXFLAGS='-std=c++11'`
export WITH_CGAL_ImageIO=OFF `
cmake -DWITH_CGAL_Core=OFF -DCMAKE_BUILD_TYPE=Release ../../..
make`
```

You can create here a shell script *specifically* named `setup` in
case you need the test script to automatically do anything for this
particular configuration, such as setting up environment variables as in
the example above. The contents of such a file, if it exists, are copied
verbatim into the start of the temporary build and test scripts created
on each configuration folder. This allows you to bootstrap the process
as needed.

Make sure to fix any problems configuring or building this reference
release before starting the automated testsuite.

Once the reference release is ready, define a variable named
`REFERENCE_PLATFORMS_DIR` with the full path to the `/cmake/platforms/`
directory of the reference release, for example:
`$CGAL_ROOT/CGAL-I-REF/cmake/platforms` in the file `.autocgalrc`.

The next step is to specify the name of the hosts. To do that, you must
define the variable `BUILD_HOSTS` in `.autocgalrc`.

For every host *h* listed in `BUILD_HOSTS` you must also specify the
variable `BUILD_ON_`*h* as a list of configuration names for which the
CGAL libraries are to be built on *h*. For each name listed in here, the
testsuite script will create a directory under the current platforms
directory (i.e. ` $CGAL_ROOT/CGAL-4.2-I-abc/cmake/platforms`), unless it
already exists.

If any of the variables `BUILD_ON_`*h* has the value 'all' then the
platforms directory structure of the reference release
(`$REFERENCE_PLATFORMS_DIR`) is replicated under the current platforms
directory. <small>This 'all' value should only be used when the
testsuite is run on a single localhost, otherwise, the testsuite script
won't be able to properly distribute each platform folder to its
corresponding host.</small>

Similarly, for every host *h* listed in `BUILD_HOSTS` you must also
specify the variable `COMPILERS_`*h* as a list of configuration names
for which the testsuite is to be run on \$h\$ (this must be a subset of
the names specified in `BUILD_ON_`*h*). You can specify only
`COMPILERS_`*h* instead of both `BUILD_ON_`*h* and `COMPILERS_`*h* if
they share the same value.

Here is an example setting in `.autocgalrc`:

```
REFERENCE_PLATFORMS_DIR="~/CGAL_autotest/CGAL-I-REF/cmake/platforms"
BUILD_HOSTS="papillon electra winmachine localhost"
COMPILERS_papillon="mips_IRIX64-6.5_CC-n32-7.30 mips_IRIX-6.5_g++-fsquangle-2.95.2"
COMPILERS_electra="sparc_SunOS-5.6_g++-2.95.2"
BUILD_ON_electra="sparc_SunOS-5.6_g++-2.95.2 sparc_SunOS-5.6_CC-5.80"
COMPILERS_winmachine="i686_CYGWINNT-5.0-1.3.22_CL.EXE-1300"
COMPILERS_localhost="i686_Linux-2.4.17_g++-3.4.0"
PROCESSORS_papillon="3"
```
The `PROCESSORS_hostname` must be set if you have more than one
processor on that host and of course if you want to give all of them to
the testsuite. If you want to run the testsuite locally and not
remotely, name the host as `localhost` and the script will run the
testsuite locally for that host.

The script copies the entire `/test` subdirectory of the release being
tested into each configuration folder to test at, thus, all executables,
temporaries and results are local to that configuration. When the
scripts finishes testing a configuration, the local `/test` subdirectory
is deleted.

The script uses `wget` to download the release to test. The different
options you may pass to `wget` you should put in the `WGET` variable in
`.autocgalrc`. By default, ` WGET=''wget''`. We noticed that you must
use a recent version of `wget` (1.9.1 is fine, but 1.8.2 is not). You
must also create a file in your `HOME` called `.wgetrc` (alternatively,
it is also possible to use another file by setting the `WGETRC`
variable), that contains the following lines:

```
 --http-user=username
 --http-passwd=password
```

Alternatively, you can use `curl` instead of `wget`. In order to do so,
you need to add `USE_CURL=' 'y' '` to your `.autocgalrc` file. `curl` is
then also used as a replacement for `ftp` for uploading the test
results. You also have to add the following line in a file called {\\tt
HOME/.curlrc}:

```
 --user username:password
```

Once the latest release has been downloaded, unzipped and untared, say
under `$CGAL_ROOT/CGAL-4.9-I-abc`, the testsuites creates a symbolic
link to it named `$CGAL_ROOT/CGAL-I`. From now on, the release being
tested is located under `CGAL-I`.

For each directory under `$CGAL_ROOT/CGAL-I/cmake/platforms`, if a
directory of the name name exist in the reference release (i.e. under
`$CGAL_ROOT/CGAL-I-REF/cmake/platforms`), the files `CMakeCache.txt` and
`setup` are copied from the reference release (if they exist).

### Command line options for the initial set up

` autotest_cgal` is meant to be run unattended by a cron job. However,
in order to simplify the initial set up some command line options are
available:

-   ` -c` Uses an already untared internal release under the symbolic
    link ` CGAL-I` instead of downloading and untaring a new one. You
    can use this option to *resume* immediately the test suite process
    in case it failed before.
-   ` -n` Skips the testing phase, finishing after the libraries has
    been built. You can use this option to verify that the mechanism
    that uses the reference release to build the current release with
    the exact same settings works fine.
-   ` -l` Do not upload the results to the server. You can use this
    option to avoid uploading incomplete results until the testsuite
    process works correctly in your system.

### Sending results

Unless you use the ` -l` option, the script will attempt to send result
using ` scp` (secured-copy, a tool from the SSH suite) to the
GeometryFactory server ` cgal.geometryfactory.com}` You must declare
yourself as a tester to Laurent Rineau, and send him the public part of
your ssh key. Usually, that is the file ` $HOME.ssh/id_rsa.pub`, or
`$HOME/.ssh/id_dsa.pub`.

After that, you can test that the sending works, by typing:

```
ssh cgaltest@cgal.geometryfactory.com
```

If the result is something like:

```
Last login: Thu July 14 12:03:18 1789 from 1.1.1.1
CentOS release 5.3 (Final)
Linux cgal.geometryfactory.com 2.6.28.4-xxxx-std-ipv6-32 #4 SMP Wed Sep 9 22:08:40 UTC 2009 i686 i686 i386 GNU/Linux
server    : 39891
ip        : 91.121.110.28
ip        : 91.121.110.28
hostname  : cgal.geometryfactory.com
```

then the test is successful (you will need to interrupt SSH with
Ctrl+C). If instead you get something like:

` Permission denied (publickey,gssapi-with-mic).`

then contact [@lrineau](https://github.com/lrineau).

### List of miscellaneous configurable variables

- `MYSHELL` is a variable that may be defined in `.autocgalrc` to pass
environment variables along to remote hosts. Here is an example of
`MYSHELL` variable :

- `MYSHELL="TERM=vt100 PATH=$PATH:/bin:/usr/local/bin:/usr/ccs/bin:/opt/SUNWspro/bin \`\
- `QTDIR=/usr/local/qt2 PARASOFT=/usr/local/parasoft /usr/local/bin/zsh -c\"`

- `CGAL_URL` is the URL where internal releases are available. The
- `LATEST` file is coming from the same location. If this will change, you
may change either `CGAL_URL`, or `LATEST_LOCATION` in `.autocgalrc`.

- `TAR, GZIP, GUNZIP` are the variables for compression and decompression
tools. They are defaulted to `tar, gzip,
gunzip` found in `$PATH`.

- `SENDMAIL` has the default value `' 'mail' '`.

- `CGAL_TESTER` has the default value `` `whoami` ``. It is used to
identify the tester. You should specify a kind of login name for this
variable.

- `CGAL_TESTER_NAME` has the default value `` `whoami` ``. It is used to
identify the tester. You should specify your full name for this
variable.

- `CGAL_TESTER_ADDRESS` has the default value `` `whoami` ``. It is used
to identify the tester. You should specify your email address for this
variable.

- `MAIL_ADDRESS` has no value by default. In case you want to set it in
`.autocgalrc`, the script will send an additional email to everyone
mentioned in this variable.

- `LOGS_DIR` has the default value `$CGAL_ROOT/AUTOTEST_LOGS`. This is
used to keep local log files.

- `CONSOLE_OUTPUT` has the default value `y`. If you want that the
script also shows messages to the console, the value is `y`
otherwise the value should be `' '`.

`USE_CURL` specifies if `curl` should be used as a replacement for
`wget` and `ftp`. Set it to `y` to use `curl`.

`FTP_OPTS` specifies the options for `ftp`. On Linux systems it should
be `-p -v -n` (the default), on cygwin it should be `-v -n`.

`NICE_OPTIONS` specifies the command line options to pass to the `nice`
command, which is used to prevent the test-suite from keeping all CPU
time for itself. The default value is `' '-19' '`.

`ULIMIT_OPTIONS` specifies the command line options to pass to the
`ulimit` command, which is used to prevent the test-suite from using
various system ressources. It can be used to help killing looping
programs. For example, specifying `-c 0 -t 10800 -v 2000000` will
prevent the creation of dumped core files, will stop processes after 3
hours, and will kill them if they reach 2GB of memory. The default value
is empty (no limit).

### Testing with Visual Studio on cygwin

In order to test with Visual Studio you need to use Cygwin and set up
the environment for the command line compiler.

In Cygwin, install at least the following packages:

-   procmail (for the lockfile utility),
-   wget,
-   ca-certificates,
-   make (called only once at the start of the test suite),
-   diffutils (for the cmp utility),
-   openssh (for scp).

Content of the \~/.wgetrc file:
```
--http-user=username
--http-passwd=password
ca_directory = /usr/ssl/certs
```
The setup of the environment can be done by setting up the VC command
line tools in the `setup` script that is used to bootstrap the build
and test process, as explained before. Merely executing the
`vcvars32.bat` that comes with VC does not work since the variables in
there will be defined only in the environment for the batch file and not
in the environment of the building/testing script (this is why the
contents of the 'setup' script are copied into the build/test script
instead of `setup` being just executed from there)

Furthermore, within the `setup` script, the following must be
considered:

The variables `INCLUDE, LIB` and `LIBPATH` will be used by the Visual
C++ compiler *directly*, that is, without the path translation that
cygwin usually performs. Thus, the values for these variables must be a
verbatim copy of the values defined in the `vcvars32.bat` of your
Visual Studio installation.

On the other hand, the `PATH` variable will be used by cygwin itself to
locate the components of the command line tools (cl.exe, link.exe,
etc.), thus, each Windows path specified in the PATH definition on
`vcvars32.bat` must be converted to a POSIX path and properly appended
to the cygwin PATH variable.

Below is a sample 'setup' script for Visual C++ 9.0:
```
export INCLUDE='C:\Program Files\Microsoft Visual Studio 9.0\VC\ATLMFC\INCLUDE;
C:\Program Files\Microsoft Visual Studio 9.0\VC\INCLUDE;C:\Program Files\Microsoft Visual Studio 8\VC\PlatformSDK\include;
C:\Program Files\Microsoft Visual Studio 8\SDK\v2.0\include;c:\Program Files\Microsoft Visual Studio .NET 2003\SDK\v1.1\include\'

export LIB='C:\Program Files\Microsoft Visual Studio 9.0\VC\ATLMFC\LIB;C:\Program Files\Microsoft Visual Studio 9.0\VC\LIB;
C:\Program Files\Microsoft Visual Studio 8\VC\PlatformSDK\lib;C:\Program Files\Microsoft Visual Studio 8\SDK\v2.0\lib;
c:\Program Files\Microsoft Visual Studio .NET 2003\SDK\v1.1\Lib\'

export LIBPATH='C:\Program Files\Microsoft Visual Studio 9.0\VC\ATLMFC\LIB;C:\Program Files\Microsoft Visual Studio 9.0\VC\LIB'

export PATH='/cygdrive/c/Program Files/Microsoft Visual Studio 9.0/Common7/IDE':
'/cygdrive/c/Program Files/Microsoft Visual Studio 9.0/VC/BIN':
'/cygdrive/c/Program Files/Microsoft Visual Studio 9.0/Common7/Tools':
'/cygdrive/c/Program Files/Microsoft Visual Studio 9.0/Common7/Tools/bin':
'/cygdrive/c/Program Files/Microsoft Visual Studio 9.0/VC/VCPackages':
'/cygdrive/c/Program Files/Microsoft Visual Studio 8/VC/PlatformSDK/bin':
'/cygdrive/c/Program Files/Microsoft Visual Studio 8/SDK/v2.0/bin':$PATH
```

Inside the directory `REFERENCE_PLATFORMS_DIR`, there should be
* a file `setup_common`, that contains the following variables
```
source "setup_VC${VC_VERSION}-${ARCH}"
export CMAKE_GENERATOR='-GNMake Makefiles'
export MAKE_CMD='nmake'
#export all the variables you need to configure CGAL and run what you need
```
* one file for each compiler (e.g. called `setup_VC9-64` in the above example). This file can be generated from the "Visual Studio Command Prompt with Native Tools (64 bits)", using the following commands
```
> bash
> export > $REFERENCE_PLATFORMS_DIR/setup-VC9-64
```
* one directory for each compiler, with the same name as the ones given in the `COMPILERS_xyz` variable, containing
** a file `CMakeCache.txt` corresponding to the desired CMake setup for building CGAL
** a file `setup` that contains the following
```
export VC_VERSION="9"
export VC_VERSION_YEAR="2008"
export ARCH="64"
export PLATFORM_REFERENCE="/cygdrive/e/CGAL/reference_platforms"
#export dependencies environment variables that are specific to this platform
source "${PLATFORM_REFERENCE}/setup_common"
```

## `add_toc_to_github_wiki_page.py`
This script can be used to generate or update the table of contents (TOC)
of a wiki page in github. The script is compatible with `python` 2 and 3.
The script expects as input the name of the file to process.
In order for the script to generate a TOC, the file first line must be
`<!--TOC-->`. Then another line must also be `<!--TOC-->`. The generated
TOC will be put between these lines (thus allowing to update an already
existing TOC).
The only sections processed are those created with the `#` keyword.
`h1` sections are not allowed, that is the first section level must be `##`.


Here is an example of a wiki page for which a TOC will be generated
```
<!--TOC-->
<!--TOC-->

## Section 1
### Subsection 1
blabla
## Section 2
blabla
```

To apply the script to all the files you can use for example the following
command:
```
find . -name '*.md' -exec python add_toc_to_github_wiki_page.py {} \;
```
