# #173 WIN32: fix boost build b2.exe for non-msvc compilers [Merged]

> Username: d5ch4k  
> Created at: 2024-04-30 21:51:30 UTC  
> Updated at: 2024-10-01 19:04:10 UTC  
> Merged at: 2024-09-30 17:47:14 UTC  
> Closed at: 2024-09-30 17:47:14 UTC  
> Url: https://github.com/boostorg/parser/pull/173  

propagate the selected compiler from cmake down to the boost bootstrap process  
without these correction the boost bootstrap build of b2 will fail on windows with e.g. clang  
for clang 'if(MSVC)' is false and the build process assumes a unix style shell and the build process fails  
I'm using Clion (Jetbrains) IDE for Multiplatform development on macOS, Linux and Windows, which makes it very easy to switch between different compilers and test Code compatibility

---

## Comment 1

> Username: tzlaine  
> Created_at: 2024-05-04 17:11:39 UTC  
> Url: https://github.com/boostorg/parser/pull/173#issuecomment-2094306184  

This seems like a reasonable change, but it breaks the build.

---

## Comment 2

> Username: codecov-commenter  
> Created_at: 2024-05-04 17:23:44 UTC  
> Url: https://github.com/boostorg/parser/pull/173#issuecomment-2094312897  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Zach+Laine) :tada:  
  
Once you merge this PR into your default branch, you're all set! Codecov will compare coverage reports and display results in all future pull requests.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---

## Comment 3

> Username: d5ch4k  
> Created_at: 2024-05-04 20:11:45 UTC  
> Url: https://github.com/boostorg/parser/pull/173#issuecomment-2094366143  

mmh, strange.  
I noticed, that the respective "Windows MSVC / build (23, windows-2022) (pull_request)" already failed on your last check-in (April 1) with exactly the same error (https://github.com/tzlaine/parser/actions/runs/8513670468/job/23317833028)  
and that fedora changed the GCC compiler from 13.0.2 (https://github.com/tzlaine/parser/actions/runs/8513670482/job/23317832249 , see configure section) to 14.0.1 (https://github.com/tzlaine/parser/actions/runs/8901839715/job/24588909706 , see configure section)  
I'll try to check if the baseline (i.e.  the develop branch with only whitespace changes in CmakeList.txt) has no errors.

---

## Comment 4

> Username: d5ch4k  
> Created_at: 2024-05-04 22:46:58 UTC  
> Url: https://github.com/boostorg/parser/pull/173#issuecomment-2094493212  

I think issue #169 breaks the build for the respective os-compiler-c++ variant (see #174)

---

## Comment 5

> Username: tzlaine  
> Created_at: 2024-09-30 17:47:10 UTC  
> Url: https://github.com/boostorg/parser/pull/173#issuecomment-2383818571  

Yep!  I just hadn't noticed those previous failures.  Thanks for doing this!

---
