# #13 Get rid of clang-3.3 -Wconstexpr-not-const warning [Merged]

> Username: ddemidov  
> Created at: 2013-06-21 11:30:13 UTC  
> Updated at: 2014-07-31 02:38:04 UTC  
> Merged at: 2013-06-21 12:23:13 UTC  
> Closed at: 2013-06-21 12:23:13 UTC  
> Url: https://github.com/boostorg/compute/pull/13  

Without the patch, clang-3.3 shows the following warning:  
  
```  
.../compute/include/boost/compute/container/vector.hpp:454:31: warning: 'constexpr' non-static member function will not be  
      implicitly 'const' in C++1y; add 'const' to avoid a change in behavior [-Wconstexpr-not-const]  
    BOOST_CONSTEXPR size_type _minimum_capacity() { return 4; }  
                              ^  
                                                  const  
.../compute/include/boost/compute/container/vector.hpp:455:27: warning: 'constexpr' non-static member function will not be  
      implicitly 'const' in C++1y; add 'const' to avoid a change in behavior [-Wconstexpr-not-const]  
    BOOST_CONSTEXPR float _growth_factor() { return 1.5; }  
                          ^  
                                           const  
  
```

---
