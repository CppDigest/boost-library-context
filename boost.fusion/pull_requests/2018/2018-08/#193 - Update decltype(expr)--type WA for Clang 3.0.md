# #193 Update decltype(expr)::type WA for Clang 3.0 [Merged]

> Username: Kojoley  
> Created at: 2018-08-16 22:38:55 UTC  
> Updated at: 2018-09-27 00:36:19 UTC  
> Merged at: 2018-09-27 00:34:44 UTC  
> Closed at: 2018-09-27 00:34:44 UTC  
> Url: https://github.com/boostorg/fusion/pull/193  

Did not check myself. Versions below 3.0 I think do not matter. Problem found on https://www.boost.org/development/tests/develop/developer/output/teeks99-02-dc3-0-opt-Docker-64on64-boost-bin-v2-libs-spirit-test-lex-lex_token_moretypes-test-clang-linux-3-0~c++11~O2-debug.html

---

## Comment 1

> Username: Flast  
> Created_at: 2018-08-17 00:44:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/193#issuecomment-413724992  

From [clang documantation](http://releases.llvm.org/6.0.0/tools/clang/docs/LanguageExtensions.html#builtin-macros), those macros are not recommended for detecting compiler version (lol). AFAIK, no compiler except LLVM clang uses version number 3.0 but it is better detecting version via feature detecting macros if possible.

---

## Comment 2

> Username: Kojoley  
> Created_at: 2018-08-17 00:47:08 UTC  
> Url: https://github.com/boostorg/fusion/pull/193#issuecomment-413725370  

I tried to find a future/extension for this but failed to found suitable.

---

## Comment 3

> Username: Flast  
> Created_at: 2018-08-17 00:56:29 UTC  
> Url: https://github.com/boostorg/fusion/pull/193#issuecomment-413726535  

fair enough.

---

## Comment 4

> Username: Flast  
> Created_at: 2018-09-27 00:34:32 UTC  
> Updated_at: 2018-09-27 00:34:41 UTC  
> Url: https://github.com/boostorg/fusion/pull/193#issuecomment-424915884  

Oops, I totally forgot this...

---
