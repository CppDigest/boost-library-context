# #30 add boost_assert.natvis and interface target sources for VS [Closed]

> Username: alandefreitas  
> Created at: 2022-11-10 18:48:38 UTC  
> Updated at: 2023-01-12 08:45:06 UTC  
> Closed at: 2023-01-12 08:45:06 UTC  
> Url: https://github.com/boostorg/assert/pull/30  



---

## Comment 1

> Username: pdimov  
> Created_at: 2022-11-10 18:56:09 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310758235  

There is already https://github.com/boostorg/assert/pull/27.

---

## Comment 2

> Username: alandefreitas  
> Created_at: 2022-11-10 19:02:54 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310766343  

> There is already https://github.com/boostorg/assert/pull/27.  
  
Yes. This PR is attempting to fix #27

---

## Comment 3

> Username: pdimov  
> Created_at: 2022-11-10 19:04:31 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310768017  

I don't understand, what does it fix?

---

## Comment 4

> Username: alandefreitas  
> Created_at: 2022-11-10 19:31:02 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310797970  

> I don't understand, what does it fix?  
  
I tried to fix the `target_sources`. That revealed another error though. I'll make this a draft until this is completely fixed.

---

## Comment 5

> Username: pdimov  
> Created_at: 2022-11-10 19:33:13 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310800375  

Adding INTERFACE sources means something entirely different - these sources are then added to all targets that link to the INTERFACE target.

---

## Comment 6

> Username: alandefreitas  
> Created_at: 2022-11-10 19:35:23 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310802585  

> Adding INTERFACE sources means something entirely different - these sources are then added to all targets that link to the INTERFACE target.  
  
Right. So it needs to be `PRIVATE` and we need to check `CMAKE_VERSION`, right?  
  
Isn't this error still going to persist?  
  
```  
CMake Error in libs/assert/CMakeLists.txt:  
  Target "boost_assert" INTERFACE_SOURCES property contains path:  
  
    "/home/runner/work/assert/boost-root/libs/assert/include/boost/assert.hpp"  
  
  which is prefixed in the source directory.  
```

---

## Comment 7

> Username: alandefreitas  
> Created_at: 2022-11-10 21:02:57 UTC  
> Url: https://github.com/boostorg/assert/pull/30#issuecomment-1310889402  

@pdimov it worked now

---

## Review 8 [Commented]

> Username: pdimov  
> Created_at: 2022-11-10 23:43:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/assert/pull/30#pullrequestreview-1176629462  

📁 CMakeLists.txt

```diff
  17 |     Boost::config
  18 | )
  19 |+ if (CMAKE_MAJOR_VERSION GREATER_EQUAL 3 AND CMAKE_MINOR_VERSION GREATER_EQUAL 19)
```

> Username: pdimov  
> Created_at: 2022-11-10 23:43:13 UTC  
> Updated_at: 2022-11-10 23:43:14 UTC  
> Url: https://github.com/boostorg/assert/pull/30#discussion_r1019707841  

`if(CMAKE_VERSION VERSION_GREATER 3.18)`  
  
(`VERSION_GREATER_EQUAL` requires CMake 3.7.)  
  
Also, maybe add the CMAKE_GENERATOR check here too, and add the missing empty line before the `if`.

> Username: alandefreitas  
> Created_at: 2022-11-11 00:08:37 UTC  
> Url: https://github.com/boostorg/assert/pull/30#discussion_r1019719330  

> if(CMAKE_VERSION VERSION_GREATER 3.18)  
  
That was the plan, but `VERSION_GREATER` is only available in CMake 3.7 and the project promises support for CMake 3.5.  
  
> Also, maybe add the CMAKE_GENERATOR check here too, and add the missing empty line before the if.  
  
The `CMAKE_GENERATOR` could be one of [Visual Studio 6](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%206.html), [Visual Studio 7](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%207.html), [Visual Studio 7 .NET 2003](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%207%20.NET%202003.html), [Visual Studio 8 2005](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%208%202005.html), [Visual Studio 9 2008](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%209%202008.html), [Visual Studio 10 2010](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2010%202010.html), [Visual Studio 11 2012](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2011%202012.html), [Visual Studio 12 2013](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2012%202013.html), [Visual Studio 14 2015](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2014%202015.html), [Visual Studio 15 2017](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2015%202017.html), [Visual Studio 16 2019](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2016%202019.html), [Visual Studio 17 2022](https://cmake.org/cmake/help/latest/generator/Visual%20Studio%2017%202022.html)...  
  
So would you prefer  
  
1)  
  
```cmake  
if (CMAKE_GENERATOR MATCHES "Visual Studio.*")  
# ...  
```  
  
or  
  
2)   
  
```cmake  
string(SUBSTRING CMAKE_GENERATOR 0 13 CMAKE_GENERATOR_SUBSTR)  
if (CMAKE_GENERATOR_SUBSTR STREQUAL "Visual Studio")  
# ...  
```  
  
or   
  
3) just leave it as it is (I don't think it negatively affects other generators)  
  
?


---
