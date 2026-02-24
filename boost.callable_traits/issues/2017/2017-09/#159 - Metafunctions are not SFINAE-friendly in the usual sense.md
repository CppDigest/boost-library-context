# #159 - Metafunctions are not SFINAE-friendly in the usual sense [Closed]

> Username: ldionne  
> Created at: 2017-09-13 04:23:33 UTC  
> Updated at: 2017-09-24 01:19:48 UTC  
> Closed at: 2017-09-24 01:13:05 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/159  

The way CallableTraits makes traditional metafunctions SFINAE-friendly does not meet what's normally expected from SFINAE-friendly metafunctions. When we say a metafunction is SFINAE-friendly, we usually mean it does not have a nested `::type` alias when it would normally fail. This has the nasty effect that code such as the following does not have the intended effect:  
  
```c++  
template <typename T>  
struct GetFunctionType  
  : boost::mpl::eval_if<std::is_function<T>,  
      boost::callable_traits::function_type<T>,  
      boost::mpl::identity<T>  
    >  
{ };  
```  
  
The intended behavior is that if the condition is not satisfied, then `function_type<T>` would not be instantiated, and hence no error would happen. This can also be a compile-time performance bug, since we often assume that no work is done when we only mention a template, without instantiating it. However, we would get a hard error while trying to instantiate the default template parameter for `function_type`, which happens when we only mention `function_type<T>` (hence when creating the list of bases), not when we try to get its nested `::type` alias.  
  
Full reproduction:  
```c++  
#include <boost/mpl/identity.hpp>  
#include <boost/mpl/eval_if.hpp>  
#include <boost/callable_traits/function_type.hpp>  
#include <type_traits>  
#include <iostream>  
  
template <typename T>  
struct GetFunctionType  
  : boost::mpl::eval_if<std::is_function<T>,  
      boost::callable_traits::function_type<T>,  
      boost::mpl::identity<T>  
    >  
{ };  
  
template <typename T, typename FunctionType = typename GetFunctionType<T>::type>  
void f(T) { std::cout << "I can get the function type!" << std::endl; }  
  
void f(...) { std::cout << "I can't get the function type!" << std::endl; }  
  
int main() {  
  f(0);  
}  
```  
  
Error message:  
```  
In file included from [...]/include/boost/callable_traits/function_type.hpp:12:  
In file included from [...]/include/boost/callable_traits/detail/core.hpp:12:  
In file included from [...]/include/boost/callable_traits/detail/utility.hpp:13:  
[...]/include/boost/callable_traits/detail/sfinae_errors.hpp:33:9: error: no type named 'type' in  
      'boost::callable_traits::error::parameters<boost::callable_traits::cannot_determine_parameters_for_this_type_>::_'  
        using sfinae_try = typename BOOST_CLBL_TRTS_DISJUNCTION(  
        ^~~~~  
[...]/include/boost/callable_traits/detail/utility.hpp:55:1: note: in instantiation of template type alias 'sfinae_try' requested here  
using try_but_fail_if_invalid = sfinae_try<T,  
^  
[...]/include/boost/callable_traits/function_type.hpp:24:1: note: in instantiation of template type alias 'try_but_fail_if_invalid' requested  
      here  
using function_type_t = //see below  
^  
[...]/include/boost/callable_traits/function_type.hpp:31:35: note: in instantiation of template type alias 'function_type_t' requested here  
template<typename T, typename U = function_type_t<T>>  
                                  ^  
[...]/dyno/test/interface_macro.cpp:79:31: note: in instantiation of default argument for 'function_type<int>' required here  
      boost::callable_traits::function_type<T>,  
                              ^~~~~~~~~~~~~~~~  
[...]/dyno/test/interface_macro.cpp:84:56: note: in instantiation of template class 'GetFunctionType<int>' requested here  
template <typename T, typename FunctionType = typename GetFunctionType<T>::type>  
                                                       ^  
[...]/dyno/test/interface_macro.cpp:85:6: note: in instantiation of default argument for 'f<int>' required here  
void f(T) { std::cout << "I can get the function type!" << std::endl; }  
     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
[...]/dyno/test/interface_macro.cpp:90:3: note: while substituting deduced template arguments into function template 'f' [with T = int, FunctionType = (no value)]  
  f(0);  
  ^  
```

---

## Comment 1

> Username: badair  
> Created at: 2017-09-24 01:13:05 UTC  
> Updated at: 2017-09-24 01:19:48 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/159#issuecomment-331679827  

Thanks for the thorough bug report @ldionne.  
  
Fixed via 16f6e2b. Since we already have good SFINAE test coverage -- we test SFINAE-ability for both lazy and eager traits [using the same macro](https://github.com/boostorg/callable_traits/blob/master/test/test.hpp#L35) (edit: my ifdef is backwards -- oh well, same effect) -- I did not add test cases for SFINAE laziness. I may add some in the future, but I'm not very worried about it. The above `mpl::eval_if` example works anecdotally.  
  
Tests passed on [AppVeyor](https://ci.appveyor.com/project/badair/callable-traits) for MSVC and [Travis](https://travis-ci.org/badair/callable_traits/builds/279083882) for GCC and Clang.  
  
The above CI links are from my personal fork, because the Boost CI queue is painfully long.

---

## Comment 2

> Username: badair  
> Created at: 2017-09-24 01:19:18 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/159#issuecomment-331680046  

I'll let this sit on the develop branch for a bit so I can see if I broke the docs anywhere.
