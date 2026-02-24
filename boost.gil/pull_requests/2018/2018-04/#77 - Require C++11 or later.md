# #77 Require C++11 or later [Merged]

> Username: mloskot  
> Created at: 2018-04-09 19:37:38 UTC  
> Updated at: 2018-04-09 20:17:27 UTC  
> Merged at: 2018-04-09 20:17:22 UTC  
> Closed at: 2018-04-09 20:17:22 UTC  
> Url: https://github.com/boostorg/gil/pull/77  

Add necessary flags to Boost.Build and CMake configurations as well as CI builds.  
  
### Description  
  
Following the agreement about [C++11 requirement in develop branch](https://lists.boost.org/boost-gil/2018/04/0015.php), Boost.GIL now officially requires compiler with support for C++11 or later.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2018-04-09 19:42:26 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/77#pullrequestreview-110589716  

📁 Jamfile

```diff
  10 |     :
  11 |     requirements
  12 |-         <toolset>intel:<debug-symbols>off
```

> Username: stefanseefeld  
> Created_at: 2018-04-09 19:42:26 UTC  
> Url: https://github.com/boostorg/gil/pull/77#discussion_r180207302  

Did you mean to remove this instruction ? Seems unrelated...

> Username: mloskot  
> Created_at: 2018-04-09 19:43:58 UTC  
> Url: https://github.com/boostorg/gil/pull/77#discussion_r180207699  

No, not at all.  
  
I shuffled the lines moving `<toolset>msvc:` to the top, so the `# MSVC` comments is in the beginning

> Username: stefanseefeld  
> Created_at: 2018-04-09 19:46:33 UTC  
> Url: https://github.com/boostorg/gil/pull/77#discussion_r180208404  

Oh, I see. I had missed that before. Thanks.

> Username: mloskot  
> Created_at: 2018-04-09 19:47:16 UTC  
> Url: https://github.com/boostorg/gil/pull/77#discussion_r180208583  

No problem. Thx for extra pair of eyes


---
