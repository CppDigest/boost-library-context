# #867 Fix hopefully the last Clang-Tidy suggestions [Closed]

> Username: SiliconA-Z  
> Created at: 2022-10-31 15:58:10 UTC  
> Updated at: 2022-11-04 17:03:21 UTC  
> Closed at: 2022-11-04 17:03:21 UTC  
> Url: https://github.com/boostorg/math/pull/867  

There were some last second things I missed.  
  
Among these warnings were "redundant static casts" and "deprecated C-headers"  
  
There were others too but those warnings were false flags so I kept the ones that actually made sense.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2022-11-03 01:04:01 UTC  
> Url: https://github.com/boostorg/math/pull/867#issuecomment-1301535505  

@AtariDreams : I think your talents might be better spent on (say) [this issue](https://github.com/boostorg/math/issues/314). There is a [new units library](https://github.com/mpusz/units) that is being proposed for standardization; if you could change the interpolators to be generically compatible with dimensioned types then you could also give feedback on the proposal and identify pain points. This would be something you could be very proud of and of course put on your resume.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-11-04 17:03:21 UTC  
> Url: https://github.com/boostorg/math/pull/867#issuecomment-1303888991  

@AtariDreams : please can you refer to my previous comments - I'm not against PR's, indeed we love them - but can you:  
  
* Please keep changes to something we can actually manage to review - if we have to sift through and check the logic of changes in 64 files, not only does that take a quite a while, but the risk of missing a mistake is actually pretty high.  
* Please don't keep on pushing more and more changes to the same PR.  At a minimum it's super annoying if you're trying to review a PR only for it to change under your feet, but I've also had to go and cancel a whole bunch of CI runs triggered by these continued pushes.  Please remember that CI is a shared resource for the whole of Boost, and that a Math lib run takes up a pretty significant amount of resources.  
  
Given that we've already rejected a PR rather similar to this one, I'm going to close this one down as well.  But please, small focused PR's, and once you've submitted a PR, please leave it alone and don't keep pushing more and more to it!  This of course, is another way of saying "please don't submit the PR until it's actually ready".

---
