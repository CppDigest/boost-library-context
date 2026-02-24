# #360 Fix clang-win name mangling [Merged]

> Username: pdimov  
> Created at: 2018-10-27 01:29:22 UTC  
> Updated at: 2021-10-02 21:00:03 UTC  
> Merged at: 2018-10-27 15:06:19 UTC  
> Closed at: 2018-10-27 15:06:19 UTC  
> Url: https://github.com/boostorg/build/pull/360  

It previously had no tag, so f.ex. Clang 6.0.1 just added "-60" to the mangled name instead of "-clangw6" as now.

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-10-27 02:22:42 UTC  
> Updated_at: 2018-10-27 02:30:17 UTC  
> Url: https://github.com/boostorg/build/pull/360#issuecomment-433583527  

Looks good to me. Although I do not know how the tag is used the PR distinguishes the tag for clang on Windows from clang on Linux or clang on Darwin.

---

## Comment 2

> Username: eldiener  
> Created_at: 2018-10-27 02:34:17 UTC  
> Url: https://github.com/boostorg/build/pull/360#issuecomment-433584199  

I realized I am a little worried about this as clang on Windows targeting mingw-64/gcc would have the same tag as clang on Windows targeting vc++. I do not know how the tag is used but those are two different compilers in my opinion.

---

## Comment 3

> Username: pdimov  
> Created_at: 2018-10-27 02:45:11 UTC  
> Url: https://github.com/boostorg/build/pull/360#issuecomment-433584753  

It doesn't; the mingw/Cygwin clang is clang-linux, and has tag `clang`, and clang-cl is clang-win, and has tag `clangw`.  
  
The tag is encoded into the library name, f.ex. `boost_atomic-clangw6-mt-d-x64-1_69.dll`.

---

## Comment 4

> Username: eldiener  
> Created_at: 2018-10-27 03:48:01 UTC  
> Url: https://github.com/boostorg/build/pull/360#issuecomment-433587801  

Thanks for the explanation of how the tag is used. The code looks fine.

---

## Comment 5

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:02 UTC  
> Url: https://github.com/boostorg/build/pull/360#issuecomment-932819733  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
