# #182 Comba Multiplier [Merged]

> Username: madhur4127  
> Created at: 2020-01-09 08:49:14 UTC  
> Updated at: 2020-08-26 04:43:46 UTC  
> Merged at: 2020-03-09 15:59:13 UTC  
> Closed at: 2020-03-09 15:59:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182  

Comba multiplier is a computer-friendly multiplication routine which is a drop-in replacement for native schoolbook multiplication.  
  
Algorithmic Differences:  
Consider writing the both operands in two lines one below the other.  
  
Naive school book multiplication first multiplies a digit of operand to all the digits of other operands. This can be visualized as a row multiplication where row is the second operand and then these computed rows are added to obtain final answer.  
  
Comba Multiplier can be thought of as a multiplication routine where the digits of answer are computed sequentially. In other words it computes the result column wise first.  
  
For example:   
```  
  2               3  
x 7               8  
--------------------  
  14  37(16+21)   24  
  
ans[0] = 8*3       = 24  
ans[1] = 2*8 + 3*7 = 37  
ans[2] = 2*7       = 14  
```  
  
**Benefits:**  
There are less additions and assignments.  
  
**Subtleness:**  
It might not be evident that how this method will save addition because a compensating addition `overflow += temp < carry` is performed. But taking advantage of hardware this can be reduced to saving the status of Carry Flag to overflow counter using `adc` instruction.  
For more optimisation details kindly see the bugs:  
1. [GCC 93141](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=93141)  
2. [LLVM 44460](https://bugs.llvm.org/show_bug.cgi?id=44460)

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2020-01-14 13:29:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-574174085  

Two quick comments:  
  
* I see many failures in test_cpp_int.cpp when using this (looks like many results are zero when they shouldn't be?).  
* I see no speed up with gcc-minw-8.1 and a very significant slowdown with msvc.  
  
I also tried an alternative "vectorizing" loop-unroll of my own, with rather similar performance results :(

---

## Comment 2

> Username: madhur4127  
> Created_at: 2020-01-17 12:11:33 UTC  
> Updated_at: 2020-01-17 22:49:45 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-575602066  

> I see many failures in test_cpp_int.cpp when using this  
  
Should be working fine now, I guess.  
  
> I see no speed up with gcc-minw-8.1 and a very significant slowdown with msvc  
  
Strange because g++-9 showed 1.2 times speedup when using 40 limbs. However, I also tried comba at higher bit counts (~10,000 bits) and saw a speedup of 35%.  
  
The problem is that the "slower" code is actually very fast and to observe the difference there is a need for a very large number of iterations which is somehow impractical. Hopefully, in the future, we can save ~4 instructions per iteration of the inner loop thereby speeding up the code even in a practical case.  
  
As far as MSVC is concerned, I think those guys have to catch up the existing optimizations of gcc and Clang first :/

---

## Comment 3

> Username: madhur4127  
> Created_at: 2020-01-18 02:45:22 UTC  
> Updated_at: 2020-01-18 02:57:27 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-575859002  

`cpp_int_tests` and `arithmetic_tests` passed but for some reason every test involving `mpfr` fails with this error:  
  
```  
BOOST_DEFINE_MATH_CONSTANT(ln_two, 6.931471805599453094172321214581765680e-01, "6.93147180559945309417232121458176568075500134360255254120680009493393621969694715605863326996418687542001481021e-01")  
   ^  
In file included from ../example/mpfr_snips.cpp:7:0:  
../../../boost/multiprecision/mpfr.hpp:1818:37: note: candidate: template<int N> static const result_type& boost::math::constants::detail::constant_ln_two<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<digits10, AllocationType>, ExpressionTemplates> >::get(const mpl_::int_<N>&) [with int N = N; unsigned int Digits10 = 100u; boost::multiprecision::mpfr_allocation_type AllocateType = (boost::multiprecision::mpfr_allocation_type)1u; boost::multiprecision::expression_template_option ExpressionTemplates = (boost::multiprecision::expression_template_option)1u]  
    static inline const result_type& get(const mpl::int_<N>&)  
                                     ^~~  
../../../boost/multiprecision/mpfr.hpp:1818:37: note:   template argument deduction/substitution failed:  
In file included from ../../../boost/math/special_functions/asinh.hpp:25:0,  
                 from ../../../boost/multiprecision/gmp.hpp:20,  
                 from ../../../boost/multiprecision/mpfr.hpp:11,  
                 from ../example/mpfr_snips.cpp:7:  
../../../boost/math/constants/constants.hpp:260:3: note:   ‘boost::math::constants::construction_traits<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<100u> >, boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::type {aka boost::integral_constant<int, 4>}’ is not derived from ‘const mpl_::int_<N>’  
```  
@jzmaddock, I guess these are somehow unrelated to this PR ? (I can't reproduce this error on my system)

---

## Comment 4

> Username: madhur4127  
> Created_at: 2020-01-19 06:32:19 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-575972192  

The latest commit shows that something broke as far as MPFR is concerned.  
  
This will make builds on `develop` and/or `master` fail from now.  
  
I have a little experience with MPFR but I am working on it.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2020-01-22 06:37:34 UTC  
> Updated_at: 2020-01-22 06:39:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-577031396  

> As far as MSVC is concerned,   
  
Some compiler systems might (but I don't think it matters to MSVC) respond more favorably to:  
```  
carry += static_cast<double_limb_type>(*(pa--) * static_cast<double_limb_type>(*(pb++)));  
```  
  
The very subtle difference shows `32*64->64` mul instead of the implemented `64*64->64` mul.

---

## Comment 6

> Username: madhur4127  
> Created_at: 2020-01-22 11:14:15 UTC  
> Updated_at: 2020-01-22 12:03:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-577133284  

@ckormanyos, If I understood you correctly, you want  
```  
carry += static_cast<double_limb_type>(*(pa--) * static_cast<double_limb_type>(*(pb++)));  
```  
to  be  
```  
carry += static_cast<double_limb_type>(*(pa--) * *(pb++));  
```  
  
Although I didn't observe any difference in assembly ([https://godbolt.org/z/pjHr8N](https://godbolt.org/z/pjHr8N)), but I made the change.

---

## Comment 7

> Username: ckormanyos  
> Created_at: 2020-01-22 19:39:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-577351122  

> @ckormanyos, If I understood you correctly, you want  
  
No. I think I might have made a mistake.  
The parentheses are confusing.  
  
You can consider:  
 ```  
c64 = (uint64_t) (a32 * (uint64_t) b32);  
 ```  
  
You cast either `a32` _or_ `b32` to 64 bit, but do not cast both.  
Then you multiply and cast the result to 64.  
  
If that is what you have already, then I apologize for my reading error.  
  
Kind regards, Chris

---

## Comment 8

> Username: ckormanyos  
> Created_at: 2020-01-22 19:49:47 UTC  
> Updated_at: 2020-01-22 19:51:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-577355772  

Actually, my original comment was correct.  
You need to read these two lines very very carefully.  
  
In the second form, which i recommend you investigate, you notice that  
* `pa` is dereferenced in its 32 bit form as a limb.  
* It is multiplied as 32-bit by the dereferenced `pb`, but `pb` _is_ _casted_ _to_ _64bit_.  
* The result is then casted to 64.  
  
```  
carry += static_cast<double_limb_type>(*(pa--)) * static_cast<double_limb_type>(*(pb++)); // old  
carry += static_cast<double_limb_type>(*(pa--) * static_cast<double_limb_type>(*(pb++))); // what you might consider  
```  
  
Imagine you were working with an 8bit architecture. 64by64 mul would be very time consuming. This old-school technique that i showed you is the pre-step to breaking down and writing in assembly. You coerce the compiler to do 32 times 64 with 64 result, which will be faster. On x64, you may very well notice no diffecence at all. But you are also writing code for x86 and ARM Cortex and other stuff like that.  
  
Be watchful because in the form that is checked in at the moment, you have 32 by 32 with 32 result, subsequently upcasted to 64, which is probably not what you intended.

---

## Comment 9

> Username: ckormanyos  
> Created_at: 2020-01-23 06:18:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-577522861  

> Some checks were not successful  
  
It is mysterious why the CI build failed? There are test failures in `constexpr` functions in one and only one build job. However the i307 build succeeded. The i307 branch experienced merging from develop from almost the same time frame as this branch.  
  
After work I'd like to take a detailed look at the differences between these two branches with something like a directory comparison program. I'll report if I find anything unusual.  
  
Kind regards, Chris

---

## Comment 10

> Username: madhur4127  
> Created_at: 2020-01-24 15:20:11 UTC  
> Updated_at: 2020-01-24 15:36:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578173945  

> Be watchful because in the form that is checked in at the moment, you have 32 by 32 with 32 result, subsequently upcasted to 64, which is probably not what you intended.  
  
> Be watchful because in the form that is checked in at the moment, you have 32 by 32 with 32 result, subsequently upcasted to 64, which is probably not what you intended.  
  
I am sorry, I made a mistake in the comment but code has what is intended. This mistake lead to miscommunication :(  
  
I guess outer cast is not required so I upcasted `pa` instead of your version of `pb`:  
```  
carry += static_cast<double_limb_type>(*(pa--)) * *(pb++); // what you might consider  
```

---

## Comment 11

> Username: madhur4127  
> Created_at: 2020-01-24 16:38:40 UTC  
> Updated_at: 2020-01-24 16:43:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578205890  

> It is mysterious why the CI build failed? There are test failures in constexpr functions in one and only one build job.  
  
Luckily I could reproduce the error with config `TOOLSET=clang COMPILER=clang++-9 CXXSTD=c++17 TEST_SUITE=misc`.  
  
```  
../../../boost/multiprecision/cpp_int/multiply.hpp:337:53: note: cannot refer to element -1 of array of 16 elements in a constant expression  
         carry += static_cast<double_limb_type>(*(pa--)) * (*(pb++));  
```  
  
I guess I have to start with revision of changes to constexpr in `c++17` standard because the tests passes for `c++14` with same config (clang). It baffles me why the test passed for `gcc` if there is a change in standard.

---

## Comment 12

> Username: jzmaddock  
> Created_at: 2020-01-24 18:00:52 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578236058  

We probably should not be taking this branch in constexpr contexts in any case - if you look at the logic for Karatsuba selection you'll see that they're excluded there and you can just copy that logic over.  
  
This is presuming of course, that index -1 is never actually accessed...

---

## Comment 13

> Username: ckormanyos  
> Created_at: 2020-01-24 19:31:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578268823  

> We probably should not be taking this branch in constexpr contexts in any case - if you look at the logic for Karatsuba selection you'll see that they're excluded there and you can just copy that logic over.  
  
Thank you for that clarification. It really makes sense.  
  
Kind regards, Chris

---

## Comment 14

> Username: ckormanyos  
> Created_at: 2020-01-25 11:45:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578399498  

Yes. That is now containing the optimized casting 32/64 that I was mentioning.

---

## Comment 15

> Username: ckormanyos  
> Created_at: 2020-01-25 12:46:01 UTC  
> Updated_at: 2020-01-25 12:49:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578403435  

> I guess outer cast is not required so I upcasted `pa` instead of your version of `pb`:  
  
That is indeed what I was mentioning. I suspect you will notice little or no difference for x64, because there probably will be little or no difference for x64.  
  
If you do generate assembly code for your personal analysis, then you are looking for (in x64) an assembly line that should look something like it resembles:  
  
```  
mul rax, rbx  
```  
  
The processor is here doing 64by64 to (I think 128, maybe 64) mul. And the processor can do no faster than that. I would need to look up the exact opcode... At any rate that is the one you seek for fast integral mul on ia64.  
  
If we ever do the deep dive into assembly programming (which I have done extensively for up to 20 architectures in time epochs gone past), then we will get all into this kind of stuff.  
  
Kind regards, Chris

---

## Comment 16

> Username: madhur4127  
> Created_at: 2020-01-27 09:39:15 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578665858  

> We probably should not be taking this branch in `constexpr` contexts in any case - if you look at the logic for Karatsuba selection you'll see that they're excluded there and you can just copy that logic over.  
  
I think that maybe making it run at compile time using `constexpr` can help produce better assembly as the compiler is aware of the control flow and instructions in the loop. And the benefit of comba multiplier is only when it can beat naive multiplication which is very hard unless the desired assembly is produced for comba multiplier which is not the case with the current compilers.  
  
Considering the performance critical nature of this code, can we let it slide this time? Waiting for your thoughts on this @jzmaddock, @ckormanyos.  
  
The changes look stable to me as of now.

---

## Comment 17

> Username: ckormanyos  
> Created_at: 2020-01-27 19:12:02 UTC  
> Updated_at: 2020-01-27 19:13:56 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578904910  

> I think that maybe making it run at compile time using constexpr can help produce better assembly as the compiler is aware...  
  
> Considering the performance critical nature of this code, can we let it slide this time? Waiting for your thoughts on this @jzmaddock, @ckormanyos.  
  
One possible course of action is, indeed, to let this development rest for the moment. One thing I really like about the architecture of your code is that it very clearly brings the O(N^2) algorithm into focus. This will defnitely allow for detailed (assembly-reading) optimizations as time progresses. Perhaps we will find a way to squeeze that code or put it into an even more optimized form. It is good that you have achieved this clarity of expressing the school-mul algorithm, which was quite a challenging thing to do. Congrats.  
  
In the meantime, we have a whole new dimension in Boost.Multiprecision with the Karatsuba branches. We can switch over to these kinds of studies and investigate countless use cases such as high-precision contants, roots, and many more.  
  
Kind regards, Chris

---

## Comment 18

> Username: ckormanyos  
> Created_at: 2020-01-27 19:18:34 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578907723  

If, on the other hand, you would really like to take the deep assembly dive, that is another option --- although not my favorite option at the moment. If that were to be of interest, I would select 3-4 architectures such as ia64, x86, ARM11, Cortex and dive deep into these, whereby starting with ia64 makes sense due to its widespread applicability in this domain.

---

## Comment 19

> Username: jzmaddock  
> Created_at: 2020-01-27 19:49:13 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578920772  

Just started looking at this, I'll try LInux/Ubunutu another time, but gcc-mingw-8.1 at least looks OK:  
  
```  
                                                           with PR               without  
cpp_int(fixed) 128       *                                  0.0013684             0.0018211  
cpp_int(fixed) 256       *                                  0.0162404             0.0185017  
cpp_int(fixed) 512       *                                  0.0380358             0.0469234  
cpp_int(fixed) 1024      *                                  0.113369              0.123057  
cpp_int        128       *                                  0.0148446             0.0114685  
cpp_int        256       *                                  0.0300817             0.0214975  
cpp_int        512       *                                  0.0702358             0.0632616  
cpp_int        1024      *                                  0.219607              0.21523  
cpp_int        2688      *                                  0.939021              1.10076  
cpp_int        2048      *                                  0.58137               0.691433  
cpp_int        4096      *                                  2.00723               2.34745  
cpp_int        8192      *                                  6.39508               7.4765  
cpp_int        16384     *                                  19.6915               22.9359  
```  
  
For MSVC though, things look pretty poor:  
  
```  
                                                            with pr              without  
cpp_int(fixed) 128       *                                  0.0223998            0.0240264  
cpp_int(fixed) 256       *                                  0.0743963            0.0609575  
cpp_int(fixed) 512       *                                  0.298065             0.195359  
cpp_int(fixed) 1024      *                                  1.14472              0.579244  
cpp_int        128       *                                  0.0358643            0.0316435  
cpp_int        256       *                                  0.138015             0.0787022  
cpp_int        512       *                                  0.581028             0.266261  
cpp_int        1024      *                                  2.1819               0.900065  
cpp_int        1344      *                                  2.87696              1.11731  
cpp_int        2048      *                                  6.36994              2.24439  
```

---

## Comment 20

> Username: ckormanyos  
> Created_at: 2020-01-27 20:08:39 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578929100  

> Just started looking at this,...  
  
I checked independently 256 fixed for 2 compilers. Good potential in that range.  
  
```  
  // MSVC 14.2, 256 fixed  
  // bits: 256, kops_per_sec: 10100 (with PR)  
  // bits: 256, kops_per_sec: 8633  (boost 1.72)  
```  
  
```  
  // GCC 8 MinGW, 256 fixed  
  // bits: 256, kops_per_sec: 26300 (with PR)  
  // bits: 256, kops_per_sec: 22000 (boost 1.72)  
```

---

## Comment 21

> Username: ckormanyos  
> Created_at: 2020-01-27 21:35:09 UTC  
> Updated_at: 2020-01-27 21:37:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-578964406  

> For MSVC though, things look pretty poor:  
  
I took a second look at the situation with MSVC at higher bit counts.  
  
In Madhur's PR, there is a hard-coded conditional expression `if(1) {...}`.  
  
When combined with the existing Karatsuba multiplicatoin in that PR, this conditional expression seems to activate either basecase-traditional-school-mul or basecase-comba-school-mul.  
  
If I twiddle this to be either `if(1) {...}` or `if(0) {...}`, I get the measurement below, showing that MSCV also benefits from comba at higher bit counts. This would make sense. These measurements are, however, difficult because it gets confusing to remember exactly what is being activated when which one is running. Due to this confusion, I feel like I would like to confirm this measurement tomorrow. But it also looks promising.  
  
```  
  // MSVC 14.2, 16384 fixed  
  // bits: 16384, kops_per_sec: 8.5 (Karatsuba + Basecase-Comba-School-Mul)  
  // bits: 16384, kops_per_sec: 5.6 (Karatsuba + Basecase-Traditional-School-Mul)  
```

---

## Comment 22

> Username: madhur4127  
> Created_at: 2020-01-30 16:26:34 UTC  
> Updated_at: 2020-01-31 13:06:26 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-580335849  

GCC fixed the bug and the changes are in the trunk (see the godbolt link: [https://godbolt.org/z/qzMQy6](https://godbolt.org/z/qzMQy6)). I will publish the benchmark's results soon.  
  
I am thinking of adding a compiler switch based on the GCC version and clang's version that will fix the bug. I will have to still think about msvc though.

---

## Comment 23

> Username: madhur4127  
> Created_at: 2020-02-02 07:27:17 UTC  
> Updated_at: 2020-02-02 14:45:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-581107001  

I guess it will take some more time for the benchmark results.  
  
I installed gcc-10 (experimental) and tried to run the benchmarking code. The code runs perfectly for stable g++/clang++ both with/without optimizations (-O2). Here are the results: [Google Sheets Link](https://docs.google.com/spreadsheets/d/1tm6zM8F4kbq045lM3PIEQqV-aSQpvnc6ytAHJEyWlCw/edit?usp=sharing)  
  
Curiously, there is a segmentation fault when turning on optimizations (-O2) for the new compiler but works correctly when optimizations are turned off.  
  
We can see the difference 1 `adc` instruction makes between `clang_comba` and `gcc_comba` (Remember: clang generates inefficient assembly but still uses `adc` and gcc instead generates much worse assembly without using `adc`). I can only hope there would a similar performance gain.  
  
Here's the stack trace of the crash after turning on optimizations, if you're interested:  
```  
AddressSanitizer:DEADLYSIGNAL  
=================================================================  
==24294==ERROR: AddressSanitizer: SEGV on unknown address 0x0000004b6690 (pc 0x00000046497d bp 0x7ffd6b1f0590 sp 0x7ffd6b1f0420 T0)  
==24294==The signal is caused by a WRITE memory access.  
    #0 0x46497d in boost::multiprecision::backends::cpp_int_base<0u, 4294967295u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long>, false>::cpp_int_base(unsigned long long) ../include/boost/multiprecision/cpp_int.hpp:255  
    #1 0x46497d in boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >::cpp_int_backend<unsigned long long>(unsigned long long, boost::enable_if_c<boost::multiprecision::backends::is_allowed_cpp_int_base_conversion<unsigned long long, boost::multiprecision::backends::cpp_int_base<0u, 4294967295u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long>, false> >::value, void>::type const*) ../include/boost/multiprecision/cpp_int.hpp:1337  
    #2 0x46497d in boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >, (boost::multiprecision::expression_template_option)1>::number<unsigned int>(unsigned int const&, boost::enable_if_c<boost::is_convertible<boost::multiprecision::detail::canonical<unsigned int, boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> > >::type, boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> > >::value&&(!boost::multiprecision::detail::is_restricted_conversion<boost::multiprecision::detail::canonical<unsigned int, boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> > >::type, boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> > >::value), void>::type*) ../include/boost/multiprecision/number.hpp:73  
    #3 0x46497d in boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >, (boost::multiprecision::expression_template_option)1> std::detail::get_max<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long>, (boost::multiprecision::expression_template_option)1, false>(mpl_::bool_<false> const&, mpl_::bool_<true> const&, mpl_::bool_<false> const&) ../include/boost/multiprecision/cpp_int/limits.hpp:149  
    #4 0x46497d in std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >, (boost::multiprecision::expression_template_option)1> >::max() ../include/boost/multiprecision/cpp_int/limits.hpp:196  
    #5 0x46497d in std::numeric_limits<boost::multiprecision::number<boost::multiprecision::backends::cpp_int_backend<0u, 0u, (boost::multiprecision::cpp_integer_type)1, (boost::multiprecision::cpp_int_check_type)0, std::allocator<unsigned long long> >, (boost::multiprecision::expression_template_option)1> >::inititializer::inititializer() ../include/boost/multiprecision/cpp_int/limits.hpp:174  
    #6 0x408fe6 in __static_initialization_and_destruction_0 ../include/boost/multiprecision/cpp_int/limits.hpp:230  
    #7 0x408fe6 in _GLOBAL__sub_I_main /home/madhur/multiprecision/perf_local/benchmark.cc:27  
    #8 0x4922ec in __libc_csu_init (/home/madhur/HDD/OSS/boost/libs/multiprecision/perf_local/gccx+0x4922ec)  
    #9 0x7ff8780e30dd in __libc_start_main (/usr/lib/libc.so.6+0x270dd)  
    #10 0x40912d in _start (/home/madhur/HDD/OSS/boost/libs/multiprecision/perf_local/gccx+0x40912d)  
```

---

## Comment 24

> Username: madhur4127  
> Created_at: 2020-02-08 15:33:08 UTC  
> Updated_at: 2020-08-26 04:43:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-583747058  

I could see the performance benefits when used as a base case for karatsuba multiplication. I tested for bit counts 1e4 bits to 1e5 bits at 1e4 bit intervals on `g++-x  -v : gcc version 10.0.1 20200202 (experimental) (GCC)`.  
  
```  
Comba    : g++-x benchmark.cc -lbenchmark -lpthread -o gccx -g -DBOOST_MP_COMBA  
Non-Comba: g++-x benchmark.cc -lbenchmark -lpthread -o gccx -g  
  
// Comba Multiplier  
--------------------------------------------------------------  
Benchmark                    Time             CPU   Iterations  
--------------------------------------------------------------  
BM_karatsuba/10000       24188 ns        24160 ns        28066  
BM_karatsuba/20000       77179 ns        77071 ns         9283  
BM_karatsuba/30000      145383 ns       145028 ns         5064  
BM_karatsuba/40000      351906 ns       351013 ns         2787  
BM_karatsuba/50000      318005 ns       317616 ns         2062  
BM_karatsuba/60000      422048 ns       421855 ns         1653  
BM_karatsuba/70000      587249 ns       586888 ns         1259  
BM_karatsuba/80000      769293 ns       765006 ns          999  
BM_karatsuba/90000      828439 ns       827934 ns          732  
BM_karatsuba/100000     965287 ns       963398 ns          725  
  
// Current Develop  
--------------------------------------------------------------  
Benchmark                    Time             CPU   Iterations  
--------------------------------------------------------------  
BM_karatsuba/10000       31713 ns        31669 ns        19055  
BM_karatsuba/20000      104265 ns       104056 ns         7035  
BM_karatsuba/30000      193443 ns       193301 ns         2797  
BM_karatsuba/40000      292207 ns       291004 ns         2304  
BM_karatsuba/50000      398557 ns       395679 ns         1355  
BM_karatsuba/60000      587262 ns       578584 ns         1066  
BM_karatsuba/70000      813129 ns       807576 ns          936  
BM_karatsuba/80000     1395501 ns      1389717 ns          767  
BM_karatsuba/90000     1010192 ns      1009758 ns          695  
BM_karatsuba/100000    1247890 ns      1240849 ns          591  
```  
Benchmark code  (for reference):  
```  
#include <iostream>  
#include <benchmark/benchmark.h>  
#include <boost/multiprecision/cpp_int.hpp>  
#include <boost/random.hpp>  
#include <cmath>  
  
using namespace boost::multiprecision;  
using namespace boost::random;  
  
static void BM_karatsuba(benchmark::State& state)  
{  
   mt19937_64                        mt(2435);  
   int                               bits = state.range(0);  
   uniform_int_distribution<cpp_int> ui((cpp_int(1) << (bits - 1)), cpp_int(1) << bits);  
   cpp_int                           x = ui(mt), y = ui(mt);  
   cpp_int                           z;  
   for (auto _ : state)  
   {  
      ++x;  
      ++y;  
      benchmark::DoNotOptimize(z = x * y);  
   }  
   state.SetComplexityN(state.range(0));  
}  
  
BENCHMARK(BM_karatsuba)->DenseRange(10'000, 100'000, 10'000);  
BENCHMARK_MAIN();  
```

---

## Comment 25

> Username: madhur4127  
> Created_at: 2020-02-08 15:44:54 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-583748182  

@jzmaddock, I know you're really busy these days but I am eagerly waiting for your thoughts on the comba multiplier. I wish to make it merge-ready which I think is.  
  
I added the compiler switch as `BOOST_MP_COMBA` in my last commit. I am thinking of defining the macro based on the versions of the compiler when the compilers fix the bug. GCC 10's stage 4 has started which is probably the last stage so the release should be soon.

---

## Comment 26

> Username: ckormanyos  
> Created_at: 2020-02-10 18:41:14 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-584287586  

> I added the compiler switch as `BOOST_MP_COMBA` in my last commit. I am thinking of defining the macro based on the versions of the compiler when the compilers fix the bug. GCC 10's stage 4 has started which is probably the last stage so the release should be soon.  
  
Good job.  
  
I still keep wondering if it's feasible to write one code for the inner loops of school multiplication without a compiler switch. It seems like the particular way of writing the loops is, in fact, portable and might lead to general cross-platform improvements in efficiency. I am still trying to find time to run a few more benchmarks.

---

## Comment 27

> Username: madhur4127  
> Created_at: 2020-03-01 10:13:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-593079198  

Comba updates in GCC are now stable (`v10.0.1 20200223 (experimental)`). No runtime error is seen when turning optimizations on. I benchmarked the changes with `-O3`.   
  
Here are the results:  
```  
------------------------------------------  
Benchmark            Iterations Iterations  
                     (non-comba)  (comba)  
------------------------------------------  
BM_karatsuba/10000        34550      45790  
BM_karatsuba/20000        11227      14249  
BM_karatsuba/30000         6428       7813  
BM_karatsuba/40000         3702       4719  
BM_karatsuba/50000         2830       3503  
BM_karatsuba/60000         2124       2519  
BM_karatsuba/70000         1580       1974  
BM_karatsuba/80000         1203       1566  
BM_karatsuba/90000         1103       1352  
BM_karatsuba/100000         926       1135  
```  
  
Considering the theoretical speedup of Comba multiplier as mentioned by Paul G Comba in his paper is 1.35, these results look promising where the speedup ranges from 1.325 (10'000 bits) to 1.225 (100'000 bits).  
  
I think the changes are performing as per the expectations and are in merge ready state. I am eagerly waiting for feedback.

---

## Comment 28

> Username: ckormanyos  
> Created_at: 2020-03-01 22:10:51 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-593152032  

> I think the changes are performing as per the expectations and are in merge ready state. I am eagerly waiting for feedback.  
  
Those look like promising results. I will try to check some values for at least two vastly different compilers and provide reliable feedback asap.  
  
There are some CI errors arising. We can also look into these. I'm not sure if they are related to the changes in this issue or not.  
  
Kind regards, Chris

---

## Comment 29

> Username: jzmaddock  
> Created_at: 2020-03-04 19:09:55 UTC  
> Updated_at: 2020-03-04 19:10:21 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-594761450  

I haven't tested with gcc-10 yet, but gcc-9 shows no slowdown at least, and interestingly neither does MSVC ;)  
  
BTW msvc evaluates the inner loop as:  
  
```  
         temp = carry;  
         carry += static_cast<double_limb_type>(*(--pa)) * (*(++pb));  
00007FF633666071  mov         edx,dword ptr [rbx+4]    
00007FF633666074  lea         r11,[r11-4]    
00007FF633666078  mov         ecx,dword ptr [r11]    
00007FF63366607B  lea         rbx,[rbx+4]    
00007FF63366607F  imul        rdx,rcx    
00007FF633666083  mov         r8,rax    
00007FF633666086  add         rax,rdx    
         overflow += carry < temp;  
00007FF633666089  cmp         rax,r8    
00007FF63366608C  adc         edi,0    
00007FF63366608F  sub         esi,1    
00007FF633666092  jne         boost::multiprecision::backends::eval_multiply_comba<128,128,1,0,void,128,128,1,0,void,128,128,1,0,void>+0F1h (07FF633666071h)    
```  
  
Which actually looks like it would be hard to optimize further?  Note that limb_type is a 32-bit integer for msvc.

---

## Comment 30

> Username: madhur4127  
> Created_at: 2020-03-05 04:54:20 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-595028221  

> Which actually looks like it would be hard to optimize further?  
  
I think yes, it can be optimized further. As shown in this [godbolt reference](https://godbolt.org/z/jGPpkW), this `cmp` is not required.  
```  
00007FF633666089  cmp         rax,r8    
00007FF63366608C  adc         edi,0   
```  
It is not required because the MSVC still doesn't understand the overflow-idiom (`overflow += carry < temp;`) and that it can be done with a simple `adc` right after `add` without the need for comparison and setting the EFLAGS. Clang and GCC both support them even for 128-bit integers (now).  
  
> There are some CI errors arising.   
  
Should be fixed now, the problem was that MSVC doesn't compile: `#if true` so I changed it to `#if 1`.   
  
All tests passed for GCC, Clang which is satisfactory.

---

## Comment 31

> Username: ckormanyos  
> Created_at: 2020-03-05 18:58:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-595391123  

>> Which actually looks like it would be hard to optimize further?  
  
> I think yes, it can be optimized further.  
  
I think these improvements are very encouraging. The compilers are doing a great job at cutting highly optimized code.  
  
Hand-written assembly could cut out maybe some of the operations and possibly reduce branch prediction. But we have discussed this before and the consensus is that we do not at the moment wish to enter the domain of hand-coded assembly in Boost.Multiprecision.  
  
Are you sure that these improvements should be limited to GCC/clang only? I remember finding encouraging speedup also for MSVC...  
  
Thank you and kind regards, Chris

---

## Comment 32

> Username: madhur4127  
> Created_at: 2020-03-06 03:08:07 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-595576920  

> Hand-written assembly could cut out maybe some of the operations and possibly reduce branch prediction. But we have discussed this before and the consensus is that we do not at the moment wish to enter the domain of hand-coded assembly in Boost.Multiprecision.  
  
Yes I remember that discussion. I think there are two ways:  
1. Write inline asm (hand written assembly)  
2. Make compiler smarter to generate the desired assembly.  
  
I think I have always chosen the 2nd option as this will be for the greater good in general. This is the reason why I raised bugs in GCC/Clang. Also [inline asm is discouraged by compilers](https://gcc.gnu.org/wiki/DontUseInlineAsm).  
  
> > Which actually looks like it would be hard to optimize further?  
  
> I think yes, it can be optimized further.  
  
Here too I was hinting about raising bugs at MSVC. But I think it won't be necessary as it is just a matter of one instruction and it's not big of a deal. If in future MSVC limb_type is 64-bit then we have to be sure the generated assembly is optimal.  
  
  
> All tests passed for GCC, Clang which is satisfactory.  
  
John, can you please restart the Travis build? One job failed because of infrastructure issues.   
On the aggregate, all the tests have passed.

---

## Comment 33

> Username: madhur4127  
> Created_at: 2020-03-06 03:15:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-595578744  

MSVC: I am observing a significant increase in build times in Appveyor as much as 30%.  
[Old](https://ci.appveyor.com/project/jzmaddock/multiprecision/builds/31183343) vs [New](https://ci.appveyor.com/project/jzmaddock/multiprecision/builds/31255010)  
  
GCC/Clang: There is no significant increase/decrease. Some builds are longer and some are shorter.   
[Old](https://travis-ci.org/boostorg/multiprecision/builds/655908250?utm_medium=notification&utm_source=github_status) vs [New](https://travis-ci.org/boostorg/multiprecision/builds/658522012?utm_source=github_status&utm_medium=notification)

---

## Comment 34

> Username: jzmaddock  
> Created_at: 2020-03-09 15:59:49 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/182#issuecomment-596618083  

Thanks Madhur for keeping on with this - now merged!

---
