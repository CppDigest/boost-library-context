# #22 - `std::unary_function` deprecation warning with GCC 12 [Closed]

> Username: joaquintides  
> Created at: 2022-05-08 14:56:56 UTC  
> Updated at: 2022-11-12 17:34:26 UTC  
> Closed at: 2022-07-10 15:21:23 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22  

Starting in GCC 12, the compiler emits a warning when `std::unary_function`/`std::binary_function` is used (they haven't gone as far as removing these for C++ standard versions beyond 14 as mandated). This affects `boost::hash` as it conditionally uses `std::unary_function` based on `BOOST_NO_CXX98_FUNCTION_BASE`, which, correctly, is still undefined for GCC 12.  
  
There are various alternatives to deal with this:  
  
* Stop using `std::unary_function` altogether (unlikely, it may break C++03 user code).  
* Stop using it based on a combination of `BOOST_NO_CXX98_FUNCTION_BASE` and `__cplusplus`.  
* Wrap in warning-stopping pragma.  
  
Thank you,

---

## Comment 1

> Username: mclow  
> Created at: 2022-05-09 01:33:04 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120541209  

I'm assuming that you're referring to the code in "boost/container_hash/hash.hpp", and that the problem is that libstdc++ makes `unary_function` etc available in C++17 and later, but gcc12 will warn if you use them - is that correct?  
  
Seen in other code:  
```  
// Ignore warnings about std::unary_function and std::binary_function.  
#pragma GCC diagnostic push  
#pragma GCC diagnostic ignored "-Wdeprecated-declarations"  
```

---

## Comment 2

> Username: joaquintides  
> Created at: 2022-05-09 07:20:52 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120736259  

Yes, this is correct. The offending bit is https://github.com/boostorg/container_hash/blob/d2986d9a64c2bb74513dad8874136064d262423b/include/boost/container_hash/hash.hpp#L123-L133

---

## Comment 3

> Username: jzmaddock  
> Created at: 2022-05-09 08:02:21 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120772950  

We should probably start setting BOOST_NO_CXX98_FUNCTION_BASE for gcc -12 ?

---

## Comment 4

> Username: joaquintides  
> Created at: 2022-05-09 08:08:53 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120780163  

Well, the thing is that GCC12 does provide `std::unary_function`/`std::binary_function` even if it warns about it, so not defining `BOOST_NO_CXX98_FUNCTION_BASE` here is technically correct.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2022-05-09 11:12:44 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120964343  

Well sort of ;)  
  
The other option is to change:  
  
```  
#if defined(BOOST_NO_CXX98_FUNCTION_BASE)  
```  
  
To  
  
```  
#if defined(BOOST_NO_CXX98_FUNCTION_BASE) || (BOOST_CXX_VERSION >= 201703)  
```

---

## Comment 6

> Username: joaquintides  
> Created at: 2022-05-09 11:25:46 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120976165  

That'd be my preferred solution.

---

## Comment 7

> Username: pdimov  
> Created at: 2022-05-09 11:40:29 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1120989289  

Is GCC 12 available on GHA?

---

## Comment 8

> Username: jzmaddock  
> Created at: 2022-05-09 12:25:50 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1121033133  

> That'd be my preferred solution.  
  
You know, the more I think about this, the more I think BOOST_NO_CXX98_FUNCTION_BASE *should* be defined.  
  
Rationale: if the compiler is actively telling us that something shouldn't be used, then we shouldn't use it, and our macros should be set accordingly.  Plus this could either be a warning or an error depending on the state of -Werror.

---

## Comment 9

> Username: mclow  
> Created at: 2022-05-09 14:30:38 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1121180644  

Whatever we decide, we should apply it boost-wide, rather than just in one place

---

## Comment 10

> Username: pdimov  
> Created at: 2022-05-09 14:51:28 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1121206201  

If we decide to define BOOST_NO_CXX98_FUNCTION_BASE, it will automatically apply Boost-wide.  
  
I'm generally not fond of making Config lie, but in this case it's justifiable because these base classes should never be used anyway.

---

## Comment 11

> Username: joaquintides  
> Created at: 2022-05-10 14:24:35 UTC  
> Updated at: 2022-05-10 16:14:03 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1122468128  

The argument about `-Werror` turning this into a hard error seems to me cogent enough to go for defining `BOOST_NO_CXX98_FUNCTION_BASE` in this situation.

---

## Comment 12

> Username: jzmaddock  
> Created at: 2022-05-10 16:06:47 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1122596596  

>The argument about -Werror turning this into a hard error seems tome cogent enough to go for defining BOOST_NO_CXX98_FUNCTION_BASE in this situation.  
  
OK, I've nearly got gcc-12 running in boost.config CI, once that's sorted I'll make this change.

---

## Comment 13

> Username: pdimov  
> Created at: 2022-05-10 16:25:53 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1122615982  

As far as I see you just need the `ubuntu-22.04` image and then the package `gcc-12` should be available for `apt install`.

---

## Comment 14

> Username: jzmaddock  
> Created at: 2022-05-10 16:27:41 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1122617830  

> As far as I see you just need the ubuntu-22.04 image and then the package gcc-12 should be available for apt install.  
  
Yup, but I keep mucking up the CI file, and clang is really picky about which other gcc versions are installed.... nearly there.

---

## Comment 15

> Username: pdimov  
> Created at: 2022-05-10 17:54:32 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1122698971  

I'll keep this open as a reminder to add GCC 12 to GHA here.

---

## Comment 16

> Username: pdimov  
> Created at: 2022-05-11 19:27:38 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1124205780  

The Config change doesn't seem to fix the issue; I still get the deprecation warnings in https://github.com/boostorg/container_hash/runs/6394302405?check_suite_focus=true. Looks like `unary_function` is deprecated in libstdc++12 in C++11 and C++14 modes as well.

---

## Comment 17

> Username: pdimov  
> Created at: 2022-05-11 19:51:12 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1124226097  

https://github.com/gcc-mirror/gcc/blob/88459c3965e2a2f62ab4d4c8b2ac8460b1a15c33/libstdc%2B%2B-v3/include/bits/stl_function.h#L112-L124

---

## Comment 18

> Username: jzmaddock  
> Created at: 2022-05-12 11:33:01 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1124881047  

Oh :(  Try it now?

---

## Comment 19

> Username: joaquintides  
> Created at: 2022-05-12 11:35:01 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1124882951  

IMHO defining `BOOST_NO_CXX98_FUNCTION_BASE` across the board is maybe a little too much lying. Other option is to provide two macros:  
  
* `BOOST_NO_CXX98_FUNCTION_BASE` (defined if deprecated or non-existent), `BOOST_NO_CXX98_FUNCTION_BASE_STRICT` (as before)  
*  `BOOST_DEPRECATED_CXX98_FUNCTION_BASE` (defined if deprecated or non-existent),  `BOOST_NO_CXX98_FUNCTION_BASE` (as before)  
  
The first option has the benefit of solving potential problems in other libs in one fell swoop.

---

## Comment 20

> Username: lrineau  
> Created at: 2022-05-12 11:49:33 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1124895065  

> Yes, this is correct. The offending bit is  
>   
> https://github.com/boostorg/container_hash/blob/d2986d9a64c2bb74513dad8874136064d262423b/include/boost/container_hash/hash.hpp#L123-L133  
  
As the version with `defined(BOOST_NO_CXX98_FUNCTION_BASE)` is perfectly fine, I wonder why there is a version using `std::unary_function`. What are the benefits?

---

## Comment 21

> Username: pdimov  
> Created at: 2022-05-12 14:13:10 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1125047649  

> What are the benefits?  
  
None. The only reason for the derivation is that `hash` used to be specified this way in TR1 and C++11, and now there might be user code somewhere relying on it. But I'm going to refactor `boost::hash` soon and when I do, I'll remove the derivation entirely, regardless of the Config macro.

---

## Comment 22

> Username: pdimov  
> Created at: 2022-05-12 14:56:40 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1125098819  

> Try it now?  
  
Works now, as expected.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2022-05-12 16:35:24 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1125202574  

>None. The only reason for the derivation is that hash used to be specified this way in TR1 and C++11, and now there might be user code somewhere relying on it.  
  
C++11 doesn't have hash inherit from unary_function (which is deprecated).  Also given that msvc has been quite pro-active in deprecating and/or removing these, and we already do not inherit from unary_function in that case with, so far as I can see, zero defect reports and/or complaints about the change, we may be worrying too much ;)  
  
>IMHO defining BOOST_NO_CXX98_FUNCTION_BASE across the board is maybe a little too much lying. Other option is to provide two macros:  
  
I think that's overkill to be honest, these types have been deprecated for 11 years and removed from the std for 5.  It's time for them to quietly fade away!

---

## Comment 24

> Username: joaquintides  
> Created at: 2022-05-12 16:41:23 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1125207797  

I tend to err on the conservative side :-) Thanks for your support John.

---

## Comment 25

> Username: mojca  
> Created at: 2022-11-12 06:31:59 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312385838  

I started investigating this problem after hitting an issue with clang (that has already been fixed by https://github.com/boostorg/container_hash/issues/24 in the meantime, but I cannot easily fetch the latest version of boost in the build process, so I'm eagerly waiting for 1.81.0 to be released).  
  
You have implemented  
```c++  
#if BOOST_LIBSTDCXX_VERSION >= 120000  
//  
// Unary function is now deprecated in C++17 and later:  
//  
#if __cplusplus >= 201703L  
#define BOOST_NO_CXX98_FUNCTION_BASE  
```  
but according to https://cplusplus.com/reference/functional/unary_function/ this has already been deprecated in **C++11**.  
  
So I'm just wandering why this is not unconditionally set on anything larger (or perhaps even equal) than C++11, independent of whether libc++ or libstdc++ is being used.  
  
And I'm silently hoping for the following suggestion to get implemented soon ;)  
  
> But I'm going to refactor `boost::hash` soon and when I do, I'll remove the derivation entirely, regardless of the Config macro.

---

## Comment 26

> Username: pdimov  
> Created at: 2022-11-12 12:55:47 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312473177  

See https://github.com/boostorg/config/commit/26a8d3803170137f6e44ba26b118f4332d021dcb.

---

## Comment 27

> Username: mojca  
> Created at: 2022-11-12 16:53:08 UTC  
> Updated at: 2022-11-12 17:08:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312526022  

Except that this doesn't really help me in any way.  
  
The code is safeguarded by `#if BOOST_LIBSTDCXX_VERSION >= 120000`, while the following code:  
```c++  
printf("%d %ld\n", BOOST_LIBSTDCXX_VERSION, __cplusplus);  
printf("%s\n", BOOST_STDLIB);  
```  
compiled with `clang++-14 -std=c++17` on Ubuntu 22.04 returns:  
```  
110100 201703  
GNU libstdc++ version 20220513  
```  
The `BOOST_LIBSTDCXX_VERSION` that is equal to `110100` doesn't pass the check for `>= 120000`.  
  
Personally I would simply remove `#if BOOST_LIBSTDCXX_VERSION >= 120000` altogether.  
  
`g++12` on the same machine returns  
```  
120100 201703  
GNU libstdc++ version 20220513  
```

---

## Comment 28

> Username: mojca  
> Created at: 2022-11-12 17:06:07 UTC  
> Updated at: 2022-11-12 17:06:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312528821  

This code is utterly weird:  
https://github.com/boostorg/config/blob/5f089cc/include/boost/config/stdlib/libstdcpp3.hpp#L140-L146  
```c++  
#ifdef __clang__  
  
#if __has_include(<expected>)  
#  define BOOST_LIBSTDCXX_VERSION 120100  
#elif __has_include(<source_location>)  
#  define BOOST_LIBSTDCXX_VERSION 110100  
#elif __has_include(<compare>)  
```  
The header `<expected>` has only been in the C++ standard since [C++23](https://en.cppreference.com/w/cpp/header/expected) and that is now a hard prerequisite to satisfy the criteria `BOOST_LIBSTDCXX_VERSION >= 120000`.  
  
So basically you expect C++23 features to be present in order to disable code that has been deprecated since C++11?  
  
(I'm testing this with boost 1.80.0. The above change is present.)

---

## Comment 29

> Username: mojca  
> Created at: 2022-11-12 17:15:31 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312530930  

And if I happen to build with `clang++-14 -stdlib=libc++`, I get  
```c++  
/_deps/boost-src/libs/config/include/boost/config/detail/select_stdlib_config.hpp:26:14: fatal error: 'cstddef' file not found  
#    include <cstddef>  
             ^~~~~~~~~  
1 error generated.  
```

---

## Comment 30

> Username: pdimov  
> Created at: 2022-11-12 17:19:38 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312531740  

That's how Boost.Config determines the version of libstdc++ under Clang, by checking whether specific stdlib headers exist. Since <expected> has been added in libstdc++ 12, it checks for it.  
  
https://github.com/gcc-mirror/gcc/tree/releases/gcc-12.1.0/libstdc%2B%2B-v3/include/std  
  
I don't know why __has_include doesn't find it for you. And in either case this has nothing to do with ContainerHash, it's a Config issue.

---

## Comment 31

> Username: jzmaddock  
> Created at: 2022-11-12 17:22:24 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312532286  

Right, basically we want to know which version of gcc the std lib shipped with, and since libstdc++ doesn't identify itself in any useful way via predefined macros, we check for the presence of headers that were known to have been added in each release.  
  
>And if I happen to build with clang++-14 -stdlib=libc++, I get  
>  
>/_deps/boost-src/libs/config/include/boost/config/detail/select_stdlib_config.hpp:26:14: fatal error: 'cstddef' file not found  
>#    include <cstddef>  
  
Well if <cstddef> is not found in the include path this is hardly our problem - nothing will compile in that situation.

---

## Comment 32

> Username: mojca  
> Created at: 2022-11-12 17:25:36 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312532956  

> I don't know why `__has_include` doesn't find it for you. And in either case this has nothing to do with ContainerHash, it's a Config issue.  
  
I agree, this needs to go to the `config` repository.  
  
I started writing here because I hit the exact same issue and the exact same comments from above apply for `clang` as they do for `gcc`.  
  
In any case the discussion about `boost/config/stdlib/libstdcpp3.hpp` is indeed way too off-topic for this thread, so I opened a new ticket that is also linked above (https://github.com/boostorg/config/issues/455). Let's continue the discussion there.

---

## Comment 33

> Username: mojca  
> Created at: 2022-11-12 17:34:26 UTC  
> Url: https://github.com/boostorg/container_hash/issues/22#issuecomment-1312534713  

> > ```  
> > /_deps/boost-src/libs/config/include/boost/config/detail/select_stdlib_config.hpp:26:14: fatal error: 'cstddef' file not found  
> > ```  
>   
> Well if is not found in the include path this is hardly our problem - nothing will compile in that situation.  
  
I'm sorry, that was in fact my fault. I forgot to install  
```  
sudo apt install libc++-14-dev libc++abi-14-dev  
```  
along the `clang-14`. That part works now.
