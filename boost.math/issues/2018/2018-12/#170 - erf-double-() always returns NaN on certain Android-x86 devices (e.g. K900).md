# #170 - erf<double>() always returns NaN on certain Android-x86 devices (e.g. K900) [Closed]

> Username: TheLivingOne  
> Created at: 2018-12-10 08:31:39 UTC  
> Updated at: 2019-09-15 18:20:54 UTC  
> Closed at: 2019-09-15 18:20:54 UTC  
> Url: https://github.com/boostorg/math/issues/170  

boost::math::erf(double) returns NaN for normal argument (e.g. 1.5, 2...) on certain Android-x86 devices (e.g. K900, Intel Atom Z2580). The same binary works normally on Android emulator and other Android-x86 devices.  
  
boost::math::erf(float) does not have the same problem so the easiest workaround is to use only the float version.  
  
Used compiler: gcc 4.9 (NDK r10e), possibly relevant options:  
-march=atom -mtune=atom -m32 -msse3 -mfpmath=sse -fstrict-aliasing -ffast-math -O2 -fno-reciprocal-math  
  
Some Atom-based notebooks with Windows or Linux could also be affected by this problem.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2018-12-10 09:49:51 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-445754247  

Yikes.  
  
I don't have access to that platform - are you able to debug or provide any info on what's going wrong?  
  
If there's access to console output then defining BOOST_MATH_INSTRUMENT might give some ideas of what's happening (or at least which version of the function is called internally).  You might also try defining BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS just in case it's the `long double` functions that are being called internally.

---

## Comment 2

> Username: TheLivingOne  
> Created at: 2018-12-10 11:42:24 UTC  
> Updated at: 2018-12-12 08:14:00 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-445787324  

I will try to create a test app and debug it if I can get my hands on the K900 again. Interestingly the bug does not appear on Asus Zenfone 5 that has similar Z2560 CPU (but probably the SoC or firmware is a bit newer version). I also tried but could not google up any relevant CPU bug information. Since K900's are getting quite rare, maybe this bug doesn't worth much effort and just should be listed somewhere as a "known issue".  
  
Update: another possibly affected device is ZTE Geek which was made with the same chip and at the same time as K900 (I don't know where to get one).

---

## Comment 3

> Username: NAThompson  
> Created at: 2018-12-10 17:38:20 UTC  
> Updated at: 2018-12-10 17:39:12 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-445903717  

Looks like you are using a fairly old compiler with a fairly new architecture with a complicated set of compiler flags. Definitely possible to get a bad instruction generated; could you add the flag `-fsanitize=undefined` to your example and see what it spits out?

---

## Comment 4

> Username: jzmaddock  
> Created at: 2018-12-10 18:22:30 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-445918579  

>Looks like you are using a fairly old compiler with a fairly new architecture with a complicated set of compiler flags. Definitely possible to get a bad instruction generated; could you add the flag -fsanitize=undefined to your example and see what it spits out?  
  
The functions are actually pretty simple: just some basic arithmetic and a call to exp().  My suspicion is that exp() is a non-functioning stub in this case.

---

## Comment 5

> Username: TheLivingOne  
> Created at: 2018-12-11 09:05:32 UTC  
> Updated at: 2018-12-11 09:54:20 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-446124930  

K900 and Geek are actually quite old and outdated devices (2013 and Android 4.x), even GCC 4.9 is newer (2015) but there are still some users. I don't think that -fsanitize could give anything interesting as the same binary works fine on later devices, Android emulators and etc. so the algorithms should be OK. If there was an uninitialized variable the error would appear on all devices.  
Update: the bug was reported to reproduce on Asus Fonepad (Intel Atom Z2460?), also 2013 and Android 4.1 and does NOT reproduce on Asus Zenfone 5 (Z2560, 2014) (same binary).   
Unfortunately I most likely won't be able to investigate this until January.  
Update 2: the set of compiler options is not that complicated, it is basically Intel's recommendations for x86 Androids.

---

## Comment 6

> Username: jzmaddock  
> Created at: 2019-09-15 18:20:54 UTC  
> Url: https://github.com/boostorg/math/issues/170#issuecomment-531587794  

Since this is not reproducible without help from the OP, I'm closing down.
