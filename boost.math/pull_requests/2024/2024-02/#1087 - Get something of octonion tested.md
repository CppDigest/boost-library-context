# #1087 Get something of octonion tested [Merged]

> Username: ckormanyos  
> Created at: 2024-02-12 12:23:37 UTC  
> Updated at: 2024-02-12 19:56:51 UTC  
> Merged at: 2024-02-12 19:56:47 UTC  
> Closed at: 2024-02-12 19:56:47 UTC  
> Url: https://github.com/boostorg/math/pull/1087  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2024-02-12 14:11:27 UTC  
> Updated_at: 2024-02-12 14:13:28 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1938754026  

So what I'm finding out is that octonions are fascination. I believe there is a relation to them and order-2 Lie groups. Long on my TODO list and I have no clue about them.  
  
We have been doing essentially no actual testing of these.  
  
Anyway, the original file [octonion_test.cpp](https://github.com/boostorg/math/blob/develop/test/octonion_test.cpp) in my opinion has more of an exemplary nature. It is described in Math's docs also as such. We can annotate it and potentially rename it as an example.  
  
For actual testing, ... I have in the branch started a new file called [octonion_test_simple.cpp](https://github.com/boostorg/math/blob/try_testing_octonion/test/octonion_test_simple.cpp). It is reminiscent of the original file but its focus will be on testing the actual octonion implementation more clearly and achieving coverage.  
  
I need to study up and get more test data. Python does a lot of this stuff. I'd like some data points for a few basic elementary functions too like `exp()`, etc. So this will be a cool little hobby...  
  
Cc: @jzmaddock and @mborland and @NAThompson

---

## Comment 2

> Username: NAThompson  
> Created_at: 2024-02-12 16:47:39 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1939128482  

@ckormanyos : Wasn't there a discussion about deprecating that file since there is now a qvm library in boost?

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2024-02-12 17:19:51 UTC  
> Updated_at: 2024-02-12 17:21:38 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1939187265  

> Wasn't there a discussion about deprecating that file since there is now a qvm library in boost?  
  
I'd like that. It seems like legacy code with old style.  
  
I could in this PR:  
- Formulate a deprecation notice (till 1.86).  
- Does this mean deprecate octonion **and** quaternion? Or just octonion?  
- Eliminate negative influence from this stuff in CodeCov.  
  
I would need help with the docs upon actual deprecation. But gone seems good on this one.  
  
I just need all-party agreement.  
  
Cc: @jzmaddock and @mborland abd @NAThompson

---

## Comment 4

> Username: mborland  
> Created_at: 2024-02-12 17:37:54 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1939216789  

I don't think QVM supports Octonion so we shouldn't deprecate this without replacement. We should add testing because it's already here.

---

## Comment 5

> Username: ckormanyos  
> Created_at: 2024-02-12 17:40:00 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1939219876  

> I don't think QVM supports Octonion so we shouldn't deprecate this without replacement. We should add testing because it's already here.  
  
I was afraid you'd say that. GSoC 2030 make octonion great again

---

## Comment 6

> Username: ckormanyos  
> Created_at: 2024-02-12 19:56:41 UTC  
> Url: https://github.com/boostorg/math/pull/1087#issuecomment-1939458623  

Ok this is better than it was.  
  
- But we still need a couple hundred lines of octonian coverage.  
- I also disabled `lcov`for Input-Streaming of octonians.  
  
We can sort this octonian thing out in a few later rounds.  
  
For now I'll merge, as it's better than earlier.

---
