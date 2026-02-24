# #59 Initialize function pointers at first use (II) [Closed]

> Username: Dani-Hub  
> Created at: 2017-11-20 19:42:09 UTC  
> Updated at: 2017-11-22 22:39:18 UTC  
> Closed at: 2017-11-22 22:21:18 UTC  
> Url: https://github.com/boostorg/filesystem/pull/59  

Fixes static initialization issue of new compare function using interlocked functions. Kudus to Daniela Engert for her initial pull request ff8c321a655490783c7aac589796a5ee35a749e1.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-11-22 10:31:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/59#pullrequestreview-78397908  

📁 src/operations.cpp

```diff
 629 |+   inline Ptr_equal_string_ordinal_ic compare()
 630 |+   {
 631 |+     for (unsigned i = 0; compare_function_state < 2; ++i)
```

> Username: Lastique  
> Created_at: 2017-11-22 10:31:36 UTC  
> Updated_at: 2017-11-22 21:11:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/59#discussion_r152524074  

This reads `compare_function_state` non-atomically (i.e. not thread-safe).

> Username: Dani-Hub  
> Created_at: 2017-11-22 12:04:57 UTC  
> Updated_at: 2017-11-22 21:11:17 UTC  
> Url: https://github.com/boostorg/filesystem/pull/59#discussion_r152544329  

Ouch, you are completely right! I thought that I could use the `<atomic>`-free interlocked approach that `boost/interprocess/detail/win32_api.hpp` uses, but now I realize that the corresponding code in the `function_address_holder::get_module` and `function_address_holder::get` is defect as well! I will take a look this night or tomorrow to improve the patch - thanks for the review so far!

> Username: Dani-Hub  
> Created_at: 2017-11-22 21:18:15 UTC  
> Url: https://github.com/boostorg/filesystem/pull/59#discussion_r152680717  

The non-atomical read access should have been fixed now via https://github.com/Dani-Hub/filesystem/commit/76c061ec79328cc4fee6a23f07e2918c66fe44a7

> Username: Dani-Hub  
> Created_at: 2017-11-22 22:39:18 UTC  
> Url: https://github.com/boostorg/filesystem/pull/59#discussion_r152694236  

I have now created ticket https://svn.boost.org/trac10/ticket/13309 to handle the same problem in `boost/interprocess/detail/win32_api.hpp` (twice)


---
