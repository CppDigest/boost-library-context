# #24 Fixes for clang in MSVC mode [Merged]

> Username: MarcelRaad  
> Created at: 2016-01-13 16:31:25 UTC  
> Updated at: 2016-03-18 11:28:56 UTC  
> Merged at: 2016-03-18 10:11:53 UTC  
> Closed at: 2016-03-18 10:11:53 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24  

In MSVC mode, clang doesn't define `__GNUC__`.  
Also, it doesn't define `__clang`, but defines `__clang__`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2016-01-13 16:52:02 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24#issuecomment-171361616  

I'm happy to apply that, but just curious, doesn't the msvc-preprocessor path work in this case?

---

## Comment 2

> Username: MarcelRaad  
> Created_at: 2016-01-13 17:06:05 UTC  
> Updated_at: 2016-01-14 12:45:47 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24#issuecomment-171365547  

You mean using the BOOST_MSVC path in has_nothrow_assign.hpp? Yes, that would also work, but only is_volatile.hpp and is_assignable.hpp are required for clang, and using the GCC path is consistent with non-MSVC clang. The code in intrinsics.hpp is independent of MSVC or GCC mode.  
Or do you mean if BOOST_MSVC is defined for clang in MSVC mode? No, BOOST_CLANG is defined.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2016-03-16 01:44:03 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24#issuecomment-197104395  

I created a similar PR without looking at the list of PRs, sorry about that.  
  
FYI, on MSC+Clang `_MSC_VER`, `__clang__` and `BOOST_CLANG` are defined (of course the last one when Config is included). `BOOST_MSVC` is not defined. So instead of `__clang` or `__clang__` simply `BOOST_CLANG` could be checked since Boost.Config is included anyway.  
  
I think that in every place where `__GNUC__` is checked, `BOOST_CLANG` should be checked as well. Maybe Config could define some macro for GNUC-compatible compilers?  
  
TL;DR  
  
If intrinsics.hpp `BOOST_CLANG` is checked and the macros for Clang are enabled (see the errors below), macros for MSVC are disabled. So let's say `has_nothrow_constructor.hpp` is compiled. Macro `BOOST_HAS_NOTHROW_CONSTRUCTOR()` is expanded so the compiler tries to instantiate `is_default_constructible<>`. Since neither `BOOST_MSVC` nor `__GNUC__` are defined neither `has_trivial_constructor.hpp` nor `is_default_constructible.hpp` are included, AFAIU the latter is required. The result is identifier not found.  
  
E.g. when I try to compile some code using Boost.Geometry I get errors for following traits:  
  
```  
has_nothrow_constructor.hpp(26,84): error : no template named 'is_default_constructible'; did you mean 'std::is_default_constructible'?  
template <class T> struct has_nothrow_constructor : public integral_constant<bool, BOOST_HAS_NOTHROW_CONSTRUCTOR(T)>{};  
intrinsics.hpp(193,80) :  note: expanded from macro 'BOOST_HAS_NOTHROW_CONSTRUCTOR'  
#     define BOOST_HAS_NOTHROW_CONSTRUCTOR(T) (__has_nothrow_constructor(T) && is_default_constructible<T>::value)  
  
has_nothrow_assign.hpp(64,7): error : no template named 'is_assignable'; did you mean 'std::is_assignable'?  
BOOST_HAS_NOTHROW_ASSIGN(T)  
intrinsics.hpp(199,96) :  note: expanded from macro 'BOOST_HAS_NOTHROW_ASSIGN'  
#     define BOOST_HAS_NOTHROW_ASSIGN(T) (__has_nothrow_assign(T) && !is_volatile<T>::value && is_assignable<T&, const T&>::value)  
  
has_trivial_assign.hpp(28,7): error : no template named 'is_assignable'; did you mean 'std::is_assignable'?  
BOOST_HAS_TRIVIAL_ASSIGN(T)  
intrinsics.hpp(187,96) :  note: expanded from macro 'BOOST_HAS_TRIVIAL_ASSIGN'  
#     define BOOST_HAS_TRIVIAL_ASSIGN(T) (__has_trivial_assign(T) && !is_volatile<T>::value && is_assignable<T&, const T&>::value)  
```  
  
My PR contains only fixes for 2 first traits because after I fixed `has_nothrow_assign.hpp` `is_assignable.hpp` was included and therefore a fix in `has_trivial_assign.hpp` was not needed. So this PR is more complete.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2016-03-18 09:41:27 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24#issuecomment-198279526  

I've managed to get clang more or less working on Windows, so I'll check this out later.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2016-03-18 10:12:09 UTC  
> Url: https://github.com/boostorg/type_traits/pull/24#issuecomment-198290760  

Confirmed working OK, many thanks!

---
