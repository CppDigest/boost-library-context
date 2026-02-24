# #511 Fix dynamic image apply_operation when building with -std=c++11 [Merged]

> Username: sdebionne  
> Created at: 2020-07-27 12:56:02 UTC  
> Updated at: 2020-07-28 06:58:56 UTC  
> Merged at: 2020-07-27 15:33:50 UTC  
> Closed at: 2020-07-27 15:33:51 UTC  
> Url: https://github.com/boostorg/gil/pull/511  

### Description  
  
Fix a regression introduce in #491 when building with -std=c++11  
  
### References  
  
Fixes #510   
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: sdebionne  
> Created_at: 2020-07-27 12:58:54 UTC  
> Url: https://github.com/boostorg/gil/pull/511#issuecomment-664380425  

@mloskot If this fix cannot make it to 1.74, do you think we should add a known issue (and reference to this commit) in the release note?

---

## Comment 2

> Username: mloskot  
> Created_at: 2020-07-27 14:22:42 UTC  
> Url: https://github.com/boostorg/gil/pull/511#issuecomment-664426929  

@sdebionne Thanks! I'll try to requestppermission to merge the fix for the release, later tonight

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2020-07-27 15:31:50 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/511#pullrequestreview-455895401  

@sdebionne  Thanks for the prompt fix.  
  
I have manually verified our [example/dynamic_image.cpp](https://github.com/boostorg/gil/blob/develop/example/dynamic_image.cpp) and @apolukhin's [Chapter12/07_gil/main.cpp](https://github.com/apolukhin/Boost-Cookbook/blob/second_edition/Chapter12/07_gil/main.cpp) compile in C++11 mode uging GCC 8.4 and clang 11.0  
  
```  
$ g++ -std=c++11 -I/mnt/d/boost.wsl dynamic_image.cpp  -ljpeg  
$ clang++ -std=c++11 -I/mnt/d/boost.wsl dynamic_image.cpp  -ljpeg  
  
$ g++ -std=c++11 -I/mnt/d/boost.wsl main.cpp  -lpng  
$ clang++ -std=c++11 -I/mnt/d/boost.wsl main.cpp  -lpng  
```

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-07-27 15:52:52 UTC  
> Url: https://github.com/boostorg/gil/pull/511#issuecomment-664479549  

I [requested for permission to merge this to the master](https://lists.boost.org/Archives/boost/2020/07/249475.php) and has just granted it. I will wait for the CI builds and merge it some time later tonight.

---
