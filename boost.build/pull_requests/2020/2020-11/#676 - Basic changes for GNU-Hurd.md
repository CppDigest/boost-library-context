# #676 Basic changes for GNU/Hurd [Merged]

> Username: pinotree  
> Created at: 2020-11-28 10:43:09 UTC  
> Updated at: 2021-10-02 21:01:20 UTC  
> Merged at: 2020-12-14 19:37:33 UTC  
> Closed at: 2020-12-14 19:37:33 UTC  
> Url: https://github.com/boostorg/build/pull/676  

This series adds basic support for GNU/Hurd in b2. Luckily there are not many changes needed, it mostly works as it is already.

---

## Review 1 [Commented]

> Username: grafikrobot  
> Created_at: 2020-12-14 17:40:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/build/pull/676#pullrequestreview-551697047  

📁 src/engine/sysinfo.cpp

```diff
  20 | 
  21 |- #if defined(OS_LINUX)
  21 |+ #if defined(OS_LINUX) || defined(__GLIBC__)
```

> Username: grafikrobot  
> Created_at: 2020-12-14 17:39:55 UTC  
> Updated_at: 2020-12-14 17:40:13 UTC  
> Url: https://github.com/boostorg/build/pull/676#discussion_r542576749  

Shouldn't that be `|| defined(OS_HURD)` ?

> Username: pinotree  
> Created_at: 2020-12-14 19:29:37 UTC  
> Url: https://github.com/boostorg/build/pull/676#discussion_r542683669  

As I explained in the commit message, this applies to anything that uses the GNU libc (glibc).

> Username: grafikrobot  
> Created_at: 2020-12-14 19:36:58 UTC  
> Url: https://github.com/boostorg/build/pull/676#discussion_r542691716  

Sorry about that.. The way GitHub hided commit messages makes for me missing many of them when reviewing PRs :-(


---

## Comment 2

> Username: grafikrobot  
> Created_at: 2020-12-14 19:37:55 UTC  
> Url: https://github.com/boostorg/build/pull/676#issuecomment-744664472  

Thanks for the changes!

---

## Comment 3

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:19 UTC  
> Url: https://github.com/boostorg/build/pull/676#issuecomment-932819897  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
