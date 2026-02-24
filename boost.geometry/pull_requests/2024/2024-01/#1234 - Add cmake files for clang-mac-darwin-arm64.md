# #1234 Add cmake files for clang/mac/darwin/arm64 [Merged]

> Username: barendgehrels  
> Created at: 2024-01-28 15:55:49 UTC  
> Updated at: 2024-04-01 11:29:39 UTC  
> Merged at: 2024-02-28 11:17:55 UTC  
> Closed at: 2024-02-28 11:17:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234  

I moved to a macbook and got several problems with Boost.Geometry unit tests, mainly:  
* some floating point results are different on mac  
* it can compile and run b2, but it does not detect unit tests  
  
Therefore, and because it is probably useful anyway, I added CMakeLists files for the tests I'm using most often.  
  
The compilation can be more compatible with the other platforms by using ` -ffp-contract=fast`  
This fixes most (but not all) of the floating point differences.  
  
Please give your opinion about the CMake parts.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-01-28 15:56:43 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1234#pullrequestreview-1847577606  

📁 test/algorithms/buffer/test_buffer.hpp

```diff
  19 |-     // Define before including any buffer headerfile
  20 |-     #define BOOST_GEOMETRY_BUFFER_USE_HELPER_POINTS
  21 |- #endif
```

> Username: barendgehrels  
> Created_at: 2024-01-28 15:56:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#discussion_r1468891982  

(unrelated small change, it's not used anymore)


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-01-28 15:58:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1913643066  

The tests in `area`, `buffer` and all set operations are all compatible with the b2/jam variants (in the sense of compiler directives, naming, and test coverage)

---

## Comment 3

> Username: pdimov  
> Created_at: 2024-01-28 18:21:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1913681456  

You shouldn't be adding the Boost root to the include path. When using CMake, we don't rely on the symbolic links created by `b2 headers`, but use the headers directly from `libs/*/include`. You need to `target_link_libraries` to all `Boost::lib` targets which you use in the tests (even when header-only) so that CMake adds the correct include paths.  
  
You also need to make your test executables dependencies of the global `tests` target. Boost's testing procedure when using CMake, as documented here: https://github.com/boostorg/cmake#testing-boost-with-cmake, is  
  
```  
mkdir __build  
cd __build  
cmake -DBUILD_TESTING=ON ..  
cmake --build . --target tests  
ctest --output-on-failure --no-tests=error  
```  
  
If your test executables aren't dependencies of `tests`, they won't be built, and the `ctest` invocation will fail.  
  
This is accomplished with  
  
```  
if (NOT TARGET tests)  
    add_custom_target(tests)  
endif()  
```  
  
and then  
  
```  
        add_dependencies(tests ${unit_test_name})  
```  
  
Your test targets need to be prefixed with your project name, which is `boost_geometry`. In CMake, target names are global. If your library adds a test called `something` and another library adds a test called `something`, there will be a conflict.  
  
Consider adding CMake tests to your Github Actions (or other) CI. For an example, you can look at one of my libraries, e.g. Function: https://github.com/boostorg/function/blob/develop/.github/workflows/ci.yml

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2024-01-28 22:45:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1913746858  

> You shouldn't be adding the Boost root to the include path. When using CMake, we don't rely on the symbolic links created by `b2 headers`, but use the headers directly from `libs/*/include`. You need to `target_link_libraries` to all `Boost::lib` targets which you use in the tests (even when header-only) so that CMake adds the correct include paths.  
>   
> You also need to make your test executables dependencies of the global `tests` target. Boost's testing procedure when using CMake, as documented here: https://github.com/boostorg/cmake#testing-boost-with-cmake, is  
>   
  
Thanks for your comments and links!

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2024-01-30 20:29:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1917837079  

Hi @pdimov I updated it. I'm following the approach you did in `outcome`.  
  
* `boost_geometry_` prefixes the unit test targets  
* dependencies to `tests` added  
* dependencies to the boost libraries added  
  
Because we had already too many dependencies (many were unused (extensions), optional (adaptations), I split those lists and cleaned it up. I hope that's OK for the super project as well.  
  
Some dependencies should never have been there, such as predef and endian.  
  
As a background: I didn't add these CMakeLists for the general purpose. Mainly because b2 didn't work anymore on my Mac, and CMake is more convenient anyway. So it's mainly for my own purpose and I'm quite dependent on it. But anyway, maybe it serves broader purpose, now that it is more generic.  
  
(It's not complete though)

---

## Comment 6

> Username: pdimov  
> Created_at: 2024-01-30 20:47:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1917866493  

This looks largely correct to me.  
  
Optional dependencies are a hassle and I don't think you'll gain that much from removing them because many will still be there as transitive dependencies. But you could do it this way if you prefer.  
  
You are linking the tests to dependencies of Boost::geometry explicitly, which shouldn't be necessary because they'll be there as transitive dependencies.  
  
I assume you are using the unit test framework in header-only mode? The target to link to is `Boost::included_unit_test_framework` in that case.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-01-31 09:41:43 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1918738074  

Thanks again @pdimov   
Indeed, I now removed explicit dependencies in the unit tests themselves and added `Boost::included_unit_test_framework`, that is indeed what we use and it works elegantly like this in cmake.  
  
This is in latest commit, and I added some folders which are also related to overlay algorithms.

---

## Comment 8

> Username: barendgehrels  
> Created_at: 2024-02-24 09:49:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1962313935  

@vissarion I now removed the alternatives, because you had them removed as well in your PR.  
Is this fine now?

---

## Review 9 [Approved]

> Username: vissarion  
> Created_at: 2024-02-28 11:08:33 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1234#pullrequestreview-1905970226  

Thanks! I am OK with changes. Also managed to build and run without errors on Ubuntu 22.04 with gcc version 12.2.0

---

## Comment 10

> Username: barendgehrels  
> Created_at: 2024-02-28 11:17:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/1234#issuecomment-1968766626  

Thanks for approving, this is very convenient (at least for me).  
Later I'll add the still missing tests (there are quite some), but the current coverage is what I'm mostly working on.

---
