# #578 cbrt math metal (and more) [Closed]

> Username: ckormanyos  
> Created at: 2021-03-21 18:57:40 UTC  
> Updated at: 2024-01-24 21:34:13 UTC  
> Closed at: 2021-10-22 12:57:24 UTC  
> Url: https://github.com/boostorg/math/pull/578  

The purpose of this draft PR is to discuss putting parts of Boost.Math on bare metal embedded systems.  
  
Hi @mborland and @jzmaddock your work has been amazing on reducing the dependencies of Boost.Math.  
  
This has allowed me to finally put a non-trivial Boost.Math function on a OS-less bare metal system with no output stream device. After literally years of trying off-and-on (and never succeeding), the reduced dependencies, mature compilers and C++11 all around in Boost.Math now make this dream a reality.  
  
I have selected the STM32F446 micro on the ST Microelectronics NUCLEO board featuring an ARM(R) CORTEX(R) M4F core, with GCC 9.3.1 `std=c++11`. See pics.  
  
The [benchmark code](https://github.com/ckormanyos/real-time-cpp/blob/cbrt_math_metal/ref_app/src/app/benchmark/app_benchmark_boost_math_cbrt.cpp) computes the [cube root of a `float`](https://github.com/ckormanyos/real-time-cpp/blob/e06d291c3c6c60067892a1d2ed30fd3c62a52615/ref_app/src/app/benchmark/app_benchmark_boost_math_cbrt.cpp#L40).  
  
![cbrt_math_metal_stm32f446](https://user-images.githubusercontent.com/2404721/111917331-ad6ccb00-8a7f-11eb-97bb-567555b0872d.JPG)

---

## Comment 1

> Username: ckormanyos  
> Created_at: 2021-03-21 19:02:50 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803642344  

The benchmark in actoini on the target is shown  
![IMG_2064](https://user-images.githubusercontent.com/2404721/111917455-66330a00-8a80-11eb-96a9-2f005397b82a.jpg)

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-03-21 19:08:58 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803643177  

There are a few issues to discuss. I deactivated RTTI and also removed inclusion of `<stdexcept>` at one location. The use of I/O streams is difficult _on the metal_, so I reduced the streaming to `std::stringstream` to an empty function at one other location.  
  
Boost.Math.Constants is proving to be a bit more difficult because `lexical_cast` is very rich in its use of O-streaming, making it difficult to embed on OS-less, heapless systems.  
  
I encountered another issue in the map file. Although I had instantiated the `cbrt` function for 32-bit built-in `float`, the map file indicates that this is being instantiated for `double`, which is 64-bits and software-simulated on this system. But I really want to use the single-precision float of the CM4 core's FPU.  
  
Here is a section from the map file.  
  
```  
08000e4c 00000040 T app::benchmark::task_init()  
08000e8c 00000028 T app::benchmark::task_func()  
08000eb8 000003b8 W double boost::math::detail::cbrt_imp<double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(double, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)  
08001270 00000068 T app::benchmark::run_boost_math_cbrt()  
080012d8 00000010 T app::led::task_init()  
080012e8 00000030 T app::led::task_func()  
08001318 00000018 t _GLOBAL__sub_I__ZN3app3led9task_initEv  
08001330 0000008c T mcal::cpu::init()  
080013bc 00000008 t (anonymous namespace)::gpt_is_initialized()  
080013c4 0000001c T __sys_tick_handler  
```  
  
The nearly 1k of code for the function is not too shabby, but should be a lot smaller when properly instantiated for built-in `float`. I would actually need some advice here, if I am doing something wrong in the benchmark code.  
  
This seems confusing, as I would expect the `cbrt` function in the benchmar code to be instantiated for `float`.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-03-21 19:49:18 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803648995  

The default behaviour for all the special functions is promotion of float->double for internal calculations, you can turn it off with `#define BOOST_MATH_PROMOTE_FLOAT_POLICY false`.  
  
>Boost.Math.Constants is proving to be a bit more difficult because lexical_cast is very rich in its use of O-streaming, making it difficult to embed on OS-less, heapless systems.  
  
Hmmm, lexical_cast and iostreams should be unused and not instantiated for float arguments, or is simply #including them the issue?  If the latter than we could do a `__has_include()` check I guess.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2021-03-21 20:07:38 UTC  
> Updated_at: 2021-03-21 20:12:13 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803651457  

> The default behaviour for all the special functions is promotion of float->double for internal calculations, you can turn it off with `#define BOOST_MATH_PROMOTE_FLOAT_POLICY false`  
  
Thanks John! That did the trick. The FPU is being used for `float` now and drove the run time for `cbrt`down from 18us to about 2us (12us with software 32-bit `float`).  
  
```  
0800071c 00000040 T app::benchmark::task_init()  
0800075c 00000028 T app::benchmark::task_func()  
08000784 000001fc W float boost::math::detail::cbrt_imp<float, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >(float, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> const&)  
```  
  
As for the next topic...  
  
> Hmmm, lexical_cast and iostreams should be unused and not instantiated for float arguments, or is simply #including them the issue?  
  
The issue is misleading. I am doing this preliminary development with `arm-none-eabi` triple-cross built in Linux for MinGW host to cut ARM code. I am of the firm belief that this compiler, although supplied by the supplier themselves, has certain missing elements in its STL, such as some of the `<bits/...>` headers actually missing.  
  
In order to provide a more even playing field, I will run Boost.Math.Constants on the metal through either ARM KEIL or IAR compilers, or both. These tend to have much better STLs, and also even better more exact link time optimization. I will report.  
  
When you live on the metal, some of the most problematic things arise with things such as RTTI (easy to deactivate), I/O streaming and locales in particular with `<iostream>` and `<iomanip>` and creation of proper memory management allocators for things such as STL containers.  
  
All-in-all, the new, lean Boost.Math (and Boost.Multiprecision) are now poised to have large parts of themselves put on the metal. This is something I have looked into for years, and it is really coming together now days.  
  
Many many thanks again to both @mborland and yoursel @jzmaddock for enabling these strides forward. It will be feasible to test for some math functions on a much wider variety of ARM(R) cores in the future. I will work on this as we progress.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-03-21 21:39:34 UTC  
> Updated_at: 2021-03-21 21:42:11 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803664401  

Hi @mborland  
  
I was wondering what you think of some of these dependencies.  
I ran the compiler including (from today's develop branch) nothing from boost other than  
  
```  
#define BOOST_MATH_PROMOTE_FLOAT_POLICY false  
#include <boost/math/special_functions/cbrt.hpp>  
```  
  
  
GCC's dependency maker came up with the following list. I am looking as some of the assert stuff, also `<boost/cstdint.hpp>` I wonder if we can refine even further and eliminate some of these? i was also wondering on the other hand if they come from some configs that can not be touched by math?  
  
```  
tmp/obj/app_benchmark_boost_math_cbrt.o: \  
 src/app/benchmark/app_benchmark_boost_math_cbrt.cpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/cbrt.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/rational.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/config.hpp \  
 C\:/boost/boost_1_76_0/boost/config.hpp \  
 C\:/boost/boost_1_76_0/boost/config/user.hpp \  
 C\:/boost/boost_1_76_0/boost/config/detail/select_compiler_config.hpp \  
 C\:/boost/boost_1_76_0/boost/config/compiler/gcc.hpp \  
 C\:/boost/boost_1_76_0/boost/config/detail/select_stdlib_config.hpp \  
 C\:/boost/boost_1_76_0/boost/config/stdlib/libstdcpp3.hpp \  
 C\:/boost/boost_1_76_0/boost/config/detail/select_platform_config.hpp \  
 C\:/boost/boost_1_76_0/boost/config/detail/suffix.hpp \  
 C\:/boost/boost_1_76_0/boost/predef/architecture/x86.h \  
 C\:/boost/boost_1_76_0/boost/predef/architecture/x86/32.h \  
 C\:/boost/boost_1_76_0/boost/predef/version_number.h \  
 C\:/boost/boost_1_76_0/boost/predef/make.h \  
 C\:/boost/boost_1_76_0/boost/predef/detail/test.h \  
 C\:/boost/boost_1_76_0/boost/predef/architecture/x86/64.h \  
 C\:/boost/boost_1_76_0/boost/detail/workaround.hpp \  
 C\:/boost/boost_1_76_0/boost/config/workaround.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/user.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/assert.hpp \  
 C\:/boost/boost_1_76_0/boost/assert.hpp \  
 C\:/boost/boost_1_76_0/boost/static_assert.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/detail/polynomial_horner3_20.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/detail/rational_horner3_20.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/policies/error_handling.hpp \  
 C\:/boost/boost_1_76_0/boost/config/no_tr1/complex.hpp \  
 C\:/boost/boost_1_76_0/boost/config/no_tr1/cmath.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/policies/policy.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/mp.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/precision.hpp \  
 C\:/boost/boost_1_76_0/boost/limits.hpp \  
 C\:/boost/boost_1_76_0/boost/throw_exception.hpp \  
 C\:/boost/boost_1_76_0/boost/exception/exception.hpp \  
 C\:/boost/boost_1_76_0/boost/assert/source_location.hpp \  
 C\:/boost/boost_1_76_0/boost/current_function.hpp \  
 C\:/boost/boost_1_76_0/boost/cstdint.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/math_fwd.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/detail/round_fwd.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/promotion.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/fpclassify.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/tools/real_cast.hpp \  
 C\:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/detail/fp_traits.hpp \  
 C\:/boost/boost_1_76_0/boost/predef/other/endian.h \  
 C\:/boost/boost_1_76_0/boost/predef/library/c/gnu.h \  
 C\:/boost/boost_1_76_0/boost/predef/library/c/_prefix.h \  
 C\:/boost/boost_1_76_0/boost/predef/detail/_cassert.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/macos.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/ios.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd/bsdi.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd/dragonfly.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd/free.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd/open.h \  
 C\:/boost/boost_1_76_0/boost/predef/os/bsd/net.h \  
 C\:/boost/boost_1_76_0/boost/predef/platform/android.h \  
 C\:/boost/boost_1_76_0/boost/type_traits/is_floating_point.hpp \  
 C\:/boost/boost_1_76_0/boost/type_traits/integral_constant.hpp \  
 src/app/benchmark/app_benchmark.h  
```

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-03-22 09:30:49 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-803910997  

The [benchmark code](https://github.com/ckormanyos/real-time-cpp/blob/d4515ea0979e3d673a7fbff5c212cf207c287da0/ref_app/src/app/benchmark/app_benchmark_boost_math_cbrt.cpp#L42) now successfully exercises 1 calculation involving `boost::math::constants::pi`, `cbrt` and `tgamma` for single-precision `float` on the metal for RasPI and STM32F446.

---

## Comment 7

> Username: mborland  
> Created_at: 2021-03-22 15:39:59 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-804161525  

@ckormanyos Predef and Config are the next two up on the docket for removal. A large number of those dependencies will be removed. For the asserts you can `#define BOOST_MATH_STANDALONE` to have macros expand to `assert` and `static_assert` instead of what is provided in Boost.Assert.

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2021-03-22 17:29:22 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-804253685  

And... with regard to lexical_cast, what I imagine eventually, is that in standalone mode, any template instantiation which would ordinarily require lexical_cast results in a static_assert plus helpful message.  And that no iostreams headers would be included at all.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-03-22 18:46:54 UTC  
> Updated_at: 2021-03-22 18:57:06 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-804307127  

> And... with regard to lexical_cast, what I imagine eventually, is that in standalone mode, any template instantiation which would ordinarily require lexical_cast results in a static_assert plus helpful message. And that no iostreams headers would be included at all.  
  
Hi @jzmaddock thank you for this description. That makes sense.  
  
> Predef and Config are the next two up on the docket for removal. A large number of those dependencies will be removed.  
  
Hi @mborland that is awesome sauce! I can really _feel_ the lighweight difference when cloning submodules in CI from develop and using Boost.Math. Earlier it seemed like I needed to clone about 4/5 of Boost. Now it seems closer to 1/5 or less.  
  
So things are looking really good. In fact, this new lean Boost.Math lets us deeply embed a lot of Boost.Math simply _off the rack_. I ended up identifying ways to deal with my seemingly broken ARM(R) GCC compilers locally on Win*. No such problems on `arm-none-eabi-gcc` on Ubuntu, nor on KEIL or IAR.  
  
So in my own [real-time-cpp repository](https://github.com/ckormanyos/real-time-cpp), I put some Boost.Math builds for `target=stm32f446` (which is an ARM(R) Cortex-M4F) into my CI --- also with a nightly CI run. I compile with super-high warning levels. I dealt with the warnings signed/unsigned comaprisonns, etc. in the branch. But these might go to far for you guys.  
  
As a next step, in my own repo, I will not only build some Boost.Math on the metal, but also run it in QEMU or on a Segger simulator, or even a KEIL simulator if the licensing is OK. That will take me a couple of weeks to sort through. i've contacted some other metal gurus for a bit of advice on this.  
  
In this link, we find some [awesome Boost.Math building on the metal](https://github.com/ckormanyos/real-time-cpp/runs/2168958958?check_suite_focus=true#step:6:1). As we progress, we can discuss when, or if, we want some Cortex/QEMU runs in CI of Boost itself...  
  
In [my own little benchmark file](https://github.com/ckormanyos/real-time-cpp/blob/master/ref_app/src/app/benchmark/app_benchmark_boost_math_cbrt.cpp), I am exercising the results of:  
  
```  
Table[N[Gamma[((456 n)/(100 Pi))^(1/3)], 21], {n, 1, 101, 10}]  
```

---

## Comment 10

> Username: ckormanyos  
> Created_at: 2021-04-23 09:34:49 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825533555  

Hi @mborland   
  
I am following your excellent progress on standalone Math.  
  
The dependencies are, in fact, strongly reduced now. I am, however, still seing some from predef and a few others. I am relatively sure that I am on the proper development branches, but maybe I should be on an mborland branch. Anyway, i still need to pick up `static_assert`, some `predef`stuff and a few more that I thought might be gone from standalone. Are these parasitic includes? Or am I still not doing things quite right?  
  
At the moment, the headers I seem to need (via empirical observation) for compiling a simple `cbrt.hpp` calculation are shown below:  
  
![grafik](https://user-images.githubusercontent.com/2404721/115851732-8eb77680-a427-11eb-9983-448d2c6081e6.png)  
  
In my GCC dependency walker, I get this list:  
  
```  
test.exe: test.cpp math/softfloat/soft_double.h \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/cbrt.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/rational.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/config.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/user.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/detail/select_compiler_config.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/compiler/gcc.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/detail/select_stdlib_config.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/stdlib/libstdcpp3.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/detail/select_platform_config.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/platform/win32.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/detail/suffix.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/user.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/assert.hpp \  
 C:/boost/modular_boost/boost/libs/assert/include/boost/assert.hpp \  
 C:/boost/modular_boost/boost/libs/static_assert/include/boost/static_assert.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/detail/workaround.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/config/workaround.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/detail/polynomial_horner3_20.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/detail/rational_horner3_20.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/policies/error_handling.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/policies/policy.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/mp.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/precision.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/throw_exception.hpp \  
 C:/boost/modular_boost/boost/libs/throw_exception/include/boost/throw_exception.hpp \  
 C:/boost/modular_boost/boost/libs/throw_exception/include/boost/exception/exception.hpp \  
 C:/boost/modular_boost/boost/libs/assert/include/boost/assert/source_location.hpp \  
 C:/boost/modular_boost/boost/libs/assert/include/boost/current_function.hpp \  
 C:/boost/modular_boost/boost/libs/config/include/boost/cstdint.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/math_fwd.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/detail/round_fwd.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/promotion.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/fpclassify.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/tools/real_cast.hpp \  
 C:/boost/modular_boost/boost/libs/math/include/boost/math/special_functions/detail/fp_traits.hpp \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/other/endian.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/version_number.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/make.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/detail/test.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/library/c/gnu.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/library/c/_prefix.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/detail/_cassert.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/macos.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/ios.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd/bsdi.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd/dragonfly.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd/free.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd/open.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/os/bsd/net.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/platform/android.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/alpha.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/arm.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/blackfin.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/convex.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/e2k.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/ia64.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/m68k.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/mips.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/parisc.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/ppc.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/ptx.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/pyramid.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/riscv.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/rs6k.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/sparc.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/superh.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/sys370.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/sys390.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/x86.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/x86/32.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/x86/64.h \  
 C:/boost/modular_boost/boost/libs/predef/include/boost/predef/architecture/z.h  
```

---

## Comment 11

> Username: jzmaddock  
> Created_at: 2021-04-23 11:34:25 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825597180  

Everything should be in latest develop now, do you have BOOST_MATH_STANDALONE defined?  
  
This reminds me of one potential niggle, I've been meaning to address - @mborland would it be simple to define BOOST_MATH_STANDALONE automatically when Boost is not present, as in:  
  
```  
#ifdef __has_include  
#if !__has_include(<boost/config.hpp>)  
#  define BOOST_MATH_STANDALONE  
#endif  
#endif  
```  
  
We would need a central place for that, which we know will always be #included though.  boost/math/tools/config.hpp is the obvious choice, but I haven't checked that every header that uses BOOST_MATH_STANDALONE includes it.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-04-23 13:03:10 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825643577  

> do you have BOOST_MATH_STANDALONE defined?  
  
Ooops. I forgot again. Thank you.  
  
This is really great. How cool is that? I can cross over MSVC-builds, GCC builds with just including "math". Awes!  
  
![grafik](https://user-images.githubusercontent.com/2404721/115874815-db5d7a80-a444-11eb-852d-be855ae26c25.png)

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2021-04-23 13:30:37 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825659936  

Hi @jzmaddock and hi @mborland so the metal build is getting very close to being able to be used _off the rack_.  
  
1) In a test involving `tgamma` and `cbrt` (selected on purpose due to pre-main init of Bernoulli numbers, we find that the true bare metal system will not have `std::mutex`, as well it should not for a pure single-core metal build.  
  
2) Need to deactivate something with exceptions.  
  
I will _mock up_ some fake mutex dummies and move forward on the mutex issue. That first issue looks like shown below. After more progress, i will report with tangible steps suggested from my side...  
  
![grafik](https://user-images.githubusercontent.com/2404721/115878300-ae12cb80-a448-11eb-851f-232f482705ab.png)

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2021-04-23 14:04:59 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825682653  

This is fantastic...  
  
[This little bit of cloning and bootstrapping](https://github.com/ckormanyos/real-time-cpp/blob/c8ddc38a7a0ba09872ab6d752e72fd593cbe88b2/.github/workflows/real-time-cpp.yml#L256) is the only stuff needed for me to use `cbrt` and `tgamma` in a non-related-to-Boost project.  
  
It is not fully on the metal yet, that particular part. But this work with STANDALONE has been truly great! It's a new world of flexibility and mobility.  
  
Thx to all for going at this so vigorously and keenly!

---

## Comment 15

> Username: jzmaddock  
> Created_at: 2021-04-23 15:23:48 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825733731  

Boost.Config has macros for no threads/exceptions, not sure how well we support them... as with everything, if it's not regularly tested then you forget and stuff drifts :(   If you can suggest a gcc configuration we can easily install on CI then we should probably get that tested, then it's just a case of figuring out when to turn on the appropriate macros.  Possibly one intermediate option might be "standalone, but with Boost.Config"?

---

## Comment 16

> Username: mborland  
> Created_at: 2021-04-23 15:38:49 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825743226  

> We would need a central place for that, which we know will always be #included though. boost/math/tools/config.hpp is the obvious choice, but I haven't checked that every header that uses BOOST_MATH_STANDALONE includes it.  
  
`boost/math/tools/config.hpp` is not included in every header that uses `BOOST_MATH_STANDALONE`. It would not be unreasonable to add your config step in there, but it would introduce an unneeded dependency on Boost.Config for several files when not using standalone mode.

---

## Comment 17

> Username: mborland  
> Created_at: 2021-04-23 16:18:27 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825767619  

It looks like I axed the support @ckormanyos is looking for in [this commit](https://github.com/boostorg/math/commit/9c243dbb4901a703cedded4b8f99e57d1d755089#diff-a2647b44bc6b72854346776290efb8be0986d097bb17278ff3147f1fff332f95). Can you try locally adding those branches back in and see if it works? I would just need to implement a standalone version of `BOOST_HAS_THREADS` if it is in fact what you need.

---

## Comment 18

> Username: jzmaddock  
> Created_at: 2021-04-23 17:15:41 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825799537  

> boost/math/tools/config.hpp is not included in every header that uses BOOST_MATH_STANDALONE. It would not be unreasonable to add your config step in there, but it would introduce an unneeded dependency on Boost.Config for several files when not using standalone mode.  
  
That's what I wondered, how about a boost/math/tools/is_standalone.hpp ?

---

## Comment 19

> Username: mborland  
> Created_at: 2021-04-23 19:37:37 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825877096  

> That's what I wondered, how about a boost/math/tools/is_standalone.hpp ?  
  
That was my thought. Something lightweight that is effectively your snippet in its own header. I'll work on that and thread detection tomorrow.

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2021-04-23 20:32:13 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825910046  

The bare metal build involving `cbrt`and `tgamma` builds when I recklessly, and without dedicated thought for content remove certain code sequences including:  
- I/O streaming in `error_handling.hpp`,  
- the use of `throw` in `throw_exception.hpp`,  
- and the use of `std::mutex` when creating/appending table(s) of Bernoulli numbers.

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2021-04-23 20:36:53 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825912153  

>  try locally adding those branches back in and see if it works?  
  
@mborland Yes I see that. I will take a closer look. The removed code sequences are somewhat familiar to me from way back when John made the initial drive to get Bernoulli number table(s) initialized.  
  
A rather trivial workaround would be to leave the code as-is (now in develop) and simply eliminate the use of `std::mutex`.  
  
I can try a few options and report back on my findings.

---

## Comment 22

> Username: ckormanyos  
> Created_at: 2021-04-23 20:46:57 UTC  
> Updated_at: 2021-04-23 21:03:33 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-825916686  

> suggest a gcc configuration we can easily install on CI then we should probably get that tested  
  
Yes @jzmaddock, in recent times, I have been using a GCC `arm-none-eabi` in CI on GHA in Ubuntu. It is version 9.2.1 and it handles quite well c++11, 14, 17, 2a. It can be easily fetched with sudo apt install. A sample is shown [here](https://github.com/ckormanyos/real-time-cpp/runs/2420281463?check_suite_focus=true#step:4:19).  
  
Another thing I have been doing with help from @gdobato (thanks Gabriel!) is directly getting the quarter 4 version of ARM GNU tools directly from its tar ball for a more modern version of GCC `arm-none-eabi` 10. This one harmonizes better with QEMU. Some other tools are then fetched with wget, a bit of python scripting runs QEMU, etc. Some of that is around [here](https://github.com/ckormanyos/real-time-cpp/runs/2420285432?check_suite_focus=true#step:5:1).  
  
In fact, you can see QEMU running successfully on a Cortex M4 target on GHA (in my unrelated project) [here](https://github.com/ckormanyos/real-time-cpp/runs/2420285432?check_suite_focus=true#step:6:29)  
  
There are several good options here and I am in the process of figuring out some stuff like getting results out of QEMU, how to harmonize with b2, etc. Still have a ways to go, but getting closer.

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2021-04-24 06:42:08 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-826044827  

> mutex dummies and move forward on the mutex issue  
  
On the mutex issue in Bernoulli numbers, we already have a run-time decision to use the unchecked tables for small index.  
  
I was wondering if that might be able to be made a compile-time decision to stick with unchecked Bernoulli.  
  
[This line](https://github.com/boostorg/math/blob/4083f0a3989ce171ac88af3e837bb7594595a3d6/include/boost/math/special_functions/bernoulli.hpp#L49) reminded me of that particular area of the code.

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2021-10-22 12:57:24 UTC  
> Updated_at: 2021-10-22 13:01:38 UTC  
> Url: https://github.com/boostorg/math/pull/578#issuecomment-949606943  

This PR is being closed with no activities required.  
  
The wonderful standalone feature enables bare metal builds. Heavy-weight code amounts from error handling could be eliminated with a compiler switch in the future.  
  
See also: [this PR in real-time-cpp](https://github.com/ckormanyos/real-time-cpp/pull/151)  
  
Cc: @jzmaddock and @mborland

---
