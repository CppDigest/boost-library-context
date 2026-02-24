# #26 Update CMakeLists.txt with install support [Merged]

> Username: pdimov  
> Created at: 2019-12-28 18:01:05 UTC  
> Updated at: 2019-12-30 21:29:16 UTC  
> Merged at: 2019-12-30 17:09:15 UTC  
> Closed at: 2019-12-30 17:09:15 UTC  
> Url: https://github.com/boostorg/atomic/pull/26  



---

## Comment 1

> Username: pdimov  
> Created_at: 2019-12-30 14:39:02 UTC  
> Url: https://github.com/boostorg/atomic/pull/26#issuecomment-569696154  

This is ready to merge, I think.

---

## Comment 2

> Username: pdimov  
> Created_at: 2019-12-30 17:08:52 UTC  
> Url: https://github.com/boostorg/atomic/pull/26#issuecomment-569736574  

I am going to merge this (sorry) because I want to use Atomic in the CI tests for tools/cmake. Atomic is the only compiled library that can have install support at the moment. The next contender, System, requires Predef to have install support (via Winapi), and it doesn't.

---

## Review 3 [Commented]

> Username: Lastique  
> Created_at: 2019-12-30 21:10:47 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/atomic/pull/26#pullrequestreview-337239923  

📁 CMakeLists.txt

```diff
   5 |- cmake_minimum_required(VERSION 3.5)
   6 |- project(BoostAtomic LANGUAGES CXX)
   6 |+ cmake_minimum_required(VERSION 3.5...3.16)
```

> Username: Lastique  
> Created_at: 2019-12-30 21:10:47 UTC  
> Url: https://github.com/boostorg/atomic/pull/26#discussion_r362097159  

Does this add an upper limit for supported CMake version? Why is this limit needed? Does it break on newer versions?

> Username: pdimov  
> Created_at: 2019-12-30 21:29:16 UTC  
> Updated_at: 2019-12-30 21:29:17 UTC  
> Url: https://github.com/boostorg/atomic/pull/26#discussion_r362100720  

No. 3.5 by itself is equivalent to 3.5...3.5. The first value controls when to issue a hard error (on 3.4 and earlier.) The second value controls CMake behavior and policies. For compatibility, a 3.16 CMake will act like a 3.5 one when (VERSION 3.5) is used. (3.5...3.16) says we prefer 3.16 to act like 3.16.


---
