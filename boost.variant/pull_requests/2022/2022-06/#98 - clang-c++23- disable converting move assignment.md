# #98 clang/c++23: disable converting move assignment [Merged]

> Username: ecatmur  
> Created at: 2022-06-29 11:36:31 UTC  
> Updated at: 2022-09-06 19:55:57 UTC  
> Merged at: 2022-08-31 18:38:27 UTC  
> Closed at: 2022-08-31 18:38:27 UTC  
> Url: https://github.com/boostorg/variant/pull/98  

Workaround https://github.com/boostorg/variant/issues/97

---

## Comment 1

> Username: ecatmur  
> Created_at: 2022-06-29 11:42:36 UTC  
> Url: https://github.com/boostorg/variant/pull/98#issuecomment-1169879014  

this shouldn't break anything (uh...), since code can just go via `operator=(variant&&)`, so just a very small ding to performance

---

## Comment 2

> Username: coveralls  
> Created_at: 2022-06-29 12:33:29 UTC  
> Url: https://github.com/boostorg/variant/pull/98#issuecomment-1169924455  

[![Coverage Status](https://coveralls.io/builds/50461101/badge)](https://coveralls.io/builds/50461101)  
  
Coverage remained the same at 94.968% when pulling **70ff3706c5dc79510c986a6dae59378fef677049 on ecatmur:patch-1** into **d2fdf2384b146b0c764077f5539f5f6ed7c40f20 on boostorg:develop**.

---

## Comment 3

> Username: apolukhin  
> Created_at: 2022-08-31 18:38:38 UTC  
> Url: https://github.com/boostorg/variant/pull/98#issuecomment-1233284678  

Many thanks for the PR!

---
