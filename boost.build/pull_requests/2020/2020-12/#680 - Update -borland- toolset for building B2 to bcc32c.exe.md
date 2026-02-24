# #680 Update "borland" toolset for building B2 to bcc32c.exe [Merged]

> Username: tanzislam  
> Created at: 2020-12-03 13:09:56 UTC  
> Updated at: 2021-10-02 21:01:22 UTC  
> Merged at: 2020-12-14 17:36:54 UTC  
> Closed at: 2020-12-14 17:36:54 UTC  
> Url: https://github.com/boostorg/build/pull/680  

For bootstrapping `b2` itself, the current `borland` toolset is broken now as `bcc32.exe` doesn't support C++11. Update that toolset to use the Clang-based `bcc32c.exe`, which has largely the same cmdline args.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2020-12-14 17:37:16 UTC  
> Url: https://github.com/boostorg/build/pull/680#issuecomment-744597138  

Thanks for the fix!

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:21 UTC  
> Url: https://github.com/boostorg/build/pull/680#issuecomment-932819902  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
