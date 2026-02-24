# #382 - BOOST_NO_CTYPE_FUNCTIONS and BSD OS's [Open]

> Username: brad0  
> Created at: 2021-07-02 00:25:45 UTC  
> Updated at: 2022-09-17 16:10:57 UTC  
> Url: https://github.com/boostorg/config/issues/382  

While looking at include/boost/config/platform/bsd.hpp and updating the OpenBSD bits I noticed this  
bit of the header.  
  
```  
//  
// The BSD <ctype.h> has macros only, no functions:  
//  
#if !defined(__OpenBSD__) || defined(__DragonFly__)  
#  define BOOST_NO_CTYPE_FUNCTIONS  
#endif  
```  
  
I was wondering if this needed any further changes for the current state, but looking at each respective  
ctype.h header for the BSD's I am not sure if this should be set or not. Could someone who is familiar  
with this area comment after taking a look at the headers? I have provided URLs below,  
  
https://gitweb.dragonflybsd.org/dragonfly.git/blob_plain/HEAD:/include/ctype.h  
https://raw.githubusercontent.com/freebsd/freebsd-src/main/include/ctype.h  
http://cvsweb.netbsd.org/bsdweb.cgi/~checkout~/src/include/ctype.h?rev=1.35&only_with_tag=MAIN  
http://cvsweb.openbsd.org/cgi-bin/cvsweb/~checkout~/src/include/ctype.h?rev=1.25

---

## Comment 1

> Username: jzmaddock  
> Created at: 2021-07-02 08:11:42 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-872808311  

Test case is here: https://github.com/boostorg/config/blob/develop/test/boost_no_ctype_functions.ipp

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-07-04 18:27:35 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-873637932  

> Test case is here: [https://github.com/boostorg/config/blob/develop/test/boost_no_ctype_functions.ipp](https://github.com/boostorg/config/blob/develop/test/boost_no_ctype_functions.ipp?rgh-link-date=2021-07-02T08%3A11%3A42Z)  
  
I think the test case is invalid, it takes addresses of non-addressable standard library functions  
[\[namespace.constraints\]/6](https://eel.is/c++draft/namespace.constraints#namespace.std-6)

---

## Comment 3

> Username: jzmaddock  
> Created at: 2021-07-05 18:12:08 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-874266949  

I'm not sure when that constraint was added, but it's not in C++11.  
  
The point is that these should be real functions and not macros as they originally were on BSD.

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-07-05 18:46:56 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-874279174  

> I'm not sure when that constraint was added, but it's not in C++11.  
  
It is C++20, [P0551](http://wg21.link/p0551).  
  
> The point is that these should be real functions and not macros as they originally were on BSD.  
  
Converting the test to C must be the simplest solution. IIRC doing `(isalpha)('x')` should done the job too.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2021-07-05 19:34:24 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-874296753  

The test case has to be C++, I'll change it, now that C++20 has apparently broken it.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2021-07-07 08:45:06 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-875414322  

Test case corrected for C++20 in develop.

---

## Comment 7

> Username: jzmaddock  
> Created at: 2021-08-15 17:53:09 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-899088361  

Can you check the revised test case on BSD?

---

## Comment 8

> Username: brad0  
> Created at: 2022-09-16 23:20:52 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249931663  

Sorry I forgot about this. How do I go about running the test individually or the whole test suite?

---

## Comment 9

> Username: Kojoley  
> Created at: 2022-09-16 23:26:16 UTC  
> Updated at: 2022-09-16 23:27:40 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249933601  

From the root of Boost, where `bootstrap.sh` is located: `b2 libs/config/test` to run the whole test suite of the Boost.Config library and `b2 libs/config/test//name_of_the_test` to run a test named `name_of_the_test` of the Boost.Config library.

---

## Comment 10

> Username: Kojoley  
> Created at: 2022-09-16 23:42:18 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249938796  

Actually, it seems that I am wrong and you need to do `b2 libs/config/test/all` to run the test suit that includes that test, and `b2 libs/config/test/all//BOOST_NO_CTYPE_FUNCTIONS` to run the test only,  
  
@jzmaddock is it done intentionally? I am asking because I do not see those test to run on https://www.boost.org/development/tests/develop/developer/config.html

---

## Comment 11

> Username: brad0  
> Created at: 2022-09-17 00:39:58 UTC  
> Updated at: 2022-09-17 00:41:18 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249961201  

Boost 1.80  
  
```  
humpty$ b2 libs/config/test/all//BOOST_NO_CTYPE_FUNCTIONS  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : x86 (cached) [1]  
  
[1] clang-13  
...found 130 targets...  
...updating 19 targets...  
clang-linux.compile.c++ bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.o  
libs/config/test/all/../no_ctype_functions_fail.cpp:27:2: error: "this file should not compile"  
#error "this file should not compile"  
 ^  
libs/config/test/all/../no_ctype_functions_fail.cpp:32:11: error: use of undeclared identifier 'boost_no_ctype_functions'  
   return boost_no_ctype_functions::test();  
          ^  
2 errors generated.  
(failed-as-expected) bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.o  
**passed** bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.test  
clang-linux.compile.c++ bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.o  
clang-linux.link bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass  
testing.capture-output bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.run  
**passed** bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.test  
...updated 19 targets...  
```  
  
From the source dir after running a build of Boost itself.

---

## Comment 12

> Username: Kojoley  
> Created at: 2022-09-17 00:56:58 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249964349  

> Boost 1.80  
>   
> ```  
> humpty$ b2 libs/config/test/all//BOOST_NO_CTYPE_FUNCTIONS  
> Performing configuration checks  
>   
>     - default address-model    : 64-bit (cached) [1]  
>     - default architecture     : x86 (cached) [1]  
>   
> [1] clang-13  
> ...found 130 targets...  
> ...updating 19 targets...  
> clang-linux.compile.c++ bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.o  
> libs/config/test/all/../no_ctype_functions_fail.cpp:27:2: error: "this file should not compile"  
> #error "this file should not compile"  
>  ^  
> libs/config/test/all/../no_ctype_functions_fail.cpp:32:11: error: use of undeclared identifier 'boost_no_ctype_functions'  
>    return boost_no_ctype_functions::test();  
>           ^  
> 2 errors generated.  
> (failed-as-expected) bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.o  
> **passed** bin.v2/libs/config/test/all/no_ctype_functions_fail.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_fail.test  
> clang-linux.compile.c++ bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.o  
> clang-linux.link bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass  
> testing.capture-output bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.run  
> **passed** bin.v2/libs/config/test/all/no_ctype_functions_pass.test/clang-linux-13/debug/threading-multi/visibility-hidden/no_ctype_functions_pass.test  
> ...updated 19 targets...  
> ```  
  
This means that the macro state is correct for your platform.  
  
> From the source dir after running a build of Boost itself.  
  
Building Boost manually is not necessary in case of running tests, b2 would do that automatically when needed.

---

## Comment 13

> Username: brad0  
> Created at: 2022-09-17 01:07:39 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1249966389  

> Building Boost manually is not necessary in case of running tests, b2 would do that automatically when needed.  
  
I only did that because when I initiated the command to run the test it looked as if it was going to run a full build. When I did the same command after doing a build it didn't behave in the same manner, but it did run the test.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2022-09-17 12:34:38 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1250063547  

The tests under /all/ are useful sometimes, but need to be taken with a pinch of salt also.  
  
I would:  
  
cd libs/config/test  
../../../b2 config_test  
  
Then make any changes you might think are required and re-run and see what if anything breaks ;)

---

## Comment 15

> Username: Kojoley  
> Created at: 2022-09-17 16:10:57 UTC  
> Url: https://github.com/boostorg/config/issues/382#issuecomment-1250097660  

> The tests under /all/ are useful sometimes, but need to be taken with a pinch of salt also.  
  
I personally fixed wrong defines in config that lived for a long time and could be detected much earlier if these were running.
