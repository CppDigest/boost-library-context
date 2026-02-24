# #64 Implement support for n-ary metafunctions in mp_compose [Merged]

> Username: grisumbras  
> Created at: 2021-07-26 18:13:35 UTC  
> Updated at: 2021-08-21 12:37:58 UTC  
> Merged at: 2021-08-21 12:37:58 UTC  
> Closed at: 2021-08-21 12:37:58 UTC  
> Url: https://github.com/boostorg/mp11/pull/64  

This makes `mp_compose<F, G>::fn<T...>` be equivalent to `G<F<T...>>`

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-07-26 18:29:23 UTC  
> Url: https://github.com/boostorg/mp11/pull/64#issuecomment-886930643  

When refactoring, please keep the comment, like this: https://github.com/boostorg/mp11/blob/9d43d1f69660617266c9168e6e121ab2b0ea2287/include/boost/mp11/algorithm.hpp#L528-L529

---

## Comment 2

> Username: grisumbras  
> Created_at: 2021-07-27 06:25:11 UTC  
> Url: https://github.com/boostorg/mp11/pull/64#issuecomment-887246010  

CI error seems to be stdlibc++-10  not playing well with clang-8 with -std=c++2a.

---
