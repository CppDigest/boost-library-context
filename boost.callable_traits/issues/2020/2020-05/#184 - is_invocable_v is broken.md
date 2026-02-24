# #184 - is_invocable_v is broken [Closed]

> Username: eugnsp  
> Created at: 2020-05-09 12:55:20 UTC  
> Updated at: 2020-07-03 15:25:14 UTC  
> Closed at: 2020-07-03 15:25:13 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/184  

It seems that `is_invocable_v` variable template is broken. `is_invocable` and `is_invocable_v` have different implementations:  
```  
template<typename T, typename... Args>  
struct is_invocable : detail::is_invocable_impl<T, Args...>::type {  
    using type = typename detail::is_invocable_impl<T, Args...>::type;  
};  
  
template<typename T, typename... Args>  
constexpr bool is_invocable_v =  
    detail::is_invocable_impl<detail::traits<T>, Args...>::type::value;  
```  
  
With my understanding, it is not `detail::traits<T>` that should be checked for invocability, but simply `T`. As a result, this assertion [fails](https://godbolt.org/z/tzqcB-):  
  
```  
#include <boost/callable_traits.hpp>  
  
int test(int) { return 0; }  
  
static_assert(  
    boost::callable_traits::is_invocable_v<decltype(test), int> ==   
    boost::callable_traits::is_invocable<decltype(test), int>::value  
);  
```  
  
Originally reported here: https://stackoverflow.com/q/61696567

---

## Comment 1

> Username: badair  
> Created at: 2020-05-12 15:51:23 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/184#issuecomment-627430922  

Thanks for the bug report, this is gross. Will fix for Boost 1.74 as well as the missing test coverage.

---

## Comment 2

> Username: badair  
> Created at: 2020-07-03 15:25:13 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/184#issuecomment-653595725  

Fixed via #185
