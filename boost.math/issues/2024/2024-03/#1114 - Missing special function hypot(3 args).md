# #1114 - Missing special function hypot(3 args) [Closed]

> Username: gpeterhoff  
> Created at: 2024-03-19 01:45:01 UTC  
> Updated at: 2025-09-09 09:37:36 UTC  
> Closed at: 2025-09-09 09:37:36 UTC  
> Url: https://github.com/boostorg/math/issues/1114  

I am missing hypot(3 args). For an exact implementation there is just the discussion https://gcc.gnu.org/bugzilla/show_bug.cgi?id=77776. Maybe you have some ideas.  
hypot(2 args) must then of course be adapted.  
math_fwd.hpp:  
```  
   // Hypotenuse function sqrt(x ^ 2 + y ^ 2).  
  
   template <class T1, class T2>  
   tools::promote_args_t<T1, T2>  
         hypot(T1 x, T2 y);  
  
   template <class T1, class T2, class Policy>  
   std::enable_if_t<policies::is_policy<Policy>::value, tools::promote_args_t<T1, T2>>  
         hypot(T1 x, T2 y, const Policy&);  
  
   // Hypotenuse function sqrt(x ^ 2 + y ^ 2 + z ^ 2).  
  
   template <class T1, class T2, class T3>  
   std::enable_if_t<!policies::is_policy<T3>::value, tools::promote_args_t<T1, T2, T3>>  
         hypot(T1 x, T2 y, T3 z);  
  
   template <class T1, class T2, class T3, class Policy>  
   std::enable_if_t<!policies::is_policy<T3>::value, tools::promote_args_t<T1, T2, T3>>  
         hypot(T1 x, T2 y, T3 z, const Policy&);  
```  
add to BOOST_MATH_DECLARE_SPECIAL_FUNCTIONS(Policy)  
```  
   template <class T1, class T2, class T3>\  
   inline std::enable_if_t<!policies::is_policy<T3>::value, tools::promote_args_t<T1, T2, T3>> \  
   hypot(T1 x, T2 y, T3 z){ return boost::math::hypot(x, y, z, Policy()); } \  
```  
and of course the implementations in hypot.hpp  
  
Questions  
* Why are you making this so complicated? Yes, the functions were implemented before C++11. But then no more updates have taken place, although boost.math requires C++14. I think the whole #define's and forwarddeclarations (math_fwd.hpp) can be saved if you declare the functions cleanly via std::enable_if_t (because of the policies), whereby the question then arises whether the policies are still up to date at all (we have C++23, C++26 is just around the corner).  
* Your special-functions throw exceptions, but std::special-functions do not (normally). So your functions are incompatible - which is of course due to your very old implementations.  
  
regards  
Gero

---

## Comment 1

> Username: cffk  
> Created at: 2025-09-06 11:38:18 UTC  
> Updated at: 2025-09-06 11:44:12 UTC  
> Url: https://github.com/boostorg/math/issues/1114#issuecomment-3261923536  

I would also appreciate it if Boost provided an implementation of  `hypot(x, y, z)`.  Even a simple implementation as a placeholder would help me.  (This is for use with `boost::multiprecision::float128` numbers.)
