<!--TOC-->

# Table of Contents
* [Which library?](#which-library)
* [License](#license)
* [Guidelines for developers](#guidelines-for-developers)
* [Changes in the CMake process](#changes-in-the-cmake-process)
* [Which mechanism to enable or disable parallelism](#which-mechanism-to-enable-or-disable-parallelism)
* [Advanced TBB configuration](#advanced-tbb-configuration)

<!--TOC-->

## Which library?

[Intel TBB](https://www.threadingbuildingblocks.org/) is the library
which is used for parallel programming in CGAL. It provides thread
support, atomic operations, task-based parallelism support, concurrent
containers and high-level tools (scalable memory allocators, parallel
sorts, etc.)

Intel TBB is designed and optimized for task-based programming. Several
layers are available.

-   For simple cases, it provides a high-level API (`parallel_for`,
    `parallel_do`, etc.).
-   For more complex cases, the developer has access to tasks and can
    control the task-scheduler behavior.
-   And if the user wants even more control, threads can be created and
    managed "by hand".

If one combines several algorithms/packages which use Intel TBB for
task-based programming, the number of threads used by each component is
automatically adjusted so that the total number of threads matches the
number of available hardware threads.

As a result, we **strongly suggest** that developers use **Intel TBB**
instead of any other library, at least for thread-related features. For
other features such as atomics, thread-local storage or locks, C++11
could be used as an alternative in the future when CGAL officially
supports C++11, since it can be safely mixed with Intel TBB. Note that
Intel TBB's API is very close to C++11 for those features.

Nevertheless, if one needs another library for a good reason, we don't
strictly forbid it as long as it is thoroughly documented and as long as
it provides control on the number of threads used. But the developer
must know that, doing so, it prevents its algorithm to be easily
nested/mixed with other parallel algorithms. For this reason, this
choice will have to appear and to be justified in the feature
submission, and the reviewers are allowed to challenge it.

## License

TBB is dual-licensed: GPLv2 with the libstdC++ Runtime Exception or
commercial COM license ([see here](https://www.threadingbuildingblocks.org/Licensing) and
[here](http://www.threadingbuildingblocks.org/faq/10)).

## Guidelines for developers

For developers who are new to TBB, we recommend to visit the
[TBB documentation page](https://www.threadingbuildingblocks.org/documentation) and read:

-   *User guide*: learn how to use Intel TBB, with examples and code
    samples. It goes through the main features of TBB. A must-read.
-   *Design Patterns*: a cookbook of common parallel design patterns and
    how to implement them in TBB. You will probably find here a useful
    recipe to get inspiration.
-   *Reference Manual*.
-   And don’t forget the codes samples which come with TBB.

Here is a quick checklist of TBB key features that a developer should
know:

-   High-level TBB mechanisms (parallel\_for, parallel\_do)
-   Parallel containers (concurrent\_vector, concurrent\_queue...), with
    a special attention to operations which are concurrently callable
    (e.g. concurrent\_vector::push\_back) and operations which are not
    (e.g. concurrent\_vector::clear)
-   Mutual exclusion (different kind of mutexes) and its pathologies
    (e.g. deadlocks).
-   [More advanced] Atomics, TBB task-scheduler

Note that TBB provides concurrency-optimized allocators. We advice the
developers to [try them](https://software.intel.com/en-us/node/506094).

## Changes in the CMake process

In order to easily link with TBB, we provide a FindTBB CMake module.

Thus, the developer of a demo/example/test using parallel code needs to
add the following lines *after* `include(${CGAL_USE_FILE})` in the
`CMakeLists.txt` file:

``` {.bash}

# Activate concurrency?
option(ACTIVATE_CONCURRENCY
       "Enable concurrency"
       ON)

if( ACTIVATE_CONCURRENCY )
  find_package( TBB REQUIRED )
  if( TBB_FOUND )
    include(${TBB_USE_FILE})
    target_link_libraries(the_target ${TBB_LIBRARIES})
  endif()
endif()
```

Note: If you are using `create_single_source_cgal_program`, replace
the `target_link_libraries` call by:

``` {.bash}
  list(APPEND CGAL_3RD_PARTY_LIBRARIES ${TBB_LIBRARIES})
```

Remarks:

-   External users using headers with concurrency-enabled code also have
    to ensure to give the right parameters to the compiler.
-   In case we go for CTest, tests there can have properties: PROCESSORS
    and RUN\_SERIAL, which should be set when testing parallized code.

## Which mechanism to enable or disable parallelism

When CMake finds TBB and links it to the program, the
`CGAL_LINKED_WITH_TBB` macro is defined. To use the parallel version of
an algorithm, two conditions must be fulfilled:

-   `CGAL_LINKED_WITH_TBB` is defined
-   The user requests the parallel version (see below to know how)

If one of these conditions is not true, the package must automatically
fall back on the sequential version.

Note that we need to keep a sequential version (i.e. we don't want to
rely on TBB to automatically fall back on a sequential version) for
several reasons:

-   One should be able to build CGAL without TBB installed
-   We don't want a hard dependency to TBB
-   The automatic TBB fallback won't probably be as fast as a true
    sequential code (but you may not notice the difference)

**Tag dispatching** must be used to allow the user to enable/disable
concurrency. Compared to macros (\#ifdef), it allows to use sequential
and parallel versions in the same application. Several techniques are
available for this, and the developer can choose whatever is best in his
case:

-   Tag dispatching by **Type** or by **Instance**.
-   Non-type template parameters (`bool`, `enum`…)

In `<CGAL/tags.h>`, two tags are defined: Sequential\_tag and
Parallel\_tag.

``` {.cpp}
  struct Sequential_tag {};
  struct Parallel_tag : public Sequential_tag {};
```

The developers should use them (and no other custom tag) in order to
keep a good consistency among packages.

Here are a few code samples showing different ways to use tag
dispatching. These examples are just here as a "quick-start guide" and
are not exhaustive, there may be more complex scenarii involving tag
dispatching. Please note that we combine "`#ifdef CGAL_LINKED_WITH_TBB`"
and tag dispatching to test both conditions. As long as
`CGAL_LINKED_WITH_TBB` is defined, both sequential and parallel versions
can be used in the same program, depending on the tag used.

**By Type**

``` {.cpp}
  // Default: sequential
  template <typename Concurrency_tag = Sequential_tag >
  class Foo
  {
  public:
    void Bar() { std::cout << "Hello sequential world" << std::endl; }
  };
  
  #ifdef CGAL_LINKED_WITH_TBB
  // Parallel
  template <>
  class Foo<Parallel_tag>
  {
  public:
    void Bar() { std::cout << "Hello parallel world" << std::endl; }
  };
  #endif // CGAL_LINKED_WITH_TBB
  
  int main(void)
  {
    Foo<> f1; // Sequential_tag is used by default
    Foo<Parallel_tag> f2;
    f1.Bar(); // Calls the sequential version
    f2.Bar(); // Calls the parallel version
    return 0;
  }
```

Note: If the developer is using custom tags inherited from
Parallel\_tag, he must provide one specialization by tag.

**By Instance**

``` {.cpp}
  // Default: sequential
  template <typename Concurrency_tag = Sequential_tag >
  class Foo
  {
  protected:
    void Bar_dispatch(Sequential_tag)
    {
      std::cout << "Hello sequential world" << std::endl;
    }
  #ifdef CGAL_LINKED_WITH_TBB
    void Bar_dispatch(Parallel_tag) 
    {
      std::cout << "Hello parallel world" << std::endl;
    }
  #endif //CGAL_LINKED_WITH_TBB
  
  public:
    void Bar() 
    {
  #ifdef CGAL_LINKED_WITH_TBB
      Bar_dispatch(Concurrency_tag());
  #else //!CGAL_LINKED_WITH_TBB
      // If TBB is not available, we fall back on the sequential function
      Bar_dispatch(Sequential_tag());
  #endif //CGAL_LINKED_WITH_TBB
    }
  };
  
  int main(void)
  {
    Foo<> f1; // Sequential_tag is used by default
    Foo<Parallel_tag> f2;
    f1.Bar(); // Calls the sequential version
    f2.Bar(); // Calls the parallel version
    return 0;
  }
```

**Using is\_convertible**

Note that it makes it possible to have some inheritage amongst tags.

``` {.cpp}
  // Default: sequential
  template <typename Concurrency_tag = Sequential_tag>
  class Foo
  {
  public:
    void Bar() 
    {
  #ifdef CGAL_LINKED_WITH_TBB
      // Parallel
      if (boost::is_convertible<Concurrency_tag, Parallel_tag>::value)
      {
        std::cout << "Hello parallel world" << std::endl;
      }
      // Sequential
      else
  #endif // CGAL_LINKED_WITH_TBB
      {
        std::cout << "Hello sequential world" << std::endl;
      }
    }
  };
  
  int main(void)
  {
    Foo<> f1; // Sequential_tag is used by default
    Foo<Parallel_tag> f2;
    f1.Bar(); // Calls the sequential version
    f2.Bar(); // Calls the parallel version
    return 0;
  }
```

**Using non-type template parameters**

``` {.cpp}
  // Default: sequential
  template <bool Is_parallel = false>
  class Foo
  {
  public:
    void Bar()
    {
  #ifdef CGAL_LINKED_WITH_TBB
      // Parallel
      if (Is_parallel)
      {
        std::cout << "Hello parallel world" << std::endl;
      }
      // Sequential
      else
  #endif // CGAL_LINKED_WITH_TBB
      {
        std::cout << "Hello sequential world" << std::endl;
      }
    }
  };
  
  int main(void)
  {
    Foo<> f1; // Is_parallel = false by default
    Foo<true> f2;
    f1.Bar(); // Calls the sequential version
    f2.Bar(); // Calls the parallel version
    return 0;
  }
```

Note: this is for internal use only. Documented classes must use
Sequential\_tag/Parallel\_tag.

Of course, more advanced techniques may be used – e.g.: specialized base
classes to provide different member variables.

## Advanced TBB configuration

An advanced user may want to specify the number of threads used by TBB.
This control is provided by the [`tbb::task_scheduler_init` class](https://software.intel.com/en-us/node/506294).
TBB automatically creates a task scheduler the first time that a thread
uses task scheduling services and destroys it when the last such thread
exits. Instead, an instance of task\_scheduler\_init might be manually
created by the user to control, among other things, the number of
threads used by the task scheduler. Here is an example:

` {`\
`   tbb::task_scheduler_init tsi(6); // 6 threads`\
`   run_parallel_algo1(); // Uses 6 threads`\
` }`\
` {`\
`   tbb::task_scheduler_init tsi(12); // 12 threads`\
`   run_parallel_algo2(); // Uses 12 threads`\
` }`

See [Intel documentation](https://software.intel.com/en-us/node/506294)
for more information.
