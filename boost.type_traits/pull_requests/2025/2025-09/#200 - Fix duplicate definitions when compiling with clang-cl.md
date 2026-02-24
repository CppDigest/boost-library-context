# #200 Fix duplicate definitions when compiling with clang-cl [Open]

> Username: tobiasleibner  
> Created at: 2025-09-15 12:04:10 UTC  
> Updated at: 2025-09-15 12:06:30 UTC  
> Url: https://github.com/boostorg/type_traits/pull/200  

[`clang-cl`](https://clang.llvm.org/docs/UsersManual.html#clang-cl) defines both `__clang__` and `_MSC_VER` (which is probably why `BOOST_MSVC` evaluates to `true` here).

---
