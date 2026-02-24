# #78 path.hpp: Unify coding style of path_traits::convert() calls [Merged]

> Username: datadiode  
> Created at: 2018-07-14 07:28:15 UTC  
> Updated at: 2018-11-24 18:06:09 UTC  
> Merged at: 2018-11-24 18:06:09 UTC  
> Closed at: 2018-11-24 18:06:09 UTC  
> Url: https://github.com/boostorg/filesystem/pull/78  



---

## Comment 1

> Username: Empact  
> Created_at: 2018-07-23 01:49:06 UTC  
> Url: https://github.com/boostorg/filesystem/pull/78#issuecomment-406916390  

These are all relative to `begin()`, and dereferencing `begin()` is valid so long as the string is not `empty()`. Guards are in place here, so it seems the existing code is fine.

---

## Comment 2

> Username: datadiode  
> Created_at: 2018-07-23 11:38:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/78#issuecomment-407028001  

Empact, you are right. I don't know why vc140 gave me an assertion in `path::string()`. I drew a wrong conclusion which falsely implied that `+` evaluated before `&*`, which would have lead to dereferencing `begin() + size()`. Using `c_str()` instead of `begin()` might still be considered preferable as it is less tricky, and in accordance with all the other calls to `path_traits::convert()` which exist in the same file.

---
