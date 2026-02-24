# #191 Initial support for the C++20 modules [Open]

> Username: apolukhin  
> Created at: 2025-04-03 17:35:29 UTC  
> Updated at: 2025-04-07 15:10:20 UTC  
> Url: https://github.com/boostorg/core/pull/191  

The PR follows an experimental modules approach from Boost.PFR https://github.com/boostorg/pfr/commit/60391652fa92b7a237583f00c863caa78ab8a0f1  
  
The approach is an experiment. Any comments, suggestions are welcome!

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2025-04-03 20:50:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/191#pullrequestreview-2741138919  

📁 include/boost/core/detail/is_same.hpp

```diff
  25 | {
  26 | 
  27 |+ BOOST_CORE_BEGIN_MODULE_EXPORT
```

> Username: Lastique  
> Created_at: 2025-04-03 20:48:45 UTC  
> Updated_at: 2025-04-03 20:50:16 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2027721723  

I don't think this should be exported. `core::detail::is_same` is an implementation detail and should not be used by users.

> Username: apolukhin  
> Created_at: 2025-04-05 14:12:52 UTC  
> Updated_at: 2025-04-05 14:13:23 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029880840  

I also do not think that this should be exported.  
  
However clang complains on exporting `namespce core {  using boost::core::detail::is_same; } ` without exporting this detail, so there's actually no choice if boost::core::is_same  is expprted

> Username: Lastique  
> Created_at: 2025-04-05 14:29:06 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029885897  

That `using`-declaration is for backward compatibility and deprecated, it should not be exported either but rather removed.


📁 module/core.cppm

```diff
  62 |+ #include <type_traits>
  63 |+ #include <utility>
  64 |+ #include <wchar.h>
```

> Username: Lastique  
> Created_at: 2025-04-03 20:49:41 UTC  
> Updated_at: 2025-04-03 20:50:16 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2027722667  

Why are all these includes needed? Everything needed by Boost.Core should be included by Boost.Core headers.

> Username: apolukhin  
> Created_at: 2025-04-05 14:23:41 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029883401  

That is true for headers. But when a module is created, we need to place all the standard library and OS stuff into a global module fragment to avoid same symbols being ambiguously exported from different modules of Boost.  
  
To do so I put all the headers right after the `module;`, so they end up in global module fragment. Later, after the `export module Boost.Core; ` I include all the library heders. Include guards of the standard library and OS stuff do they work, so the library headers do not actually include them again. As a result, only the library classes, functions and aliases apear after `export module Boost.Core; `, so the module exports only the library stuff

> Username: Lastique  
> Created_at: 2025-04-05 14:31:39 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029886529  

If supporting modules requires duplicating includes from all headers then the whole modules undertaking is a non-starter. This is unmaintainable. If we're to support modules, this should not be required.

> Username: apolukhin  
> Created_at: 2025-04-05 18:40:02 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029943224  

Now I also have a feeling that I'm doing something wrong. I'll try another approach soon

---

📁 module/core.cppm

```diff
 142 |+ }
 143 |+ #else
 144 |+ #include <boost/visit_each.hpp>
```

> Username: Lastique  
> Created_at: 2025-04-03 20:50:07 UTC  
> Updated_at: 2025-04-03 20:50:16 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2027723086  

Why the duplicate list of includes?


---

## Review 2 [Commented]

> Username: apolukhin  
> Created_at: 2025-04-05 14:26:54 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/core/pull/191#pullrequestreview-2744911122  

📁 module/core.cppm

```diff
  67 |+ export module Boost.Core;
  68 |+ 
  69 |+ #ifdef BOOST_CORE_HAS_STD_MODULE
```

> Username: apolukhin  
> Created_at: 2025-04-05 14:26:54 UTC  
> Updated_at: 2025-04-05 14:27:03 UTC  
> Url: https://github.com/boostorg/core/pull/191#discussion_r2029885355  

This macro should be changed to some Boost.Config macro that is defined if the compiler understands modules and there is module std. The includes in the headers should be guarded by that macro and use `import std; ` if it is available


---

## Comment 3

> Username: pdimov  
> Created_at: 2025-04-05 18:54:56 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2781042391  

@anarthal   
  
https://anarthal.github.io/cppblog/modules  
https://anarthal.github.io/cppblog/modules2  
https://anarthal.github.io/cppblog/modules3

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-04-05 20:22:47 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2781081760  

?

---

## Comment 5

> Username: anarthal  
> Created_at: 2025-04-05 22:44:07 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2781120855  

Oh, I misread this. @apolukhin you might have a look at the articles pointed out by Peter and the discussion we had on the ML. I will have a look at the PR on Monday.

---

## Comment 6

> Username: apolukhin  
> Created_at: 2025-04-06 07:19:28 UTC  
> Updated_at: 2025-04-06 07:24:14 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2781280593  

@anarthal yes, I saw the links before and followed the approach from the links.  
  
@Lastique noted that there's something wrong with such approach. Including all the standard headers before the `export module` seems to be an ad-hoc solution that scales poorly.  
  
I'm planing to do thefollowin experiment with module partitions: In each header a new module partition is declared and in PMI all of them are exported

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-04-06 12:44:50 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2781405657  

> yes, I saw the links before and followed the approach from the links.  
  
You also need to read the third post, which describes the approach we ultimately settled on.

---

## Comment 8

> Username: anarthal  
> Created_at: 2025-04-07 10:03:13 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2782782767  

If we want to support dual builds, I strongly recommend being able to run the library's test suite under modular builds. Otherwise, we're just shipping untested code, which I don't think we should do.  
  
See how the two PRs mentioned in the articles have full test suites and CI builds:  
  
https://github.com/boostorg/mp11/pull/104  
https://github.com/boostorg/charconv/pull/255

---

## Comment 9

> Username: apolukhin  
> Created_at: 2025-04-07 10:59:23 UTC  
> Updated_at: 2025-04-07 11:23:12 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2782927337  

> You also need to read the third post, which describes the approach we ultimately settled on.  
  
Oops, I was sure that saw it but now I realize the missed it. Thanks!  
I see that the settled approach with `export namespace` is quite close to the libstdc++ approach for `std` [module](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/src/c%2B%2B23/std.cc.in)  
  
@pdimov @anarthal I'll concentrate on  C++20 modularization of libraries I maintain. Please, confirm that I understood the core principles correctly:  
* module should be in `modules/` directory  
* includes should transparently for the user change into `import` depending on `BOOST_USE_MODULES` macro  
* module should place all the library entities into a global module fragment and use `export namespace`.   
* module build requires usable module `std`  
* all the tests should also run on the library module  
* module name is lowercased (for example `boost.core`)

---

## Comment 10

> Username: anarthal  
> Created_at: 2025-04-07 15:10:18 UTC  
> Url: https://github.com/boostorg/core/pull/191#issuecomment-2783677013  

I'd say so, yes. Also, remember to add the relevant CMake and CI code. I'm happy to review the PRs for your libraries if it helps.

---
