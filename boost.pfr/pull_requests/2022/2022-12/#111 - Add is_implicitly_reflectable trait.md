# #111 Add is_implicitly_reflectable trait [Merged]

> Username: denzor200  
> Created at: 2022-12-01 08:12:44 UTC  
> Updated at: 2022-12-18 16:11:27 UTC  
> Merged at: 2022-12-18 16:11:27 UTC  
> Closed at: 2022-12-18 16:11:27 UTC  
> Url: https://github.com/boostorg/pfr/pull/111  

Boost Fusion extension sample: https://godbolt.org/z/EsPv4T9s8  
Boost Spirit with Fusions extension sample: https://godbolt.org/z/Wva4x7xWY

---

## Comment 1

> Username: coveralls  
> Created_at: 2022-12-01 11:41:56 UTC  
> Updated_at: 2022-12-16 19:49:41 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#issuecomment-1333637990  

## Pull Request Test Coverage Report for [Build 3716023067](https://coveralls.io/builds/55241914)  
  
* **0** of **0**   changed or added relevant lines in **0** files are covered.  
* No unchanged relevant lines lost coverage.  
* Overall coverage remained the same at **100.0%**  
  
---  
  
  
  
|  Totals | [![Coverage Status](https://coveralls.io/builds/55241914/badge)](https://coveralls.io/builds/55241914) |  
| :-- | --: |  
| Change from base [Build 3117643244](https://coveralls.io/builds/52730529): |  0.0% |  
| Covered Lines: | 337 |  
| Relevant Lines: | 337 |  
  
---  
##### 💛  - [Coveralls](https://coveralls.io)

---

## Review 2 [Commented]

> Username: apolukhin  
> Created_at: 2022-12-16 06:47:02 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/111#pullrequestreview-1220335577  

Looks very good. Added a few nitpicks

📁 doc/pfr.qbk

```diff
 469 |     [[*BOOST_PFR_USE_STD_MAKE_INTEGRAL_SEQUENCE*] [Define to `0` if you are hit by the template instantiation depth issues with `std::make_integer_sequence` and wish to use Boost.PFR version of that metafunction. Define to `1` to override Boost.PFR detection logic. ]]
 470 |     [[*BOOST_PFR_HAS_GUARANTEED_COPY_ELISION*] [Define to `0` if your compiler does not implement C++17 guaranteed copy elision properly and fails to reflect aggregates with non-movable fields. Define to `1` to override Boost.PFR detection logic. ]]
 471 |+     [[*BOOST_PFR_ENABLE_IMPLICITLY_REFLECTION*] [Define to `0` if you are hit by lots of non-effective choices made by implicitly reflection. Define to `1` to override Boost.PFR detection logic. ]]
```

> Username: apolukhin  
> Created_at: 2022-12-16 06:26:08 UTC  
> Updated_at: 2022-12-16 06:47:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050408346  

Probably `BOOST_PFR_ENABLE_IMPLICIT_REFLECTION` is the beteer name


📁 include/boost/pfr/traits.hpp

```diff
  21 |+ 
  22 |+ /// Has a static const member variable `value` when it known that type T can or can't be reflected using Boost.PFR; otherwise, there is no member variable.
  23 |+ /// Every user CAN(and in some difficult cases - SHOULD) specialize is_reflectable on his own.
```

> Username: apolukhin  
> Created_at: 2022-12-16 06:29:11 UTC  
> Updated_at: 2022-12-16 06:47:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050409873  

Do not use caps and prefer using `may` instead of `can` (ISO terminology)

---

📁 include/boost/pfr/traits.hpp

```diff
  34 |+ ///
  35 |+ /// \customadaptor using is_reflectable trait and so others.
  36 |+ template<class T, class WhatFor>
```

> Username: apolukhin  
> Created_at: 2022-12-16 06:37:10 UTC  
> Updated_at: 2022-12-16 06:47:02 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050414191  

Does it make sense to add the default for `WhatFor`. For example `class WhatFor=All`?

> Username: denzor200  
> Created_at: 2022-12-16 18:50:16 UTC  
> Updated_at: 2022-12-16 18:50:17 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1051059339  

Perhaps, it makes sense, for example instead this:  
`template<class All> struct is_reflectable<A, All> : std::false_type {};`  
it makes possible to write like this:  
`template<> struct is_reflectable<A> : std::false_type {};`  
  
Also, it reduces amount of user-side questions, because it allows to write a more simple example during onboarding, like this: `boost::pfr::is_reflectable<A>::value` instead of this: `boost::pfr::is_reflectable<A, boost::pfr::boost_fusion_tag>::value`

> Username: denzor200  
> Created_at: 2022-12-16 18:55:33 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1051063470  

It's pretty difficult to implement, it requires to make `is_reflectable` non-specializable and to implement some specific logic to detect that "a T was specialized with specific `boost::pfr::All` tag".  
We decided in PM that game is not worth the candle.


---

## Review 3 [Commented]

> Username: denzor200  
> Created_at: 2022-12-16 16:23:34 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/pfr/pull/111#pullrequestreview-1221100717  

📁 include/boost/pfr/traits.hpp

```diff
  49 |+ #if BOOST_PFR_ENABLE_IMPLICITLY_REFLECTION
  50 |+ /// Checks the input type for the potential to be reflected.
  51 |+ /// Use is_reflectable specializations if you are disagree with is_implicitly_reflectable's default decision.
```

> Username: denzor200  
> Created_at: 2022-12-16 16:11:14 UTC  
> Updated_at: 2022-12-16 16:23:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050922098  

Specialize is_reflectable if you are disagree with is_implicitly_reflectable's default decision.

---

📁 include/boost/pfr/traits.hpp

```diff
  66 |+ #if BOOST_PFR_ENABLE_IMPLICITLY_REFLECTION
  67 |+ /// Checks the input type for the potential to be reflected.
  68 |+ /// Use is_reflectable specializations if you are disagree with is_implicitly_reflectable_v's default decision.
```

> Username: denzor200  
> Created_at: 2022-12-16 16:13:14 UTC  
> Updated_at: 2022-12-16 16:23:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050923922  

Specialize is_reflectable if you are disagree with is_implicitly_reflectable_v's default decision.

---

📁 include/boost/pfr/traits.hpp

```diff
  62 |+ /// \endcode
  63 |+ template<class T, class WhatFor>
  64 |+ constexpr bool is_reflectable_v = is_reflectable<T, WhatFor>::value;
```

> Username: denzor200  
> Created_at: 2022-12-16 16:19:33 UTC  
> Updated_at: 2022-12-16 16:23:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050929422  

Remove `is_reflectable_v` because it can't be used in sfinae context.  
`is_reflectable` strictly intended to be used with sfinae, and `is_reflectable_v` devalues the main sense of `is_reflectable`.

---

📁 include/boost/pfr/traits.hpp

```diff
  33 |+ ///       because is_reflectable is more sharp than is_implicitly_reflectable
  34 |+ ///
  35 |+ /// \customadaptor using is_reflectable trait and so others.
```

> Username: denzor200  
> Created_at: 2022-12-16 16:23:04 UTC  
> Updated_at: 2022-12-16 16:23:34 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1050932501  

What is "customadaptor" ?

> Username: denzor200  
> Created_at: 2022-12-16 19:05:47 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1051070912  

It's a link for "Custom Boost.PFR integration into other library" from https://github.com/boostorg/pfr/pull/107.  
This article contains sample for integrating Boost.PFR into Boost.Json.  
We can't write that sample in this PR because to integrate PFR as fallback we have to apply patch on Boost.Json's internals, also that sample have no sense.

> Username: denzor200  
> Created_at: 2022-12-16 19:06:05 UTC  
> Url: https://github.com/boostorg/pfr/pull/111#discussion_r1051071101  

This line should be removed from the PR


---
