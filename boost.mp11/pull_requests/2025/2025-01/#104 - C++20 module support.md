# #104 C++20 module support [Closed]

> Username: anarthal  
> Created at: 2025-01-11 18:20:36 UTC  
> Updated at: 2025-09-10 17:24:09 UTC  
> Closed at: 2025-09-10 17:24:09 UTC  
> Url: https://github.com/boostorg/mp11/pull/104  

Adds support for `import boost.mp11`.  
This pull request is intended to get feedback and support discussion in the ML. It depends on changes in Boost.CMake, Boost.Core, Boost.ThrowException and Boost.Assert that aren't in develop.  
Any feedback is welcome.

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-01-15 16:49:45 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2593435137  

Looks nicer.  
  
Why do we have to include mp11.hpp in the GMF though? Can't we attach things to the named module?

---

## Comment 2

> Username: anarthal  
> Created_at: 2025-01-15 22:31:36 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2594071350  

For mp11 in concrete, yes, we can. It would require removing the cassert include and placing it in the GMF. It likely requires reverting to the BOOST_MP11_EXPORT macros we used to have.  
  
For libraries that need to access entities in the detail namespace in tests (charconv being the one I've experimented with), this is not possible, since it causes redefinition errors for the tests that include detail headers.  
  
Is there a benefit to having names attached to the named module? (Genuine question that I don't know the answer to).

---

## Comment 3

> Username: pdimov  
> Created_at: 2025-01-16 00:40:32 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2594218980  

I don't understand why we'd need `<cassert>` in the GMF, and I also don't understand why we'd need to revert to the export macros. Are we aiming to support configurations where BOOST_USE_MODULES is inconsistently defined in TUs? Should we?  
  
IIUC the compiler needs to perform less work for names in the named module, because ODR is guaranteed and there's no need to merge declarations.

---

## Comment 4

> Username: anarthal  
> Created_at: 2025-01-16 10:41:49 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2595174175  

> I don't understand why we'd need <cassert> in the GMF  
  
If we do:  
  
```cpp  
export module boost.mp11;  
#define BOOST_MP11_INTERFACE_UNIT  
#include <boost/mp11.hpp> // Has an #include <cassert>  
  
export namespace boost::mp11 { /* ... */ }  
```  
  
Any implementation-defined C++ entity declared in `cassert` get incorrectly attached to the `boost.mp11`. If we used the new `boost/config/std/cassert.hpp` header that just imports std, we don't get the `assert` macro.  
  
Now, the above actually fails because apparently, in a module unit, imports are only allowed immediately after the module declaration:  
  
```  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/modules/boost_mp11.cppm:9:  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/include/boost/mp11.hpp:21:  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/include/boost/mp11/tuple.hpp:24:  
[build] [redacted]/boost-with-modules/libs/mp11/include/boost/mp11/detail/std/type_traits.hpp:2:1: error: imports must immediately follow the module declaration  
[build]     2 | import std;  
[build]       | ^  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/modules/boost_mp11.cppm:9:  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/include/boost/mp11.hpp:21:  
[build] In file included from [redacted]/boost-with-modules/libs/mp11/include/boost/mp11/tuple.hpp:25:  
[build] [redacted]/boost-with-modules/libs/mp11/include/boost/mp11/detail/std/cstddef.hpp:2:1: error: imports must immediately follow the module declaration  
[build]     2 | import std;  
```  
  
We could work this around by making `boost/config/std/$HEADER.hpp` do nothing for module units (e.g. if `BOOST_CONFIG_MODULE_UNIT` is defined or something like this).  
  
> I also don't understand why we'd need to revert to the export macros.  
  
I thought MSVC didn't like export using with names attached to a module. Let me double-check this.  
  
> Are we aiming to support configurations where BOOST_USE_MODULES is inconsistently defined in TUs? Should we?  
  
No, and I don't think so.

---

## Comment 5

> Username: anarthal  
> Created_at: 2025-01-16 10:51:30 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2595196310  

I was right, entities attached to modules can't be exported with export using. This is true even in clang:  
  
```  
[build] [redacted]/boost-with-modules/libs/mp11/modules/boost_mp11.cppm:16:13: error: using declaration referring to 'mp_transform' with module linkage cannot be exported  
[build]    16 | using mp11::mp_transform;  
```  
  
So if we get down this path, we need to revert to the BOOST_MP11_EXPORT macros.

---

## Comment 6

> Username: anarthal  
> Created_at: 2025-01-24 15:00:48 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2612739488  

What would you finally like to do with this? I see three options:  
  
1. Throw it away and forget about it  
2. Wait until modules are more mature - e.g. until MSVC fixes the two bugs I raised and CMake's import std becomes stable.  
3. Merge it for this or next release as experimental - I'd put some time in cleaning up CIs and writing docs.  
  
IMO 2 is the more reasonable. 3's only point would be pushing modules forward.

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-01-24 15:03:10 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2612745008  

(2), I think.

---

## Comment 8

> Username: msqr1  
> Created_at: 2025-02-26 07:09:46 UTC  
> Updated_at: 2025-02-26 07:10:42 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2684124735  

@anarthal Great idea, I also came up with mostly the same thing, but for a general purpose app that does this automatically, this may be able to help you: https://github.com/msqr1/importizer  
  
Look at transitional modularization

---

## Review 9 [Commented]

> Username: Lastique  
> Created_at: 2025-04-07 08:38:18 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/104#pullrequestreview-2745984477  

> Username: Lastique  
> Created_at: 2025-04-07 08:38:17 UTC  
> Updated_at: 2025-04-07 08:38:18 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#discussion_r2030739928  

Perhaps, this sort of files should be made common, in a separate internal Boost library.

> Username: anarthal  
> Created_at: 2025-04-07 09:45:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#discussion_r2030877604  

They are in my Boost.Config branch (https://github.com/anarthal/config/tree/feature/cxx20-modules), but MP11 is supposed to have zero Boost dependencies, so some of them have been copied here. The Boost.Charconv PR uses the ones in Boost.Config.

> Username: Lastique  
> Created_at: 2025-04-07 21:02:10 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#discussion_r2032004830  

I think, I have a better idea of how this could be implemented, that does not require creating headers like this. See [here](https://github.com/boostorg/charconv/pull/255#issuecomment-2784613210).


---

## Review 10 [Commented]

> Username: Lastique  
> Created_at: 2025-04-07 08:43:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/mp11/pull/104#pullrequestreview-2746000344  

📁 include/boost/mp11/algorithm.hpp

```diff
  12 |+ 
  13 |+ #include <boost/mp11/version.hpp>
  14 |+ import boost.mp11;
```

> Username: Lastique  
> Created_at: 2025-04-07 08:43:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#discussion_r2030749736  

Say, if another library includes multiple MP11 headers and thus produces multiple `import` statements. Would this be harmful in any way? For example, would this affect compilation performance? Would it make sense to move the above two lines in a separate internal header with its own include guard, so that only one `import` is produced, no matter how many interface headers are included?

> Username: anarthal  
> Created_at: 2025-04-07 09:46:34 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#discussion_r2030879591  

I don't think there is any performance penalty in importing several times.


---

## Comment 11

> Username: Lastique  
> Created_at: 2025-04-07 09:14:58 UTC  
> Updated_at: 2025-04-07 09:20:01 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2782628920  

Although not needed for MP11, what would be the strategy if the library needs a third-party include, for which there is no module? For example, `#include <unistd.h>` or `#include <windows.h>`? Note that some of these includes may depend on predefined macros, such as `_POSIX_SOURCE` or `_WIN32_WINNT`.

---

## Comment 12

> Username: anarthal  
> Created_at: 2025-04-07 10:00:02 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-2782773890  

> Although not needed for MP11, what would be the strategy if the library needs a third-party include, for which there is no module? For example, `#include <unistd.h>` or `#include <windows.h>`? Note that some of these includes may depend on predefined macros, such as `_POSIX_SOURCE` or `_WIN32_WINNT`.  
  
The canonical approach is including these in the global module fragment. Depending on where the include is, and the modularization technique we're using (see https://anarthal.github.io/cppblog/modules3 for the techniques - Boost.Mp11 uses technique 1, while Boost.Charconv uses technique 2):  
  
* If we're including the third-party header in a cpp file, we need to make sure that it's included before the `export module boost.xyz` statement. Should be relatively trivial.  
* If we're including the third-party header in a public header, and using modularization technique 1 (the cppm includes everything after `export module boost.xyz`), then you need to ifdef-out all third-party includes, then include them again in the global module fragment.  
* If we're using technique 2 (the cppm includes everything before the `export module boost.xyz`), then you leave the include as-is, and don't need to duplicate anything.  
  
Having a lot of third-party includes might be a good reason to go for 2. 1 is the ideal one because it's better for compile times and allows better ODR checks, but might be infeasible for reasons like this, or to support the test suite (as is charconv's case).  
  
Charconv PR: https://github.com/boostorg/charconv/pull/255

---

## Comment 13

> Username: anarthal  
> Created_at: 2025-09-10 17:24:09 UTC  
> Url: https://github.com/boostorg/mp11/pull/104#issuecomment-3275841496  

To avoid wasting CI resources on the boostorg account, I'll close this PR and reopen once everything works on my fork.

---
