# #1211 - boost/math defaulting to slow 128-bit float on aarch64 [Open]

> Username: dslarm  
> Created at: 2024-10-14 12:36:49 UTC  
> Updated at: 2024-11-27 21:50:29 UTC  
> Url: https://github.com/boostorg/math/issues/1211  

Whilst running a workload which uses boost::math::digamma, I discovered unexpectedly slow performance on aarch64 platforms.   
  
The default seen in the distros (Ubuntu 22.04, Rocky9) on this platform (Linux aarch64) is to build with 128b floats.  This then causes software emulation steps - and its large slowdown.  
  
This seems a poor default - and whilst you can resolve at application compile time, by passing the compiler flag `-DBOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS` - it's unlikely people will know to do so.     
  
On aarch64 a 100x speed up is had by adding the define compared to the current default.  On x86, it brings around a 6x speed up.  
  
`g++ workload-datasets/boost/btest.cpp -Ofast -mcpu=native`  
`g++ workload-datasets/boost/btest.cpp -Ofast -mcpu=native -DBOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS`  
  
  
```  
#include <boost/math/special_functions/digamma.hpp>  
#include <iostream>  
#include <chrono>  
#include <cstdlib>  
  
#define N 1000 * 1000 * 100  
void long_operation() {  
	double d = 0;  
    for (int i = 1; i < N ; ++i)  
    	d += boost::math::digamma((double) i);  
  
   std::cout << d << std::endl;  
  
}  
  
int main(int argc, char *argv[]) {  
  
using std::chrono::high_resolution_clock;  
    using std::chrono::duration_cast;  
    using std::chrono::duration;  
    using std::chrono::milliseconds;  
  
    auto t1 = high_resolution_clock::now();  
    int reps;  
    if (argc > 1)  
	reps = std::atoi(argv[1]);  
    else reps = 1;  
    for (int r = 0; r < reps; ++r)  
	long_operation();  
    auto t2 = high_resolution_clock::now();  
  
    /* Getting number of milliseconds as an integer. */  
    auto ms_int = duration_cast<milliseconds>(t2 - t1);  
    std::cout << ms_int.count() << "ms\n";  
    return 0;  
}  
```

---

## Comment 1

> Username: rdoeffinger  
> Created at: 2024-10-14 14:37:44 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2411455309  

I think the issue and poor default comes from here, IMO promotion to long double is not appropriate on any architecture that is relevant today (anyone running on one where it is can still override in several ways).  
So I would suggest the below:  
```  
--- a/include/boost/math/policies/policy.hpp  
+++ b/include/boost/math/policies/policy.hpp  
@@ -86,11 +86,10 @@ namespace policies{  
 #define BOOST_MATH_PROMOTE_FLOAT_POLICY true  
 #endif  
 #ifndef BOOST_MATH_PROMOTE_DOUBLE_POLICY  
-#ifdef BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS  
+// long double has almost universally poor performance  
+// (whether on x86 80-bit float or emulated 128-bit on AArch64  
+// for example), so should never be on by default.  
 #define BOOST_MATH_PROMOTE_DOUBLE_POLICY false  
-#else  
-#define BOOST_MATH_PROMOTE_DOUBLE_POLICY true  
-#endif  
 #endif  
 #ifndef BOOST_MATH_DISCRETE_QUANTILE_POLICY  
 #define BOOST_MATH_DISCRETE_QUANTILE_POLICY integer_round_outwards  
```

---

## Comment 2

> Username: jzmaddock  
> Created at: 2024-10-14 17:49:47 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2411888083  

You are correct that we should not be promoting to an emulated type, that's just silly.  
  
Changing to not promoting double on x86 systems has been on my list of things I should probably look at for a while - however it's a major breaking change that would also break every one of our tests - so not to be taken lightly unfortunately.

---

## Comment 3

> Username: rdoeffinger  
> Created at: 2024-10-14 19:22:24 UTC  
> Updated at: 2024-10-14 19:23:07 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2412044828  

Hm, macOS for example does not even provide a long double type (at least on M1 and onwards - not sure for Intel), how do the tests work there?  
Changing the define does not cause any failures in the tests there, however it seems much fewer tests are run?  
I kind of ask, because if the tests don't work on any pure IEEE-754 platform that seems to be a bit of a coverage hole there...

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-10-15 07:53:16 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2413151478  

We have an expected error rate for the "largest real type", and all the other real's are then assumed to be zero error.  The largest real depends on the compiler/platform, so as you say for MacOS and MSVC that's double, long double for most GCC configurations.  Unfortunately, these are set per-test not centrally, and we would need to go through and double check all the error rates we get to make sure there's nothing buggy there if we change over.  It's all doable, there have just always been more important things to do...

---

## Comment 5

> Username: rdoeffinger  
> Created at: 2024-10-15 08:09:13 UTC  
> Updated at: 2024-10-15 08:16:16 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2413185092  

Most tests use an epsilon relative to the type of the test, not the most precise type.  
But around 100 tests are broken as-is anyway when running on a platform with 128 bit long double, due to a variety of bugs in the tests (and maybe also the code).  
I'll send some patches, but at this point, a large number of tests are simply not working anyway.  
EDIT: Scratch the comment about BOOST_CHECK_CLOSE, it might be just a confusing print, mixing absolute and relative difference...

---

## Comment 6

> Username: jzmaddock  
> Created at: 2024-10-15 10:32:16 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2413514518  

>EDIT: Scratch the comment about BOOST_CHECK_CLOSE, it might be just a confusing print, mixing absolute and relative difference...  
  
Not quite... it's a really bad design fault in the now rather old Boost.Test: the tolerance is expressed as a percentage, but the printout is the actual relative difference (so they are different by a factor of 100), so it's 8.39188e-30 found, vs 8e-28 / 100, ie 8e-30 expected.  So a "trivial" fail, because the tolerance is slightly tight for that platform.

---

## Comment 7

> Username: rdoeffinger  
> Created at: 2024-10-15 13:04:57 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2413862863  

Ah thanks! Now that I have a pass on the baseline, I will look into some of the failures with the promotion policy changed and see if I can help out with some fixes. No promises, and unlikely get all the way, but maybe it helps enough to speed things up a bit.

---

## Comment 8

> Username: rdoeffinger  
> Created at: 2024-10-15 15:44:22 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2414387525  

I sent https://github.com/boostorg/math/pull/1214  
While I have not tested on x86, I think it should get you really close to change this define, at the very least on !x86

---

## Comment 9

> Username: rdoeffinger  
> Created at: 2024-10-16 12:03:47 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2416620835  

Seem it is kind of a duplicate of https://github.com/boostorg/math/issues/241 which is over 4 years old, I think it's really time to address it.  
It seems it is a very low number of tests that have an actual issue, so there should be some way to manage the change.

---

## Comment 10

> Username: rdoeffinger  
> Created at: 2024-11-18 21:57:45 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2484216255  

Changing the default is easily possible now without breaking tests by applying the latest updated version of https://github.com/boostorg/math/pull/1214  
Can then discuss how to best proceed with the change, e.g. leave x86 as-is for now to avoid the compatibility concerns

---

## Comment 11

> Username: mborland  
> Created at: 2024-11-19 13:54:22 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2485786547  

> Changing the default is easily possible now without breaking tests by applying the latest updated version of [#1214](https://github.com/boostorg/math/pull/1214) Can then discuss how to best proceed with the change, e.g. leave x86 as-is for now to avoid the compatibility concerns  
  
I don't see a huge problem with changing the default for systems with obvious benefit like yours. In addition to John's comment on x64, we could disable promotion universally on GCC-13 and up since excess precision (promotion) is now handled at the compiler level. Users would have disable GCC with a flag and change their promotion default. Since we have a release going on now we could add a warning to the docs that in maybe 2-3 release cycles the default changes (1 year). Thoughts? @jzmaddock, @NAThompson, @ckormanyos

---

## Comment 12

> Username: rdoeffinger  
> Created at: 2024-11-27 21:50:28 UTC  
> Url: https://github.com/boostorg/math/issues/1211#issuecomment-2504840614  

Anything I should do to help getting #1214 and #1220 in?  
There are failing tests, but best I can tell they are not related to the actual changes - but I will admit that I am not 100% on that.
