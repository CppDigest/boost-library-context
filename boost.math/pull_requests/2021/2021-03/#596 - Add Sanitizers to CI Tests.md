# #596 Add Sanitizers to CI Tests [Merged]

> Username: jzmaddock  
> Created at: 2021-03-31 16:01:04 UTC  
> Updated at: 2021-04-04 16:50:00 UTC  
> Merged at: 2021-04-04 08:16:33 UTC  
> Closed at: 2021-04-04 08:16:33 UTC  
> Url: https://github.com/boostorg/math/pull/596  



---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-03-31 16:01:45 UTC  
> Url: https://github.com/boostorg/math/pull/596#issuecomment-811181641  

Note that at present this PR only tests drone integration, everything else has been removed until we get the new tests green.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-04-04 08:16:26 UTC  
> Url: https://github.com/boostorg/math/pull/596#issuecomment-812993856  

@mborland , @NAThompson , Just a heads up that I'm merging this PR which adds, `-fsanitize=address, undefined, thread, integer` tests into the mix (on drone).  There is one sporadic failure from naive_monte_carlo_test_4 under the thread sanitizer (https://github.com/boostorg/math/issues/598), but other than that all is green.    
  
There are also quite a number of tests which are disabled for sanitizer runs: some just make no sense (compile tests, concept checks), but some just gobble up so much memory that they kill the system.  Which is to say, you can't completely rely on these to catch everything, but they should catch the most grievous errors in most things.  
  
Finally, while there are no false positives so far, I wouldn't rule these out down the road...

---

## Comment 3

> Username: NAThompson  
> Created_at: 2021-04-04 16:50:00 UTC  
> Url: https://github.com/boostorg/math/pull/596#issuecomment-813063725  

@jzmaddock : I've seen a few false positives with TSAN before, but the signal to noise is very high. The most common problem is not false positives, but true positives that you really have no control over, say, in MPI libraries.

---
