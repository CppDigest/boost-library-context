# #80 - <functional> might not be included soon enough when building treap_multiset_test.cpp [Closed]

> Username: dkrejsa  
> Created at: 2023-03-08 01:16:11 UTC  
> Updated at: 2023-04-20 18:15:57 UTC  
> Closed at: 2023-04-20 18:15:51 UTC  
> Url: https://github.com/boostorg/intrusive/issues/80  

Working with a set of proprietary C++ headers derived from Dinkum, building the treap_multiset_test.cpp test fails due to the definition of `std::greater<T>` not being visible yet at the point in generic_multiset_test.hpp where it is used.  
  
In this environment the <functional> header does not appear to be included at that point yet, although it is included later.  
Adding  
  
    #include <functional>  
  
immediately after the include of `<typeinfo>` in generic_multiset_test.hpp, appears to fix the problem.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2023-04-20 18:15:57 UTC  
> Url: https://github.com/boostorg/intrusive/issues/80#issuecomment-1516753520  

Many thanks for the report!
