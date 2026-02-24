# #13 Suppress deprecated warning in C++11 mode GCC. [Closed]

> Username: Flast  
> Created at: 2015-07-15 02:35:44 UTC  
> Updated at: 2015-07-21 01:08:53 UTC  
> Closed at: 2015-07-17 18:43:27 UTC  
> Url: https://github.com/boostorg/core/pull/13  

This PR suppresses deprecated warning on C++11 mode GCC: [see regression test output](http://www.boost.org/development/tests/develop/output/Flast-FreeBSD10-gcc-5-1-0~gnu++11-fusion-gcc-5-1-0-warnings.html).

---

## Comment 1

> Username: Lastique  
> Created_at: 2015-07-15 08:27:58 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121527708  

I believe, `#pragma GCC diagnostic push` has been supported only since gcc 4.6 while C++11 has been supported since 4.4.

---

## Comment 2

> Username: pdimov  
> Created_at: 2015-07-15 09:33:29 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121549196  

The way we were going to handle auto_ptr going away was to define BOOST_NO_AUTO_PTR, and this declaration is already guarded.

---

## Comment 3

> Username: Lastique  
> Created_at: 2015-07-15 09:48:33 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121558923  

I think the intention of this PR is to silence the warnings while auto_ptr is still available. BOOST_NO_AUTO_PTR is for the case when auto_ptr is removed.

---

## Comment 4

> Username: Flast  
> Created_at: 2015-07-15 09:48:52 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121558981  

> I believe, #pragma GCC diagnostic push has been supported only since gcc 4.6 while C++11 has been supported since 4.4.  
  
Good catch!  
  
> The way we were going to handle auto_ptr going away was to define BOOST_NO_AUTO_PTR,  
  
`BOOST_NO_AUTO_PTR` is categorized into 'C++03 defects' in [Boost.Config docs](http://www.boost.org/doc/libs/1_58_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_describe_c__03_defects). I'm not sure it is right way to describe which is deprecated.

---

## Comment 5

> Username: pdimov  
> Created_at: 2015-07-15 13:08:42 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121609734  

`BOOST_NO_AUTO_PTR` is the macro we set when there's no `auto_ptr` at all. In the past, this was because the standard library didn't have `auto_ptr` yet; in the future, this'll be because the standard library no longer has it. But Andrey is right that it doesn't describe the case when `auto_ptr` exists, but its use causes deprecation warnings to be issued.

---

## Comment 6

> Username: pdimov  
> Created_at: 2015-07-15 13:21:36 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121614033  

The correct condition is tricky, as the compiler may be emulating `g++` and using its standard library. Perhaps `#if defined(__GNUC__) && (__GNUC__ * 100 + __GNUC_MINOR__ >= 406)`?

---

## Comment 7

> Username: Lastique  
> Created_at: 2015-07-15 13:40:35 UTC  
> Updated_at: 2015-07-15 13:59:37 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121620395  

On Wed, Jul 15, 2015 at 4:21 PM, Peter Dimov notifications@github.com wrote:  
  
> The correct condition is tricky, as the compiler may be emulating g++ and using its standard library. Perhaps #if defined(**GNUC**) && (**GNUC** \* 100 + **GNUC_MINOR** >= 406)?  
  
I may be mistaken, but I think Intel doesn't support gcc pragmas, at  
least the warning-related ones. I cannot verify this as I don't have  
it available. Also, that condition probably doesn't cover clang as it  
pretends to be gcc 4.2, AFAIR.  
  
Maybe we're better off with something like this:  
  
```  
#if ((defined(BOOST_GCC) && (BOOST_GCC / 100) >= 406) || defined(__clang__)) \  
    && (defined(__GXX_EXPERIMENTAL_CXX0X__) || (__cplusplus >= 201103L))  
```  
  
Note: I also don't know if clang supports this particular warning  
option. Needs to be tested.

---

## Comment 8

> Username: Lastique  
> Created_at: 2015-07-15 13:48:02 UTC  
> Updated_at: 2015-07-15 13:48:22 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121621972  

As an alternative idea: why not just include <memory> on C++11 compilers and not define `get_pointer` in namespace boost for standard pointers? By the way, what is the rationale for having `get_pointer` for `std::unique_ptr` and `std::shared_ptr` in namespace boost?

---

## Comment 9

> Username: Lastique  
> Created_at: 2015-07-15 13:54:45 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121623392  

Sorry, disregard my last comment. I was under impression that the standard defined `get_pointer` overloads for `std::mem_fn`.

---

## Comment 10

> Username: pdimov  
> Created_at: 2015-07-15 14:35:07 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121635313  

Ideally we need `BOOST_HAS_PRAGMA_GCC_DIAGNOSTIC`, but for now, `#if defined(BOOST_GCC) && (BOOST_GCC >= 40600)` seems best. We'll deal with Clang (or Intel) later if someone complains.

---

## Comment 11

> Username: Flast  
> Created_at: 2015-07-16 04:23:47 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-121825471  

> but for now, `#if defined(BOOST_GCC) && (BOOST_GCC >= 40600)` seems best.  
  
Should it be `#if defined(BOOST_GCC_CXX11) && (BOOST_GCC >= 40600)` ?

---

## Comment 12

> Username: Lastique  
> Created_at: 2015-07-17 18:43:27 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-122370538  

I've committed https://github.com/boostorg/core/commit/c641e8e5447f936a2804f72b0a03a134f53773ab and https://github.com/boostorg/core/commit/5e8b9152be6166dc4de779beaf795cbdcec5d384 which should silence the warnings for gcc and clang.

---

## Comment 13

> Username: Flast  
> Created_at: 2015-07-21 01:08:51 UTC  
> Url: https://github.com/boostorg/core/pull/13#issuecomment-123118756  

Thank you, Andrey!

---
