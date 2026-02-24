# #184 - Feature request: quad-double precision. [Open]

> Username: cosurgi  
> Created at: 2020-01-17 14:04:48 UTC  
> Updated at: 2025-06-30 20:30:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184  

Hi,  
  
as suggested in [math #303](https://github.com/boostorg/math/issues/303) I repost this here:  
  
How feasible it is to integrate quad-double precision, It has 212 bits in significand and is faster than MPFR:  
* [source code](https://www.davidhbailey.com/dhbsoftware/qd-2.3.22.tar.gz), it is on BSD license.   
* [article/documentation](https://www.davidhbailey.com/dhbpapers/quad-double.pdf)  
* [quad double precision homepage]( https://www.davidhbailey.com/dhbsoftware/)  
  
I have just recently [integrated boost multiprecision](https://gitlab.com/yade-dev/trunk/merge_requests/362) with a fairly large numerical computation software [YADE](https://yade-dem.org/doc/).  
  
Float128 isn't enough for my needs, yade would greatly benefit from quad-double (as it is much faster than MPFR). Also yade can serve as a really large testing ground. I have implemented [CGAL numerical traits](https://gitlab.com/yade-dev/trunk/blob/highPrecisionReal/lib/high-precision/CgalNumTraits.hpp) and [EIGEN numerical traits](https://gitlab.com/yade-dev/trunk/blob/highPrecisionReal/lib/high-precision/EigenNumTraits.hpp) to use multiprecision. And all of them are tested daily in our pipeline, see [an example run here](https://gitlab.com/yade-dev/trunk/pipelines/110095678). Or a more specific example for float128: [test (need to scroll up)](https://gitlab.com/yade-dev/trunk/-/jobs/406901045#L227) and [check](https://gitlab.com/yade-dev/trunk/-/jobs/406610946)

---

## Comment 1

> Username: cosurgi  
> Created at: 2020-01-17 15:05:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575662578  

@ckormanyos   
  
>> How feasible it is to integrate quad-double precision...  
  
> This is a good question. I would combine that with also software support for float128_t for those platforms lacking quadmath. In fact, I would see full cross-plattform availability of a float128_t (be it hardware backend or software pure) as a prerequisite before going for a float256_t.  
  
This definitely would be great. Especially if float128 will use compiler/assembler version when available.  
  
> Then there is the topic of design questions.  
>  
> Exrtract float128_t/256_t from the same template?  
> Support other digit splits, etc.  
> Lots of questions.  
  
Yeah, it definitely depends on the approach. The simplest one is of course to use [qd-2.3.22.tar.gz](https://www.davidhbailey.com/dhbsoftware/qd-2.3.22.tar.gz) verbatim and only add the necessary multiprecision wrapper. A much more interesting approach is to extract the design used to "quadruple" the precision in there. In such a way that quad-float128 would be simply another template of the same design. 512bits is exactly what I will need in my calculations.

---

## Comment 2

> Username: ckormanyos  
> Created at: 2020-01-17 22:02:20 UTC  
> Updated at: 2020-01-17 22:04:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575812477  

> A much more interesting approach is to extract the design used to "quadruple" the precision in there. In such a way that quad-float128 would be simply another template of the same design  
  
I was thinking more of the binary128 and binary256 formats that are mentiod in IEEE754:2008. But these would be (at least in their portable and generic form) software-intensive and thus lose the hardware acceleration that is the very thing you seek. So that is not exactly what the original request is about.  
  
> The simplest one is of course to use qd-2.3.22.tar.gz verbatim and only add the necessary multiprecision wrapper...  
  
This might unfortunately have liensing inconsistencies, in the sense that using the code directly with BSL might be problematic.  
  
I think that binary128/256 is an idea we could talk about. But I mentioned previously, I think there would be several important design considerations. Another idea would be to create a set of popular backends intended to wrap a given type for the "number" template in Boost.Multiprecision. That might be a way to navigate through the licensing differences. I mean, you could even wrap the old FORTRAN77 REAL*16 that way.  
  
I think I'd like to continue this discussion and see what possibilities there are.  
  
Kind regards, Chris

---

## Comment 3

> Username: cosurgi  
> Created at: 2020-01-17 22:56:53 UTC  
> Updated at: 2020-01-17 22:59:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575826750  

Since binary128/256 would be software intensive I would prefer other approach. When hardware support appears in future CPUs adding such binary support should be fairly easy. As you said, it is not a matter of this thread.  
  
> a set of popular backends intended to wrap a given type for the "number" template in Boost.Multiprecision  
  
This solution is much more interesting for me. I think that you have already done that with `__float128` and `mpfr::` (the file `/usr/include/mpreal.h` from the debian package `libmpfrc++-dev`), am I correct?  
  
Also the idea to use the "quadrupling" method described in [that paper](https://www.davidhbailey.com/dhbpapers/quad-double.pdf) is a worthwhile direction for me. Even if it means completely reimplementing this idea in boost from scratch. This will take a lot of time though.  
  
So I could summarise following:  
  
1. backend intended to wrap quad-double. The user has to install and configure [qd-2.3.22.tar.gz](https://www.davidhbailey.com/dhbsoftware/qd-2.3.22.tar.gz) by himself, but then boost would immediately "understand" and wrap it.  
2. "quadrupling" precision approach, a pure boost-only solution.

---

## Comment 4

> Username: ckormanyos  
> Created at: 2020-01-18 09:15:19 UTC  
> Updated at: 2020-01-18 09:16:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575881355  

>> a set of popular backends intended to wrap a given type for the "number" template in Boost.Multiprecision  
  
> 1. backend intended to wrap quad-double. The user has to install and configure qd-2.3.22.tar.gz by himself, but then boost would immediately "understand" and wrap it.  
  
Correct. There are already Boost.Multiprecision "float" backend wrappers for __float128/quadmath, GMP/MPIR, MPFR and an "int" wrapper for tommath, and these require that the user environment is able to find and link to the required target-specific lib.  
  
I'm not sure what it means for an existing UDT number library to be important/cool/special enough to be wrapped with a supported backend in Boost? I'm also a bit hesitant to directly use DD/QD prototypes when looking at the licensing of the original work. Nonetheless, DD and QD have been around for a while. In fact, I used them heavily about 10-12 years ago. Back then there was a different DD package with 106 binary digits. Maybe it was the predecessor of the work we are mentioning in this post.  
  
Even though I'm not particularly fond of the "FP hack" required for x86 architecture in these packages, and I'm also not sure exactly how portable DD and QD are, these libs are quite useful.  
  
This idea seems feasible to me.  
  
Kind regards, Chris

---

## Comment 5

> Username: pabristow  
> Created at: 2020-01-18 10:04:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575884703  

The downside of double-double (and I presume of quad-double) has been shown to be some uncertainty estimating about epsilon and consequence difficulties in estimating precision and uncertainties.   
  
The Apple/Darwin platforms all show 'different' behaviour in many Boost.Math tests.  
  
 So the 106 or 212 significand bits may be over-optimistic.  If you value speed more then ...

---

## Comment 6

> Username: ckormanyos  
> Created at: 2020-01-18 10:42:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575887060  

> The downside of double-double (and I presume of quad-double) has been shown to be some uncertainty estimating about epsilon and consequence difficulties in estimating precision and uncertainties.  
  
Indeed. Interestingly enough, two of the greater challenges in potentially wrapping DD/QD and higher orders would be getting the types to behave like proper C++ types and figuring out how to mix the function name calls within BSL headers.  
  
These are the two challenges I'm facing in another (unpublished) project that wraps the high precision type of a popular computer algebra system for Boost.Multiprecision.  
  
Still, I think the DD/QD topic might be interesting to pursue in a feasibility study. Perhaps some of the epsilon inconsistencies could be addressed if the concepts of unity, comparison, frexp, ldexp are established more clearly in small add-on functionalities beyond the raw original implementation.  
  
Kind regards, Chris

---

## Comment 7

> Username: jzmaddock  
> Created at: 2020-01-18 12:18:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575893697  

This is a good idea, and in principle easy enough.  
  
As mentioned above already, epsilon is likely to the the main sticking point, there are 2 possible definitions:  
  
* A strict interpretation of the std, is to pick the smallest number which can be added to 1 and yield a different result - this is `numeric_limits<double>::min()` or even `denorm_min()`.  This is the definition that gcc on apple used, and it's pretty useless for any real work.  
* A loose definition, would be 2^(B-1) if we have B bits of precision (53*4 in this case).  This is more useful generally, but can still lead to nasty surprises.  
  
BTW some time back I tried pretty hard to get Boost.Math working with apples "double double" and just couldn't get everything working - so much of the reasoning about what happens to a number under operation X breaks for these types that it's a bit of a loosing battle.  That said, we have since added cpp_dec_float and mpf_float both of which have hidden guard digits and seem to be working OK.  In principle, these types aren't that different I hope.

---

## Comment 8

> Username: ckormanyos  
> Created at: 2020-01-18 13:22:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575898057  

>> How feasible it is to integrate quad-double precision, It has 212 bits in significand and is faster than MPFR:  
  
> This is a good idea, and in principle easy enough.  
  
Thanks John, for your experienced advice. That's what I was seeking in terms of a kind of _nod_.  
  
If there are no other blocking points, then I will write this up ASAP as a feasibility study for Boost GSoC 2020. The timing of this user-raised issue is, I believe, perfect for that potential venue.  
  
Kind regards, Chris

---

## Comment 9

> Username: NAThompson  
> Created at: 2020-01-18 16:13:35 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575913443  

I'm curious about @cosurgi's use case. I feel like the current Boost.Multiprecision covers the intended use well as it stands, which only maybe a factor of 2 available for performance increase. But if you're using quad double, well, performance can't be a huge issue anyway.

---

## Comment 10

> Username: cosurgi  
> Created at: 2020-01-18 17:47:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575922472  

@NAThompson even a two-times performance increase means waiting for result one month or two months :)  
  
My use case will be quantum mechanics and quantum chromodynamics. Currently I am preparing the yade source code for all of this. The experimental measurements in some cases are more precise than 20 or even 30 significant digits. Performing high precision calculations is a big deal here. Since I plan to calculate volumes significantly larger than Planck's length I have estimated that my target required precision is about 150 digits, which is 512bits, And it has to be as fast as possible. I know it will take years to achieve, but that's the goal.  
  
My current strategy to achieve this is to make sure that yade is 100% compatibile with boost multiprecision. So that when new faster and more precise types appear I will be able to instantly benefit from them. And hope that someday CPUs with native floating point 512 bit type will appear :)

---

## Comment 11

> Username: cosurgi  
> Created at: 2020-01-18 17:50:11 UTC  
> Updated at: 2020-01-18 17:50:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575922644  

BTW: MPFR is about 10-times slower. The difference in waiting one month and 10 months for a result becomes really meaningful.

---

## Comment 12

> Username: ckormanyos  
> Created at: 2020-01-19 09:36:31 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575985215  

> MPFR is about 10-times slower. The difference in waiting one month and 10 months for a result...  
  
So let's prefer the 1 month.

---

## Comment 13

> Username: ckormanyos  
> Created at: 2020-01-19 09:45:20 UTC  
> Updated at: 2020-01-19 09:49:53 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575985983  

> This is a good idea, and in principle easy enough.  
> As mentioned above already, epsilon is likely to the the main sticking point, ...  
  
Yes.  
  
> BTW some time back I tried pretty hard to get Boost.Math working with apples "double double" and just couldn't get everything working  
  
This comment rang a bell in my mind. So I mounted a drive from 2007. As it turns out, I developed my own version of `q_float` in March 2005. I had completely forgotten about this. It appears as though my original work used add/sub/mul/div routines originally from a developer known as K. Briggs in `doubledouble`. But these had subsequently been modified in a file called `quad_float`, that I had discovered in something at that time called `WinNTL-5_3_2`, which appears to live on to the present day in newer versions.  
  
I got remarkably far with this code, and am somewhat surprised that I had forgotten about it. If we pursue this topic, I will be sharing my work in a smaller e-mail circle.  
  
In that work, it looks like I simply empirically selected 31 for `digits10` and calculated base-2 `digits` from that. Using this, I selected `epsilon` to simply be 10^-30. But I had curiously failed to implement any number for `max_digits10`.  
  
Bset regards, Chris

---

## Comment 14

> Username: jzmaddock  
> Created at: 2020-01-19 09:56:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575987016  

Interesting comments and use cases - thanks for this.  
  
There's one other potential blocker that springs to mind: the special functions are kind of tricky to implement for types with large bit counts, but small exponent ranges.  quad_float is perhaps the archetype here :(  
  
There's an open bug for tgamma on this: https://github.com/boostorg/multiprecision/issues/150, but it wouldn't surprise me if there aren't others lurking once you go hunting.  
  
Question: can double_double be doubled?  And doubled again etc?  In other words could these be implemented via nested templates?

---

## Comment 15

> Username: ckormanyos  
> Created at: 2020-01-19 09:57:59 UTC  
> Updated at: 2020-01-19 09:58:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575987122  

> The simplest one is of course to use qd-2.3.22.tar.gz verbatim and only add the necessary multiprecision wrapper.  
  
One thing I mentioned about this package is licensing of the code.  
  
I believe that I am getting this interpretation. Not yet, however, fully sure... But I also notice that the _x86-FPU-fix_ encloses on the top-level call mechanism all fucntions using the package. This means, as is confirmed in the DD/QD documentation, that the code will have a hard time being used in multithreading for x86. I would prefer to use a package that sets and resets the _x86-FPU-fix_ within the individual subroutines that need it, thereby allowing for a synchronization mechanism if trying for multithreading.  
  
So at the present, I am not aware of an existing kind of _DD_ or _QD_ that would fully satisfy my personal requirements. That being said, one could focus on a popular one. But there seem to be several popular ones dating all the way back to `doubledouble` that I had mentioned above.  
  
Best regards, Chris

---

## Comment 16

> Username: ckormanyos  
> Created at: 2020-01-19 10:08:46 UTC  
> Updated at: 2020-01-19 10:09:23 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575988012  

> Question: can double_double be doubled? And doubled again etc? In other words could these be implemented via nested templates?  
  
You read my mind. I think the answers are yes and yes. But I am also wary of such nested templates having once gotten poor results with an integer type using something like this. But that experience was more than 20 ago, I was less experienced at programming C++ back then and compilers got much (massively much) better at parsing nested templates since then.

---

## Comment 17

> Username: jzmaddock  
> Created at: 2020-01-19 10:14:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575988522  

>But I also notice that the x86-FPU-fix encloses on the top-level call mechanism all fucntions using the package  
  
Wouldn't that only be required for code executing on the old x87 co-processor, and not x64 code using the SSE registers?

---

## Comment 18

> Username: ckormanyos  
> Created at: 2020-01-19 11:17:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-575994296  

>> But I also notice that the x86-FPU-fix encloses on the top-level call mechanism all fucntions using the package  
  
> Wouldn't that only be required for code executing on the old x87 co-processor, and not x64 code using the SSE registers?  
  
Yes. I have also just sent you some work in another thread. In that work, the _x86-FPU-fix_ is only needed for that and only for that legacy FPU.

---

## Comment 19

> Username: ckormanyos  
> Created at: 2020-01-25 17:02:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-578423126  

> write this up ASAP as a feasibility study for Boost GSoC 2020. The timing of this user-raised issue is, I believe, perfect for that potential venue.  
  
This has been done near the bottom of this page  
[Boost.Multiprecision: Double-Double and Quad-Double Types](http://github.com/boostorg/boost/wiki/Google-Summer-of-Code:-2020).

---

## Comment 20

> Username: jzmaddock  
> Created at: 2020-01-29 19:27:52 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-579920014  

@cosurgi : There's a very tentative version in draft PR referenced above.  I would welcome feedback if you'd like to give it a try and see how far you can get.  
  
There are some issues, and some possibly controversial design choices:  
  
* I've fixed up add/subtract/divide to handle infinities correctly.  However, for the ultimate performance, this is perhaps not such a good idea?  
* The one and only very basic test case, has a number of failures.  string IO is horribly broken, and printing out the value 64 does not result in "64" for example.  This is a direct consequence of some of the arithmetic operators - for example 2^10 does not lead to exactly 1024  :(  
* I've done no testing of special functions or Boost.Math support generally.  
* Many more tests are required, although there are likely to be so many failures from our existing boilerplated tests it's hard to know quite how to proceed.  Iostream round tripping isn't even worth trying - it will never work correctly IMO.  
  
Anyhow, if you have any real world code you can plug this into, it would be interesting to hear how you get on.  It's all a bit too "fast and loose" for me!

---

## Comment 21

> Username: cosurgi  
> Created at: 2020-02-03 15:06:36 UTC  
> Updated at: 2020-02-03 15:21:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581458264  

@jzmaddock I'm sorry for late reply. This looks very interesting. Yes I have a real-world code [YADE with a full blown testing suite](https://gitlab.com/yade-dev/trunk/-/merge_requests/383/pipelines) for math functions I will try to use it this or the next week.  
  
Just to be sure - I should take [this commit](https://github.com/boostorg/multiprecision/pull/190/commits/439b12efeb58dc463bba69f392bdc82fed59a5fe) ?  
  
The issues:  
  
* YADE heavily depends on correct handling of NaN and Inf. Without them CGAL does not work at all.  
* Ouch: string IO roundtripping is very important for me.  
* I have tests of all math functions, they are tested against [python mpmath](https://gitlab.com/yade-dev/trunk/blob/finalizeHighPrecisionReal/py/tests/testMath.py#L50). I picked the tolerances by experimenting with the results. That's why string IO is important - strings are the only way to get high precision numbers into python.  
* Ouch again. Maybe we could craft something here? Even using `cpp_bin_float` as a helper type is acceptable for me. String IO was never supposed to be fast. It just should be accurate.  
  
See some [example math tests results for MPFR 150](https://gitlab.com/yade-dev/trunk/-/jobs/407216815#L232) - scroll up to see the math functions being tested by the [earlier mentioned script](https://gitlab.com/yade-dev/trunk/blob/finalizeHighPrecisionReal/py/tests/testMath.py).

---

## Comment 22

> Username: cosurgi  
> Created at: 2020-02-03 17:38:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581530203  

Oh, btw if 2^10 is 1023.99999999(9)..... then it's acceptable for me, because the error will be smaller than the tolerance level. We only need the working round tripping.

---

## Comment 23

> Username: jzmaddock  
> Created at: 2020-02-03 17:47:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581534093  

>Oh, btw if 2^10 is 1023.99999999(9)..... then it's acceptable for me, because the error will be smaller than the tolerance level. We only need the working round tripping.  
  
This is actually the reason that round tripping doesn't work - I mean it's approximately correct (to within an epsilon or so), but without a correctly functioning pow() we basically can't implement correct string output.  Actually even the basic arithmetic operations aren't correctly rounded either...  as long as  string IO is approximately correct (to within an epsilon or so - ie drop one digit and it would round to the "correct" result), wouldn't your tests still function OK?

---

## Comment 24

> Username: cosurgi  
> Created at: 2020-02-03 19:05:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581566541  

> wouldn't your tests still function OK?  
  
heh. I suppose that all math tests would work. But the round tripping test would fail ;) I suppose I could implement an exception for this one.

---

## Comment 25

> Username: jzmaddock  
> Created at: 2020-02-03 19:45:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581584942  

Meanwhile.... there are a few issues with the current version - mostly order of initialization issues in numeric_limits<qd_real>, give me a day or so and I'll push a fix as they prevent tgamma and others from working.

---

## Comment 26

> Username: jzmaddock  
> Created at: 2020-02-03 20:00:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-581591635  

Nevermind, fix pushed.  The C99 functions including tgamma etc are now working, note that they are not quite std conforming in that they don't adhere to the non-normative appendix F which deals with handling of infinities and NaNs.

---

## Comment 27

> Username: cosurgi  
> Created at: 2020-02-15 21:39:28 UTC  
> Updated at: 2020-02-17 14:29:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-586643806  

@jzmaddock I'm sorry for the delay. I wanted to finish YADE integration with all other multiprecision types.  
  
Now I have started working on this. YADE is compiling too long for quick experiments. To solve this problem I made a small [minieigen-real](https://gitlab.com/cosurgi/minieigen-real) project. It has tests for everything that YADE needs, and compiles 1 minute instead of 15 minutes :) Now all the work happens in this [merge request](https://gitlab.com/cosurgi/minieigen-real/-/merge_requests/10).  
  
I did `git cherry-pick` of your two commits. The `quad_double.hpp` file is in the same path in both projects, so commits can be cherry picked (other boost files are installed via debian package `libboost-dev-all`, but I can change that if needed).  
  
I had a compilation error: `error: ‘value’ declared ‘static’ in ‘constexpr’ function` and I needed to [modify quad_double.hpp](https://gitlab.com/cosurgi/minieigen-real/-/merge_requests/10/diffs?commit_id=7759a16d07ab45151b25aff0b0e3a18871a45c75). It looks like the `value` variable was unused in the first case?  
  
**EDIT: this works now.** ~~Now it is compiling, and throws on runtime `Value nan can not be represented in the target integer type.`~~  
  
* See the [full pipeline](https://gitlab.com/cosurgi/minieigen-real/pipelines/118212572) example  
* See for example a ["good run" on `boost::multiprecision::cpp_bin_float`](https://gitlab.com/cosurgi/minieigen-real/-/jobs/438666242)  
* ~~And [quad double run](https://gitlab.com/cosurgi/minieigen-real/-/jobs/438666233) - some compilation warnings and crash in runtime.~~  
* And [quad double run](https://gitlab.com/cosurgi/minieigen-real/-/jobs/439901717) - some compilation warnings and problems with power and logarithm.  
  
 Do you have an account in gitlab? I would like to add you to [minieigen-real](https://gitlab.com/cosurgi/minieigen-real) project, because you can have there a fully working pipeline to play around with. It even prints a crash backtrace when there is a segfault :) To test locally you can use following commands (inside `src/` dir (**)):  
  
```  
make test_ci_256         CXXFLAGS="-D EIGEN_DONT_ALIGN -D EIGEN_DONT_VECTORIZE -D EIGEN_DISABLE_UNALIGNED_ARRAY_ASSERT" -j 10  
make test_ci_mne_256     CXXFLAGS="-D EIGEN_DONT_ALIGN -D EIGEN_DONT_VECTORIZE -D EIGEN_DISABLE_UNALIGNED_ARRAY_ASSERT" -j 10  
  
# test everything:  
make all    CXXFLAGS="-D EIGEN_DONT_ALIGN -D EIGEN_DONT_VECTORIZE -D EIGEN_DISABLE_UNALIGNED_ARRAY_ASSERT" && make test  
```  
  
These `DONT_VECTORIZE` defines refer to Eigen library. The "SSE" in the pipeline names refers to vectorization (SIMD) enabled or disabled. For testing I don't use SSE, because it may spew unwanted errors at beginning. To speed up compilation I use ccache.  
  
(**) Sorry about that, I didn't clean up the old build system. I stopped using `src/` dir, because I wanted to be able to `git cherry-pick` commits between YADE and minieigen-real. Now due to cherry picking your commits from boost more directories appeared here ;) But thanks to that the directory structure `include/boost/multiprecision` is familiar to you :) YADE tests are inside `py/` dir, and YADE multiprecision wrapper is in `lib/high-precision/Real.hpp` file.

---

## Comment 28

> Username: cosurgi  
> Created at: 2020-02-15 22:56:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-586649062  

It is possible that to get the tests to pass a special case for quad256 will have to be added [here](https://gitlab.com/cosurgi/minieigen-real/-/blob/quadDouble/py/tests/testMath.py#L48). The default value of `self.expectedEpsilon` is calculated using typical formula which fits both cpp_bin_float and MPFR.  
  
(the `if(True):` in the next line is because YADE skips using `mpmath` when possible, and in this line in YADE there is a check if `mpmath` is available)

---

## Comment 29

> Username: cosurgi  
> Created at: 2020-02-16 03:00:11 UTC  
> Updated at: 2020-02-16 18:29:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-586663174  

The previous throw `Value nan can not be represented in the target integer type` was a stupid mistake on my part. Now some tests are starting to work! The most broken are following functions:  
  
* `pow` calls `qd_real::log` and obtains NaN  
* `qd_real::log` - complains about negative arguments which [isn't true](https://gitlab.com/cosurgi/minieigen-real/-/blob/quadDouble/py/tests/testMath.py#L187).  
* [Eigen `highest`, `lowest`](https://gitlab.com/cosurgi/minieigen-real/-/blob/quadDouble/lib/high-precision/EigenNumTraits.hpp#L47) has some problems, [tested from here](https://gitlab.com/cosurgi/minieigen-real/-/blob/quadDouble/py/tests/testMath.py#L314), and exported [to python from here](https://gitlab.com/cosurgi/minieigen-real/-/blob/quadDouble/py/high-precision/_math.cpp#L572)  
  
See the [latest pipeline test](https://gitlab.com/cosurgi/minieigen-real/-/jobs/438735169).  
  
You can test this locally in python, after compiling with `cd src ; make test_ci_256` (eventually turn off SSE using `CXXFLAGS` from my previous post) you can start python to experiment: `cd ../build ; ipython3`, and then:  
  
```python  
import esup_256 as mth;import mpmath;from mpmath import mpf; from mpmath import mpc  
mth.pow(-1.5,2)  
mth.lowest()  
mth.sinh(mpc(1+2j)) - mpmath.sinh(mpc(1+2j))  
```  
  
Produces this output:  
```  
In [1]: import esup_256 as mth;import mpmath;from mpmath import mpf; from mpmath import mpc  
In [2]: mth.pow(0.5,2)  
Out[2]: mpf('0.25')  
In [3]: mth.pow(-1.5,2)  
ERROR (qd_real::log): Non-positive argument.  
Out[3]: mpf('nan')  
In [4]: mth.pow(55.5,2)  
Out[4]: mpf('3080.25')  
In [6]: mth.lowest()  
---------------------------------------------------------------------------  
ValueError                                Traceback (most recent call last)  
---------------------------------------------------------------------------  
In [7]: mth.sinh(mpc(1+2j)) - mpmath.sinh(mpc(1+2j))  
Out[7]: mpc(real='3.79822709830391949898929690782478286168838633344797798651191199633e-64', imag='-3.03858167864313559919143752625982628935070906675838238920952959707e-63')  
```  
  
And here you see that raising negative arguments to some power does not work. And  `mth.lowest()` has problem. I suspect string formatting problems, because printing lowest() gives following [string: `"-/.////e-2147483647"`](https://gitlab.com/cosurgi/minieigen-real/-/jobs/438735169#L311)(that's line 311 in the pipeline test), with some starting slashes.  
  
Surprisingly complex logarithm or even complex hyperbolic sine do work.

---

## Comment 30

> Username: cosurgi  
> Created at: 2020-02-16 12:34:08 UTC  
> Updated at: 2020-02-16 12:39:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-586701347  

Actually, it is only single place where `pow(…,…)` is called with a negative argument. And it is to square. I think that negative args should work with natural powers. It happens when I am testing CGAL numerical traits:  
  
* the test: [here](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/py/tests/testMath.py#L262)  
* CGAL num traits: [here](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/lib/high-precision/CgalNumTraits.hpp#L70)  
* everywhere else pow takes positive argument, test is [here](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/py/tests/testMath.py#L293)  
  
Maybe I should use `x*x` in CGAL, but this power works for all other types see the [latest pipeline](https://gitlab.com/cosurgi/minieigen-real/pipelines/118236747). This won't solve the problem though - power produces `NaN` way too often.  
  
From the looks of it, I think that fixing:  
  
* power  
* logarithm  
* string representation: `"-/.////e-2147483647"` of `lowest()` number  
  
Would solve most or all of our problems here.  
  
BTW: it is now being tested with [error tolerances](https://gitlab.com/cosurgi/minieigen-real/-/blob/master/py/tests/testMath.py#L57) for MPFR 100 decimal places. I should lower these tolerances later.

---

## Comment 31

> Username: cosurgi  
> Created at: 2020-02-16 19:49:15 UTC  
> Updated at: 2020-02-17 00:18:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-586745207  

BTW: after reading the paper and source code I think that extracting "Quadding" method from this paper into a template is possible. The requirement is that underlying type satisfies IEEE rounding method of the last bit. I will probably need quad-float128 sometime in the future. We could get back to that later.  
  
EDIT: explanation for this requirement is very simple: if the last bit is wrong (incorrectly rounded), then the next element in the quad - although by itself correct, is completely useless - because a single bit preceding it is all wrong.

---

## Comment 32

> Username: cosurgi  
> Created at: 2020-03-31 15:20:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-606693865  

Just a quick heads up. I see that you have done a lot of [progress here](https://github.com/boostorg/multiprecision/pull/190/commits), should I try testing it again with your latest changes?

---

## Comment 33

> Username: jzmaddock  
> Created at: 2020-03-31 16:13:15 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-606726584  

It's way better than it was - especially the library support - there's still one test failing that's not been investigated though....

---

## Comment 34

> Username: cosurgi  
> Created at: 2020-03-31 16:21:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-606730903  

Which one?

---

## Comment 35

> Username: kshegunov  
> Created at: 2021-01-18 00:17:41 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-761906786  

> Question: can double_double be doubled? And doubled again etc? In other words could these be implemented via nested templates?  
  
Should be in principle. I've done some preliminary work I needed for my own work [here](https://bitbucket.org/kshegunov/ans/src/master/ans-math/accumulator.h), mostly based on a paper by Knuth, I believe, on error-free transformations, but it's utterly unfinished as I had other stuff I had to do. Still the problems are significant, as to have the DD type behave like a real C++ primitive there's a lot of high precision constants necessary [e.g.](https://bitbucket.org/kshegunov/ans/src/master/ans-math/eft_data.h) coefficients for the elementary functions' approximation polynomials, for normalizations of the function arguments, etc.

---

## Comment 36

> Username: ckormanyos  
> Created at: 2021-02-23 21:52:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-784538286  

Restarting (with my offer to make potential progress as mentor on this) for [Boost Google Summer of Code 2021](https://github.com/boostorg/wiki/wiki/Google-Summer-of-Code:-2021)

---

## Comment 37

> Username: cosurgi  
> Created at: 2021-02-23 22:28:08 UTC  
> Updated at: 2021-02-23 22:35:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-784558914  

Very nice, thank you! I will be watching closely, ready to integrate into YADE. In principle it shouldn't be much more than just changing this file: https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/RealHP.hpp#L106 and https://gitlab.com/yade-dev/trunk/-/blob/master/lib/high-precision/RealHP.hpp#L119 :)  
  
Then we will have all YADE testing at our hand, for example this: https://gitlab.com/yade-dev/trunk/-/jobs/1048563190

---

## Comment 38

> Username: tinko92  
> Created at: 2021-03-04 23:32:53 UTC  
> Updated at: 2021-03-04 23:33:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-791028818  

I saw this issue recently and took notice because it seems connected to the floating-point expansions that I implemented for applications in geometric predicates (based on the works of Shewchuk, see https://www.cs.cmu.edu/~quake/robust.html ). Maybe a templated implementation that takes an arbitrary number of components could offer the same functionality as double-double or quad-double with more flexibility.  
  
My implementation of expansion arithmetic can currently be found in a PR to Boost.Geometry. It only contains the operations +, -, *, all implemented for exact computations. Because I think the concept might be of interest and most of the implementation that would be required for a demo is there already, I created a quick proof of concept based on the backend-skeleton. It is not polished and the sqrt and divide methods are probably not very robust, but it should be sufficient to illustrate the idea.  
  
The code can be found at https://github.com/tinko92/floating_point_expansion_number_type  
  
Here is an example of the results that this proof-of-concept produces: Let a = 8, b=3, then the formula (a/b) * b - a  
produces for n components:  
n=2: -2.46519e-32  
n=3: -1.36846e-48  
n=4: -7.59645e-65  
...  
n=8: -7.21326e-130  
  
which is roughly in line with what would be expected if a double has a precision of ~16 digits.  
  
The choice of algorithms is probably not ideal because my implementations of +, - and * are all for exact calculations and if we want to limit the number of components one could probably use faster algorithms, maybe the ones presented in http://perso.ens-lyon.fr/jean-michel.muller/07118139.pdf .  
  
The example I created uses a templates with a fixed limit of components, but it could also be dynamic for arbitrary precision (but limited by the exponent range, of course, so it would never make sense to have an result of more than ~40 components) using an std::vector instead of an array. There are other potential questions, e.g. whether to use zero-elimination for short expansions, whether to use the compress algorithm from Shewchuk's paper for renormalization or the renormalization presented by Joldes et al. and so on.

---

## Comment 39

> Username: ckormanyos  
> Created at: 2021-03-06 18:56:08 UTC  
> Updated at: 2021-03-06 18:56:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-792023349  

> I saw this issue recently and took notice because it seems connected to the floating-point expansions that I implemented for applications in geometric predicates ...  
  
Many thanks. Will look into this (and probably ask relevant questions) as the work on quad materializes and becomes closer to getting moving.

---

## Comment 40

> Username: ckormanyos  
> Created at: 2021-06-26 08:09:45 UTC  
> Updated at: 2021-06-26 08:10:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-868966393  

Work on quas had begun within the context of GSoC 2021. Some design choices have been made already/are to be made in the future.  
  
Let's review:  
  
- We decided to start quad-double BSL backend by reverting one step back and first constructing so-called `double_float` template class composed of two built-in floating-point components, low and high. The very preliminary draft of this is [here](https://github.com/BoostGSoC21/multiprecision/blob/develop/include/boost/multiprecision/cpp_double_float.hpp).  
- We feel it is best to implement this class and subsequently move on later to the quad double case, whereby it is not yet exactly clear if `quad_float` should be or how it will be based on/utilize the `double_float` case.  
- In addition, the preliminary draft of `double_float` is intended to be almost usable as a standalone class, with all its operators and elementary functions available directly via ADL. As a second step, we intend to place the syntactic backend `number` requirements on top of this (such as `eval_whatever` functions).  
  
@jzmaddock and @pabristow and @mborland and @NAThompson: Comments, suggestions, ideas?

---

## Comment 41

> Username: ckormanyos  
> Created at: 2021-06-26 09:29:11 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-868975245  

See also [this post](https://github.com/BoostGSoC21/multiprecision/issues/14)

---

## Comment 42

> Username: ckormanyos  
> Created at: 2021-08-17 07:54:54 UTC  
> Updated at: 2021-08-17 07:56:10 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-900077690  

> Work on quad has begun...  
  
If you have been following this older thread, there is a lot of new progress on _double_/_quad_-float within the context of the GSoC21 (thanks @sinandredemption and @cosurgi). We now have a really cool draft of _quad_. We decided to implement a flexible template called  
  
```  
template<typename FloatingPointType>  
class cpp_quad_float;  
```  
  
which can be instantiated for `float`, `double`, `long` `double` or `boost::multiprecision::float128`. This class quadruples these floating-point types basically in terms of precision.   
  
If you get the fork [here](https://github.com/BoostGSoC21/multiprecision), you can use `<boost/multiprecision/cpp_quad_float.hpp>` and some of its convenience types such as `boost::multiprecision::cpp_quad_dbl`. A sample is shown below.  
  
If you like, try quad-float on your favorite function or part of Boost.Math. We would like to hear field reports.  
  
I like using `boost::math::tgamma()`. The sample below shows a sample of this.  
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/math/constants/constants.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/cpp_quad_float.hpp>  
  
int main()  
{  
   using big_float_type = boost::multiprecision::cpp_quad_dbl;  
  
   // N[Sqrt[Pi], 201]  
   // 1.77245385090551602729816748334114518279754945612238712821380778985291128459103218137495065673854466541622682362428257066623615286572442260252509370960278706846203769865310512284992517302895082622893210  
  
   std::cout << std::endl << "represent_tgamma_half" << std::endl;  
  
   const big_float_type g    = boost::math::tgamma(big_float_type(0.5F));  
   const big_float_type ctrl = sqrt(boost::math::constants::pi<big_float_type>());  
  
   const big_float_type delta = fabs(1 - (g / ctrl));  
  
   const std::streamsize original_streamsize = std::cout.precision();  
   std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10) << g    << std::endl;  
   std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10) << ctrl << std::endl;  
   std::cout << std::scientific << std::setprecision(4) << delta << std::endl;  
   std::cout.precision(original_streamsize);  
   std::cout.unsetf(std::ios::scientific);  
  
   const bool result_is_ok = (delta < std::numeric_limits<big_float_type>::epsilon() * 40U);  
  
   std::cout << "result_is_ok: " << std::boolalpha << result_is_ok << std::endl;  
  
   return (result_is_ok ? 0 : -1);  
}  
```  
  
Cc: @jzmaddock, @mborland, @NAThompson, @pabristow

---

## Comment 43

> Username: pabristow  
> Created at: 2021-08-24 15:22:32 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-904739176  

Would the normal math tests – but including type boost::multiprecision::cpp_quad_dbl be a good starting test?  
  
Will take a fair while obviously, so unsuitable for a CI test?  
  
Or has this been done already?  
  
From: Christopher Kormanyos ***@***.***>  
Sent: 17 August 2021 08:55  
To: boostorg/multiprecision ***@***.***>  
Cc: Paul A. Bristow ***@***.***>; Mention ***@***.***>  
Subject: Re: [boostorg/multiprecision] Feature request: quad-double precision. (#184)  
  
  
Work on quad has begun...  
  
If you have been following this older thread, there is a lot of new progress on double/quad-float within the context of the GSoC21 (thanks @sinandredemption<https://github.com/sinandredemption> and @cosurgi<https://github.com/cosurgi>). We now have a really cool draft of quad. We decided to implement a flexible template called  
  
template<typename FloatingPointType>  
  
class cpp_quad_float;  
  
which can be instantiated for float, double, long double or boost::multiprecision::float128. This class quadruples these floating-point types basically in terms of precision.  
  
If you get the fork here<https://github.com/BoostGSoC21/multiprecision>, you can use <boost/multiprecision/cpp_quad_float.hpp> and some of its convenience types such as boost::multiprecision::cpp_quad_dbl. A sample is shown below.  
  
If you like, try quad-float on your favorite function or part of Boost.Math. We would like to hear field reports.  
  
#include <iomanip>  
  
#include <iostream>  
  
  
  
#include <boost/math/constants/constants.hpp>  
  
#include <boost/math/special_functions/gamma.hpp>  
  
#include <boost/multiprecision/cpp_quad_float.hpp>  
  
  
  
int main()  
  
{  
  
   using big_float_type = boost::multiprecision::cpp_quad_dbl;  
  
  
  
   // N[Sqrt[Pi], 201]  
  
   // 1.77245385090551602729816748334114518279754945612238712821380778985291128459103218137495065673854466541622682362428257066623615286572442260252509370960278706846203769865310512284992517302895082622893210  
  
  
  
   std::cout << std::endl << "represent_tgamma_half" << std::endl;  
  
  
  
   const big_float_type g    = boost::math::tgamma(big_float_type(0.5F));  
  
   const big_float_type ctrl = sqrt(boost::math::constants::pi<big_float_type>());  
  
  
  
   const big_float_type delta = fabs(1 - (g / ctrl));  
  
  
  
   const std::streamsize original_streamsize = std::cout.precision();  
  
   std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10) << g    << std::endl;  
  
   std::cout << std::setprecision(std::numeric_limits<big_float_type>::digits10) << ctrl << std::endl;  
  
   std::cout << std::scientific << std::setprecision(4) << delta << std::endl;  
  
   std::cout.precision(original_streamsize);  
  
   std::cout.unsetf(std::ios::scientific);  
  
  
  
   const bool result_is_ok = (delta < std::numeric_limits<big_float_type>::epsilon() * 40U);  
  
  
  
   std::cout << "result_is_ok: " << std::boolalpha << result_is_ok << std::endl;  
  
  
  
   return (result_is_ok ? 0 : -1);  
  
}  
  
Cc: @jzmaddock<https://github.com/jzmaddock>, @mborland<https://github.com/mborland>, @NAThompson<https://github.com/NAThompson>, @pabristow<https://github.com/pabristow>  
  
—  
You are receiving this because you were mentioned.  
Reply to this email directly, view it on GitHub<https://github.com/boostorg/multiprecision/issues/184#issuecomment-900077690>, or unsubscribe<https://github.com/notifications/unsubscribe-auth/AAIG4AJDGU2UEFYLEYB2POLT5IIVTANCNFSM4KIIZANA>.  
Triage notifications on the go with GitHub Mobile for iOS<https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675> or Android<https://play.google.com/store/apps/details?id=com.github.android&utm_campaign=notification-email>.

---

## Comment 44

> Username: ckormanyos  
> Created at: 2021-08-24 17:34:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-904840378  

> Would the normal math tests – but including type boost::multiprecision::cpp_quad_dbl be a good starting test?  
  
Hi @pabristow thank you for following up on this. Actually, there are quite a few dedicated Multiprecision tests in the standard Multiprecision CI which exercise both the tape's mathematical functions and operations as well as certain special functions from Math.  
  
We are in the process of using/adapting these for the reduced range(s) of double/quad-float. I thik we have about 1/3 or more of the tests running... So we are moving forward on that. We made a dedicated double/quad-float CI for this projec only in the GSoC timeframe so we have been running tests on pushes, PR and also a nightly CI.

---

## Comment 45

> Username: mborland  
> Created at: 2022-08-13 17:53:36 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1214197566  

@ckormanyos Seeing as though we did not participate in GSOC 2022 what still needs to happen on the GSOC 2021 fork to bring quad-double to MP?

---

## Comment 46

> Username: ckormanyos  
> Created at: 2022-08-15 05:42:24 UTC  
> Updated at: 2022-08-15 05:53:29 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1214639918  

> what still needs to happen on the GSOC 2021 fork to bring quad-double to MP  
  
Hi Matt (@mborland) we got a good start, but a lot still remains to do for getting Multiprecision-Ready.  
  
Together with Janek (@cosurgi) and our excellent GSoC '21 colleague Fahad (@sinandredemption) we got a good start on the basic algorithms and functionality.  
  
We implemented this thing with double-float and quad-float whereby instantiation with float, double, long double or even __float128 is foreseen.  
  
Quad-`float` is tricky since it has something like around 30 decimal digits but an exponent range of 39. Otherwise, the design was strong.  
  
From the top of my mind, we still need to...  
- Verify the design or choices made in numeric limits.  
- Ensure that algorithms add, sub, mul, div, sqrt are properly and efficiently implemented.  
- Implement more `eval_` functions. In this design we dicided to first go for standalone implementations, then subsequently hook them into `number` via `eval_` functions. If we (or anyone) started again, one might go directly to the `eval_` functions, as standalone is not really needed in the design.  
- Optimize elementary transcendentals for these low digit counts.  
- Integrate the backends into a lot of the existing standard tests. We did some testing hook ups to standard tests of sine, cosine, exp and the like but the list of these and their content are somewhat far from complete.  
  
This was (and is) a really cool project and it would be good to make some progress. I remember toward the end we did some really preliminary benchmarking and were a bit disappointed with the performance of quad double.  
  
Perhaps Fahad has some clear recollections of the state of open points.  
  
We also have some open issues and a few branches. They might handle the merge from main: I'll see if a good branch can be rebased or merged from main into branch tonight.  
  
Cc: @jzmaddock

---

## Comment 47

> Username: ckormanyos  
> Created at: 2022-12-28 18:08:10 UTC  
> Updated at: 2022-12-28 18:09:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1366831497  

> I'll see if a good branch can be rebased or merged from main into branch  
  
Hi Matt (@mborland) I was able to successfully merge the develop branch from BoostOrg into the GSoC double-double/quad-double GSoC21 repository.  
  
At the moment, only a few elementary function tests and (most of) the arithmetic tests run in a special CI process that I wrote for that project. The branches were about 500 commits behind BoostOrg, so I took a bit of time to merge.  
  
At the moment, the three branches `develop`, `gsoc2021_double_float_chris` and `gsoc2021_double_float` have been synchronized with both each other as well as with develop from BoostOrg. Any of these branches can be used to get an overview of what's going on.  
  
If I get a chance in the upcoming year, I'd like to get back to this project and maybe see if double-double or quad-double can be finished (enough) for productive use. We still have to work out the edge cases and get all the elementary function tests running.  
  
If anyone has interest, time, ideas, thoughts, please feel free to add/join-in  
  
Cc: @cosurgi and @mborland and @jzmaddock and @sinandredemption

---

## Comment 48

> Username: ckormanyos  
> Created at: 2022-12-28 18:11:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1366832985  

The fork in its updated state can be found [here](https://github.com/BoostGSoC21/multiprecision).  
  
Cc: @mborland and @jzmaddock

---

## Comment 49

> Username: jzmaddock  
> Created at: 2022-12-28 18:21:55 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1366838256  

Thanks Chris!  As you say, it was really just the corner cases and adequate testing that was holding this back, would be nice to see it finished off...

---

## Comment 50

> Username: ckormanyos  
> Created at: 2023-01-04 12:55:20 UTC  
> Updated at: 2023-01-04 12:56:18 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1370896892  

Hi @cosurgi and @sinandredemption  
  
- I spent a lot of time standardizing `double`-`float` and the new work behaves quite well with respect to the requirements of a Boost.Multiprecision type. Ultimately I decided to first handle `cpp_double_fp_backend` and when this is production-ready, then and only then move forward to `cpp_quad_fp_backend`  
- I invested a lot of time recently in NaNs, inf, zeros and implementing the needed `eval_`-whatever-functions. I also added a lot of (fork-local) tests including elementary functions, arithmetic tests, float-I/O, complex adaption and more.  
- I went for implementation of rudimentary `constexpr`-ness and targeted C++14.  
- Some mysterious behaviors remain such as with 10-byte `long` `double`, round tripping and spurios tolerance mysteries, but these are limited and the behavior is so far quite good.  
- Below you will find a sample of the cool things you can do with `number<cpp_double_double>` for instance.  
  
1. At this time, I could actually use some help implementing the full testing including spec-fun.  
2. I would also like to optimize some more `eval_`-functions such as trig functions (I did optimize exp() and log()).  
  
Can anyone actually help me with 1?  
Is anyone interested in contributing to 2?  
  
Cc: @mborland and @jzmaddock   
  
```  
#include <iomanip>  
#include <iostream>  
  
#include <boost/math/constants/constants.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/cpp_double_fp.hpp>  
  
int main()  
{  
  using double_double_type = boost::multiprecision::cpp_double_double;  
  
  constexpr double_double_type a = 1.5;  
  constexpr double_double_type b = 3.5;  
  constexpr double_double_type c = a * b;  
  constexpr double_double_type d = b / a;  
  
  // Test rudimentary compile time constexpr.  
  static_assert(c == 5.25);  
  static_assert(d < 3);  
  
  // Test tgamma(), sqrt() and pi<>().  
  
  double_double_type tg = boost::math::tgamma(double_double_type(0.5));  
  
  const auto flg = std::cout.flags();  
  
  std::cout << std::setprecision(static_cast<std::streamsize>(std::numeric_limits<double_double_type>::digits10))  
            << std::fixed  
            << tg  
            << std::endl;  
  
  std::cout << std::setprecision(static_cast<std::streamsize>(std::numeric_limits<double_double_type>::digits10))  
            << std::fixed  
            << sqrt(boost::math::constants::pi<double_double_type>())  
            << std::endl;  
  
  const auto relative = fabs(1 - (tg / sqrt(boost::math::constants::pi<double_double_type>())));  
  
  const auto result_tg_is_ok = (relative < (std::numeric_limits<double_double_type>::epsilon() * 10));  
  
  const auto result_is_ok = result_tg_is_ok;  
  
  std::cout << "result_is_ok: " << std::boolalpha << result_is_ok << std::endl;  
  
  std::cout.flags(flg);  
  
  return (result_is_ok ? 0 : -1);  
}  
  
```

---

## Comment 51

> Username: sinandredemption  
> Created at: 2023-01-05 07:51:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1371885031  

Hello @ckormanyos   
  
I would definitely want to contribute to #1. Please allow me some time to re-familiarize myself with the codebase, and setup my development environment.

---

## Comment 52

> Username: ckormanyos  
> Created at: 2023-01-05 13:33:49 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1372221655  

> I would definitely want to contribute to https://github.com/boostorg/multiprecision/issues/1  
  
Hi @sinandredemption (Fahad) I'm happy to see you on this thread.  
  
At the present time, I am doing quite a few small, but significant changes reagrding use of NaNs, inf, limits, zeros and the 128-bit floating-point type.  
  
I actually found that Multiprecision standalone is really clean regarding some of these items. I also just finished up using Boost.Multiprecision's near-native `float128_type` instead of the wrapped `number` type that we had used during the GSoC. This is a big round of changes. So in a short time, you can use `cpp_double_float128` with a lot less overhead. In fact, I even removed the dependency for non-ANSI so you can use this type with eithe std=c++14,17,2a,... or std=gnu++14,17,2a.  
  
One of the first significant testing problems I found has been in rounding near the _split_-point of the division between fisrt/second. So if you'd like to look at the rounding tests that would be a great place to start.  
  
Cc: @cosurgi and @jzmaddock and @mborland

---

## Comment 53

> Username: ckormanyos  
> Created at: 2023-01-05 13:39:36 UTC  
> Updated at: 2023-01-05 13:40:45 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1372227345  

Hi @sinandredemption there is also another point regarding even more adaption to `constexpr`-ness. There are still some `constexpr` artifacts (like in `numeric_limits`) that are compile-time-evaluated yet non-`constexpr`. Often times this is because of the use of something like `sqrtf()`, `sqrt()`, `sqrtl()`, `sqrtq()` or similar for `frexp()`, `ldexp()`.  
  
This results in a handful of functions that _could_ use some of Matt's (@mborland) `constexpr`-cmath faculties to go all the way `constexpr` on `numeric_limits<>`. This will possibly also apply to functions like `eval_sqrt()`, probably even `eval_log()` and `eval_exp()` too.  
  
This could also be an interesting place to jump on and contribute. I can explain more in offline chat(s) if/when needed.

---

## Comment 54

> Username: ckormanyos  
> Created at: 2023-01-06 14:04:28 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1373687563  

Introduction of the `cpp_double_fp_backend<>` template has begun in #515, but this will take a few revisions until complete.  
  
If/when this finishes in a whle, I'd then suggest we move on with _quad_-_float_  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock and @mborland

---

## Comment 55

> Username: AuroraDysis  
> Created at: 2024-01-11 21:06:04 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1887961024  

If possible, could you support a greater variety of combinations?  
  
For example, [MultiFloats.jl](https://github.com/dzhang314/MultiFloats.jl) is a very fast library that represents extended-precision numbers with 2x, 3x, ..., up to 8x the precision of double, which might be helpful.

---

## Comment 56

> Username: ckormanyos  
> Created at: 2024-01-12 06:36:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1888511307  

> could you support a greater variety of combinations?  
  
Hi @AuroraDysis that looks interesting and many thanks for your interest in double/quad/multi-float(s).  
  
During the GSoC and after that, I remember We/I were having trouble figuring out the exact algorithms for add/sub/mul/div and struggling with overflow/underfolw/NanNs. For the moment, this project is on the back burner.  
  
Perhaps the Julia code you reference could give inshight into this. If I/we ever pick up this issue, we will be sure to have a look, but i fear this might have rather low priority at the moment.

---

## Comment 57

> Username: AuroraDysis  
> Created at: 2024-01-12 07:01:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1888533837  

@ckormanyos Thanks for your reply. I found multi-floats to be very useful in my research area and believe it will play an even more important role in the future. If you have the chance to pick up this project again and need some help, I'd like to contribute or collaborate with you.  
  
As for double floats, [DoubleFloats.jl](https://github.com/JuliaMath/DoubleFloats.jl) is the most mature library I have ever used, although it is not as fast as [MultiFloats.jl](https://github.com/dzhang314/MultiFloats.jl).

---

## Comment 58

> Username: ckormanyos  
> Created at: 2024-01-13 16:41:12 UTC  
> Updated at: 2024-01-13 16:41:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-1890588570  

> found multi-floats to be very useful in my research area and believe it will play an even more important role in the future. If you have the chance to pick up this project again and need some help, I'd like to contribute or collaborate with you.  
  
The plan is to do this and look into multi-floats and ultimately get something adhering to Boost.Multiprecision/Math. If (which actually means when) I/we watm this up again, I'll be sure to look into your referencs and contact you regarding forward motion.  
  
Thanks @AuroraDysis for your interest and willingness to help. When this gets moving again, we will be in touch.

---

## Comment 59

> Username: LegalizeAdulthood  
> Created at: 2024-12-13 16:40:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2541835205  

I'm not an expert in the details of floating-point math, but I'm pretty good at beating build systems into submission `:)`, and I poked around on [my fork of the existing QD library](https://github.com/LegalizeAdulthood/QD) to make it build under CMake.  If I can be of some assistance, please ping me.

---

## Comment 60

> Username: ckormanyos  
> Created at: 2024-12-13 17:13:32 UTC  
> Updated at: 2024-12-13 17:14:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2541892428  

> If I can be of some assistance, please ping me  
  
Well when we left off, there were some edge cases in the full test suite of Boost.Math's special functions when using the double-double and quad-double types. Some of these were probably resolved since there actually were problems at edge exponents of elliptic integrals and similar like Carlson funcs.  
  
Aside from that, we had two competing implementations of mul/div and these seemed to slightly differ.  
  
I would like to:  
  
- Find the right primitives for add/sub/mul/div/sqrt  
- Pass all of our local repo tests.  
- Pass the tests when our types are used in Boost.Math's specfun tests.  
  
It might take a while to, let's say, warm this up, but we really weren't too far off the mark in the day.  
  
I might have a slot to look into this in upcoming, but I really have a lot of time pressuer in life.  
  
Kind regards @LegalizeAdulthood   
  
Cc: @mborland and @jzmaddock and @cosurgi

---

## Comment 61

> Username: LegalizeAdulthood  
> Created at: 2024-12-13 20:10:26 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2542226264  

Is there a repo with a github wirkflow for the tests I can clone?

---

## Comment 62

> Username: ckormanyos  
> Created at: 2024-12-14 18:02:19 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543217011  

> Is there a repo with a github wirkflow for the tests I can clone?  
  
Yes certainly. Again i think we got pretty close to stability.  
  
- I'm torn between two worlds, the old double-double and newer implementations.  
- I also forgot to mention, we were getting stuck and tripped up on infs, NaNs and such edge cases.  
  
So if we can just get stable operations and edge case handling this thing will be done. The formal testing and plug in to Multiprecisin was/is about done. but I need to get the branch and see if it still plays with Boost-Develop branch.

---

## Comment 63

> Username: ckormanyos  
> Created at: 2024-12-14 18:04:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543222699  

> Is there a repo with a github wirkflow for the tests I can clone?  
  
Yes Richard (@LegalizeAdulthood) we did this work in the 2021 Boost-GSoC.  
  
The repo is [here](https://github.com/BoostGSoC21/multiprecision).  
  
I'll take a few minutes ASAP to see if it shakes out well with merge develop into our branch and then we can start to boogy.  
  
Cc: @mborland and @jzmaddock

---

## Comment 64

> Username: ckormanyos  
> Created at: 2024-12-14 18:20:22 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543256862  

OK @LegalizeAdulthood in [this sync attempt](https://github.com/BoostGSoC21/multiprecision/pull/150) I'm attempting to merge Boost.Develop Multiprecision into our branch at GSoC2021 to get this thing ready for action.

---

## Comment 65

> Username: ckormanyos  
> Created at: 2024-12-15 08:38:02 UTC  
> Updated at: 2024-12-15 08:39:37 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543558929  

> attempting to merge Boost.Develop Multiprecision into our branch at GSoC2021 to get this thing ready for action.  
  
It worked rather well. But ONLY the `cpp_double_fp_backend` is exercised in CI at the moment. Quad is still pending.  
  
The blaze-down on _double_-something is as follows:  
  
- Running only `cpp_double_fp_backend` (no quad backend in  CI yet).  
- Most tests pass for double-`float`, double-`double`, double-`long double` and (if available) double-`__float128`.  
- Algebra, limits and all normal stuff such as elementary transcendental functions all pass.  
- There are five (5) files failing on _specfun_.  
- Of these _specfun_ failures, some fail simple tolerances. Other failures are due to limited exponent range. These don't bother me.  
- Some very few selected _specfun_ failures are due to confusion between zero and NaN or similar. These are pretty much the last remaining true problems in `double`-whatever.  
  
I did, in fact, merge this to the develop of the GSoC branch even _with_ certain failures of _specfun_.  
  
So what does this mean?  
  
- It is difficult to reach the quality needed for Multiprecision backend.  
- But at least `cpp_double_fp_backend` is close to getting there.  
- Algebra, most elementary transcendental functions and limits work fine.  
- I will be pursuing the final edge cases that fail.  
- We need to review add/sub/mul/div/sqrt to ensure that the right, optimal algorithms are being used.  
- Edge cases of zeros/NaNs seem to need work.  
- Some specfun tests having huge exponential results need to be excluded for double-double testing.  
- Need to restore `test_exp.cpp` which I removed temporarily from the Jamfile.  
  
Cc: @LegalizeAdulthood and @jzmaddock and @mborland

---

## Comment 66

> Username: ckormanyos  
> Created at: 2024-12-15 12:03:55 UTC  
> Updated at: 2024-12-15 12:04:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543846235  

> I found multi-floats to be very useful in my research area and believe it will play an even more important role in the future. If you have the chance to pick up this project again and need some help, I'd like to contribute or collaborate with you.  
  
Hello @AuroraDysis based on interest from @LegalizeAdulthood as well as my own, I am, in fact, picking up this project again.  
  
I think at the moment the project is struggling right at the double-double level to actually get the right algorithms for add/sub/mul/div/sqrt and a few more. I looked at the links to the Julia-based code you gave and the double-double primitives look quite good. Have you ever converted any of these DD/QD primitives to the C/C++ domain? I can start that if need be.

---

## Comment 67

> Username: AuroraDysis  
> Created at: 2024-12-15 12:44:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2543859617  

Hi @ckormanyos, I'm excited to hear about the renewed interest in this project.  
  
> I think at the moment the project is struggling right at the double-double level to actually get the right algorithms for add/sub/mul/div/sqrt and a few more. I looked at the links to the Julia-based code you gave and the double-double primitives look quite good. Have you ever converted any of these DD/QD primitives to the C/C++ domain? I can start that if need be.  
  
As for your question, I have not yet had the opportunity to convert it to C/C++.

---

## Comment 68

> Username: ckormanyos  
> Created at: 2024-12-23 10:27:48 UTC  
> Updated at: 2024-12-23 10:30:08 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2559389225  

> Is there a repo with a github wirkflow for the tests I can clone?  
  
Hi @LegalizeAdulthood just for info, we are finishing the extreme edge-cases of the `cpp_double_fp_backend<T>` template at the moment. We have a few tiny issues with subnormals NaNs, infinities and zeros.  
  
The type is, however, robust enough to use for, let's say, regular calculations such as those that do not over/underflow or reach NaNs.  
  
Just for _fun_, I plugged the `cpp_double_double` backend having precision of 32 decimal digits into a full Mandelbrot calculation. I compared this with `cpp_dec_float<32>`. The double-double type is about twice as fast (see table below).  
  
Some may consider this to be _slow_, as I do, compared to the expectations for this type. But I definitely think this type is worth pursuing for several reasons. The safety (infinities, NaNs, divide-by-zero, etc.) checks that make this type well-behaved also slow it down considerably. I could imagine a version that throws away checks and is specifically called _unsafe_. Furthermore, I feel that the double-double backend is/could-be particulalry well-suited for GPU programming if we ever get around to that. So the initial timing report indicates quite some potential down the road.  
  
### VERY Initial Timing Report  
  
- Full classic Mandelbrot image $2048{\times}2048$ pixels at 32 decimal digit precision.  
- `cpp_double_double` versus `cpp_dec_float<32>`  
- $2000$ iterations, half-width $1.35$, centered around the point $(0.0, -0.75i)$.  
- Using 15 cores multithreading on Intel Core-i9.  
  
| number type                                    | time (s)  |  
| -------------------------------------- | --------- |  
| `cpp_double_double`                       | 27          |  
| `cpp_dec_float<32>`                       | 52          |  
  
  
Cc: @mborland and @sinandredemption and @cosurgi and @jzmaddock

---

## Comment 69

> Username: ckormanyos  
> Created at: 2025-01-05 19:51:30 UTC  
> Updated at: 2025-01-05 20:04:09 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2571733317  

So, we are just about done cleaning up and optimizing `cpp_double_fp_backend`. We have working `cpp_double_float`, `cpp_double_double` , `cpp_double_long_double` and (with the right compilers) `cpp_double_float128`.  
  
I squeezed down the perf a bit on:  
- Full classic Mandelbrot image $2048{\times}2048$ pixels at 32 decimal digit precision.  
- `cpp_double_double` versus `cpp_dec_float<32>`  
- $2000$ iterations, half-width $1.35$, centered around the point $(0.0, -0.75i)$.  
- Using 15 cores multithreading on Intel Core-i9.  
  
| number type                                      | time (s)  |  
| ---------------------------------------- | --------- |  
| `cpp_double_double`                         | 19          |  
| `cpp_dec_float<32>`                         | 52          |  
| `cpp_bin_float<32, digit_base_10>`  | 89          |  
  
See the image below. And also note that low digits is one of the few and rare parameter ranges in which `dec_float` _might_ have the upper-hand on `bin_float` perf. So `cpp_couble_fp_backend<double>` is really fast here.  
  
There could/would be a lot more to push down if we enabled something like a template parameter:  
  
```  
const bool SkipEdgeChecks = false  
```  
  
![Image](https://github.com/user-attachments/assets/8653235b-d844-440b-bfe1-c833be2a4671)  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock and @mborland

---

## Comment 70

> Username: ckormanyos  
> Created at: 2025-01-05 19:53:38 UTC  
> Updated at: 2025-01-05 19:53:48 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2571733838  

I have a good mind to finish the docs and try for a review among our authors and to get `cpp_double_fp_backend` into 1.88 in the early spring.  
  
Cc: @sinandredemption and @cosurgi and @jzmaddock and @mborland

---

## Comment 71

> Username: LegalizeAdulthood  
> Created at: 2025-01-06 18:43:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2573699021  

Which version of the library were you using for the QD backend?

---

## Comment 72

> Username: ckormanyos  
> Created at: 2025-01-06 20:29:39 UTC  
> Updated at: 2025-01-06 20:41:25 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2573872171  

> Which version of the library were you using for the QD backend?  
  
None of them. Neither of them. No previously published QD is used as a library. Nor is any previously published DD used as a library.  
  
Hi Richard (@LegalizeAdulthood)  
  
- In this first step, we are _only_ handling the `cpp_double_fp_backend`.  
- The quad-fp-backend is scheduled for later.  
- We will not be using any published library code. This is self-written code. From a subjective viewpoint, there was no suitable backend code around the internet that worked in all edge cases. So I rewrote the algorithms from old and new literature to better handle efficiency, min/max/exactness situations.

---

## Comment 73

> Username: LegalizeAdulthood  
> Created at: 2025-01-06 23:21:34 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2574097541  

@ckormanyos I see, very interesting!  I found the existing QD library to be pretty crufty as well.  I'm not sure if it was because of the fortran support or just the author's inclinations.  
  
> So I rewrote the algorithms from old and new literature to better handle efficiency, min/max/exactness situations.  
  
With the existing QD library I did notice that some tests failed around the edge cases.  (See my [cmake branch on my fork](https://github.com/LegalizeAdulthood/QD)).  That made me a little nervous, but I didn't have time to dig into the details of hte literature to understand the failure.  
  
Is your rewrite availble on github?

---

## Comment 74

> Username: ckormanyos  
> Created at: 2025-01-07 06:23:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2574478353  

>> So I rewrote the algorithms from old and new literature to better handle efficiency, min/max/exactness situations.  
  
> Is your rewrite availble on github?  
  
Yes. It is header-only C++ at the moment located [here](https://github.com/BoostGSoC21/multiprecision). The code is very new and in an infant state, but it is getting there.  
  
If you clone that repo, it is a fork of what we call the Boost.Multiprecision submodule.  
  
To use it:  
  
- Locate the directory `include`.  
- Add that `include` directory to your compiler include path.  
- Then in your code `#include <boost/multiprecision/cpp_double_fp_backend.hpp>`.  
- You can start by using one of the prefabricated types like `boost::multiprecision::cpp_double_double` and use it just like you would use ordinary `double`.  
- See example below.  
  
We can/will be modifying code internals in the future. So these algorithms like add/sub/mul/div/sqrt can evolve. Feel free to comment, add change requests or get involved if you like.  
  
```  
  
#include <boost/multiprecision/cpp_double_fp.hpp>  
  
#include <iostream>  
#include <iomanip>  
#include <sstream>  
  
auto main() -> int  
{  
  using dd_type = boost::multiprecision::cpp_double_double;  
  
  dd_type frac = dd_type { 1 } / 3;  
  
  std::stringstream strm { };  
  
  strm << std::setprecision(std::numeric_limits<dd_type>::digits10) << frac;  
  
  std::cout << strm.str() << std::endl;  
}  
```

---

## Comment 75

> Username: ckormanyos  
> Created at: 2025-01-07 21:13:12 UTC  
> Updated at: 2025-01-07 21:15:42 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2576230363  

Hello @LegalizeAdulthood   
  
One other real benefit of our Multiprecision types is that they unabatedly and seamlessly interoperate with Boost.Math. A lot of great math can be done this way.  
  
In the example below, I check that (for `cpp_double_double`)  
  
$$  
\Gamma\left(1/2\right)~{\approx}~\sqrt{\pi}  
$$  
  
```  
  
#include <boost/math/constants/constants.hpp>  
#include <boost/math/special_functions/gamma.hpp>  
#include <boost/multiprecision/cpp_double_fp.hpp>  
  
#include <iostream>  
#include <iomanip>  
#include <sstream>  
  
auto main() -> int  
{  
  using dd_type = boost::multiprecision::cpp_double_double;  
  
  constexpr dd_type half = dd_type { 1 } / 2;  
  
  const dd_type g_half { boost::math::tgamma(half) };  
  const dd_type sqrt_pi { sqrt(boost::math::constants::pi<dd_type>()) };  
  
  std::stringstream strm { };  
  
  strm << "g_half : " << std::setprecision(std::numeric_limits<dd_type>::digits10) << g_half  << '\n';  
  strm << "sqrt_pi: " << std::setprecision(std::numeric_limits<dd_type>::digits10) << sqrt_pi << '\n';  
  
  std::cout << strm.str() << std::endl;  
}  
```  
  
This is a great achievement in generic programming basically out of the box. We worked hard for this over the years, and @jzmaddock has been our guide and led this great effort for years.  
  
Cc: @mborland and @NAThompson

---

## Comment 76

> Username: cosurgi  
> Created at: 2025-01-08 18:39:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578367263  

I am now running a benchmark of `yade --stdperformance 5` against `boost::multiprecision::cpp_double_double`. It is a simple simulation where 10000 spheres (imagine they are grains of sand) fall down inside a box and settle down. Below is an image from [YADE paper](https://arxiv.org/pdf/2108.04207) where we have implemented high precision calculations with MPFR inside yade.  
  
![Image](https://github.com/user-attachments/assets/fd951b2c-aa1d-4d6b-97b9-25c54c92229a)

---

## Comment 77

> Username: cosurgi  
> Created at: 2025-01-08 18:46:00 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578377772  

Forgot to mention. I am using develop branch with latest commit 9f346580

---

## Comment 78

> Username: cosurgi  
> Created at: 2025-01-08 19:00:35 UTC  
> Updated at: 2025-01-08 19:12:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578411920  

However to compile yade with `boost::multiprecision::cpp_double_double` I had some compiler errors concerning MPFR.   
  
In this configuration yade is using `boost::multiprecision::cpp_double_double` as the base precision type, but simultaneously higher precision types are also used and compiled in. These can be either `boost::multiprecision::mpfr_float_backend` or `boost::multiprecision::cpp_bin_float` depending on configuration options.  
  
At first shot I used MPFR. And there were some compiler errors such as:  
  
```  
/usr/include/boost/math/constants/constants.hpp:313:3: error: no matching function for call to   
‘boost::math::constants::detail::constant_catalan  
<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<62, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_off>   
>::get(boost::math::constants::construction_traits  
<boost::multiprecision::number<boost::multiprecision::backends::mpfr_float_backend<62, boost::multiprecision::allocate_dynamic>, boost::multiprecision::et_off>,   
boost::math::policies::policy<boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy,   
boost::math::policies::default_policy, boost::math::policies::default_policy, boost::math::policies::default_policy> >::type)’  
  
  313 |   BOOST_DEFINE_MATH_CONSTANT(catalan, 9.159655941772190150546035149323841107e-01, "9.15965594177219015054603514932384110774149374281672134266498119621763019776254769479356512926115106248574422619e-01")  
  
/usr/include/boost/multiprecision/mpfr.hpp:2270:37: note: candidate: ‘template<int N> static const   
 2270 |    static inline const result_type& get(const std::integral_constant<int, N>&)  
      |                                     ^~~  
/usr/include/boost/multiprecision/mpfr.hpp:2270:37: note:   template argument deduction/substitution failed:  
```  
I think this error may be because I used the linux systemwide installation of boost 1.74 and only replaced the `/usr/include/boost/multiprecision` directory with new [multiprecison code](https://github.com/BoostGSoC21/multiprecision). Hence it is a mix of two different boost versions in the same directory, so some incompatibilities between Boost.Math and Boost.Multiprecision might have arisen.  
  
But if you think it might be due to latest changes in Boost.Multiprecision I could try to make a minimal failing example and we could move this into a separate issue.  
  
  
But I was able to compile yade despite these errors and run the benchmarks. I could simply comment out the offending line, because these errors were in diagnostics/testing part of the code - the code not used by `yade --stdperformance`.  
  
I will post the benchmark results once they finish.

---

## Comment 79

> Username: ckormanyos  
> Created at: 2025-01-08 19:16:24 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578438791  

> I am now running a benchmark of yade `--stdperformance 5` against `boost::multiprecision::cpp_double_double`  
  
Awesome Janek (@cosurgi) thank you. Now I'm scared to see how the infant `cpp_double_fp_backend` faces its first tough _real_ _world_ challenge.  
  
> Hence it is a mix of two different boost versions in the same directory, so some incompatibilities between Boost.Math and Boost.Multiprecision might have arisen.  
  
It would be best to have both Math and Multiprecision synchronized. In your case, however, if these are the only two Boost libraries you need, then you could actually make use of their new standalone feature. These two libraries are specifically and purposely standalone and do not rely on any other parts of Boost. This all happened about 2 years ago so right around that 1.74 time.  
  
> But if you think it might be due to latest changes in Boost.Multiprecision we could move this into a separate issue.  
  
It does seem like a coincidence that you encounter resolution of function problems around 62 deigits, which is similar to the range of `cpp_double_double`. But at the moment I could only speculate. I think the best bet is to synchronize both Math as well as the Multiprecision submodules to the state of today and include these two prior to your system include of 1.74.  
  
Thanks Janek and let's see how this thing shakes out.

---

## Comment 80

> Username: ckormanyos  
> Created at: 2025-01-08 19:19:05 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578445129  

> Forgot to mention. I am using develop branch with latest commit  
  
Hi @cosurgi this is really _hot_ _off_ _the_ _press_, but I am actually cycling the `cpp_double_fp_backend`-branch in the real Multiprecision submodule right now. And everything just went GREEN here. So you might like to get on that [branch and PR 648](https://github.com/boostorg/multiprecision/pull/648) for the bleeding edge.

---

## Comment 81

> Username: cosurgi  
> Created at: 2025-01-08 19:24:51 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578459507  

Not so good news. Single run of `yade --stdperformance` with `boost::multiprecision::float128` took about 15 minutes. Now  1 hour has passed with `boost::multiprecision::cpp_double_double` and the single run still didn't finish.

---

## Comment 82

> Username: ckormanyos  
> Created at: 2025-01-08 19:50:12 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578503760  

> Not so good news. Single run of yade `--stdperformance` with `boost::multiprecision::float128` took about 15 minutes. Now 1 hour has passed with boost::multiprecision::cpp_double_double and the single run still didn't finish.  
  
That is kind of a preliminary disaster. Let's see if it finishes at all. One problem I encountered in my first performance run was that interconversions between `cpp_double_double` and any other MP type rely on string conversions at the moment. So if there are mixed MP types that could explain the disaster.  
  
Otherwise we would need to find out how and why this is so disasterously slooooowwwww.

---

## Comment 83

> Username: ckormanyos  
> Created at: 2025-01-08 19:53:07 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578509050  

> how and why this is so disasterously slooooowwwww  
  
Another point. You are doing full physics with probably lots of use of elementary transcendental functions. At the moment, `cpp_double_fp_backend` _only_ has specializations for `exp()` and `log()`. So you are using excruciatingly slow elementary functions from the default cataloge of Multiprecision (like using Taylor series for `sin()` etc.  
  
I have been doing exclusively algebra-only tests to mark the real performance of the raw type. On the other hand, we could be experiencing a real disaster...

---

## Comment 84

> Username: ckormanyos  
> Created at: 2025-01-08 19:55:50 UTC  
> Updated at: 2025-01-08 19:55:58 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578514815  

I will take a day and run John's suite of performance tests that stress floating-point operations and report back when finished. We did some of these during the GSoC with @sinandredemption and they were looking good. It is time to reproduce these tests (which admittedly I have not done this year, but will).

---

## Comment 85

> Username: cosurgi  
> Created at: 2025-01-08 20:51:09 UTC  
> Updated at: 2025-01-08 21:10:40 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578640884  

The first run is finished, the very preliminary result is that `cpp_double_double` is 7.3 times slower than `float128`.  
  
Indeed I am using here several transcendental and elementary functions. Of all of them, I think `sqrt(…)` and `pow(… , 2)` are used really the most.  
  
EDIT: oh yeah, and probably a bit of trigonometric `sin(…)`, `cos(…)`, `tan(…)` functions too.

---

## Comment 86

> Username: ckormanyos  
> Created at: 2025-01-08 21:04:26 UTC  
> Updated at: 2025-01-08 21:05:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578664030  

> The first run is finished, the very preliminary result is that `cpp_double_double` is 7.3 times slower than `float128`.  
  
Thank you Janek for this preliminary result, even though somewhat challenging.  
  
I just ran algebraic tests locally `cpp_double_double` versus `cpp_bin_float<32>` and the double-double type was about 3-10 times faster except for string operations. I will publish the table tomorrow.  
  
Hmmmm... I wonder if you are facing off a software-emulated type with a hardware type ( `float128`). That would not ever be a _fair_ fight. I will publish more detailed results tomorrow. Just for fun, you could run Yade in that test with something like one of either `cpp_bin_float` or `cpp_dec_float` configured for 32 decimal digits of precision?  
  
I think we need to see what you are actually testing here. I suppose the elementary transcendental functions could make a huge difference but that huge would surprise me. Well, we will eventually get to the bottom of this.

---

## Comment 87

> Username: cosurgi  
> Created at: 2025-01-08 21:09:01 UTC  
> Updated at: 2025-01-08 22:41:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578672268  

> So if there are mixed MP types that could explain the disaster.  
  
Yade supports mixed MP types. But in this particular simulation they are not used. Everything is done with `cpp_double_double`. So no string conversions involved.

---

## Comment 88

> Username: cosurgi  
> Created at: 2025-01-08 22:50:17 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578829097  

> It would be best to have both Math and Multiprecision synchronized.  
  
Yes, upgrading Boost.Math solved the compilation problem. Now I can compile yade with new type just with this super small diff:  
  
```diff  
--- a/lib/high-precision/Real.hpp  
+++ b/lib/high-precision/Real.hpp  
@@ -130,10 +130,10 @@ namespace math {  
-#include <boost/multiprecision/float128.hpp>  
+#include <boost/multiprecision/cpp_double_fp.hpp>  
 namespace yade {  
 namespace math {  
-       using UnderlyingReal = boost::multiprecision::float128;  
+       using UnderlyingReal = boost::multiprecision::cpp_double_double;  
 }  
 }  
```  
  
And also full math diagnostics which I have implemented in yade has now compiled without problems. This might come useful, because I was testing almost all math functions, see Table 4 in [YADE paper](https://arxiv.org/pdf/2108.04207). (The large errors in `cpp_bin_float` have since been fixed https://github.com/boostorg/multiprecision/issues/264 )  
  
> you could run Yade in that test with something like one of either cpp_bin_float or cpp_dec_float configured for 32 decimal digits of precision?  
  
You are right, I will try `boost::multiprecision::cpp_bin_float<32>` and `boost::multiprecision::mpfr_float_backend<32>`  
  
Are you sure it should be 32 decimal places? `digits10` says it's 31…  
  
Also I have in mind testing `cpp_double_long_double` and `cpp_double_float128` against `cpp_bin_float` and `mpfr_float_backend`. This will take some time though.

---

## Comment 89

> Username: cosurgi  
> Created at: 2025-01-08 23:21:41 UTC  
> Updated at: 2025-01-09 12:53:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-2578866210  

> facing off a software-emulated type with a hardware type (`float128`). That would not ever be a *fair* fight.  
  
Are you sure `float128` is hardware? I was thinking that it was offered by the compiler either as `__float128` or `_Quad`, both of which do not have associated hardware assembly instructions, but the compiler emulates them. I can be wrong though.  
  
But yeah, I will now compare against `cpp_bin_float` and `mpfr_float_backend`.

---

## Comment 90

> Username: ckormanyos  
> Created at: 2025-06-28 18:08:12 UTC  
> Updated at: 2025-06-28 18:08:46 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3015964652  

So we just merged `cpp_double_fp_backend` from GSoC21 into develop with c4e86ea00d98a950aa4a6918648a12b6ef7dc3af  
  
The double-floating-point backend is the first step to getting the quad one. We need to see what experiences we make for a few months with double-fp. Then we can start discussing tangible plans for the quad backend.  
  
In the double-fp work, we wrote the add/sub/mul/div/sqrt algorithms from scratch (albeit based on previous examples and literature). So we did not actually wrap any existing library. I believe this is the best way in this case and will also be best for quad.

---

## Comment 91

> Username: cosurgi  
> Created at: 2025-06-30 12:08:55 UTC  
> Updated at: 2025-06-30 12:09:47 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3018909552  

Yes, this was an epic effort. Congratulations everyone involved! Especially Chris, thank you for pushing it towards the end!

---

## Comment 92

> Username: ckormanyos  
> Created at: 2025-06-30 18:32:23 UTC  
> Updated at: 2025-06-30 18:32:43 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3020297101  

> Yes, this was an epic effort. Congratulations everyone involved! Especially Chris, thank you for pushing it towards the end!  
  
Thank you Janek. Thank you everyone.  
  
What I'm thinking is we can let the (intermediate state) of `cpp_double_fp_backend` get into the field for a while and see what we learn from our client experiences.  
  
Not too long after that, we might consider addressing the original point which was quad-fp. I think it is feasible. I'm not sure exactly how to adapt the algorithms, but I think we will figure that out in time.  
  
Cc: @sinandredemption and @mborland and @jzmaddock

---

## Comment 93

> Username: ckormanyos  
> Created at: 2025-06-30 18:34:39 UTC  
> Updated at: 2025-06-30 18:35:01 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3020303244  

Oh we can also optimize the `cpp_double_fp_backend` to some extent. I have a few ideas already on that. But as @jzmaddock has mentioned on several occasions, get it working, then optimize it. And I definitely agree with that general wisdom.

---

## Comment 94

> Username: LegalizeAdulthood  
> Created at: 2025-06-30 20:06:44 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3020537279  

> What I'm thinking is we can let the (intermediate state) of cpp_double_fp_backend get into the field for a while and see what we learn from our client experiences.  
  
Does having it in develop imply it will be in the next boost release (1.89)?

---

## Comment 95

> Username: ckormanyos  
> Created at: 2025-06-30 20:25:56 UTC  
> Updated at: 2025-06-30 20:28:38 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3020582224  

>> What I'm thinking is we can let the (intermediate state) of `cpp_double_fp_backend` get into the field for a while and see what we learn from our client experiences.  
  
> Does having it in develop imply it will be in the next boost release (1.89)?  
  
Yes Richard (@LegalizeAdulthood) the `cpp_double_fp_backend` has made the July 3rd deadline for inclusion in Boost 1.89. We decided to press for this.  
  
Basically in our development flow, getting into develop prior to the posted deadline (which is in a few days) will get merged to master and subsequently be released in the upcoming release in usual cases.  
  
So it's in! Yeah!

---

## Comment 96

> Username: LegalizeAdulthood  
> Created at: 2025-06-30 20:30:21 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/184#issuecomment-3020592850  

Woo hoo!
