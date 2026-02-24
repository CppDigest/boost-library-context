# #133 Fix C++20 incompatibility from using exception specifications [Merged]

> Username: Lastique  
> Created at: 2024-04-07 10:40:45 UTC  
> Updated at: 2024-04-07 21:25:02 UTC  
> Merged at: 2024-04-07 17:34:35 UTC  
> Closed at: 2024-04-07 17:34:35 UTC  
> Url: https://github.com/boostorg/program_options/pull/133  

C++20 removed support for exception specifications, so at least clang-19 is now emitting errors on them. Replaced exception specifications with `BOOST_NOEXCEPT_OR_NOTHROW`, which converts `throw()` to `noexcept`, but keeps the code formally compatible with C++03.  
  
This has been brought up during Boost 1.85.0 release preparation.  
  
This PR is similar to the following PRs, and once this one or one of those is merged, the others can be closed:  
  
https://github.com/boostorg/program_options/pull/118 - this PR is similar to that one, but resolves merge conflicts.  
https://github.com/boostorg/program_options/pull/129 - unlike that PR, this one does not remove exception specifications from destructors, which keeps the code formally compatible with C++03.  
  
This PR also updates GitHub Actions config to fix CI failures on clang-13 through 15 due to their incompatibility with libstdc++-13 in C++23 mode. (The failures are unrelated to this PR.)

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-04-07 10:42:28 UTC  
> Url: https://github.com/boostorg/program_options/pull/133#issuecomment-2041420924  

@pdimov Please, take a look.

---

## Comment 2

> Username: Lastique  
> Created_at: 2024-04-07 11:15:06 UTC  
> Url: https://github.com/boostorg/program_options/pull/133#issuecomment-2041433621  

Actually, `throw()` was removed even in C++20, so I've updated the description of the PR.

---

## Comment 3

> Username: Lastique  
> Created_at: 2024-04-07 21:25:01 UTC  
> Url: https://github.com/boostorg/program_options/pull/133#issuecomment-2041613977  

Thanks.

---
