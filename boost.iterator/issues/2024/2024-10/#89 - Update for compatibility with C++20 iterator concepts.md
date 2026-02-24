# #89 - Update for compatibility with C++20 iterator concepts [Open]

> Username: Lastique  
> Created at: 2024-10-30 18:03:24 UTC  
> Updated at: 2024-10-30 18:03:24 UTC  
> Url: https://github.com/boostorg/iterator/issues/89  

In C++20, forward iterators (and more advanced) no longer require the iterator reference type to be an actual reference. Instead, it is required that a reference can be formed to the reference type.  
  
Since Boost.Iterator iterators still define their categories using C++03 rules, some iterators receive unnecessarily strict categories in terms of C++20. [Here](https://github.com/boostorg/iterator/issues/88#issuecomment-2447875807) is one example.  
  
Perhaps, we should use C++20 rules for deducing iterator categories in C++20 and later and the current rules in C++17 and older.
