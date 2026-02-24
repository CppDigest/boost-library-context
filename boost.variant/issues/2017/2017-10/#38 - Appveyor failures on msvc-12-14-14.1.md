# #38 - Appveyor failures on msvc-12/14/14.1 [Closed]

> Username: pdimov  
> Created at: 2017-10-27 14:49:46 UTC  
> Updated at: 2017-10-31 11:29:27 UTC  
> Closed at: 2017-10-31 06:07:12 UTC  
> Url: https://github.com/boostorg/variant/issues/38  

See https://ci.appveyor.com/project/boostorg/boost/build/1.0.4245-develop/job/g7u448hj39mupcf8  
  
Odd since Variant's own Appveyor passes this test but there you go.

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-10-31 06:07:12 UTC  
> Url: https://github.com/boostorg/variant/issues/38#issuecomment-340669368  

Hmmm... that issue was fixed in variant commit https://github.com/boostorg/variant/commit/b0852f6e60784b5f15b2869539816fdbab29ebe3  
That commit was merged to boost here https://github.com/boostorg/boost/commit/626efcee947815089410d08f95bba14e5e542c11  
  
The CI had run for an outdated commit https://github.com/boostorg/boost/commit/fea8a0888731fc307faaab6033b0c260cdec9d82. So yes, it must fail.  
  
The CI commit https://github.com/boostorg/variant/commit/b0852f6e60784b5f15b2869539816fdbab29ebe3 **with the fix** was tested here https://travis-ci.org/boostorg/boost/builds/292815094?utm_source=github_status&utm_medium=notification but the build was cancelled. So the variant still shows as a "failed", however it is **actually** "OK".  
  
  
P.S.: great thanks for adding CI to the Boost root project!

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-31 11:29:26 UTC  
> Url: https://github.com/boostorg/variant/issues/38#issuecomment-340734893  

I can confirm that this commit got tested on Appveyor here: https://ci.appveyor.com/project/boostorg/boost/build/1.0.4270-develop and did pass. On Travis, at the time I had auto-cancel enabled because it couldn't keep up, but I've restarted that job now just in case, even though I expect no problems with it.
