# #46 - File is missing: lock_pool_init1.ipp [Closed]

> Username: barendgehrels  
> Created at: 2021-02-22 14:03:34 UTC  
> Updated at: 2021-02-23 09:24:53 UTC  
> Closed at: 2021-02-22 19:05:14 UTC  
> Url: https://github.com/boostorg/atomic/issues/46  

The file `lock_pool_init1.ipp` is not found when compiling Boost.Atomic.  
  
The file exists, but can't be found. So the error can be mitigated by using:  
```  
cp boost_1_75_0/libs/atomic/src/lock_pool_init1.ipp boost_1_75_0/include/boost/preprocessor/iteration/detail/iter/limits/lock_pool_init1.ipp  
```  
  
But that is obviously a workaround. What is the structural solution? Or is it an issue within the library? We had the same with `1.74.0` but in `1.75.0` we still have it.  
  
This is my minimal reproduction scenario:  
```  
clang -I boost_1_75_0/include -std=c++14 -MD -MT /tmp/lock_pool.cpp.o -MF /tmp/lock_pool.cpp.o.d -o /tmp/lock_pool.cpp.o -c boost_1_75_0/libs/atomic/src/lock_pool.cpp  
```  
  
  
The complete error message:  
```  
In file included from boost_1_75_0/libs/atomic/src/lock_pool.cpp:1120:  
In file included from boost_1_75_0/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:1343:  
boost_1_75_0/include/boost/preprocessor/iteration/detail/iter/limits/forward1_256.hpp:19:18: fatal error: 'lock_pool_init1.ipp' file not found  
#        include BOOST_PP_FILENAME_1  
                 ^  
boost_1_75_0/include/boost/preprocessor/iteration/detail/iter/forward1.hpp:29:56: note: expanded from macro 'BOOST_PP_FILENAME_1'  
#    define BOOST_PP_FILENAME_1 BOOST_PP_ARRAY_ELEM(2, BOOST_PP_ITERATION_PARAMS_1)  
                                                       ^  
boost_1_75_0/libs/atomic/src/lock_pool.cpp:1118:81: note: expanded from macro 'BOOST_PP_ITERATION_PARAMS_1'  
#define BOOST_PP_ITERATION_PARAMS_1 (3, (1, BOOST_ATOMIC_DETAIL_LOCK_POOL_SIZE, "lock_pool_init1.ipp"))  
                                                                                ^~~~~~~~~~~~~~~~~~~~~  
boost_1_75_0/include/boost/preprocessor/array/elem.hpp:23:114: note: expanded from macro 'BOOST_PP_ARRAY_ELEM'  
#    define BOOST_PP_ARRAY_ELEM(i, array) BOOST_PP_TUPLE_ELEM(BOOST_PP_ARRAY_SIZE(array), i, BOOST_PP_ARRAY_DATA(array))  
                                                                                                                 ^  
note: (skipping 10 expansions in backtrace; use -fmacro-backtrace-limit=0 to see all)  
boost_1_75_0/include/boost/preprocessor/tuple/rem.hpp:30:28: note: expanded from macro 'BOOST_PP_REM'  
# define BOOST_PP_REM(...) __VA_ARGS__  
                           ^  
boost_1_75_0/include/boost/preprocessor/variadic/elem.hpp:27:86: note: expanded from macro 'BOOST_PP_VARIADIC_ELEM'  
#     define BOOST_PP_VARIADIC_ELEM(n, ...) BOOST_PP_CAT(BOOST_PP_VARIADIC_ELEM_, n)(__VA_ARGS__,)  
                                                                                     ^  
boost_1_75_0/include/boost/preprocessor/variadic/limits/elem_64.hpp:18:55: note: expanded from macro 'BOOST_PP_VARIADIC_ELEM_2'  
#    define BOOST_PP_VARIADIC_ELEM_2(e0, e1, e2, ...) e2  
                                                      ^  
1 error generated.  
```  
  
And this is the message in gcc:  
```  
boost_1_75_0/libs/atomic/src/lock_pool.cpp:1118:81: fatal error: lock_pool_init1.ipp: No such file or directory  
 #define BOOST_PP_ITERATION_PARAMS_1 (3, (1, BOOST_ATOMIC_DETAIL_LOCK_POOL_SIZE, "lock_pool_init1.ipp"))  
                                                                                 ^~~~~~~~~~~~~~~~~~~~~  
compilation terminated.  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-02-22 19:05:14 UTC  
> Updated at: 2021-02-22 19:06:00 UTC  
> Url: https://github.com/boostorg/atomic/issues/46#issuecomment-783602037  

The file is part of Boost.Atomic sources, you should not be copying it anywhere. Boost.Atomic clearly compiles in CI and Boost releases, so there is something wrong with your build environment. You seem to be using some custom build system, which, I think, does not work the same way Boost.Build does, which causes the error.  
  
> This is my minimal reproduction scenario:  
> ```  
> clang -I boost_1_75_0/include -std=c++14 -MD -MT /tmp/lock_pool.cpp.o -MF /tmp/lock_pool.cpp.o.d -o /tmp/lock_pool.cpp.o -c boost_1_75_0/libs/atomic/src/lock_pool.cpp  
> ```  
  
This command line is incomplete as it is missing  `-I boost_1_75_0/libs/atomic/src`.

---

## Comment 2

> Username: Lastique  
> Created at: 2021-02-22 19:23:01 UTC  
> Url: https://github.com/boostorg/atomic/issues/46#issuecomment-783613180  

Also, note that the official build system performs a number of configure time checks and defines config macros based on their results. If you're using a custom build system you have to reproduce these checks and define macros similarly.

---

## Comment 3

> Username: barendgehrels  
> Created at: 2021-02-23 09:24:52 UTC  
> Url: https://github.com/boostorg/atomic/issues/46#issuecomment-784031001  

That helps. Indeed we use a custom build. Thank you!
