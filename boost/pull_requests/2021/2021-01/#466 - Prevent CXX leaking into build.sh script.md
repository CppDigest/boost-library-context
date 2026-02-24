# #466 Prevent CXX leaking into build.sh script. [Merged]

> Username: grafikrobot  
> Created at: 2021-01-18 15:30:16 UTC  
> Updated at: 2021-01-20 01:22:09 UTC  
> Merged at: 2021-01-19 22:00:15 UTC  
> Closed at: 2021-01-19 22:00:15 UTC  
> Url: https://github.com/boostorg/boost/pull/466  

Many CI setups set CXX without user consent and without the ability to unset it. This causes the b2 build to fail as setting CXX for it is and intentional user choice that needs special care. This avoids the issue by clearing out CXX when invoking build.sh.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-01-19 11:50:42 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-762792847  

Can this be merged pleased?  
  
BTW if you wanted to retain the ability to build via `CXX=whatever`, then the following environment variables are set on CI integration:  
  
CI  (travis and drone.)  
TRAVIS  
DRONE  
  
HTH, John.

---

## Comment 2

> Username: sdarwin  
> Created_at: 2021-01-19 18:29:23 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763036471  

> Can this be merged pleased?  
  
Without this fix it looks like clang-3.8, 4.0, 5.0, and gcc-4.8, 4.9, 5 won't build the B2 engine.    
  
```  
Building B2 engine..  
A C++11 capable compiler is required for building the B2 engine.  
Toolset 'cxx' does not appear to support C++11.  
> g++-4.8 check_cxx11.cpp  
In file included from /usr/include/c++/4.8/thread:35:0,  
                 from check_cxx11.cpp:14:  
/usr/include/c++/4.8/bits/c++0x_warning.h:32:2: error: #error This file requires compiler and library support for the ISO C++ 2011 standard. This support is currently experimental, and must be enabled with the -std=c++11 or -std=gnu++11 compiler options.  
 #error This file requires compiler and library support for the \  
  ^  
check_cxx11.cpp: In function 'int main()':  
check_cxx11.cpp:20:12: error: '_' does not name a type  
     { auto _ = std::thread::hardware_concurrency(); }  
            ^  
```

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2021-01-19 18:37:36 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763041301  

> BTW if you wanted to retain the ability to build via `CXX=whatever`, then the following environment variables are set on CI integration:  
>   
> CI (travis and drone.)  
> TRAVIS  
> DRONE  
  
If someone wants to build b2 with a specific `CXX` they can do it the manual way instead of using `bootstrap.sh` :-)  
  
Ping, @pdimov or @glenfe or somebody to merge?

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-01-19 21:59:28 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763168632  

Does this mean that we'll no longer need https://github.com/boostorg/boost/pull/465?

---

## Comment 5

> Username: grafikrobot  
> Created_at: 2021-01-19 22:19:15 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763178298  

> Does this mean that we'll no longer need #465?  
  
Indeed, we no longer need that one. Should I do another PR to revert it?

---

## Comment 6

> Username: pdimov  
> Created_at: 2021-01-19 22:23:44 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763180266  

Yes please.

---

## Comment 7

> Username: grafikrobot  
> Created_at: 2021-01-20 01:22:09 UTC  
> Url: https://github.com/boostorg/boost/pull/466#issuecomment-763258835  

> Yes please.  
  
Here it is.. https://github.com/boostorg/boost/pull/467

---
