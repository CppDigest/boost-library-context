# #33 result_of<F&(…)> fails on MSVC-12 if F is a function pointer [Merged]

> Username: morinmorin  
> Created at: 2017-09-20 14:54:42 UTC  
> Updated at: 2017-09-23 18:59:07 UTC  
> Merged at: 2017-09-23 18:59:07 UTC  
> Closed at: 2017-09-23 18:59:07 UTC  
> Url: https://github.com/boostorg/utility/pull/33  

Background  
----------  
For a callable object of type `F`, the correct usage of decltype-based `result_of` to compute the return type of its lvalue call is `result_of<F&(…)`. So I updated some codes in Boost.Iterator to fix the usage of `result_of` (e.g. boostorg/iterator#35). However, the regression test fails on MSVC-12 if `F` is a function pointer type. That compiler has N3276 decltype support but does not support expression SFINAE.  
  
  
Analysis  
--------  
On compilers without the support of expression SFINAE, decltype-based `result_of<FP&(…)>` (**`FP` is a function pointer type**) fails to compile. For example, this code fails:  
```  
#define BOOST_NO_SFINAE_EXPR  
#define BOOST_RESULT_OF_USE_DECLTYPE  
#include <boost/utility/result_of.hpp>  
typedef double(*FP)(int);  
boost::result_of<FP&(int)> ouch;  
```  
This is because `result_of<FP&(…)>` tries to instantiate an incomplete type in the following instantiation chain ("A -> B" means "A attempts to instantiate B"):  
  
1. `result_of_is_callable_<FP&, …>` -> `result_of_select_call_wrapper_type_<FP&>`  
2. `result_of_select_call_wrapper_type_<FP&>` -> `result_of_callable_fun_<FP&>`  
3. `result_of_callable_fun_<FP&>` -> `result_of_callable_fun_2_<FP>`  
  
`result_of_callable_fun_2_<FP>` is an incomplete type, because the primary template of `result_of_callable_fun_2_` does not have definition and it has specialization only for function types.  
  
This PR fixes up the problem by  
  
- Make `result_of_select_call_wrapper_type_<F&>` instantiate `result_of_callable_fun_<F>` instead of `result_of_callable_fun_<F&>` (when `F` is not a class type).  
- Remove `result_of_callable_fun_<F&>` and add `result_of_callable_fun_<F>`.  
  
(Since I don't have MSVC, I've been running the code on old GCC that does not support expression SFINAE and on newer GCC with `#define BOOST_NO_SFINAE_EXPR`.)

---

## Comment 1

> Username: morinmorin  
> Created_at: 2017-09-20 14:55:03 UTC  
> Url: https://github.com/boostorg/utility/pull/33#issuecomment-330877827  

Hi @ericniebler, could you please take a look when you have time?

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-09-23 18:57:15 UTC  
> Url: https://github.com/boostorg/utility/pull/33#issuecomment-331662823  

This change fixes Boost.Iterator tests. MSVC 8-14.1 pass the `result_of` tests after the change.

---
