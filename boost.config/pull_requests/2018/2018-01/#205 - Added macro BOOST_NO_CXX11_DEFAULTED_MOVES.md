# #205 Added macro BOOST_NO_CXX11_DEFAULTED_MOVES [Merged]

> Username: akrzemi1  
> Created at: 2018-01-04 00:12:10 UTC  
> Updated at: 2018-01-25 10:12:09 UTC  
> Merged at: 2018-01-25 10:12:09 UTC  
> Closed at: 2018-01-25 10:12:09 UTC  
> Url: https://github.com/boostorg/config/pull/205  

Some compilers (GCC 4.4, 4.5, MSVC 12) have added support for defaulted functions and rvalue references, but move constructor and move assignment are not treated as special member functions yet so they cannot be defaulted.  
  
The docs in QBK are updated but I just couldn't generate HTML out of them.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2018-01-10 18:32:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/205#pullrequestreview-87935381  

📁 test/boost_no_cxx11_defaulted_moves.ipp

```diff
  15 |+   struct foo {
  16 |+     foo(foo&&) = default;
  17 |+ 	foo& operator=(foo&&) = default;
```

> Username: Lastique  
> Created_at: 2018-01-10 18:32:09 UTC  
> Updated_at: 2018-01-11 20:23:00 UTC  
> Url: https://github.com/boostorg/config/pull/205#discussion_r160761545  

Is there a tab indent here?


---

## Review 2 [Commented]

> Username: pdimov  
> Created_at: 2018-01-10 18:33:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/205#pullrequestreview-87935642  

📁 test/boost_no_cxx11_defaulted_moves.ipp

```diff
   8 |+ 
   9 |+ //  MACRO:         BOOST_NO_CXX11_DEFAULTED_MOVES 
  10 |+ //  TITLE:         C++0x defaulting of move constructor/assignmet unavailable
```

> Username: pdimov  
> Created_at: 2018-01-10 18:33:06 UTC  
> Updated_at: 2018-01-11 20:23:00 UTC  
> Url: https://github.com/boostorg/config/pull/205#discussion_r160761760  

"assignment" misspelled. (Next line too.)


---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2018-01-10 18:34:38 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/config/pull/205#pullrequestreview-87936078  

📁 include/boost/config/detail/suffix.hpp

```diff
 688 | #endif
 689 | 
 690 |+ // Lack of defaulted moves is implied by the lack of either rvalue references or any dafaulted functions
```

> Username: pdimov  
> Created_at: 2018-01-10 18:34:38 UTC  
> Updated_at: 2018-01-11 20:23:00 UTC  
> Url: https://github.com/boostorg/config/pull/205#discussion_r160762118  

"defaulted" misspelled. :-)


---
