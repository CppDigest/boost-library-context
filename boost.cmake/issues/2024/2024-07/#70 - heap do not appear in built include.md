# #70 - heap do not appear in built include [Closed]

> Username: biggiantpigeon  
> Created at: 2024-07-18 11:33:34 UTC  
> Updated at: 2024-07-19 07:57:19 UTC  
> Closed at: 2024-07-19 07:57:19 UTC  
> Url: https://github.com/boostorg/cmake/issues/70  

Hello, I'm trying to use cmake to build boost, further use it in my ownproject. Then I find module heap although pulled as submodule in `libs`, but not appear in `include` folder after my build and install. I can sure copy it from your released pre-built version, but I want to solve this in an elegant way. Any suggestions?

---

## Comment 1

> Username: pdimov  
> Created at: 2024-07-18 11:37:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2236284207  

What Boost version?

---

## Comment 2

> Username: biggiantpigeon  
> Created at: 2024-07-18 11:38:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2236284816  

> What Boost version?  
  
1.82.0

---

## Comment 3

> Username: pdimov  
> Created at: 2024-07-18 11:39:22 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2236286449  

Does the issue still persist in newer versions, such as for instance 1.85.0?

---

## Comment 4

> Username: pdimov  
> Created at: 2024-07-18 11:41:00 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2236289120  

How does your project use Boost? Do you use CMake to build and install Boost first, or do you build Boost as a subproject, using `add_subdirectory`?

---

## Comment 5

> Username: biggiantpigeon  
> Created at: 2024-07-19 02:52:28 UTC  
> Updated at: 2024-07-19 03:22:42 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2237979746  

> Does the issue still persist in newer versions, such as for instance 1.85.0?  
  
I'll tried, it's the same for 1.85.0.  
  
> How does your project use Boost? Do you use CMake to build and install Boost first, or do you build Boost as a subproject, using `add_subdirectory`?  
  
I did the 'build and install' first.  
  
By the way, I choose only these libraries to build:  
```  
-DBOOST_INCLUDE_LIBRARIES="filesystem;chrono;thread;system;atomic;date_time;math;graph;program_options"  
```  
 will that be the reason? If it is, what option can I add to solve this, considering I want to keep my built output as small as possible? Thanks.

---

## Comment 6

> Username: pdimov  
> Created at: 2024-07-19 06:25:26 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2238383913  

You would need to add `heap` to the list if you need it to be installed.

---

## Comment 7

> Username: biggiantpigeon  
> Created at: 2024-07-19 07:57:14 UTC  
> Url: https://github.com/boostorg/cmake/issues/70#issuecomment-2238588870  

yes, it works now, thanks a lot.
