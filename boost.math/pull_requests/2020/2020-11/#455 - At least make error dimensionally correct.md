# #455 At least make error dimensionally correct. [Merged]

> Username: NAThompson  
> Created at: 2020-11-10 13:30:57 UTC  
> Updated at: 2020-11-13 18:04:21 UTC  
> Merged at: 2020-11-13 13:21:24 UTC  
> Closed at: 2020-11-13 13:21:24 UTC  
> Url: https://github.com/boostorg/math/pull/455  

@bbbales2: Could you make sure this fixes your issue?  
  
I now wonder how many places I've made this same mistake . . .   
  
Fixes #449

---

## Comment 1

> Username: bbbales2  
> Created_at: 2020-11-10 14:03:37 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-724721741  

Yeah this fixes it and makes sense to me. Thanks!  
  
So Boost is currently 1.74.0. If this pull request goes through, does that mean I should expect this change when 1.75.0 appears?

---

## Comment 2

> Username: NAThompson  
> Created_at: 2020-11-10 14:29:17 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-724737160  

@jzmaddock : Is 1.75 closed for merging?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-11-10 16:03:11 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-724798180  

I now wonder if we should map the tolerance via tolerance -> tolerance/diff as well.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2020-11-10 16:32:37 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-724817054  

> Is 1.75 closed for merging?  
  
Yes, the beta candidates are out, there may be an opportunity to merge bug fixes post-beta but the timing will be tight!

---

## Comment 5

> Username: NAThompson  
> Created_at: 2020-11-12 16:21:53 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-726182750  

It appears that we are now seriously constrained by CI resources. It looks like Travis is only doing one job at a time; moreover it doesn't look like it moves from one job to the next immediately.

---

## Comment 6

> Username: bbbales2  
> Created_at: 2020-11-12 17:50:26 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-726236241  

@NAThompson okay cool. I will just put a fix for this in our code for now. Lemme know when you know what version this might appear in, and I can roll back the fix in our code when we upgrade boost.

---

## Comment 7

> Username: NAThompson  
> Created_at: 2020-11-13 13:23:32 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-726762128  

@jzmaddock : Looking at the build log, it appears that only bug fixes have gone in since the merge to master:  
  
```  
f3952b8b41ddb737155104f1738b544da77e9c8b (HEAD -> develop, origin/develop, origin/HEAD) At least make error dimensionally correct. (#455)  
6cbd2259946184e020a564fd57f2f1948f61ab23 Merge pull request #454 from boostorg/issue453  
8e3c2bc77d042136e8c3a5f4982e6ff617d41787 Merge pull request #452 from boostorg/issue451  
c25cae2067abc689997881781c985b7e2ddbf9e2 (origin/issue453) Correct testing mistake in test_legendre.hpp.  
889620031aaad68bdc6eba46be80ee4517e563e5 Fix up legendre_p(n, n-1, x) as special case. Add tests. Fixes https://github.com/boostorg/math/issues/453.  
56c4c890d107b23064fdaf384dc9e085aa62ef36 (origin/issue451) Fix spurious overflow in gamma_p/q calculation. Add tests. Fixes: https://github.com/boostorg/math/issues/451  
6b8c2691d38b44ec61f2664a9635948058aaca13 Correct expected error rate - epsilon should be as a percentage for BOOST_CHECK_CLOSE.  
83f1aa42e11577396b0f4f7ae49a0ac96e1c0ae6 Merge pull request #448 from suyash-patil/develop  
01888dc92970db2117dad9b98795dcef340d4e79 fixed CSS property  
a5ff4371daabf0dfcde6d89ad843196e4a811f46 Merge pull request #447 from suyash-patil/develop  
eb532dc219fac300a10b84d49f9f34710dbb8cfe small typo fixed  
992794d591dbb894ee31be90726adfb87c2820e3 (origin/master) Merge branch 'master' into develop  
```  
  
Does this qualify for a post-beta merge?

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2020-11-13 18:04:21 UTC  
> Url: https://github.com/boostorg/math/pull/455#issuecomment-726925634  

Indeed, I don't see why not - note however, that the beta isn't quite out yet so master is still closed.  Which is OK as it lets the CI tests cycle ;)

---
