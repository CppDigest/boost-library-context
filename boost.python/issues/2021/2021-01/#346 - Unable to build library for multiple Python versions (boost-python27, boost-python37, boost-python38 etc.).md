# #346 - Unable to build library for multiple Python versions (boost-python27, boost-python37, boost-python38 etc.) [Closed]

> Username: yurybura  
> Created at: 2021-01-12 18:55:38 UTC  
> Updated at: 2021-02-09 13:12:11 UTC  
> Closed at: 2021-02-09 13:12:11 UTC  
> Url: https://github.com/boostorg/python/issues/346  

After commit https://github.com/boostorg/python/commit/60405cc48cf8cc2f7b32d85942b47cc39aa27b4f the version was removed from the library name. Now isn't possible build multiple versions of Boost,Python with command `./b2 python=2.7,3.7,3.8` (like suggested in post https://stackoverflow.com/a/56241975/2393925).  
Dear @SoapGentoo, @stefanseefeld could you explain why major and minor versions were removed from library name?

---

## Comment 1

> Username: PhoebeHui  
> Created at: 2021-02-09 02:26:12 UTC  
> Url: https://github.com/boostorg/python/issues/346#issuecomment-775608442  

Relate to PR https://github.com/boostorg/python/pull/286.

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2021-02-09 02:47:34 UTC  
> Url: https://github.com/boostorg/python/issues/346#issuecomment-775616815  

Python versions were *not* removed from the library names. Rather, the code was refactored a bit, which took a few release cycles as it involved multiple repos.  
When I run `./b2 python=2.7,3.8 --with-python -j8`, I get libraries `libboost_python27.so` as well as `libboost_python38.so`.  
If you can't reproduce that, please provide details (platform, exact commands, as well as resulting build artefacts).

---

## Comment 3

> Username: SoapGentoo  
> Created at: 2021-02-09 10:19:45 UTC  
> Url: https://github.com/boostorg/python/issues/346#issuecomment-775829705  

Same for me, the python libraries in Gentoo all have the normal major+minor suffixes

---

## Comment 4

> Username: yurybura  
> Created at: 2021-02-09 13:12:11 UTC  
> Url: https://github.com/boostorg/python/issues/346#issuecomment-775925050  

@stefanseefeld @stefanseefeld   
Thank you for your attention. `python-tag` rule with version suffix is defined in boostcpp,jam. The problem is in vcpkg build script.
