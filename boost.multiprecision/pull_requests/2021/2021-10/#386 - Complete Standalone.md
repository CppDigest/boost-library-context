# #386 Complete Standalone [Merged]

> Username: mborland  
> Created at: 2021-10-23 06:37:06 UTC  
> Updated at: 2021-12-11 14:13:44 UTC  
> Merged at: 2021-12-10 07:10:39 UTC  
> Closed at: 2021-12-10 07:10:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386  

Consolidates all of the open standalone based PRs for testing. Big ticket item in this one not reflected in the other PRs is `boost.lexical_cast` workarounds. Once all the current tests pass I will add a standalone test-suite to the Jamfile.

---

## Comment 1

> Username: mborland  
> Created_at: 2021-11-27 12:05:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-980550000  

@jzmaddock, @ckormanyos, and @NAThompson this is clean and should be ready for review. If there's anything else that should be added to the standalone test-suite let me know.

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2021-11-27 13:12:11 UTC  
> Updated_at: 2021-11-27 13:14:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-980603811  

> ready for review  
  
Hi @mborland this is phenomenal. Many thanks.  
  
What I checked worked fantastically.  
  
I checked locally, with great success on a rather detailed project needing both `Boost.Math` and `Boost.Multiprecision`. For math, I used develop branch of modular-boost. For Multiprecision, I used your standalone branch in your repo directly. The only other dependency that my particular project has is `<boost/version.hpp>`, which I retrieved from its directory.  
  
I checked intensively on `cpp_dec_float` and `cpp_bin_float`, but only really these two. In another un-named project, I checked the standalone nature of multiprecision's integer types.  
  
I have not checked rational types, nor any `float128`, nor complex stuff at the moment in standalone Multiprecision.  
  
Cc: @jzmaddock and @NAThompson

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2021-11-28 13:40:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-981087548  

Hi @mborland I was doing some further testing of standalone Multiprecision today.  
  
I'm not entirely sure, but I did find what appear to be dependencies on `Boost.Core`. These take the form of uses of `boost::lazy_enable_if_c` at lines such as [these](https://github.com/mborland/multiprecision/blob/d77c23842b03a3f62ff17ad36234848a4cdb3a1f/include/boost/multiprecision/detail/default_ops.hpp#L2260) and others.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-11-28 14:11:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-981092411  

> Hi @mborland I was doing some further testing of standalone Multiprecision today.  
>   
> I'm not entirely sure, but I did find what appear to be dependencies on `Boost.Core`. These take the form of uses of `boost::lazy_enable_if_c` at lines such as [these](https://github.com/mborland/multiprecision/blob/d77c23842b03a3f62ff17ad36234848a4cdb3a1f/include/boost/multiprecision/detail/default_ops.hpp#L2260) and others.  
  
Good catch. Removed in the latest commit.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2021-11-28 14:32:33 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-981095664  

Hi @mborland sorry about finding these so late, but I find a couple instances each of...  
- `remove_reference`  
- `remove_cv`  
- `is_convertible`  
  
*without* being in namespace `std`. These used need `std::`. I will try to make a complete list. A bunch in `number.hpp` maybe elsewhere too

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2021-11-28 14:36:02 UTC  
> Updated_at: 2021-11-28 14:37:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-981096168  

Hi @mborland also  
  
- `cpp_int.hpp`, line 936 uses `boost::uint_t<N>::least`.  
- `traits/is_byte_container.hpp` line 36 uses `boost::false_type`  
- `integer.hpp` has a few instances of unqualified `is_integral`

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2021-11-30 16:35:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-982808135  

Hi @mborland thanks for this. I keep finding some.  
  
This time, I put my changes in the boost:standalone_chris branch.  
  
Could you please take a look?  
  
With that, we have `cpp_int` compiling on MSVC. I still need to look at bare metal GCC/clang and I have not looked at rational yet either. i find that `cpp_dec_float` and `cpp_bin_float` are OK for standalone.

---

## Comment 8

> Username: mborland  
> Created_at: 2021-12-01 11:18:35 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-983540849  

> Hi @mborland thanks for this. I keep finding some.  
>   
> This time, I put my changes in the boost:standalone_chris branch.  
>   
> Could you please take a look?  
>   
> With that, we have `cpp_int` compiling on MSVC. I still need to look at bare metal GCC/clang and I have not looked at rational yet either. i find that `cpp_dec_float` and `cpp_bin_float` are OK for standalone.  
  
Thanks for the edits. I merged them into this branch after testing locally.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2021-12-01 13:50:45 UTC  
> Updated_at: 2021-12-01 13:51:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-983660003  

Hi @mborland it compiler locally on MSVC for a few limited tests on `cpp_int` in standalone mode.  
  
This keeps getting better and better, closer and closer.  
  
I noticed some of my changed logic in the Pre-processor slipped into that commit. That was on my side a mistake, as I had not intended these changes, I was merely testing them. I think we should revert these Pre-processor lines. Sorry. My bad.  
  
I see a small problem on CI in `misc`test suite on clang 9.3 on Darwin (i believe) but i can not decipher this. I can not figure out why.

---

## Comment 10

> Username: mborland  
> Created_at: 2021-12-01 15:19:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-983744794  

> I see a small problem on CI in `misc`test suite on clang 9.3 on Darwin (i believe) but i can not decipher this. I can not figure out why.  
  
I don't see anything obvious, but the change to pre-processor logic would have impacted it. Latest commit reverts the pre-processor changes and has more minor changes.

---

## Comment 11

> Username: mborland  
> Created_at: 2021-12-02 11:22:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984537088  

@ckormanyos We are now back to green on CI. Please let me know if you run into anything else.

---

## Comment 12

> Username: ckormanyos  
> Created_at: 2021-12-02 12:01:50 UTC  
> Updated_at: 2021-12-02 12:03:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984563689  

> We are now back to green on CI. Please let me know if you run into anything else.  
  
Hi @mborland sorry about the delayed response. I have a report of larger scope. I took the opportunity to embed a simple multiprecision calculation on a standalone bare-metal embedded controller system.  
  
Environment ARM(R) Cortex-M4F, STM32F446 Nucleo board, GCC 11.2.0 (self-built compiler version). Benchmark code similar to [this](https://github.com/ckormanyos/real-time-cpp/blob/master/ref_app/src/app/benchmark/app_benchmark_boost_multiprecision_sqrt.cpp).  
  
Here is my report:  
  
1. Bottom line... We are definitely going in the right direction and I am able to perform square root (and cube root) calculations, 101 digits _on the metal_. This is very nice.  
2. Starting point: I synchronized your `mborland:standalone` branch and essentially copied the current state of your branch one-to-one into my `boost:standalone_chris` branch.  
3. To this, I further added some lines, and commented out some others.  
4. I find that there are problems with some of the macros for exceptions. For some reason, I'm not sure if these were even working for my embedded compiler.  
5. I concentrated on 51 and 101 digit calculations with `cpp_dec_float`. This file needs the inclusion of `<ios>`. I also found 3 instances of `boost::lexical_cast` in the `to_string` method.  
6. I added a few casts to remove a bunch of warnings conversion, sign conversion, warn for able to use `nullptr`, etc.  
7. I rewrote the logic of `BOOST_WORKAROUND` macro, as I believe this thing might actually be missing. Not sure but we need to figure this out.  
  
So we actually need to look at my branch and find out the best way to properly integrate these changes. Simply using `std::atoi` in place of `lexical_cast` is not refined enough. I also simply unabatedly refactored lots of preprocessor logic. This must be done with more refinement and attention to detail.  
  
As a next step, I will put `cpp_bin_float` onto the metal.  
  
  
  
  
I ran across a whole bunch of tiny details.

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2021-12-02 12:04:54 UTC  
> Updated_at: 2021-12-02 12:06:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984565929  

@mborland and @jzmaddock and @NAThompson my previous detailed report above expresses how fantastic your efforts have turnout out --- providing fantastic potential new ability to have fully standalone Math and Multiprecision. This is truly phenomenal, and, although a bit of work remains, it is really getting there.  
  
Thanks for getting into this.

---

## Comment 14

> Username: mborland  
> Created_at: 2021-12-02 17:39:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984850223  

@ckormanyos When you are building with standalone are you providing a copy of Boost.Config? Because config has no dependencies a decision was made to make it the one requirement to use multiprecision in standalone mode. Ultimately the standalone multiprecision and config will get packaged together so that this is not a concern.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2021-12-02 18:07:51 UTC  
> Updated_at: 2021-12-02 18:09:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984871646  

> When you are building with standalone are you providing a copy of Boost.Config?  
  
Hi @mborland these are the exact include directories that I used with GCC 11.2 `arm-none-eabi`.  
  
```C  
TGT_INCLUDES  = -IC:/boost/modular_boost/boost/libs/multiprecision/include        \  
                -IC:/boost/modular_boost/boost/libs/math/include                  \  
                -IC:/boost/modular_boost/boost/libs/config/include  
```  
  
In this include set, I am using my own `boost:standalone_chris` branch as it is checked in at the moment.  
  
I intend to set up a GCC test on a more traditional `x86_64` flavor using exactly these dependencies and see if I am getting some `arm-none-eabi` phenomena or something else.

---

## Comment 16

> Username: ckormanyos  
> Created_at: 2021-12-02 18:51:30 UTC  
> Updated_at: 2021-12-02 18:54:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984904067  

Hi @mborland the following program eliminates extraneous dependencies and brings certain potential issues into focus.  
  
I compiled with GCC `x86_64-pc-linux-gnu` on WSL using either of these two commands:  
  
USE MY Branch:  
  
```C  
// In this first case we use my branch  
// g++ -Wall -Wextra -Os -march=native -std=c++11 -I/mnt/c/boost/modular_boost/boost/libs/multiprecision/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
```  
  
OR: Use Matt's branch (and get compiler errors/warnings) --- now isolated and findable.  
  
```C  
// In this second case, we use Matt's branch.  
// g++ -Wall -Wextra -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
```  
  
Here is the test program  
  
```C  
///////////////////////////////////////////////////////////////////////////////  
//  Copyright Christopher Kormanyos 2021.  
//  Distributed under the Boost Software License,  
//  Version 1.0. (See accompanying file LICENSE_1_0.txt  
//  or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
/*  
TGT_INCLUDES  = -IC:/boost/modular_boost/boost/libs/multiprecision/include        \  
                -IC:/boost/modular_boost/boost/libs/math/include                  \  
                -IC:/boost/modular_boost/boost/libs/config/include  
*/  
  
// Include patsh for MSVC  
//C:/boost/modular_boost/boost/libs/multiprecision/include;C:/boost/modular_boost/boost/libs/math/include;C:/boost/modular_boost/boost/libs/config/include;  
  
// We will now build on Windows Subsystem for LINUX (WSL2).  
// In this first case we use my branch  
// cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
// g++ -Wall -Wextra -Os -march=native -std=c++11 -I/mnt/c/boost/modular_boost/boost/libs/multiprecision/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
  
// We will now build on Windows Subsystem for LINUX (WSL2).  
// In this second case, we use Matt's branch.  
// cd /mnt/c/Users/User/Documents/Ks/PC_Software/Test  
// g++ -Wall -Wextra -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
  
#if !defined(BOOST_MP_STANDALONE)  
#define BOOST_MP_STANDALONE  
#endif  
  
#if !defined(BOOST_MATH_STANDALONE)  
#define BOOST_MATH_STANDALONE  
#endif  
  
#if !defined(BOOST_NO_RTTI)  
#define BOOST_NO_RTTI  
#endif  
  
#if !defined(BOOST_DISABLE_THREADS)  
#define BOOST_DISABLE_THREADS  
#endif  
  
#if !defined(BOOST_NO_EXCEPTIONS)  
#define BOOST_NO_EXCEPTIONS  
#endif  
  
#if !defined(BOOST_MATH_DISABLE_ERROR_HANDLING)  
#define BOOST_MATH_DISABLE_ERROR_HANDLING  
#endif  
  
#define BOOST_MATH_PROMOTE_FLOAT_POLICY false  
  
#include <boost/multiprecision/cpp_dec_float.hpp>  
  
namespace detail  
{  
  template<typename NumericType>  
  bool is_close_fraction(const NumericType a,  
                         const NumericType b,  
                         const NumericType tol = NumericType(std::numeric_limits<NumericType>::epsilon() * NumericType(100)))  
  {  
    using std::fabs;  
  
    const NumericType ratio     = fabs(NumericType((NumericType(1) * a) / b));  
  
    const NumericType closeness = fabs(NumericType(1 - ratio));  
  
    return (closeness < tol);  
  }  
}  
namespace local  
{  
  using big_float_type =  
    boost::multiprecision::number<boost::multiprecision::cpp_dec_float<101, std::int32_t, void>,  
                                  boost::multiprecision::et_off>;  
  
  const big_float_type  
    control  
    (  
      "31.628046625318695665073052332494419076141092173717691871017272913161597775345210805036251680627035384"  
    );  
}  
  
bool run_boost_multiprecision_sqrt()  
{  
  using local::big_float_type;  
  
  // N[Sqrt[3001/3], 101]  
  // 31.628046625318695665073052332494419076141092173717691871017272913161597775345210805036251680627035384  
  
  const big_float_type big_float_arg    = big_float_type(3001) / 3;  
  const big_float_type big_float_result = sqrt(big_float_arg);  
  
  using local::control;  
  
  const bool app_benchmark_result_is_ok = detail::is_close_fraction(control, big_float_result);  
  
  return app_benchmark_result_is_ok;  
}  
  
int main()  
{  
  bool result_is_ok = true;  
  
  for(unsigned i = 0U; i < 64U; ++i)  
  {  
    result_is_ok &= run_boost_multiprecision_sqrt();  
  }  
  
  return result_is_ok ? 0 : -1;  
}  
```

---

## Comment 17

> Username: jzmaddock  
> Created_at: 2021-12-02 19:23:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-984931621  

Apologies for not following the discussion closely - just a quick note to say that to test standalone mode (and that we haven't missed anything), the test_arithmetic_*.cpp tests plus maybe test_sf_import_c99.cpp are designed to pretty much "instantiate everything".  There are a few external Boost dependencies, but mostly I hope these can just be #if'ed out in standalone mode since we don't need to test say boost::hash.

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2021-12-02 21:20:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985009807  

> to test standalone mode (and that we haven't missed anything), the test_arithmetic_*.cpp tests plus maybe test_sf_import_c99.cpp are designed to pretty much "instantiate everything"  
  
Ok thanks John. Understood.

---

## Review 19 [Commented]

> Username: ckormanyos  
> Created_at: 2021-12-02 21:22:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-822032707  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
1947 |+          else
1948 |+          {
1949 |+             static_assert(sizeof(exponent_type) == 1, "Can not use this exponent type in standalone mode. Please de-activate and try again");
```

> Username: ckormanyos  
> Created_at: 2021-12-02 21:22:12 UTC  
> Updated_at: 2021-12-02 21:22:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r761474785  

Hi Matt. I believe in this particular change in line 1949, you might mean?  
  
```C  
static_assert(sizeof(exponent_type) > 1, "...");  
```

> Username: mborland  
> Created_at: 2021-12-03 10:17:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r761809098  

This is designed to fail if the else branch is hit.


---

## Comment 20

> Username: ckormanyos  
> Created_at: 2021-12-02 21:25:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985013123  

Thanks again Matt, I am tracking down some of the remaining phenomena exposed in my standalone test. There are some existing issues that I'll try to localize a bit better. I will report when I have clear descriptions.  
  
Cc: @mborland

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2021-12-03 08:02:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985295739  

> tracking down some of the remaining phenomena exposed in my standalone test.  
  
In my `boost:standalone_chris` branch I've commited a minimal set of changes from `mborland:standalone` to get `cpp_dec_float` working in a very simple test on GCC truly standalone either on x86_64 or on the ARM(R) metal. These changes are minimal compared with Matt's current branch. But there are a few nuggets.  
  
In one file, there is missing `CHAR_BITS` and `INT_MAX`, so I added include of `<climits>`. I also added a `void`-statement to a closing macro to eliminate warnings shown by `-Wall -Wextra`, which is what we support at the moment.  
  
My biggest source of confusion is that, try as i may, i do not understand why `BOOST_WORKAROUND` is not working. So I reworked the logic of that where the GCC compilers errored-out on it.  
  
As a next step, I will look into `cpp_bin_float` and maybe the tests that john suggested above.  
  
Cc: @mborland and @jzmaddock

---

## Comment 22

> Username: mborland  
> Created_at: 2021-12-03 10:19:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985397956  

>   
  
I will take a look at your branch. For the testing I have separated out a number tests names `standalone_*` that include the arithmetic tests. They can be run as a test suite using `../../../b2 standalone`.

---

## Comment 23

> Username: ckormanyos  
> Created_at: 2021-12-03 14:07:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985548612  

Hi @mborland here is another little batch of change requests for the next iteration whenever we/you get around to it...  
  
Enclose [this line](https://github.com/mborland/multiprecision/blob/cca570d213a2b5a5478420257e8b9d0d2af9c56e/include/boost/multiprecision/cpp_dec_float.hpp#L1950) and [this line](https://github.com/mborland/multiprecision/blob/cca570d213a2b5a5478420257e8b9d0d2af9c56e/include/boost/multiprecision/cpp_dec_float.hpp#L2122) in:  
  
```  
#if !defined(BOOST_NO_EXCEPTIONS)  
// ...  
#endif  
```  
  
There are `lexical_cast` instantiations [here](https://github.com/mborland/multiprecision/blob/cca570d213a2b5a5478420257e8b9d0d2af9c56e/include/boost/multiprecision/detail/default_ops.hpp#L1083) and [here](https://github.com/mborland/multiprecision/blob/cca570d213a2b5a5478420257e8b9d0d2af9c56e/include/boost/multiprecision/detail/default_ops.hpp#L1116)  
  
We are at GCC 5 or above, so suggest to remove preprocessor logic entirely [here](https://github.com/mborland/multiprecision/blob/cca570d213a2b5a5478420257e8b9d0d2af9c56e/include/boost/multiprecision/detail/number_compare.hpp#L129)

---

## Comment 24

> Username: ckormanyos  
> Created_at: 2021-12-03 16:43:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985667629  

> Enclose... `#if !defined(BOOST_NO_EXCEPTIONS)`  
  
Oops sorry, Matt, I forgot, should those two spots be better as `BOOST_MP_THROW_EXCEPTION(...)` ?

---

## Comment 25

> Username: mborland  
> Created_at: 2021-12-03 18:02:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985720948  

>   
  
Yes; `BOOST_MP_THROW_EXCEPTION` is effectively the same but less verbose.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2021-12-04 09:27:08 UTC  
> Updated_at: 2021-12-04 09:28:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985997965  

Hi @mborland good finds on `cpp_bin_float`.  
  
An issue is still bothering me with `BOOST_WORKAROUND(...)`  
  
When I compile any dec-float or bin-float standalone stuff with something like:  
  
```C  
g++ -Wall -Wextra -Os -march=native -std=c++11 -I/mnt/c/boost/boost_multiprecision_standalone/include -I/mnt/c/boost/modular_boost/boost/libs/math/include -I/mnt/c/boost/modular_boost/boost/libs/config/include ./test.cpp -o test.exe  
```  
Here `math` and `config` are taken from their respective develop branches of modular-boost, whereas `multiprecision` is your current standalone branch.  
  
I end up with a bunch of errors resulting from the macro `BOOST_WORKAROUND(...)`, as shown below.  
  
Matt, do you get such errors? Or is maybe something wrong with my assumptions or my environment?  
  
The macro is not being comprehended by the compiler.  
  
  
```  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/number.hpp:12,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:406:22: error: missing binary operator before token "("  
  406 | #if !BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
      |                      ^  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:533:22: error: missing binary operator before token "("  
  533 | #if !BOOST_WORKAROUND(BOOST_MSVC, < 1900)  
      |                      ^  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/default_ops.hpp:2102,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/generic_interconvert.hpp:9,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/number.hpp:12,  
                 from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/functions/pow.hpp:712:21: error: missing binary operator before token "("  
  712 | #if BOOST_WORKAROUND(BOOST_MSVC, < 1800)  
      |                     ^  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/detail/functions/pow.hpp:729:21: error: missing binary operator before token "("  
  729 | #if BOOST_WORKAROUND(BOOST_MSVC, < 1800)  
      |                     ^  
In file included from /mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/cpp_dec_float.hpp:29,  
                 from ./test.cpp:61:  
/mnt/c/boost/boost_multiprecision_standalone/include/boost/multiprecision/number.hpp:1054:21: error: missing binary operator before token "("  
 1054 | #if BOOST_WORKAROUND(BOOST_MSVC, < 1900) || (defined(__apple_build_version__) && BOOST_WORKAROUND(__clang_major__, < 9))  
      |                     ^  
```

---

## Comment 27

> Username: jzmaddock  
> Created_at: 2021-12-04 09:36:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-985999087  

I wonder if we're relying on an implicit #include of boost/config/workaround.hpp ?

---

## Comment 28

> Username: mborland  
> Created_at: 2021-12-04 10:18:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986004196  

> I wonder if we're relying on an implicit #include of boost/config/workaround.hpp ?  
  
I expect that's the case since I don't see those errors in the test suite locally or CI. Currently retooling a version of the arithmetic tests to strip out boost dependencies to avoid missing these implicit includes.

---

## Comment 29

> Username: ckormanyos  
> Created_at: 2021-12-04 10:23:13 UTC  
> Updated_at: 2021-12-04 10:24:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986004662  

>  expect that's the case since I don't see those errors in the test suite locally or CI.  
  
Thanks Matt. That is understood.  
  
My concern is, however, that something is parasitically being hidden within the larger context of Boost stuff being included in the tests and CI.  
  
My question is more direct. Could you please make a little test program, maybe select a local installation of GCC. Perhaps use a little program similar to mine above, and *only* provide to the compiler something like the following include paths?  
  
```  
-I/mnt/c/boost/boost_multiprecision_standalone/include  
-I/mnt/c/boost/modular_boost/boost/libs/math/include  
-I/mnt/c/boost/modular_boost/boost/libs/config/include  
```  
  
Then I really would like to know if you get errors --- like I do --- on `BOOST_WORKAROUND(...)`.  
  
This one has me scratching my noggin...

---

## Comment 30

> Username: mborland  
> Created_at: 2021-12-04 10:29:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986005331  

@ckormanyos Will do. I think we need something analogous to the include tests that math has. That's where I caught a ton of similar issues in standalone math development.

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2021-12-04 10:48:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986007671  

>  I think we need something analogous to the include tests that math has.   
  
Yes. That seems like a really good idea.  
  
You also need to ensure that some kind of a relic of a boost is not hanging around in `/usr/include` on your `*nix` system, as this is often the case.  
  
I am being so direct and unrelenting on this matter since I am actually somewhat sure that there is an issue with that `BOOST_WORKAROUND(...)` mechanism on a true naked standalone compile system.

---

## Comment 32

> Username: ckormanyos  
> Created_at: 2021-12-04 12:30:06 UTC  
> Updated_at: 2021-12-04 12:31:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986019769  

Another issue I've run across. My GCC 11.2 `arm-none-eabi` metal compiler got the `#pragma` warning that is written [here](https://github.com/mborland/multiprecision/blob/f54db087207398ed9982decfe64809411e5ae467/include/boost/multiprecision/detail/number_base.hpp#L99):  
  
I was thinking of an alternate logic which does a better job of isolating: MinGW, with GCC below GCC 9, yet when thread-local is-active...  
  
... and I've come up with this:  
  
```C  
#if !defined(BOOST_NO_CXX11_THREAD_LOCAL)  
# if((defined(__GNUC__) && defined(__MINGW32__)) && (__GNUC__ < 9) && !defined(__clang__))  
   // Thread local storage:  
   // Note fails on GCC Mingw below version 9, see https://sourceforge.net/p/mingw-w64/bugs/527/  
#  pragma GCC warning "thread_local on mingw is broken, please use MSys mingw gcc-9 or later, see https://sourceforge.net/p/mingw-w64/bugs/527/"  
#  define BOOST_MP_THREAD_LOCAL  
# else  
# define BOOST_MP_THREAD_LOCAL thread_local  
# endif  
#else  
#define BOOST_MP_THREAD_LOCAL  
#endif  
```  
  
Thoughts?  
  
Cc: @jzmaddock and @mborland

---

## Comment 33

> Username: jzmaddock  
> Created_at: 2021-12-04 12:40:58 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986021130  

Would that be simpler to understand as:  
  
```  
#if defined(BOOST_NO_CXX11_THREAD_LOCAL)  
#define BOOST_MP_THREAD_LOCAL  
#elif !(defined(__MINGW32__) && (__GNUC__ < 9) && !defined(__clang__))  
#define BOOST_MP_THREAD_LOCAL thread_local  
#define BOOST_MP_USING_THREAD_LOCAL  
#else  
#pragma GCC warning "thread_local on mingw is broken, please use MSys mingw gcc-9 or later, see https://sourceforge.net/p/mingw-w64/bugs/527/"  
#define BOOST_MP_THREAD_LOCAL  
#endif  
```  
  
Do we need to worry about BOOST_NO_CXX11_THREAD_LOCAL being set?  I assume there are no threads at all on this platform?

---

## Comment 34

> Username: ckormanyos  
> Created_at: 2021-12-04 13:34:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986028138  

> assume there are no threads at all on this platform?  
  
Correct. And wheter you use it or not on this particular platform, the toolchain is built without threads. This means that `thread_local` will be _known_ to the compiler, but have no meaning.  
  
My real point was that my ARM compiler was hitting that `#pragma` warning, and I did not think that was good.  
  
@jzmaddock so if you prefer that logic above, that's fine with me. I would, however, recommend `(defined(__GNUC__) && (__GNUC__ < 9))` when checking the `__GNUC__` version --- perhaps this is simply out of habit on my side, perhaps there is possibly `__MINGW__` without `__GNUC__` that I do not know...

---

## Comment 35

> Username: ckormanyos  
> Created_at: 2021-12-04 17:08:35 UTC  
> Updated_at: 2021-12-04 17:08:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986059747  

This is *Awesome* !  
  
I get Matt's branch, pull it off the rack --- no changes needed, ... I put it on the metal for dec-float and bin-float at 101 digits.   
  
No problems whatsoever --- smooth.  
  
Cc: @mborland and @jzmaddock and @NAThompson

---

## Comment 36

> Username: ckormanyos  
> Created_at: 2021-12-04 18:23:50 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986071529  

Hi Matt,  
  
All of the following are in my branch... Squeezing out the a few more trivial chnge requests...  
  
`<cpp_int/checked.hpp>` has 6 instantiations of `boost::integer_traits` that can be switched over to `std::numeric_limits`.  
  
A second optional set of changes regards the GCC warning for zero when it could/should be `nullptr` constant. I found a few of these. These are warnings only and it's really optional to change them.  
  
Still not sure if we found everything, but we are really getting close on `cpp_int`, `cpp_bin_float` and `cpp_dec_float`.

---

## Comment 37

> Username: ckormanyos  
> Created_at: 2021-12-05 08:38:57 UTC  
> Updated_at: 2021-12-05 08:39:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986188357  

Hi Matt, I'm a bit perplexed at why our recent changes seem to lead to test failures in CI. The few changes we made in your last commit seem quite trivial and I had thought they were correct.  
  
Do you see any obvious reason why we get failures on some misc and arithmetic tests?

---

## Comment 38

> Username: ckormanyos  
> Created_at: 2021-12-05 08:57:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986190723  

> see any obvious reason why we get failures on some misc and arithmetic tests  
  
Ummm just a thought ...  
  
maybe it is the nuance that GCC's signed and unsigned version of `__int128` does not have a specialization of `std::numeric_limits`. Some of the failing platforms use 128-bit for the `double_limb_type`.

---

## Comment 39

> Username: mborland  
> Created_at: 2021-12-05 09:06:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986192098  

> > see any obvious reason why we get failures on some misc and arithmetic tests  
>   
> Ummm just a thought ...  
>   
> maybe it is the nuance that GCC's signed and unsigned version of `__int128` does not have a specialization of `std::numeric_limits`. Some of the failing platforms use 128-bit for the `double_limb_type`.  
  
That was the conclusion I came to. There was a specialization provided for `integer_traits<__int128>`

---

## Comment 40

> Username: ckormanyos  
> Created_at: 2021-12-06 06:29:27 UTC  
> Updated_at: 2021-12-06 06:29:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986478627  

Great work, Matt!  
  
For my local tests, `cpp_bin_float`, `cpp_dec_float` and `cpp_int` satisfy standalone, C++11 on MSVC and GCC.  
  
I ran a preliminary check on `boost::multiprecision:float128` and it was also OK. For that type, I needed a reminder to compile with `-std=gnu++11` and link with `-lquadmath`.  
  
- So the big open type now is rational. I do not have such a large experience with rational.  
- We might also look more closely at `skeleton`.  
- I think there might also be some visibility loggers we should look into.  
  
**Question:**  
Before moving forward to these remaining parts, I was wondering if we should merge this large chunk of Matt's standalone work to develop?  
  
Thoughts?  
  
Cc: @mborland and @jzmaddock and @NAThompson

---

## Comment 41

> Username: ckormanyos  
> Created_at: 2021-12-06 06:33:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986480305  

> For my local tests, `cpp_bin_float`, `cpp_dec_float` and `cpp_int` satisfy standalone, C++11 on MSVC and GCC.  
  
Oh. I just realized, in my list above, I had completely forgotten `gmp_float` and `mpfr_float`. I will look into these directly on local tests ASAP.

---

## Comment 42

> Username: ckormanyos  
> Created_at: 2021-12-06 09:52:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-986611582  

Hi @mborland I found one significant change needed for `gmp_float`. It is in my branch.  
  
I admit that I was not exactly sure why the compiler found that a particular `BOOST_ASSERT`. I ended up re-writing, perhaps simplifying about 5 or around 10 lines of code in `detail/number_base.hpp` around lines 1500 or so. Take a look if you agree.  
  
I also added one more zero-to-nullptr and cleaned up0 some column spacing near some variables at one point.  
  
With these changes, I am getting locally good standalone results for dec-float, bin-float, cpp_int, GMP and MPFR.  
  
I also add that we have not performed a deep-dive into the standalone characteristics of TOMMATH.

---

## Comment 43

> Username: mborland  
> Created_at: 2021-12-06 19:08:48 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-987087890  

I don't see anything obvious required to make tommath work. There are instances of lexical cast, but they are all commented out.

---

## Comment 44

> Username: NAThompson  
> Created_at: 2021-12-06 20:31:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-987183780  

So `lexical_cast` seems to be causing all the problems . . . but how does it convert these data structures into text in the first place? I feel like @jzmaddock had to provide an implementation to get it to work at all . . .

---

## Comment 45

> Username: jzmaddock  
> Created_at: 2021-12-07 10:07:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-987766439  

That block of commented out lexical_cast's can be removed.  
  
However, there is still a lexical_cast required as most conversions run through `last_chance_eval_convert_to` inside default_ops.hpp which uses.... lexical_cast.  Basically tommath has conversion to string and not much else.  You may find that other backends will go via `last_chance_eval_convert_to` for some specific types as well :(

---

## Comment 46

> Username: mborland  
> Created_at: 2021-12-07 10:25:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-987781546  

> That block of commented out lexical_cast's can be removed.  
>   
> However, there is still a lexical_cast required as most conversions run through `last_chance_eval_convert_to` inside default_ops.hpp which uses.... lexical_cast.  Basically tommath has conversion to string and not much else.  You may find that other backends will go via `last_chance_eval_convert_to` for some specific types as well :(  
  
Does `last_chance_eval_convert_to` get used a lot? I have it conditionally removed and replaced with an error depending on if standalone is defined or not. If it needs more finesse than that I can sink time into it.

---

## Comment 47

> Username: jzmaddock  
> Created_at: 2021-12-07 17:22:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-988124867  

> Does last_chance_eval_convert_to get used a lot? I have it conditionally removed and replaced with an error depending on if standalone is defined or not. If it needs more finesse than that I can sink time into it.  
  
The way to find out is to make those two methods non-compilable with a static_assert and then try and build all the tests.  
  
Here's the first ones that came up:  
  
number_backend_float_architype -> long double  
gmp_float -> long double  
logged_adaptor<gmp_float> -> long double  
gmp_rational -> long double  
gmp-int -> long double  
tommath_int -> unsigned long long  
tommath_int -> unsigned  
tommath_int -> long double  
  
There are probably more as the compiler will likely bail out after the first few errors, conversions to __int128 and __float128 might be suspect.

---

## Comment 48

> Username: ckormanyos  
> Created_at: 2021-12-09 12:08:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-989793564  

Hi guys, as we progress in Multiprecision, it seems like we are starting to get potential merge conflicts, (or even getting some) around now.  
  
I was wondering if we should merge this good intermediate stand into develop and continue forward from that point?  
  
Thoughts?  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 49

> Username: jzmaddock  
> Created_at: 2021-12-09 13:24:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-989850094  

Might not be a bad idea - I'm trying to wiz through as many bug reports as I can right now, so the potential for conflicts is certainly there.  I'll try and review the diffs later.

---

## Comment 50

> Username: ckormanyos  
> Created_at: 2021-12-09 13:41:29 UTC  
> Updated_at: 2021-12-09 13:43:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-989862872  

> Might not be a bad idea - I'm trying to wiz through as many bug reports as I can right now, so the potential for conflicts is certainly there. I'll try and review the diffs later.  
  
Yeah. I already found two trivial conflicts via git merge. I resolved them  (for me locally in my branch) with Araxis Merge (which I have associated with my Git Merge).  
  
So in the `boost:standalone_chris` branch I have all the changes from Matt plus the merges from `boost:develop`.  
  
I think if @mborland and @jzmaddock could take a look at the diffs, etc., then we should move forward on a merging (Matt's) this PR to develop --- but I'd definitely merge develop into Matt's branch prior to merge-to-develop and try to fix the trivial conflicts if or when they arise locally.  
  
Matt, you could compare my bracn with yours and see the diffs. That might hlep.  
  
Doing this soon will get the bulk of standalone into develop and free the way for finishing standalone and handling other bug fixes for 1.79.

---

## Review 51 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 16:59:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-827955660  

📁 .github/workflows/multiprecision.yml

```diff
 200 |+         working-directory: ../boost-root/libs/config/test
 201 |+       - name: Test
 202 |+         run: ../../../b2 -j2 toolset=$TOOLSET ${{ matrix.suite }} define=CI_SUPPRESS_KNOWN_ISSUES
```

> Username: jzmaddock  
> Created_at: 2021-12-09 16:59:04 UTC  
> Updated_at: 2021-12-09 16:59:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r765982637  

Is this actually running tests standalone?  Looks to be a duplicate of what we already so far?

> Username: mborland  
> Created_at: 2021-12-10 17:49:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766870630  

Yes. It is quite similar, but defines the test suite as only standalone, and I only have it run against g++-11 to reduce the number of tests being added.


---

## Review 52 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 17:26:31 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-827987821  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
 122 |-                                 detail::static_array <std::uint32_t, cpp_dec_float_elem_number>,
 123 |-                                 detail::dynamic_array<std::uint32_t, cpp_dec_float_elem_number, Allocator> >::type;
 127 |+                                 detail::static_array <std::uint32_t, std::uint32_t(cpp_dec_float_elem_number)>,
```

> Username: jzmaddock  
> Created_at: 2021-12-09 17:26:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766005058  

This looks just plain wrong to me - the 2nd template argument should be of type `cpp_dec_float_elem_number`, unless I'm mistaken you've made it type "function returning std::uint32_t".  Maybe I'm missing something because there's no way this should have got through CI?

> Username: jzmaddock  
> Created_at: 2021-12-09 17:30:29 UTC  
> Updated_at: 2021-12-09 17:30:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766008038  

Ugh, my bad, I realise it's a cast now - Doh!  
  
Could we make this a static_cast please for numpties like me ;)

> Username: ckormanyos  
> Created_at: 2021-12-09 18:27:30 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766049028  

> make this a static_cast please  
  
Good idea. Matt in upcoming changes could you please do that?


---

## Review 53 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 17:37:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-827998936  

📁 include/boost/multiprecision/cpp_dec_float.hpp

```diff
1942 |          exp = boost::lexical_cast<exponent_type>(static_cast<const char*>(str.c_str() + (pos + 1u)));
1943 |+          #else
1944 |+          BOOST_IF_CONSTEXPR(std::is_integral<exponent_type>::value)
```

> Username: jzmaddock  
> Created_at: 2021-12-09 17:37:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766013083  

The exponent type is definitely an integral type.

> Username: ckormanyos  
> Created_at: 2021-12-09 18:28:11 UTC  
> Updated_at: 2021-12-09 18:28:12 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766049516  

Correct.

> Username: mborland  
> Created_at: 2021-12-10 17:38:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766863469  

Will the exponent always be a built-in integral type? That was this is trying to catch.

> Username: jzmaddock  
> Created_at: 2021-12-11 11:07:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r767142366  

Yes, we don't support anything else.  64-bit exponents support a crazy-large enough range anyway!


---

## Review 54 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 18:12:51 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-828035985  

📁 include/boost/multiprecision/cpp_int/checked.hpp

```diff
  23 | 
  24 |+ template <typename T>
  25 |+ inline constexpr T type_max(T) noexcept
```

> Username: jzmaddock  
> Created_at: 2021-12-09 18:12:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766039079  

Stupid point of pedantry: the function parameter could be removed here and we could just call `type_max<T>()` instead.


---

## Review 55 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 18:15:08 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-828038233  

📁 include/boost/multiprecision/cpp_int/misc.hpp

```diff
1337 | template <typename T>
1338 |- inline BOOST_CXX14_CONSTEXPR constexpr_gcd(T a, T b) noexcept
1338 |+ inline BOOST_CXX14_CONSTEXPR T constexpr_gcd(T a, T b) noexcept
```

> Username: jzmaddock  
> Created_at: 2021-12-09 18:15:07 UTC  
> Updated_at: 2021-12-09 18:15:08 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766040522  

Good catch!!


---

## Review 56 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 18:44:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-828065995  

📁 test/standalone_test_arithmetic.hpp

```diff
   1 |+ ///////////////////////////////////////////////////////////////
```

> Username: jzmaddock  
> Created_at: 2021-12-09 18:43:59 UTC  
> Updated_at: 2021-12-09 18:44:00 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766060613  

Is it feasible to fold these changes into test_arithmetic.hpp (with some suitable pp-logic)?  
  
I ask, because down the road it would be much too easy to modify one file and not the other.

> Username: mborland  
> Created_at: 2021-12-10 17:44:21 UTC  
> Updated_at: 2021-12-10 17:44:22 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766867477  

In the upcoming PR I am going to re-write this to use only the STL. There are still going to be confounding variables from using boost.test etc.

> Username: jzmaddock  
> Created_at: 2021-12-11 11:10:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r767142655  

There's no Boost.Test usage in the library, there's just a thin testing framework in test/test.hpp.  It does use boost's lightweight_test.hpp, but that should be trivial to emulate as there's not much in there.


---

## Review 57 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 18:45:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-828067331  

📁 test/standalone_test_arithmetic_ab.cpp

```diff
   5 |+ //  LICENSE_1_0.txt or copy at https://www.boost.org/LICENSE_1_0.txt
   6 |+ 
   7 |+ #ifdef _MSC_VER
```

> Username: jzmaddock  
> Created_at: 2021-12-09 18:45:23 UTC  
> Updated_at: 2021-12-09 18:45:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766061583  

Shouldn't this file define BOOST_MP_STANDALONE?


---

## Review 58 [Commented]

> Username: jzmaddock  
> Created_at: 2021-12-09 18:49:37 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/multiprecision/pull/386#pullrequestreview-828071353  

📁 test/standalone_test_arithmetic_tommath.cpp

```diff
  11 |+ #include "standalone_test_arithmetic.hpp"
  12 |+ 
  13 |+ #define BOOST_MP_STANDALONE
```

> Username: jzmaddock  
> Created_at: 2021-12-09 18:49:36 UTC  
> Updated_at: 2021-12-09 18:49:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#discussion_r766064434  

I think these tests may have a problem: they include "standalone_test_arithmetic.hpp" **before** defining BOOST_MP_STANDALONE, but "standalone_test_arithmetic.hpp" will (indirectly) include boost/multiprecision/number.hpp via test.hpp.


---

## Comment 59

> Username: jzmaddock  
> Created_at: 2021-12-09 18:53:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-990130217  

Guys: thanks for all the hard work that's gone into this.  
  
This all looks good to me apart from a few small comments above, and some more work on the testing - as things stand, I'm not convinced that you're really testing things in a full standalone mode.  But that can all be done later.  
  
On a related note: I've just discovered that we're not really testing all the backends on CI due not installing the needed dependencies :(  This PR updates CI as well as fixing the Eigen support bug: https://github.com/boostorg/multiprecision/pull/402

---

## Comment 60

> Username: ckormanyos  
> Created_at: 2021-12-10 07:14:05 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-990674126  

OK I went ahead and merged this strong intermediate stand to develop --- thereby bringing the bulk of standalone into develop.  
  
Many thanks Matt for masterfully pursuing this endeavor which will significantly simplify and streamline the use of Multiprecision for all.  
  
We still need to handle the review comments (at least some of them) from @jzmaddock.  
  
@mborland I will continue to try and keep up with your pace as we finalize standalone.  
  
Cc: @NAThompson.

---

## Comment 61

> Username: ckormanyos  
> Created_at: 2021-12-10 11:36:06 UTC  
> Updated_at: 2021-12-10 11:38:42 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-990896800  

Hi @mborland I found an interesting nuance when running our newly merged develop branch through my own independent CI in one of my unrelated projects.  
  
I find interesting warnings at places like [this](https://github.com/ckormanyos/wide-integer/runs/4481323063?check_suite_focus=true#step:4:49).  
  
In this CI, warnings are treated as errors.  
  
So the story goes like this... The compiler is mentioning that, although there are `noexcept` qualifiers on some code, some of the relevant code actually throws. At first I did not understand what is going on.  
  
But I think the compiler is on to something. If you look [here](https://github.com/boostorg/multiprecision/blob/6eec3d6d285121735c1b678da3466d8b43212050/include/boost/multiprecision/number.hpp#L105), the constructor of `number` is activated for `is_convertible` from `const` `char*`. The constructor subsequently constructs with the assignment operator.  
  
Now this particular constructor is qualified with `noexcept`, but the assignment operator of `cpp_int`, from `const` `char*` for example is allowed to throw. So I actually think that this particular constructor of `number` must either be allowed to throw or we need an additional constructor --- one throwing, the other non-throwing for conversion from `const` `char*`  
  
I do not know why my CI picked this up only now after using `std::` type traits?  
  
Thoughts?  
  
Cc: @jzmaddock

---

## Comment 62

> Username: jzmaddock  
> Created_at: 2021-12-10 15:31:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991069138  

Is that constructor actually noexcept? It really shouldn't be given the declaration.

---

## Comment 63

> Username: jzmaddock  
> Created_at: 2021-12-10 15:37:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991073931  

This works as expected:  
  
```  
   const char* p = "";  
  
   static_assert(!noexcept(boost::multiprecision::uint128_t(p)), "Oops");  
```

---

## Comment 64

> Username: jzmaddock  
> Created_at: 2021-12-10 15:43:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991077954  

There is a mistake in that constructor though: it should verify that default constructing the backend is noexcept as well as testing the assignment.

---

## Comment 65

> Username: ckormanyos  
> Created_at: 2021-12-10 16:52:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991132319  

> Is that constructor actually noexcept? It really shouldn't be given the declaration.  
  
Indeed @jzmaddock I don't really know, now that you mention it. Indeed the syntax has a lot of depth on that constructor.  
  
I discovered this phenomenon newly in the standalone merges when treating warnings as errors via `-Werror`. So whatever i got was simply a warning.  
  
Admittedly, John, I have not fully digested what is or what is not going on, but I think some compilers are taking issue with that constructor. I don't know how to improve it. Or if I should just neglect these warnings?

---

## Comment 66

> Username: jzmaddock  
> Created_at: 2021-12-11 11:06:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991601530  

I don't think we should ever neglect those warnings, but I think I've found the culprit:  
  
```  
   BOOST_MP_FORCEINLINE BOOST_MP_CXX14_CONSTEXPR void negate() noexcept((Checked == unchecked))  
   {  
      BOOST_IF_CONSTEXPR(Checked == checked)  
      {  
         BOOST_MP_THROW_EXCEPTION(std::range_error("Attempt to negate an unsigned type."));  
      }  
      m_data = ~m_data;  
      ++m_data;  
   }  
```  
  
The noexcept specifier is C++11, but the `if constexpr` is C++17, so I'm assuming that in pre-c++17 mode the compiler doesn't spot that the is expression is a constant and assumes that a throw may happen.  
  
BTW, I'm unable to reproduce your warnings with gcc-10.2, but perhaps you could test making that method noexcept only when BOOST_NO_CXX17_IF_CONSTEXPR is _not_ defined?  
  
What I do see when building with -Wall -pedantic is a small blizzard of warnings starting with:  
  
```  
In file included from ../../../boost/multiprecision/detail/number_base.hpp:18,  
                 from ../../../boost/multiprecision/traits/is_variable_precision.hpp:9,  
                 from ../../../boost/multiprecision/detail/precision.hpp:9,  
                 from ../../../boost/multiprecision/number.hpp:11,  
                 from ../../../boost/multiprecision/cpp_int.hpp:15,  
                 from ../../../t.cpp:4:  
../../../boost/multiprecision/traits/std_integer_traits.hpp:31:18: warning: ISO C++ does not support '__int128' fo  
r 'type name' [-Wpedantic]  
   31 | struct is_signed<__int128> : public std::integral_constant<bool, true> {};  
      |                  ^~~~~~~~  
../../../boost/multiprecision/traits/std_integer_traits.hpp:33:27: warning: ISO C++ does not support '__int128' fo  
r 'type name' [-Wpedantic]  
   33 | struct is_signed<unsigned __int128> : public std::integral_constant<bool, false> {};  
      |                           ^~~~~~~~  
../../../boost/multiprecision/traits/std_integer_traits.hpp:35:20: warning: ISO C++ does not support '__int128' fo  
r 'type name' [-Wpedantic]  
   35 | struct is_unsigned<__int128> : public std::integral_constant<bool, false> {};  
```  
  
Which I also think we need to do something about.

---

## Comment 67

> Username: ckormanyos  
> Created_at: 2021-12-11 12:46:19 UTC  
> Updated_at: 2021-12-11 12:47:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991631418  

> I think I've found the culprit  
  
Nice. Good find, John! Thank you very much for looking into this.  
  
> The `noexcept` specifier is C++11, but the if constexpr is C++17, so I'm assuming that in pre-c++17 mode the compiler doesn't spot that the is expression is a constant and assumes that a throw may happen.  
  
> BTW, I'm unable to reproduce your warnings with gcc-10.2, but perhaps you could test making that method `noexcept` only when `BOOST_NO_CXX17_IF_CONSTEXPR` is not defined?  
  
Yes, that is a good suggestion. I will try. Indeed, also I can not reproduce those warnings locally. So I will need to make a branch of Multiprecision and clone that branch when running CI in my unrelated project in order to try this. I will attempt to get that setup running.  
  
> do see when building with `-Wall -pedantic` is a small blizzard of warnings...  
  
I was under the (perhaps subjective) impression that it is not allowed to use `__int128` in combination with `-pedantic` because GCC's signed and unsigned versions of `__int128` do not qualify as ISO C++ type(s). Usually, I compile with `-Wall` `-Wextra` (but not `-pedantic`) when using 128-bit types.  
  
While we are on the topic of warning(s), I would potentially strive for CGG/clang `-Wall` `-Wextra` `-Wconversion` `-Wsign-conversion`. These warnings have been revealing a bunch of stuff that also interests me. I'm not sure if we can achieve `-pedantic` with 128-bit type(s).  
  
As soon as we get a bit more stable, I have a bunch of local work that addresses similar blizzard(s) of warning(s) I encounter when using elevated warning flags. I intend to run a bunch of this work (predominantly involving casts, suffixes like `u`, siwtch-default-cases, etc.) by you guys as we move forward on standalone as we progress toward 1.79.  
  
Cc: @jzmaddock and @mborland

---

## Comment 68

> Username: mborland  
> Created_at: 2021-12-11 14:13:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/386#issuecomment-991658006  

> > I think I've found the culprit  
>   
> Nice. Good find, John! Thank you very much for looking into this.  
>   
> > The `noexcept` specifier is C++11, but the if constexpr is C++17, so I'm assuming that in pre-c++17 mode the compiler doesn't spot that the is expression is a constant and assumes that a throw may happen.  
>   
> > BTW, I'm unable to reproduce your warnings with gcc-10.2, but perhaps you could test making that method `noexcept` only when `BOOST_NO_CXX17_IF_CONSTEXPR` is not defined?  
>   
> Yes, that is a good suggestion. I will try. Indeed, also I can not reproduce those warnings locally. So I will need to make a branch of Multiprecision and clone that branch when running CI in my unrelated project in order to try this. I will attempt to get that setup running.  
>   
> > do see when building with `-Wall -pedantic` is a small blizzard of warnings...  
>   
> I was under the (perhaps subjective) impression that it is not allowed to use `__int128` in combination with `-pedantic` because GCC's signed and unsigned versions of `__int128` do not qualify as ISO C++ type(s). Usually, I compile with `-Wall` `-Wextra` (but not `-pedantic`) when using 128-bit types.  
>   
> While we are on the topic of warning(s), I would potentially strive for CGG/clang `-Wall` `-Wextra` `-Wconversion` `-Wsign-conversion`. These warnings have been revealing a bunch of stuff that also interests me. I'm not sure if we can achieve `-pedantic` with 128-bit type(s).  
>   
> As soon as we get a bit more stable, I have a bunch of local work that addresses similar blizzard(s) of warning(s) I encounter when using elevated warning flags. I intend to run a bunch of this work (predominantly involving casts, suffixes like `u`, siwtch-default-cases, etc.) by you guys as we move forward on standalone as we progress toward 1.79.  
>   
> Cc: @jzmaddock and @mborland  
  
I think the complaints about `__int128` can be suppressed by using `boost::multiprecision::int128_t`. I opened a new PR that will hit the review points and move the discussion out of a closed PR.

---
