# #201 Use super project to build locale and its dependencies in cmake(-subdir)-test [Merged]

> Username: Flamefire  
> Created at: 2023-10-15 09:29:14 UTC  
> Updated at: 2023-10-15 16:08:12 UTC  
> Merged at: 2023-10-15 14:23:37 UTC  
> Closed at: 2023-10-15 14:23:37 UTC  
> Url: https://github.com/boostorg/locale/pull/201  

The transitive dependencies of Boost.Locale are many and might be changing. To avoid frequently required updates use the super-project with `BOOST_INCLUDE_LIBRARIES` to auto-select the required libs.

---
