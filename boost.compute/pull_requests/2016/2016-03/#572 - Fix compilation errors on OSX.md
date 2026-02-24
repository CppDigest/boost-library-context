# #572 Fix compilation errors on OSX [Merged]

> Username: jszuppe  
> Created at: 2016-03-16 12:33:01 UTC  
> Updated at: 2016-03-19 09:34:28 UTC  
> Merged at: 2016-03-19 02:35:03 UTC  
> Closed at: 2016-03-19 02:35:03 UTC  
> Url: https://github.com/boostorg/compute/pull/572  

Unfortunately, commit 3826e9cae47f56d6ce91a6127800bce979155a0c (fixing some unsigned/signed int comparison warnings on Windows) introduced compilation errors for OSX that I did not notice.   
  
See build 1300.9: https://travis-ci.org/boostorg/compute/builds/115231140.  
  
This PR fixes those erros and add OSX Travis-CI build without running tests (because currently a few tests fail on OSX) in order to avoid such a mistakes in the future. (OSX build with tests is still allowed to fail.) There is also a small code refactoring in `strided_iterator.hpp`  .

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-03-16 16:56:12 UTC  
> Url: https://github.com/boostorg/compute/pull/572#issuecomment-197422634  

> Coverage decreased (-0.0%)  
  
lol

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-03-19 02:35:05 UTC  
> Url: https://github.com/boostorg/compute/pull/572#issuecomment-198619965  

Looks good! Thanks!

---

## Comment 3

> Username: kylelutz  
> Created_at: 2016-03-19 02:35:19 UTC  
> Url: https://github.com/boostorg/compute/pull/572#issuecomment-198620051  

That's weird about coveralls...

---
