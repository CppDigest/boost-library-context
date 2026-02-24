# #478 - Multiple failures in concept check on GCC and Clang [Closed]

> Username: mborland  
> Created at: 2020-12-31 16:05:32 UTC  
> Updated at: 2020-12-31 18:07:29 UTC  
> Closed at: 2020-12-31 18:07:29 UTC  
> Url: https://github.com/boostorg/math/issues/478  

In the concept checks there are multiple instances of a macro called `this_should_not_compile` that are causing failure in the misc test suite. This occurs in multiple version of GCC, Clang under cxxstd=11,14, and 17. The errors from GCC and Clang follow or can be seen [here](https://github.com/mborland/math/runs/1630649192?check_suite_focus=true#step:16:741) starting at 741.  
  
GCC:  
```  
In file included from compile_test/std_real_concept_check.cpp:8:0:  
../../../boost/lexical_cast/detail/inf_nan.hpp: In function ‘bool boost::detail::put_inf_nan_impl(CharT*, CharT*&, const T&, const CharT*, const CharT*)’:  
compile_test/poison.hpp:90:18: error: ‘this_should_not_compile’ is not a member of ‘boost::detail::lcast_math’  
 #define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                  ^  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: note: in expansion of macro ‘isnan’  
             if (lcast_math::isnan(value)) {  
                             ^~~~~  
compile_test/poison.hpp:90:44: error: expected ‘)’ before ‘}’ token  
 #define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                                            ^  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: note: in expansion of macro ‘isnan’  
             if (lcast_math::isnan(value)) {  
                             ^~~~~  
compile_test/poison.hpp:90:44: error: expected primary-expression before ‘}’ token  
 #define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                                            ^  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: note: in expansion of macro ‘isnan’  
             if (lcast_math::isnan(value)) {  
                             ^~~~~  
compile_test/poison.hpp:90:44: warning: no return statement in function returning non-void [-Wreturn-type]  
 #define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                                            ^  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: note: in expansion of macro ‘isnan’  
             if (lcast_math::isnan(value)) {  
                             ^~~~~  
../../../boost/lexical_cast/detail/inf_nan.hpp: At global scope:  
compile_test/poison.hpp:90:47: error: expected declaration before ‘}’ token  
 #define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                                               ^  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: note: in expansion of macro ‘isnan’  
             if (lcast_math::isnan(value)) {  
                             ^~~~~  
```  
  
Clang:  
```  
In file included from compile_test/cstdfloat_concept_check_4.cpp:12:  
In file included from compile_test/instantiate.hpp:14:  
In file included from ../../../boost/math/distributions.hpp:15:  
In file included from ../../../boost/math/distributions/arcsine.hpp:35:  
In file included from ../../../boost/math/constants/constants.hpp:14:  
In file included from ../../../boost/math/tools/convert_from_string.hpp:15:  
In file included from ../../../boost/lexical_cast.hpp:32:  
In file included from ../../../boost/lexical_cast/try_lexical_convert.hpp:44:  
In file included from ../../../boost/lexical_cast/detail/converter_lexical.hpp:54:  
In file included from ../../../boost/lexical_cast/detail/converter_lexical_streams.hpp:63:  
../../../boost/lexical_cast/detail/inf_nan.hpp:145:29: error: extraneous closing brace ('}')  
compile_test/poison.hpp:90:54: note: expanded from macro 'isnan'  
#define isnan(x) this_should_not_compile(x)}}}}}}}}}}}}}}}}}}}  
                                                     ^  
```

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-12-31 17:11:32 UTC  
> Url: https://github.com/boostorg/math/issues/478#issuecomment-753008091  

This should have been fixed in https://github.com/boostorg/math/commit/db42e6bced785fa5d7fcb92ad7406f139a4ca10b ?  
  
Explanation: there are/were C++ compiler which define isinf etc as macros which then breaks our code in random ways.  The fix is make sure that all calls to std lib functions which are sometimes implemented as macros are called in a way that prevents macro expansion, and this test verifies this.  Lexical_cast used to use our fpclassify routines, so was OK, but has now been changed to use C++11 directly, hence the breakage.  
  
We could also take a hard line and insist on a std lib which does not define these as macros, and that would be fine in most places, but it wouldn't surprise me if there aren't some (obscure) platforms which still macro-ise these even with otherwise modern compilers.

---

## Comment 2

> Username: mborland  
> Created at: 2020-12-31 18:07:10 UTC  
> Url: https://github.com/boostorg/math/issues/478#issuecomment-753018521  

That commit is missing from the branch with my stats PR so this is an erroneous issue. I am seeing all the errors reported in the past two weeks, but did not see this in closed issues.   
  
I learned the hard way the past couple days that even modern compilers like to break code with macros. I did not realize you had to wrap `std::numeric_limits<T>::max()` in parenthesis to ensure it will work with MSVC.
