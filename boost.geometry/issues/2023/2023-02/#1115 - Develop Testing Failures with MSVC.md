# #1115 - Develop Testing Failures with MSVC [Open]

> Username: rconde01  
> Created at: 2023-02-21 16:19:42 UTC  
> Updated at: 2023-06-20 14:44:29 UTC  
> Url: https://github.com/boostorg/geometry/issues/1115  

On Windows, Visual Studio 2022 17.4.5. I'm just getting setup to do some boost geometry dev and following the contribution tutorial. There seem to be a number of failures when running `b2 test` in the `libs/geometry` directory. Unsure if these are expected. See the attached file.  
  
[geometry_test_results.txt](https://github.com/boostorg/geometry/files/10795462/geometry_test_results.txt)

---

## Comment 1

> Username: vissarion  
> Created at: 2023-02-22 10:13:37 UTC  
> Url: https://github.com/boostorg/geometry/issues/1115#issuecomment-1439753408  

OK it seems that there are several rutime errors (most probably due to accuracy issues) in formulas, buffer and overlay. Similar errors are encountered when compiling with the `-std=c++2a` flag for some compilers *e.g.* `gcc-8` and `clang-9`.

---

## Comment 2

> Username: vissarion  
> Created at: 2023-06-19 14:49:42 UTC  
> Url: https://github.com/boostorg/geometry/issues/1115#issuecomment-1597322925  

@rconde01 could you tell us if https://github.com/boostorg/geometry/pull/1120 fixed your issue here?

---

## Comment 3

> Username: rconde01  
> Created at: 2023-06-19 15:09:32 UTC  
> Url: https://github.com/boostorg/geometry/issues/1115#issuecomment-1597357242  

Yes i will try to look today

---

## Comment 4

> Username: rconde01  
> Created at: 2023-06-20 14:44:28 UTC  
> Url: https://github.com/boostorg/geometry/issues/1115#issuecomment-1598932113  

There are still a number of build and test failures:  
[geometry_test_results_04796496c6bf5a1d33c8f282465bfa3804761932_debug.txt](https://github.com/boostorg/geometry/files/11801594/geometry_test_results_04796496c6bf5a1d33c8f282465bfa3804761932_debug.txt)
