# #129 - use of deprecated header [Closed]

> Username: jefftrull  
> Created at: 2020-02-12 16:49:22 UTC  
> Updated at: 2020-02-13 08:01:18 UTC  
> Closed at: 2020-02-13 07:57:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/129  

`path.hpp` now produces a deprecation warning due to the inclusion of `boost/io/detail/quoted_manip.hpp`, which is apparently replaced by `boost/io/quoted.hpp`

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-13 07:57:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/129#issuecomment-585598831  

It's already fixed in develop.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-02-13 08:01:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/129#issuecomment-585600162  

Merged to master just now.
