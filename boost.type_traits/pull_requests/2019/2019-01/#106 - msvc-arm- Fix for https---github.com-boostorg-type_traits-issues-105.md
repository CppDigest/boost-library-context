# #106 msvc/arm: Fix for https://github.com/boostorg/type_traits/issues/105 [Closed]

> Username: jzmaddock  
> Created at: 2019-01-09 17:14:36 UTC  
> Updated at: 2019-01-10 17:56:56 UTC  
> Closed at: 2019-01-10 17:56:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106  



---

## Review 1 [Commented]

> Username: janisozaur  
> Created_at: 2019-01-09 20:59:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/106#pullrequestreview-190935585  

📁 appveyor.yml

```diff
  43 |       ARGS: --toolset=msvc-14.1 address-model=32 cxxflags=-clr asynch-exceptions=on
  44 |+     - APPVEYOR_BUILD_WORKER_IMAGE: Visual Studio 2017
  45 |+       ARGS: --toolset=msvc-14.1 architecture=arm testing.launcher=../../type_traits/test/null_runner.bat
```

> Username: janisozaur  
> Created_at: 2019-01-09 20:59:32 UTC  
> Updated_at: 2019-01-10 11:04:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#discussion_r246541949  

Wouldn't `testing.execute=off` suffice here?

> Username: jzmaddock  
> Created_at: 2019-01-10 11:08:41 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#discussion_r246719067  

I didn't know that existed - but yes just what we need!


---

## Review 2 [Commented]

> Username: janisozaur  
> Created_at: 2019-01-09 21:00:58 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/type_traits/pull/106#pullrequestreview-190936120  

📁 include/boost/type_traits/detail/is_function_cxx_11.hpp

```diff
  98 | 
  99 |- #ifdef _MSC_VER
  99 |+ #if defined(_MSC_VER) && !defined(_M_ARM) && !defined(_M_ARM64)
```

> Username: janisozaur  
> Created_at: 2019-01-09 21:00:58 UTC  
> Updated_at: 2019-01-10 11:04:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#discussion_r246542565  

In a purely hypothetical universe where MSVC supports _even more_ architectures, wouldn't that re-surface? Or is that too hypothetical?

> Username: jzmaddock  
> Created_at: 2019-01-10 11:12:00 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#discussion_r246720074  

For now yes ;)  Besides the hypothetical new architecture may need the extra specialisations just as likely as not need them?  
  
More likely IMO is that __vectorcall stops being the same as the default and becomes something different again on arm64 as it is on x64.  Or not.


---

## Comment 3

> Username: janisozaur  
> Created_at: 2019-01-09 21:04:10 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#issuecomment-452854770  

https://github.com/boostorg/type_traits/blob/c65f41d4c01383bb650faa2e89a8a2e53bc18c46/appveyor.yml#L85 seems to be an executable that prints out options used to build it. That won't run on x86 when compiled for arm and will fail the build.

---

## Comment 4

> Username: janisozaur  
> Created_at: 2019-01-09 21:54:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#issuecomment-452882977  

Thanks for such a prompt fix.

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2019-01-10 17:56:55 UTC  
> Url: https://github.com/boostorg/type_traits/pull/106#issuecomment-453192023  

Closing - I've condensed the commits and merged to develop manually.

---
