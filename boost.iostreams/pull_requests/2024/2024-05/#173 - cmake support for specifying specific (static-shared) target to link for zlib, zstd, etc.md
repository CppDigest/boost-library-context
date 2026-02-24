# #173 cmake support for specifying specific (static/shared) target to link for zlib, zstd, etc [Open]

> Username: nigels-com  
> Created at: 2024-05-07 05:39:59 UTC  
> Updated at: 2025-02-27 12:00:57 UTC  
> Url: https://github.com/boostorg/iostreams/pull/173  

For zstd the `BOOST_IOSTREAMS_ENABLE_ZSTD` option is hard-coded to the shared library target: `zstd::libzstd_shared`  
  
This PR accepts other targets such as a static library target for the purpose of generality.  
  
@pdimov (for visibility and discussion)

---

## Review 1 [Commented]

> Username: nigels-com  
> Created_at: 2024-05-09 00:28:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/iostreams/pull/173#pullrequestreview-2046960931  

📁 CMakeLists.txt

```diff
  32 |-     target_link_libraries(boost_iostreams PRIVATE ${target})
  32 |+ 
  33 |+     if(TARGET ${name})
```

> Username: nigels-com  
> Created_at: 2024-05-09 00:28:14 UTC  
> Updated_at: 2024-05-09 00:28:15 UTC  
> Url: https://github.com/boostorg/iostreams/pull/173#discussion_r1594833064  

This logic does actually seem to be working as intended.  
Inclined to withdraw this PR, but will revisit.


---

## Comment 2

> Username: nigels-com  
> Created_at: 2025-02-27 12:00:56 UTC  
> Url: https://github.com/boostorg/iostreams/pull/173#issuecomment-2687750607  

Looks like this needs rework along the lines of PR #176   
We also need static zlib option.

---
