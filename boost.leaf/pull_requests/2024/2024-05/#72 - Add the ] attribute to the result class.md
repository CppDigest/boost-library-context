# #72 Add the [[nodiscard]] attribute to the result class [Merged]

> Username: indiosmo  
> Created at: 2024-05-09 11:32:32 UTC  
> Updated at: 2024-07-30 22:32:48 UTC  
> Merged at: 2024-07-29 02:52:41 UTC  
> Closed at: 2024-07-29 02:52:41 UTC  
> Url: https://github.com/boostorg/leaf/pull/72  



---

## Comment 1

> Username: zajo  
> Created_at: 2024-05-09 14:22:15 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2102762965  

The difficulty with these failures is that with LEAF being a standalone library, it doesn't use Boost Config. We need the correct compiler-specific ifdefs in leaf/config.hpp.

---

## Comment 2

> Username: indiosmo  
> Created_at: 2024-05-09 18:26:51 UTC  
> Updated_at: 2024-05-09 18:34:39 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2103197984  

Not sure I understand, the macro in question is already defined in leaf/config.hpp, line 212.  
  
```cpp  
  #if defined(__has_attribute) && defined(__SUNPRO_CC) && (__SUNPRO_CC > 0x5130)  
  #   if __has_attribute(nodiscard)  
  #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]  
  #   endif  
  #elif defined(__has_cpp_attribute)  
      //clang-6 accepts [[nodiscard]] with -std=c++14, but warns about it -pedantic  
  #   if __has_cpp_attribute(nodiscard) && !(defined(__clang__) && (__cplusplus < 201703L)) && !(defined(__GNUC__) && (__cplusplus < 201100))  
  #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]  
  #   endif  
  #endif  
  #ifndef BOOST_LEAF_ATTRIBUTE_NODISCARD  
  #   define BOOST_LEAF_ATTRIBUTE_NODISCARD  
  #endif  
```  
  
* edit1  
Perhaps you mean this is not sufficient, and boost config handles the missing cases, is that it?  
  
* edit2  
I'm looking into Boost Config and the only mention of [[nodiscard]] is in detail/suffix.hpp, and it's exactly the same macro definition that is in leaf/config.hpp so I'm not sure how Boost Config would help here.

---

## Comment 3

> Username: zajo  
> Created_at: 2024-05-10 02:08:23 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2103719685  

I mean the ifdefs are not correct, we need to inspect the error logs to see what's the problem. The goal is to make leaf/config.hpp behave the same as boost/config.hpp (which I presume is correct) in terms of detecting the relevant [nodiscard] support in various compiler versions. I might be able look into it during the weekend.

---

## Comment 4

> Username: indiosmo  
> Created_at: 2024-05-10 13:33:09 UTC  
> Updated_at: 2024-05-10 13:34:52 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2104619875  

I looked into boost/config.hpp and it seems to be exactly the same as leaf/config.hpp as far as [[nodiscard]] is concerned.  
  
Also, I tried looking at the CI logs but all the ones that failed are blank after line 49.  
  
![image](https://github.com/boostorg/leaf/assets/4126274/9361f18e-98df-41eb-bea1-de8b7c8521aa)

---

## Comment 5

> Username: zajo  
> Created_at: 2024-05-10 17:20:03 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2104987562  

The logs don't end at line 49, it just takes a while for github to fetch the whole thing. The logs are tens of thousands of lines long, that's why. :)

---

## Comment 6

> Username: indiosmo  
> Created_at: 2024-05-10 17:45:20 UTC  
> Updated_at: 2024-05-10 18:18:01 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2105020226  

I've reverted the changes to see if all tests pass, as at a cursory look it seems that some of the errors are unrelated to the change. Let's see.  
  
edit:  
Nevermind, here's a failure for nodiscard specifically:  
  
2024-05-10T16:59:48.1125656Z In file included from ./boost/leaf/result.hpp:9:0,  
2024-05-10T16:59:48.1128442Z                  from libs/leaf/test/_hpp_result_test.cpp:6:  
2024-05-10T16:59:48.1129879Z ./boost/leaf/config.hpp:219:47: error: expected identifier before '[' token  
2024-05-10T16:59:48.1131442Z  #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]

---

## Comment 7

> Username: zajo  
> Created_at: 2024-05-11 19:41:49 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2106002287  

Yes, the issue is in the nodiscard macro.

---

## Comment 8

> Username: indiosmo  
> Created_at: 2024-07-25 14:21:35 UTC  
> Updated_at: 2024-07-25 14:22:31 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2250448453  

Hi @zajo, I have some time so I'm revisiting this.  
  
What I'm having trouble with is that the current guard only enables the attribute if compiling with c++17 or above and it still fails for example using gcc 10 regardless of the standard.  
  
```  
2024-05-09T13:44:00.3622608Z gcc.compile.c++ bin.v2/libs/leaf/test/_hpp_result_test.test/gcc-10/debug/x86_64/cxxstd-11-iso/threading-multi/_hpp_result_test.o  
2024-05-09T13:44:00.3624449Z In file included from ./boost/leaf/result.hpp:9,  
2024-05-09T13:44:00.3625318Z                  from libs/leaf/test/_hpp_result_test.cpp:6:  
2024-05-09T13:44:00.3631902Z ./boost/leaf/config.hpp:219:47: error: expected identifier before ‘[’ token  
2024-05-09T13:44:00.3633198Z   219 | #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]  
```  
  
I don't understand how line 219 is being called when building with -std=c++11 if the guard above it is:  
`#   if __has_cpp_attribute(nodiscard) && __cplusplus >= 201703L`  
  
Ignoring that for now and looking further:  
```  
2024-05-09T13:44:00.3624449Z In file included from ./boost/leaf/result.hpp:9,  
2024-05-09T13:44:00.3625318Z                  from libs/leaf/test/_hpp_result_test.cpp:6:  
2024-05-09T13:44:00.3631902Z ./boost/leaf/config.hpp:219:47: error: expected identifier before ‘[’ token  
2024-05-09T13:44:00.3633198Z   219 | #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]  
2024-05-09T13:44:00.3633984Z       |                                               ^  
2024-05-09T13:44:00.3635764Z ./boost/leaf/result.hpp:177:33: note: in expansion of macro ‘BOOST_LEAF_ATTRIBUTE_NODISCARD’  
2024-05-09T13:44:00.3637134Z   177 | class BOOST_LEAF_SYMBOL_VISIBLE BOOST_LEAF_ATTRIBUTE_NODISCARD result  
2024-05-09T13:44:00.3637923Z       |                                 ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
Looks like we end up mixing both `__attribute__` and `[[attribute]]` syntax, which doesn't seem to be supported by gcc.  
`class __attribute__((__visibility__("default"))) [[nodiscard]] result`

---

## Comment 9

> Username: indiosmo  
> Created_at: 2024-07-25 14:47:35 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2250529949  

Here's some exploration https://godbolt.org/z/c6fWx9KEz  
  
It builds with gcc 7.5 and std=c++11, it fails with c++17 with both attributes, but works with only one of them.  
  
With nodiscard first:  
```  
<source>:16:38: error: expected identifier before '__attribute__'  
 #   define BOOST_LEAF_SYMBOL_VISIBLE __attribute__((__visibility__("default")))  
                                      ^  
<source>:21:39: note: in expansion of macro 'BOOST_LEAF_SYMBOL_VISIBLE'  
 class  BOOST_LEAF_ATTRIBUTE_NODISCARD BOOST_LEAF_SYMBOL_VISIBLE result  
````   
  
With visible first:  
```  
<source>:8:47: error: expected identifier before '[' token  
 #       define BOOST_LEAF_ATTRIBUTE_NODISCARD [[nodiscard]]  
```  
  
So the issue is in fact mixing both.  
Mixing only works starting on GCC 13.1.  
  
Seems to work on all compilers if we use [[gnu:] syntax instead of `__attribute__`:  
`#   define BOOST_LEAF_SYMBOL_VISIBLE [[gnu::visibility("default")]]`  
  
I'm commiting this so we can test.

---

## Comment 10

> Username: zajo  
> Created_at: 2024-07-25 19:02:40 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2251209207  

Thanks. And yes, feel free to change the configuration macros if needed.

---

## Comment 11

> Username: indiosmo  
> Created_at: 2024-07-25 19:04:50 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2251212959  

Looks like there are issues with the CI environment:  
  
```  
2024-07-25T19:02:11.9945506Z ##[command]/usr/bin/docker exec  1e5273ba1dd06360032578220225456afc2a23ff95f326ef97318aa6eefbfbd7 sh -c "cat /etc/*release | grep ^ID"  
2024-07-25T19:02:12.1407151Z /__e/node20/bin/node: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.28' not found (required by /__e/node20/bin/node)  
```

---

## Comment 12

> Username: zajo  
> Created_at: 2024-07-25 19:12:22 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2251226125  

Yeah, occasionally old environments get deprecated. I'll look into it.

---

## Comment 13

> Username: zajo  
> Created_at: 2024-07-26 00:55:36 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2251755849  

This is fixed in latest develop, you can integrate that in your branch to re-run gha.

---

## Comment 14

> Username: indiosmo  
> Created_at: 2024-07-27 13:43:45 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2254153234  

Still having issues with the CI environment: `test/try_catch_system_error_test.cpp:149:1: fatal error: error writing to /tmp/cc3wazIO.s: No space left on device`

---

## Comment 15

> Username: zajo  
> Created_at: 2024-07-27 18:00:27 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2254214595  

Yes, I will fix this weekend, and then I'll merge your PR assuming tests pass. Thank you once again.

---

## Comment 16

> Username: indiosmo  
> Created_at: 2024-07-29 11:16:17 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2255659402  

Thanks @zajo. Is this going to be in for boost 1.86? Do we need to contact anyone about it and to update the release notes?

---

## Comment 17

> Username: zajo  
> Created_at: 2024-07-29 19:08:43 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2256699677  

Wait, why was the visibility definition changed?

---

## Comment 18

> Username: indiosmo  
> Created_at: 2024-07-29 19:58:00 UTC  
> Updated_at: 2024-07-29 20:00:45 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2256787138  

The visibility value itself was unchanged.  
What changed was the syntax from `__attribute__`  to [[gnu:]] since we're using brackets for nodiscard.  
GCC wouldn't build with mixed attributes syntax up to version 13.

---

## Comment 19

> Username: zajo  
> Created_at: 2024-07-29 22:09:19 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2257095073  

Thanks. I have requested from the release managers to include this in the current release.

---

## Comment 20

> Username: zajo  
> Created_at: 2024-07-30 22:32:46 UTC  
> Url: https://github.com/boostorg/leaf/pull/72#issuecomment-2259316053  

The master branch was opened for bug fixes after the beta release, so this will make it in the upcoming Boost release. Thanks once again.

---
