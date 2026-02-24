# #604 Embed manifest via linker [Merged]

> Username: Kojoley  
> Created at: 2020-05-12 21:56:30 UTC  
> Updated at: 2021-10-02 21:01:54 UTC  
> Merged at: 2020-05-27 19:43:53 UTC  
> Closed at: 2020-05-27 19:43:53 UTC  
> Url: https://github.com/boostorg/build/pull/604  

The feature is enabled by default only for MSVC 11 and above not to break  
derived toolsets.  
  
Unfortunately it cannot be enabled on clang-cl with MSVC linker because of:  
```  
>clang-cl test.cpp /link /manifest:embed  
LINK : fatal error LNK1158: cannot run 'rc.exe'  
clang-cl: error: linker command failed with exit code 1158 (use -v to see invocation)  
```  
  
Note: `embed-manifest-file` feature was broken before the change and still is  
broken under `embed-manifest-via=mt`. The fix seems to be obvious, but I am not  
fully understand what happens inside link/link.dll rule to fix it here.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2020-05-27 17:24:19 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/604#pullrequestreview-419447470  

📁 src/tools/msvc.jam

```diff
 268 |+ [[bbv2.builtin.features.embed-manifest-via]]`embed-manifest-via`::
 269 |+ This feature is specific to the `msvc` toolset (see <<Microsoft Visual C++>>),
 270 |+ and controls whether manifest should be embedded via linker or minifest tool.
```

> Username: grafikrobot  
> Created_at: 2020-05-27 17:21:58 UTC  
> Updated_at: 2020-05-27 18:34:29 UTC  
> Url: https://github.com/boostorg/build/pull/604#discussion_r431313381  

"minifest" ==> "manifest"


---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:01:53 UTC  
> Url: https://github.com/boostorg/build/pull/604#issuecomment-932819986  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
