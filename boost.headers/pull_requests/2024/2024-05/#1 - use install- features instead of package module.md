# #1 use install- features instead of package module [Merged]

> Username: grisumbras  
> Created at: 2024-05-23 14:14:02 UTC  
> Updated at: 2024-06-14 14:32:49 UTC  
> Merged at: 2024-06-14 14:32:49 UTC  
> Closed at: 2024-06-14 14:32:49 UTC  
> Url: https://github.com/boostorg/headers/pull/1  

Together with boostorg/boost_install#70 and bfgroup/b2#392 allows property-set-based install prefixes (in other words, installing into a prefix determined by the property set) and proper staging (aka DESTDIR). bfgroup/b2#392 needs to be merged first, so that options like. `--includedir` continue to work.

---
