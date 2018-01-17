Let's say you have an assertion in the code, and one day you have a test case that triggers it. For example, like in https://github.com/CGAL/cgal/issues/2284 :
```
terminate called after throwing an instance of 'CGAL::Assertion_exception'
  what():  CGAL ERROR: assertion violation!
Expr: n==0 || dleft_frac >= float_prior(float_prior(1.))
File: /home/lrineau/Git/other-cgal2/Mesh_3/include/CGAL/Mesh_3/Protect_edges_sizing_field.h
Line: 1135
```

How frustrating! You do not even know if the issue is with `n`, or `dleft_frac`, and what is the local geometric configuration that lead to the assertion triggering. Usually, to debug that, if the debugging with a debugger does not give conclusion, I modify the code. Instead of:

``` c++
    CGAL_assertion(n==0 || dleft_frac >= float_prior(float_prior(1.)));
```

I write:

``` c++
    if(!(n==0 || dleft_frac >= float_prior(float_prior(1.)))) {
      // do somehing,
      // print A LOT of information on std::cerr,
      // dump the input triangulation into a file,
      // ...
      CGAL_error();
    }

```

But that modification can only be temporay, and I cannot commit it into CGAL because the `if(..)` would be evaluated even with CGAL_NDEBUG defined. And thus I have to rewrite it again if the same assertion triggers again on a different data set!

Note that the expansion of `CGAL_assertion(expr);` is similar to:

```c++
    if(! expr) {
      ::CGAL::assertion_fail( "text version <expr>", __FILE__, __LINE__));
    }
```

And `CGAL::assertion_fail` is the function that throws exceptions (with the default behavior).

My usual modification to debug assertions is similar to injecting code in the body of the `if(!expr)`... And two weeks ago I found out that I can abuse the second parameter of another macro to do the same: `CGAL_assertion_msg(A,B)`. It is used that way:

```c++
    CGAL_assertion_msg( n==0, "Help!");
```

that expanses to:

```c++
    if(! expr) {
      ::CGAL::assertion_fail( "text version <expr>",
                              __FILE__, __LINE__,
                              "Help"!));
    }
```

The fourth parameter of CGAL::assertion_fail is a plain string, with the default value "". That string is by default displayed in the `what()` message of the thrown exception.

The trick is:

```c++
    CGAL_assertion( n==0, (dump_triangulation(tr), ""))
```

The expression `(dump_triangulation(tr), "")` calls `dump_triangulation(tr)`, but its value is "". It uses the comma operator:

http://en.cppreference.com/w/cpp/language/operator_other#Built-in_comma_operator

The trick is that we can call almost any function/functor, in a sequence using the comma, and even several of them:

```c++
    CGAL_assertion( n==0, (dump_triangulation(tr), dump_point(point_a), ""))
```

to call also `dump_point(point_a)`. And actually the most convient way is to use lambda expressions, for example in this code:

```c++
    if(it == (points_.end() - 2)) {
        CGAL_assertion_msg(is_loop(),
#ifndef CGAL_CFG_NO_CPP0X_LAMBDAS
                           [it] {
                             std::stringstream s;
                             s << "Problem at segment ("
                               << *it << ", " << *(it+1) << ")";
                             return s.str();
                           }().c_str()
#else // no C++ lamdbas
                           ""
#endif // no C++ lamdbas
                           );
        it = points_.begin();
      } else {
        ++it;
      }
```

With that code, if the compiler uses C++11, I can inject the code of the
lambda expression:

```c++
                 std::stringstream s;
                 s << "Problem at segment ("
                   << *it << ", " << *(it+1) << ")";
                 return s.str();
```

in the body of the `if()` of the assertion `CGAL_assertion(is_loop())`, and the message displayed by the non-caught exception can now display information about the local geometry.

That modified code is not really pretty, but in my opinion there is a sort of elegance in it:

  - if the code is compiler with `CGAL_NDEBUG` or `NDEBUG`, the whole content of `CGAL_assertion_msg` is removed,

  - the content of the functor (or lambda expression) can be anything: it can display a customized message, but it could also dump the input data, or the current data-structure in-memory, to a file, to help debugging,

  - if the functor was implemented by a function or a callable class, then the preprocessing test using CGAL_CFG_NO_CPP0X_LAMBDAS could be removed.
