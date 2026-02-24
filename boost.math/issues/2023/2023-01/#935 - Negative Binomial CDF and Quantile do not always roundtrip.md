# #935 - Negative Binomial CDF and Quantile do not always roundtrip [Open]

> Username: mborland  
> Created at: 2023-01-30 19:58:09 UTC  
> Updated at: 2023-02-05 05:09:43 UTC  
> Url: https://github.com/boostorg/math/issues/935  

Failure can be found here: https://github.com/scipy/scipy/pull/17432/checks?check_run_id=10961417302  
Found on PR: https://github.com/scipy/scipy/pull/17432  
  
Minimal reproducer:  
  
```  
import numpy as np  
import numpy.testing as npt  
from scipy import stats  
  
dist = stats.nbinom(5, 0.5)  
supp = 6  
cdf_supp = dist.cdf(supp)  # ideally 0.7255859375  
cdf_supp0 = cdf_supp - 10*np.spacing(cdf_supp)  # 0.7255859374999989  
npt.assert_array_equal(dist.ppf(cdf_supp0), supp)  # both should be 6  
```  
  
Note in their code says:  
  
In very rare cases, the finite precision calculation of ppf(cdf(supp))  
can produce an array in which an element is off by one.  We nudge the  
CDF values down by 10 ULPs help to avoid this.  
  
At 15 ULPs it seems to be correct.

---

## Comment 1

> Username: mdhaber  
> Created at: 2023-01-30 22:22:39 UTC  
> Updated at: 2023-01-30 22:23:33 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1409445468  

Note that this failure only occured on our [macos_arm64_test](https://github.com/scipy/scipy/runs/10962045507). This is the same platform in which we see the overflow warnings.   
  
Actually, they could be related. I didn't realize it when I wrote   
  
> Maybe more accurately, I don't recall a case in which there is an overflow warning and the code produces an incorrect solution : )  
  
but we are silencing overflow warnings from the nbinom PPF calculation, too.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2023-01-31 14:02:12 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1410409539  

I wonder if this is truly a bug, but more particularly this comes down to what we actually want the quantile to do here as different libraries take slightly different approaches.  
  
Our goal for quantile is to find integer k such that `negative_binomial(k, n, p) >= probability`, and we approach that by bracketing the root, and then refining the brackets until both ends generate a probability >= the target.  Now this is conceptually the right thing to do, and for random probability values it homes in on the result very rapidly.  However, any attempt to round trip like this will automatically generate an edge case where the two ends of the bracketed root are either side of but very close to an integer - so the algorithm keeps refining the brackets until one end randomly pops over the integer boundary, and both ends then agree which way to round (by rounding UP).  An alternative is to treat it as a continuous function, find the root and round to nearest, but that can generate absurdities too.  Or we could introduce some sort of fudge factor whereby if it looks like it's dead on an integer we pick that one rather than risking an arbitrary round up?  
  
So I guess my question would be: is the original bug a result of a needed feature, or simply someone doing a naive sanity check?

---

## Comment 3

> Username: mdhaber  
> Created at: 2023-02-01 00:58:04 UTC  
> Updated at: 2023-02-01 01:44:48 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1411286340  

> Our goal for quantile is to find integer k such that negative_binomial(k, n, p) >= probability  
  
In general, the PPF is the *smallest* integer k such that the CDF >= probability, right?  
  
> is the original bug a result of a needed feature, or simply someone doing a naive sanity check?  
  
I can't speak for whether users will actually need the edge behavior to be perfect for science/engineering use. Nonetheless, users check cases like this to verify their understanding of the function and to check that it's working as they expect, and they will find and report edge cases like this as a bug. This can cause confusion and erode confidence in SciPy, so we try to get the edge cases right, too.  
  
> an edge case where the two ends of the bracketed root are either side of but very close to an integer - so the algorithm keeps refining the brackets  
  
It sounds like you are using a typical bracketing rootfinder, say bisection, and the ends of the bracket are `a` and `b`. When solving for the PPF, do you evaluate the CDF at `a` and `b` or `floor(a)` and `floor(b)`? (It would not matter in SciPy because our `cdf(x)` is equivalent to `cdf(floor(x))` for discrete distributions.)  
  
Either way, can't you terminate as soon as the bracket width drops below 1? At that point (or as soon as `floor(b) == floor(a +1)`, really) you can round up from the left or down from the right. From what you described, it sounded like the bracket width was getting very tiny.  
  
Please forgive me if I'm confusing myself. I've certainly done that before when thinking about this problem!

---

## Comment 4

> Username: jzmaddock  
> Created at: 2023-02-01 08:57:13 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1411688629  

Me too!  
  
I think there may be a solution though:  
  
1) Terminate the root finder if the bracket on the root drops below 1 then:  
2) Return the `ceil` of either end if the two ends do not straddle an integer, or  
3) Double check the `ceil` of the left end otherwise to see if it satisfies the condition, before falling back to the `ceil` of the right end.  
  
We might even save one or two CDF evaluations per quantile call that way.

---

## Comment 5

> Username: mdhaber  
> Created at: 2023-02-01 14:32:09 UTC  
> Updated at: 2023-02-01 14:48:43 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1412157816  

I think the situation is improved by terminating early, yup. Less likely to run into the trouble you described.  
  
This is making me wonder about how   
> one end randomly pops over the integer boundary  
  
In the round-trip case. If the CDF were numerically monotonic, this wouldn't happen, right? This would mean that the bracket was no longer valid because the root is *at* the boundary, but now both ends of the bracket are on one side of the boundary. Not a bracket.  
  
But since there are more floating point values within the support of a discrete distribution than there are between 0 and 1 (the range of the CDF), it seems that the CDF will always be a bit step-wise. So when "one end randomly pops over the integer boundary", does this happen such that `cdf(a) == cdf(b)==desired_prob`?  
  
I think if this is the case and you round up, I think you run the risk of returning one integer too high.  
  
This would be very rare if you terminate as soon as possible, but I can see how it would happen if you continue to refine the bracket.  
  
This brings another question to mind - how *is* the CDF defined if `cdf(x)` is not equivalent to `cdf(floor(x))`? Is the result at non-integral `x` have a statistical interesting or is it like an interpolant?

---

## Comment 6

> Username: jzmaddock  
> Created at: 2023-02-02 13:25:47 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1413743678  

Just looking through our code at this.... looks like this case should already be handled (albeit it may be possible to be more efficient), so I pushed this test case to investigate more what's actually happening rather than just speculating: https://github.com/boostorg/math/pull/938/files#diff-f08ad2489004236ba31ecf0b1d938d4433525c9bfecd328bba33398f65642e18  
  
But it's passing the tests, what am I missing? https://drone.cpp.al/boostorg/math/1163/1/2

---

## Comment 7

> Username: mdhaber  
> Created at: 2023-02-02 15:50:32 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1413960671  

> But it's passing the tests, what am I missing?  
  
Maybe the architecture?  
https://github.com/boostorg/math/issues/935#issuecomment-1409445468

---

## Comment 8

> Username: jzmaddock  
> Created at: 2023-02-02 17:39:34 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1414123620  

Ah, arm64, missed that, thought it was M1, I think we can emulate arm64/Linux at least, will try and see.

---

## Comment 9

> Username: mborland  
> Created at: 2023-02-02 17:41:25 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1414125740  

> Ah, arm64, missed that, thought it was M1, I think we can emulate arm64/Linux at least, will try and see.  
  
macOS ARM should be limited to M1

---

## Comment 10

> Username: mborland  
> Created at: 2023-02-02 17:43:59 UTC  
> Updated at: 2023-02-02 17:44:55 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1414128724  

@jzmaddock I don't know if it matters but these are the policies being used in the build:  
  
```  
cpp_args = [  
   '-DBOOST_MATH_DOMAIN_ERROR_POLICY=ignore_error',  
   '-DBOOST_MATH_EVALUATION_ERROR_POLICY=user_error',  
   '-DBOOST_MATH_OVERFLOW_ERROR_POLICY=user_error'  
   '-DBOOST_MATH_OVERFLOW_ERROR_POLICY=user_error',  
   '-DBOOST_MATH_PROMOTE_DOUBLE_POLICY=false'  
 ]  
```  
  
On M1 `long doubles` are just `doubles`.

---

## Comment 11

> Username: jzmaddock  
> Created at: 2023-02-02 17:53:29 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1414139308  

OK, is it a release build as well?

---

## Comment 12

> Username: mborland  
> Created at: 2023-02-02 17:58:59 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1414146573  

> OK, is it a release build as well?  
  
[Here ](https://github.com/scipy/scipy/blob/main/meson.build)is the file with all the compiler flags. I don't see anything specifying debug or release builds. I am likely just ignorant of their build system.

---

## Comment 13

> Username: jzmaddock  
> Created at: 2023-02-03 09:21:46 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1415449065  

Hmm, still passing, what's the clang version?  We have "Clang version 14.0.0 (clang-1400.0.29.202)"

---

## Comment 14

> Username: mborland  
> Created at: 2023-02-03 16:17:14 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416090767  

> Hmm, still passing, what's the clang version? We have "Clang version 14.0.0 (clang-1400.0.29.202)"  
  
C++ compiler for the host machine: c++ (clang 13.1.6 "Apple clang version 13.1.6 (clang-1316.0.21.2.3)")

---

## Comment 15

> Username: jzmaddock  
> Created at: 2023-02-03 18:33:05 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416249834  

Thanks Matt, sadly, the test is still passing, can I get you take a look locally if you have an M1 machine there?  
  
There *might* be ways to re-write the code, which *might* also be more efficient, and *might* fix the issue, but I'd really like a reproducer so I can understand what's going on.

---

## Comment 16

> Username: mborland  
> Created at: 2023-02-03 18:39:52 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416256003  

>   
  
I can't reproduce on your branch with M1 using clang 14 and GCC-12 with C++14,17,20,and 2b. I also had a slightly modified version [here](https://github.com/boostorg/math/pull/936/files) that runs cleanly.

---

## Comment 17

> Username: jzmaddock  
> Created at: 2023-02-03 18:52:33 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416268101  

Then I don't know what to make of the report, @mdhaber do you know the exact command line used to build, and/or can you provide a reproducer?  I feel we're missing something vital here.

---

## Comment 18

> Username: mdhaber  
> Created at: 2023-02-04 00:05:27 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416545595  

I don't. Maybe @mckib2 can help with that   
Could you try updating the termination tolerance, though?  It seems like it it's good for performance whether or not it fixed this test failure.

---

## Comment 19

> Username: mckib2  
> Created at: 2023-02-04 02:00:39 UTC  
> Updated at: 2023-02-04 03:04:50 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416601572  

> Then I don't know what to make of the report, @mdhaber do you know the exact command line used to build, and/or can you provide a reproducer? I feel we're missing something vital here.  
  
I was seeing this locally on an M1 Mac Mini (along with the aforementioned overflows).  I'll try to get a reproducer on that and report the environment specifics later this evening  
  
EDIT: scratch that, I'm seeing "FloatingPointError: overflow encountered in _nbinom_ppf" when trying to run @mborland's minimal reproducer at the top of this issue.  I'll try to see if I can resolve that, or it's taking too long, I'll ignore that exception and press on

---

## Comment 20

> Username: mborland  
> Created at: 2023-02-04 03:12:52 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416644987  

@mckib2 can you try running [this then](https://github.com/boostorg/math/pull/936/files)? Correct me if I am wrong but it should be a direct translation of the minimal python reproducer.

---

## Comment 21

> Username: mckib2  
> Created at: 2023-02-04 03:49:45 UTC  
> Updated at: 2023-02-04 03:53:00 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416651425  

Alright, with #941 applied, I can reproduce the issue with the original reproducer up top.  Here's info on my Mac Mini, please ask for any other interesting info:  
  
macOS Monterey version 12.6  
Mac mini (M1, 2020)  
Chip Apple M1  
Memory 8 GB  
  
```  
nmckibben@Nicholass-Mac-mini ~ % clang --version  
Apple clang version 14.0.0 (clang-1400.0.29.102)  
Target: arm64-apple-darwin21.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
nmckibben@Nicholass-Mac-mini ~ % uname -a  
Darwin Nicholass-Mac-mini.local 21.6.0 Darwin Kernel Version 21.6.0: Mon Aug 22 20:20:05 PDT 2022; root:xnu-8020.140.49~2/RELEASE_ARM64_T8101 arm64  
```  
  
Interestingly, meson is using a different compiler than the first one encountered on the PATH as above:  
```  
C compiler for the host machine: cc (clang 13.0.0 "Apple clang version 13.0.0 (clang-1300.0.27.3)")  
C linker for the host machine: cc ld64 710.1  
C++ compiler for the host machine: c++ (clang 13.0.0 "Apple clang version 13.0.0 (clang-1300.0.27.3)")  
C++ linker for the host machine: c++ ld64 710.1  
Host machine cpu family: aarch64  
Host machine cpu: arm64  
```  
  
Not sure which one I'd trust -- maybe what meson configure reports?

---

## Comment 22

> Username: jzmaddock  
> Created at: 2023-02-04 12:17:03 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416737301  

Sorry @mckib2 : do we have a reproducer or no?  Did you try the C++ file referenced: https://github.com/boostorg/math/pull/936/files ?

---

## Comment 23

> Username: jzmaddock  
> Created at: 2023-02-04 13:21:05 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416749464  

Still no reproduction here on:  
  
```  
uname -a  
Darwin dronerunnermac8 21.6.0 Darwin Kernel Version 21.6.0: Thu Sep 29 20:13:46 PDT 2022; root:xnu-8020.240.7~1/RELEASE_ARM64_T8101 arm64  
```  
  
```  
sw_vers  
ProductName:	macOS  
ProductVersion:	12.6.1  
BuildVersion:	21G217  
```  
  
```  
Clang version 13.1.6 (clang-1316.0.21.2.3)  
```  
  
We've tried clang-14.0 as well.

---

## Comment 24

> Username: mckib2  
> Created at: 2023-02-05 05:05:26 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416924168  

> Sorry @mckib2 : do we have a reproducer or no? Did you try the C++ file referenced: https://github.com/boostorg/math/pull/936/files ?  
  
Sorry -- forgot to go back and do this.  I checked out mborland/935 and added this to the root cmakelists to get an exectuable:  
  
```cmake  
add_executable(issue935 test/git_issue_935.cpp)  
target_compile_features(issue935 PRIVATE cxx_std_17)  
target_link_libraries(issue935 PUBLIC Boost::math)  
```  
  
Running the resulting executable, I get the following:  
```  
/Users/nmckibben/Documents/math/cmake-build-debug/issue935  
Error at /Users/nmckibben/Documents/math/test/git_issue_935.cpp:main:23:  
  ULP distance in double precision is 1.13e+15, which exceeds 1, error/ulps  = 1.13e+15.  
  Expected: +7.00000000000000000 = +0x1.cp+2  
  Computed: +6.00000000000000000 = +0x1.8p+2  
  Mollified relative error: +0.14285714285714285  
Error count: 1, total ulp distance = 1125899906842624  
  
Process finished with exit code 1  
```  
  
So yes, this does reproduce the round-trip failure for me

---

## Comment 25

> Username: mckib2  
> Created at: 2023-02-05 05:09:43 UTC  
> Url: https://github.com/boostorg/math/issues/935#issuecomment-1416924717  

If it's interesting to compare to your own, here are my CMakeCache.txt and build.ninja files  
[CMakeCache.txt](https://github.com/boostorg/math/files/10610263/CMakeCache.txt)  
[build.ninja.txt](https://github.com/boostorg/math/files/10610265/build.ninja.txt)
