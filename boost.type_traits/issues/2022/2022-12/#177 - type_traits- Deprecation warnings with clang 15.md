# #177 - type_traits: Deprecation warnings with clang 15 [Open]

> Username: cs96and  
> Created at: 2022-12-09 12:37:10 UTC  
> Updated at: 2022-12-10 13:46:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/177  

I am getting a lot of deprecation warnings when using Boost with Clang 15.  E.g.  
  
```  
boost/type_traits/has_nothrow_constructor.hpp:27:84: warning: builtin __has_nothrow_constructor is deprecated; use __is_nothrow_constructible instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_nothrow_assign.hpp:65:7: warning: builtin __has_nothrow_assign is deprecated; use __is_nothrow_assignable instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_nothrow_copy.hpp:36:89: warning: builtin __has_nothrow_copy is deprecated; use __is_nothrow_constructible instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_trivial_constructor.hpp:41:69: warning: builtin __has_trivial_constructor is deprecated; use __is_trivially_constructible instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_trivial_destructor.hpp:30:86: warning: builtin __has_trivial_destructor is deprecated; use __is_trivially_destructible instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_trivial_assign.hpp:29:7: warning: builtin __has_trivial_assign is deprecated; use __is_trivially_assignable instead [-Wdeprecated-builtins]  
  
boost/type_traits/has_trivial_copy.hpp:34:4: warning: builtin __has_trivial_copy is deprecated; use __is_trivially_copyable instead [-Wdeprecated-builtins]  
```  
The errors are all in boost/type_traits/intrinsics.hpp.  This is with boost 1.72.0, however I took a look at the 1.80.0 source code and it doesn't look like it has been fixed.  
  
See the Clang 15 documentation for more details...  
https://releases.llvm.org/15.0.0/tools/clang/docs/LanguageExtensions.html#type-trait-primitives

---

## Comment 1

> Username: glenfe  
> Created at: 2022-12-10 13:46:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/177#issuecomment-1345268484  

@cs96and It should be fixed in  the upcoming 1.81 release via https://github.com/boostorg/type_traits/commit/a1d0b207c523c13b4fa46fd9b77bdb15ce554051
