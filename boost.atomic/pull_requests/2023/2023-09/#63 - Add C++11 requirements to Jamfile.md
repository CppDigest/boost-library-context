# #63 Add C++11 requirements to Jamfile [Merged]

> Username: Flamefire  
> Created at: 2023-09-11 10:51:30 UTC  
> Updated at: 2023-09-14 06:35:30 UTC  
> Merged at: 2023-09-11 14:16:53 UTC  
> Closed at: 2023-09-11 14:16:53 UTC  
> Url: https://github.com/boostorg/atomic/pull/63  

`static_assert` is used (e.g. in `detail/core_operations_emulated.hpp:183`) but not declared in the Jamfile requirements causing dependencies to fail when compiled in C++03 mode

---

## Comment 1

> Username: Lastique  
> Created_at: 2023-09-11 14:17:00 UTC  
> Url: https://github.com/boostorg/atomic/pull/63#issuecomment-1713982262  

Thanks.

---

## Comment 2

> Username: pdimov  
> Created_at: 2023-09-14 02:11:45 UTC  
> Url: https://github.com/boostorg/atomic/pull/63#issuecomment-1718643622  

I don't think this is a good idea. With it, under macOS by default, now silently nothing builds and no tests run, but everything succeeds. E.g. https://github.com/boostorg/auto_index/issues/4.

---

## Comment 3

> Username: Flamefire  
> Created_at: 2023-09-14 06:35:29 UTC  
> Url: https://github.com/boostorg/atomic/pull/63#issuecomment-1718844852  

This pattern is used already in multiple other libraries such as context, fiber, math, stacktrace and wave and as far as I understood the whole point of the `requires` rule: Don't build if we know it will error to allow building either a single lib with `cxxstd=98,11,14` or all of Boost with any standard skipping libraries and its dependents that cannot be build while allowing the build as a whole to succeed.  
  
I.e.: Exclude C++11 libraries from C++03 builds  
  
Without this dependent libraries will need to know the requirements of its dependencies and update their CI/build files when the dependencies change which IMO is worse.

---
