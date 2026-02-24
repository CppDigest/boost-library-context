# #778 Fixes/workarounds for clang 3.5-3.9 [Merged]

> Username: awulkiew  
> Created at: 2020-11-24 22:56:02 UTC  
> Updated at: 2020-11-25 16:18:52 UTC  
> Merged at: 2020-11-25 13:38:07 UTC  
> Closed at: 2020-11-25 13:38:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/778  

This PR adds fixes/workarounds for two problems that cause compilation failures with clang 3.5-3.9:  
1. 3.5 - Class template partial specialization cannot be deduced for `boost::variant` and parameter pack. The solution to that is to use `BOOST_VARIANT_ENUM_PARAMS` in specializations. The list of types can be analyzed with variadic templates afterwards.  
2. 3.5-3.9 - The instantiation of `std::is_constructible` for 2d `model::point` and 3 parameters causes the compilation error related to the initialization of 2-element array with too many elements. The solution to that is to enable the constructor only if the number of arguments is lesser or equal to `Dimension`.   
  
See the [regression matrix](https://www.boost.org/development/tests/develop/developer/geometry.html) for details.  
  
Note that the error 2 was caused partially by this change to `cross_product`: https://github.com/boostorg/geometry/pull/745  
in particular by specific `std::is_constructible` checks for 3 parameters. I think they are incorrect themselves and should be changed. There is a PR for that already: https://github.com/boostorg/geometry/pull/747

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2020-11-25 10:31:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/778#pullrequestreview-538357361  

Looks OK to me.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2020-11-25 14:39:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/778#issuecomment-733746328  

>   
>   
> Looks OK to me.  
  
Looks OK to me as well - pity that it's necessary, but fine.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-11-25 16:18:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/778#issuecomment-733807082  

@barendgehrels Thanks! It isn't entirely necessary but it's an easy fix and allows to compile BG with more compilers.  
What do you think about this related fix: https://github.com/boostorg/geometry/pull/747 ?

---
