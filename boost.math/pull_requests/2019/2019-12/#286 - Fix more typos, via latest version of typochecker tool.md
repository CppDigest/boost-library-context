# #286 Fix more typos, via latest version of typochecker tool [Merged]

> Username: bwignall  
> Created at: 2019-12-20 15:49:37 UTC  
> Updated at: 2019-12-24 10:03:49 UTC  
> Merged at: 2019-12-23 19:03:58 UTC  
> Closed at: 2019-12-23 19:03:58 UTC  
> Url: https://github.com/boostorg/math/pull/286  

With current version of typochecker utility (https://github.com/bwignall/typochecker), which has an updated set of likely typos, this "should" eliminate a lot of false positives:  
  
`python PATH/TO/corrector.py -d path/to/math -w significand -w Mathematica -w cont -w comm`

---

## Comment 1

> Username: NAThompson  
> Created_at: 2019-12-23 19:03:53 UTC  
> Url: https://github.com/boostorg/math/pull/286#issuecomment-568555060  

Just read through all these diffs; looks good thanks!

---

## Comment 2

> Username: pabristow  
> Created_at: 2019-12-24 10:03:49 UTC  
> Url: https://github.com/boostorg/math/pull/286#issuecomment-568713034  

From reading these, and a forensic examination of the source code, I conclude that this typochecker program now includes a metaphysical AI algorithm that in Phase 1 cunningly detects places where a typos might have existed and inserts a suitable one from its databse.  In Phase 2 these typos are rediscovered and corrected.  (If one turns the sound volume up to ear and speaker damaging levels one can hear a 'whoop' when these events occur during both Phase 1 and Phase 2).  
  
They will be merged after the conclusion of Solstice Celebrations...

---
