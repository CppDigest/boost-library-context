# #183 - error: must specify at least one argument for '...' parameter of variadic macro [Closed]

> Username: iskunk  
> Created at: 2023-11-21 22:29:40 UTC  
> Updated at: 2023-11-28 10:28:45 UTC  
> Closed at: 2023-11-28 10:28:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/183  

I am building Boost 1.83.0 with the Intel LLVM compiler and `-std=c++14`. It previously compiled without issue; however, I upgraded the compiler to 2024.0.0, and it now gives errors of the following form:  
```  
[ 33%] Building CXX object libs/mysql/test/unit/CMakeFiles/boost_mysql_unittests.dir/test/execution_processor/static_execution_state_impl.cpp.o  
In file included from /home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_state_impl.cpp:28:  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:30:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
   30 | BOOST_DESCRIBE_STRUCT(row1, (), (fvarchar, ftiny))  
      | ^  
/home/src/boost/1.83.0/libs/describe/include/boost/describe/class.hpp:64:33: note: expanded from macro 'BOOST_DESCRIBE_STRUCT'  
   64 |     BOOST_DESCRIBE_MAYBE_UNUSED BOOST_DESCRIBE_BASES_(C BOOST_DESCRIBE_PP_UNPACK Bases) \  
      |                                 ^  
/home/src/boost/1.83.0/libs/describe/include/boost/describe/class.hpp:51:36: note: expanded from macro 'BOOST_DESCRIBE_BASES_'  
   51 | #define BOOST_DESCRIBE_BASES_(...) BOOST_DESCRIBE_BASES(__VA_ARGS__)  
      |                                    ^  
/home/src/boost/1.83.0/libs/describe/include/boost/describe/detail/bases.hpp:48:62: note: expanded from macro 'BOOST_DESCRIBE_BASES'  
   48 | { return boost::describe::detail::base_descriptor_fn_impl( 0 BOOST_DESCRIBE_PP_FOR_EACH(BOOST_DESCRIBE_BASE_IMPL, C, ##__VA_ARGS__) ); }  
      |                                                              ^  
/home/src/boost/1.83.0/libs/describe/include/boost/describe/detail/pp_for_each.hpp:120:33: note: expanded from macro 'BOOST_DESCRIBE_PP_FOR_EACH'  
  120 |     BOOST_DESCRIBE_PP_FOR_EACH_0))(F, __VA_ARGS__))  
      |                                 ^  
/home/src/boost/1.83.0/libs/describe/include/boost/describe/detail/pp_for_each.hpp:64:9: note: macro 'BOOST_DESCRIBE_PP_FE_EXTRACT' defined here  
   64 | #define BOOST_DESCRIBE_PP_FE_EXTRACT(_0, _1, _2, _3, _4, _5, _6, _7, _8, _9, _10, _11, _12, _13, _14, _15, _16, _17, _18, _19, _20, _21, _22, _23, _24, _25, _26, _27, _28, _29, _30, _31, _32, _33, _34, _35, _36, _37, _38, _39, _40, _41, _42, _43, _44, _45, _46, _47, _48, _49, _50, _51, _52, V, ...) V  
      |         ^  
```  
  
This appears to be a typical case of increasing compiler strictness. Here is the full list of `error:` lines seen in `make -k` output:  
```  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/detail/typing/row_traits.cpp:48:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/detail/typing/row_traits.cpp:54:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/detail/typing/row_traits.cpp:61:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/detail/typing/row_traits.cpp:67:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/detail/typing/row_traits.cpp:80:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:30:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:38:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:49:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:56:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/execution_processor/static_execution_processor_helpers.hpp:64:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/static_execution_state.cpp:34:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
/home/src/boost/1.83.0/libs/mysql/test/unit/test/static_results.cpp:35:1: error: must specify at least one argument for '...' parameter of variadic macro [-Werror,-Wgnu-zero-variadic-macro-arguments]  
```

---

## Comment 1

> Username: anarthal  
> Created at: 2023-11-22 13:33:43 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1822780980  

This looks derived from Boost.Describe. I've just checked with the author and it's a spurious warning, related to a feature that's widely supported but technically only allowed in newer C++ standards. The solution is thus to suppress it.  
  
The warnings you're receiving are related to tests though, not to actual library code - you should be able to use the library without building these.  
  
On my side, I'll add warning suppression code for Describe uses in my code so this doesn't happen.  
  
Thanks for reporting,  
Ruben.

---

## Comment 2

> Username: aminiussi  
> Created at: 2023-11-24 15:44:30 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1825840631  

Same issue with the Cray compiler 16.0.0 (based on clang)

---

## Comment 3

> Username: anarthal  
> Created at: 2023-11-27 13:46:21 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1827864454  

I'm in the process of solving it and adding a CI build. Is this fix urgent enough for you as to include a hotfix in Boost 1.84, or can we wait to release it until 1.85?

---

## Comment 4

> Username: iskunk  
> Created at: 2023-11-27 20:38:54 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1828563833  

If there's a simple enough patch I can apply to 1.83 to get the build working again, then that'll take off the pressure. (My org builds and runs the tests, for higher confidence in our use of Boost.)

---

## Comment 5

> Username: anarthal  
> Created at: 2023-11-27 23:38:48 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1828809091  

You can get rid of this error by applying [this patch](https://gist.github.com/anarthal/468f6d3c2dfe337ab3e00b7884d30cd5). The library hasn't changed a lot in 1.84. I've tested the patch with the 1.84 beta and it works, too. The patch disables pedantic warnings for Boost.MySQL tests.  
  
I've raised #186 to track this. A major update is coming with 1.85, so I'll apply the actual changes there, recovering the pedantic flag where possible and suppressing any warnings coming from it.  
  
Please confirm that the patch solved your issue and I'll close this.  
  
Thanks,  
Ruben.

---

## Comment 6

> Username: iskunk  
> Created at: 2023-11-28 03:23:59 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1829010064  

I had to tweak the patch to apply to `[ab]/libs/mysql/cmake/test_utils.cmake` rather than `[ab]/cmake/test_utils.cmake`, as I am building the big tarball, but yes---it resolves the build error that spurred this bug report.  
  
Thank you for addressing this issue diligently. I am indeed building Boost with CMake, as the B2 build was unusable for me, and with tests enabled for greater confidence in the result.

---

## Comment 7

> Username: anarthal  
> Created at: 2023-11-28 10:28:45 UTC  
> Url: https://github.com/boostorg/mysql/issues/183#issuecomment-1829531754  

Oh thanks. I created it relative to the root of the individual Boost lib instead of the big tarball.  
  
I'll close this, and be sure to merge #186 for 1.85.  
  
Should you have any other issue or question, I'm here to help.  
  
Regards,  
Ruben.
