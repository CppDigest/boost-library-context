# #475 - Support of c++20 [Closed]

> Username: fsmoke  
> Created at: 2020-11-30 11:06:05 UTC  
> Updated at: 2021-05-30 12:04:15 UTC  
> Closed at: 2021-05-26 03:40:50 UTC  
> Url: https://github.com/boostorg/hana/issues/475  

If i turning on support of c++20 on my compiler(msvc) i've got errors:  
  
1>  d:\libraries\boost_1_72_0\boost\hana\traits.hpp(71,62): error C2039: 'is_literal_type': is not a member of 'std'  
  
1>  d:\libraries\boost_1_72_0\boost\hana\traits.hpp(197,50): error C2039: 'result_of': is not a member of 'std'  
  
because above traits removed in c++20...and std::is_pod is deprecated!

---

## Comment 1

> Username: ricejasonf  
> Created at: 2020-11-30 20:24:46 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-736022159  

There is a fixed merged in for `std::is_pod`: https://github.com/boostorg/hana/pull/473/files

---

## Comment 2

> Username: sbolding-LANL  
> Created at: 2021-05-11 16:35:09 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-838797017  

@ricejasonf, there is also an issue with `std::is_literal_type` that produces a warning at c++17 and error at c++20.  Should that just be removed for greater than c++14 to avoid the compiler warnings? I can submit a PR with that change.

---

## Comment 3

> Username: ricejasonf  
> Created at: 2021-05-11 16:46:32 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-838812776  

A PR would be great! @ldionne is the one who would accept it.  
  
I wonder why I am not seeing these errors. It must all be in that `traits.h` file.

---

## Comment 4

> Username: sbolding-LANL  
> Created at: 2021-05-11 16:49:22 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-838816639  

We only started seeing it when going to gcc 11, but I was able to recreate it on Wandbox by just including `boost/hana.hpp`.  I will submit a PR.

---

## Comment 5

> Username: JamesJCode  
> Created at: 2021-05-18 23:16:10 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-843628028  

This PR is also required for MSVC to build with C++20 support enabled due to ```std::is_literal_type``` being removed.

---

## Comment 6

> Username: ldionne  
> Created at: 2021-05-26 03:40:50 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-848430414  

This should be fixed now.

---

## Comment 7

> Username: egorpugin  
> Created at: 2021-05-26 23:15:23 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-849180620  

`#if __cplusplus < 201703L`  
  
msvc sets `__cplusplus` to `199711L`, so it won't work.

---

## Comment 8

> Username: JamesJCode  
> Created at: 2021-05-26 23:17:24 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-849181418  

> `#if __cplusplus < 201703L`  
>   
> msvc sets `__cplusplus` to `199711L`, so it won't work.  
  
If you pass the option `/Zc:__cplusplus`, it behaves properly. They haven't changed the default behaviour though due to legacy code always expecting `199711L`.

---

## Comment 9

> Username: egorpugin  
> Created at: 2021-05-26 23:20:11 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-849182556  

Does hana set `/Zc:__cplusplus` by default and does it expose it to dependent targets?  
If no, there will be errors surely. Either in unit tests or in user projects without that flag.

---

## Comment 10

> Username: JamesJCode  
> Created at: 2021-05-27 08:08:49 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-849430469  

I don't think it does - I set it myself in my root CMakeLists file.

---

## Comment 11

> Username: ldionne  
> Created at: 2021-05-27 12:29:57 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-849591713  

I don't use MSVC, can one of you @jafj or @egorpugin please send a PR to add it?

---

## Comment 12

> Username: JamesJCode  
> Created at: 2021-05-28 11:12:23 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850343534  

I only use it occasionally in a VM to check cross-compilation compatability of code, but I can probably take a look in the next few days. Not a difficult PR to work up I think.

---

## Comment 13

> Username: ldionne  
> Created at: 2021-05-28 11:16:41 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850345862  

Thanks. The PR itself is easy to come up with, what I’d like is to validate that it solves your problem properly.

---

## Comment 14

> Username: JamesJCode  
> Created at: 2021-05-28 11:18:26 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850346809  

Yes of course - that's exactly what I'm aiming to check; didn't mean to sound dismissive! I've got a project I'm working on I can cross-check it with (which is the one I ran in to this issue with).

---

## Comment 15

> Username: ldionne  
> Created at: 2021-05-28 13:35:14 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850423550  

> Yes of course - that's exactly what I'm aiming to check; didn't mean to sound dismissive!   
  
Ahah, no worries, that's the furthest from my mind!  
  
> I've got a project I'm working on I can cross-check it with (which is the one I ran in to this issue with).  
  
Awesome, thank you!

---

## Comment 16

> Username: JamesJCode  
> Created at: 2021-05-29 23:22:10 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850912889  

So I've just looked at this, and with Microsoft Visual Studio Community 2019 Version 16.9.6 (pretty recent!) my project is now compiling against the head commit of hana master repository without having to set any compiler flags.  
  
Health warning: I've removed my global CMake addition of the `/Zc:__cplusplus` flag, but I also include a whole host of other external projects which may or may not also be doing that. To be honest, I think it shouldn't compile, so I'm a bit surprised... I'll try and reduce this to a small test project to remove the wider project includes.  
  
@egorpugin do you have a use-case against head which doesn't compile due to this?

---

## Comment 17

> Username: egorpugin  
> Created at: 2021-05-30 00:00:53 UTC  
> Updated at: 2021-05-30 00:01:50 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850916336  

I just `#include <boost/hana/traits.hpp>` and see the image. (Current fix is applied.)  
This is msvc from VS16.10, `/std:c++latest`, boost-1.76.  
  
![image](https://user-images.githubusercontent.com/4325333/120087735-24af8280-c0f3-11eb-973d-58f5d416d512.png)

---

## Comment 18

> Username: JamesJCode  
> Created at: 2021-05-30 08:14:28 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850960963  

OK. I've got a simple reproducable case now. PR incoming, but this fixes the error on MSVC for me. At line 53 of `hana/CMakeLists.txt`, simply add:  
  
```  
if(MSVC)  
        target_compile_options(hana INTERFACE /Zc:__cplusplus)  
endif()  
```

---

## Comment 19

> Username: JamesJCode  
> Created at: 2021-05-30 08:26:34 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850962364  

@ldionne PR #488 fixes this issue

---

## Comment 20

> Username: egorpugin  
> Created at: 2021-05-30 09:22:30 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850968816  

The problem is that other code might not work `/Zc:__cplusplus`.  
I used some libs that failed with `/Zc:__cplusplus`, so I turned it off.  
If you poison user projects with `/Zc:__cplusplus`, that's might be an issue.

---

## Comment 21

> Username: JamesJCode  
> Created at: 2021-05-30 09:27:38 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850969411  

Yes, but there's no other nice way this can be magicallty fixed as this is a header library. The options are:  
  
1. Apply this PR. Some downstream code will break.  
2. Leave things as they are. Users will have compilation errors and need to set the flag themselves.  
3. Some horrible hacky thing whereby we detect the CXX version set in CMake, and pass our own compile-time variable in.  
  
3 would likely work, but it feels a bit messy coupling the build system to the source like that...

---

## Comment 22

> Username: egorpugin  
> Created at: 2021-05-30 09:46:40 UTC  
> Updated at: 2021-05-30 09:48:15 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850971773  

I think we can do more fine tuned condition right in code.  
  
1. Msvc or ms stl defines `_HAS_CXX20` and similar macros.  
(Probably somewhere here or near this file https://github.com/microsoft/STL/blob/main/stl/inc/yvals_core.h)  
`#if !defined(_MSC_VER) && __cplusplus < 201703L || defined(_MSC_VER) && !defined(_HAS_CXX20) && !defined(_HAS_CXX23)`  
2. We can try to rely on some modern cpp check macros like `__cpp_lib_coroutine` or `__cpp_lib_concepts` which are from c++20  
`#if !defined(_MSC_VER) && __cplusplus < 201703L || defined(_MSC_VER) && !defined(__cpp_lib_coroutine)`

---

## Comment 23

> Username: JamesJCode  
> Created at: 2021-05-30 11:54:17 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850987732  

You're right - needed a bit of tweaking (the `_HAS_CXXYY` variables are set to `0` or `1` rather than being defined or not). The following in `traits.hpp` works for me with every setting of `CMAKE_CXX_STANDARD` from `11` to `20`.  
  
```  
#if (!defined(_MSC_VER) && __cplusplus < 202002L) || (defined(_MSC_VER) && defined(_HAS_CXX20) && _HAS_CXX20 == 0)  
    BOOST_HANA_INLINE_VARIABLE constexpr auto is_pod = detail::hana_trait<std::is_pod>{};  
#endif  
#if (!defined(_MSC_VER) && __cplusplus < 201703L) || (defined(_MSC_VER) && defined(_HAS_CXX17) && _HAS_CXX17 == 0)  
    BOOST_HANA_INLINE_VARIABLE constexpr auto is_literal_type = detail::hana_trait<std::is_literal_type>{};  
#endif  
```  
  
Much cleaner. I'll put in a PR now.

---

## Comment 24

> Username: JamesJCode  
> Created at: 2021-05-30 12:04:15 UTC  
> Url: https://github.com/boostorg/hana/issues/475#issuecomment-850989020  

PR #489 has the above fix
