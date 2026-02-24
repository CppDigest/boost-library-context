# #20 - Standalone version of nowide is not friendly for building as sub-project [Closed]

> Username: ivaigult  
> Created at: 2017-07-18 10:12:52 UTC  
> Updated at: 2019-12-27 17:05:59 UTC  
> Closed at: 2019-12-27 17:05:59 UTC  
> Url: https://github.com/boostorg/nowide/issues/20  

- Root CMakeLists.txt unconditionally enables testing.   
- There is no way to disable building of shared library nor static library nor tests.

---

## Comment 1

> Username: Flamefire  
> Created at: 2019-11-28 16:59:16 UTC  
> Url: https://github.com/boostorg/nowide/issues/20#issuecomment-559561038  

Please see my fork if you are still interested. I created proper CMake files with focus on usage as sub project.
