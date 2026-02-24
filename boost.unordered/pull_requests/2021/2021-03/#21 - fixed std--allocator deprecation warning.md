# #21 fixed std::allocator deprecation warning [Closed]

> Username: tobias-loew  
> Created at: 2021-03-15 08:08:44 UTC  
> Updated at: 2021-05-15 08:20:53 UTC  
> Closed at: 2021-05-15 08:20:53 UTC  
> Url: https://github.com/boostorg/unordered/pull/21  

Hi,  
this is a fix for the very annoying std::allocator-deprecation warnings.  
I've changes the evaluation of the fallback-type from greedy to lazy, by quoting it, and only evaluating when needed.  
This warning shows up on msvc 14.2 (VS 16.9.1). I don't know if it also occurs with other compilers/std-libs.  
I hope it can make it as bugfix still into 1.76  
  
Tobias

---

## Comment 1

> Username: tobias-loew  
> Created_at: 2021-03-15 10:13:19 UTC  
> Updated_at: 2021-03-15 10:14:20 UTC  
> Url: https://github.com/boostorg/unordered/pull/21#issuecomment-799296390  

Unfortunately, the tests on the develop branch currently fail (WITHOUT this PR).  
The failing builds are:  
- TravisCI: Compiler: clang C++; label="clang 32 bit"; user_config="using clang : : clang++ -m32 ;" CXXSTD=03  
- Environment: APPVEYOR_BUILD_WORKER_IMAGE=Visual Studio 2015, TOOLSET=msvc-10.0,msvc-11.0,msvc-12.0,msvc-14.0  
  
I've checked the output including this PR and it shows exactly the same errors as the develop branch.  
The other tests succeeded.

---
