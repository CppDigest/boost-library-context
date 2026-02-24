# #46 Use the original program source for program creation/compilation [Merged]

> Username: ddemidov  
> Created at: 2014-01-07 17:06:43 UTC  
> Updated at: 2014-01-07 19:54:02 UTC  
> Merged at: 2014-01-07 18:11:08 UTC  
> Closed at: 2014-01-07 18:11:08 UTC  
> Url: https://github.com/boostorg/compute/pull/46  

Instead of building the program from source with the added comment block (used for distinction between different platforms and devices when offline cache is in use), only use the altered source for the hash computation. This way users will not get unexpected results from `program.source()`.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2014-01-07 18:11:14 UTC  
> Url: https://github.com/boostorg/compute/pull/46#issuecomment-31762855  

Looks good! Merged!

---
