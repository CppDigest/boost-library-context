# #404 [experimental] Printable support for Visual C++ [Merged]

> Username: ricejasonf  
> Created at: 2018-05-30 17:32:30 UTC  
> Updated at: 2018-06-20 03:51:49 UTC  
> Merged at: 2018-06-20 03:51:49 UTC  
> Closed at: 2018-06-20 03:51:49 UTC  
> Url: https://github.com/boostorg/hana/pull/404  

- Conditionally strips out preceding `struct` keyword since  
    Microsoft's compiler includes that in the outputs of type_info::name  
  
NOTE: I was not able to test this on Visual C++ as it is closed source and the version is likely not released.

---

## Comment 1

> Username: ricejasonf  
> Created_at: 2018-05-31 20:58:26 UTC  
> Url: https://github.com/boostorg/hana/pull/404#issuecomment-393677821  

@ldionne   
  
I implemented your suggestion to use a regex in the tests.  
  
The printed output would look like: `metafunction<*implementation-defined*>`.  
  
The only part that is cleaned up is the outer `template`, `metafunction`, `metafunction_class` part just like before. The types rendered within are whatever the compiler gives us.

---
