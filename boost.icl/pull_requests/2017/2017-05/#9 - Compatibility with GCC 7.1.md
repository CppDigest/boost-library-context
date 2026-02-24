# #9 Compatibility with GCC 7.1 [Merged]

> Username: mgaunard  
> Created at: 2017-05-04 16:18:29 UTC  
> Updated at: 2017-11-21 22:46:36 UTC  
> Merged at: 2017-11-21 22:46:36 UTC  
> Closed at: 2017-11-21 22:46:36 UTC  
> Url: https://github.com/boostorg/icl/pull/9  

The libstdc++ shipped with GCC 7.1 uses a unary class template for std::string.  
type_to_string needs to be adapted to deal with this properly.

---

## Comment 1

> Username: duarten  
> Created_at: 2017-07-11 18:54:17 UTC  
> Url: https://github.com/boostorg/icl/pull/9#issuecomment-314538795  

+1

---

## Comment 2

> Username: jwakely  
> Created_at: 2017-09-12 16:37:15 UTC  
> Url: https://github.com/boostorg/icl/pull/9#issuecomment-328910423  

> The libstdc++ shipped with GCC 7.1 uses a unary class template for std::string.  
  
No it doesn't!  
  
There's an explanation at https://bugzilla.redhat.com/show_bug.cgi?id=1485641

---
