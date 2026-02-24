# #337 max_element perf [Merged]

> Username: skozilla  
> Created at: 2014-12-09 03:24:38 UTC  
> Updated at: 2014-12-11 11:39:44 UTC  
> Merged at: 2014-12-11 05:43:50 UTC  
> Closed at: 2014-12-11 05:43:50 UTC  
> Url: https://github.com/boostorg/compute/pull/337  

added **perf_max_element** and **perf_stl_max_element** for performance testing

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-09 03:42:53 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66230594  

[![Coverage Status](https://coveralls.io/builds/1582757/badge)](https://coveralls.io/builds/1582757)  
  
Coverage remained the same when pulling **288ffb30e2bce1a7e8654ac163290f12ef6ee5ba on skozilla:master** into **848d95601ea11fd9d29558e4f1db17cc28e84858 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-12-09 16:37:48 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66312562  

Cool! What kind of performance numbers do you get on your system?  
  
Also, could you squash this down to one commit with all the changes?  
  
Other than that, it looks good. Thanks for adding this!

---

## Comment 3

> Username: skozilla  
> Created_at: 2014-12-10 03:34:01 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66399887  

It seems that the performance is somewhat worse using the compute method than the stl method:  
http://imgur.com/KYZLaBI  
Although I am using a fairly old GPU (Radeon HD 6570) with opencl 1.2   
  
I'll squash it down to one commit as soon as i figure out how to do this.

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-12-10 04:15:58 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66402490  

Interesting, the implementation for `max_element()` is currently pretty simple and certainly could be improved. Having a benchmark is a good first step. If you'd be interested in working on improving this let me know and I'll give you some pointers.  
  
Also, squashing commits is fairly easy and can be done with something like `git rebase -i origin/develop` (this is a good guide: http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html).  
  
Thanks!

---

## Comment 5

> Username: skozilla  
> Created_at: 2014-12-11 02:44:52 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66561352  

i think i messed something up.   
I tried rebasing my local copy and pushing to to my remote, and it seems to have caused a miss match in the commit tree structure.

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-12-11 03:50:26 UTC  
> Updated_at: 2014-12-11 06:08:07 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66565482  

Hmm, strange. Try doing this from your `master` branch (where `097a802` should be the last commit):  
  
```  
git fetch kylelutz && git reset kylelutz/develop --hard && git cherry-pick 097a802  
```  
  
That will basically reset your current `master` branch to be the same as my `develop` branch and then pull just the `"max_element perf added"` commit on top of it. Then you should be able to just do `git push -f origin HEAD` to update the pull-request. Let me know if you have any problems.

---

## Comment 7

> Username: skozilla  
> Created_at: 2014-12-11 05:13:35 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66570179  

Thanks, that worked. I spent a while trying to figure it out.   
  
I would be interested in optimizing some OpenCL kernels to improve performance and writing performance testing code for different methods.

---

## Comment 8

> Username: kylelutz  
> Created_at: 2014-12-11 05:47:51 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66576176  

Looks great! Thanks!  
  
And it would be great to have your help working on optimizations. The current implementation used by `min_element()`, `max_element()`, and `minmax_element()` is in the  [`detail::find_extrema()`](https://github.com/kylelutz/compute/blob/master/include/boost/compute/algorithm/detail/find_extrema.hpp) and [`detail::find_extrema_with_atomics()`](https://github.com/kylelutz/compute/blob/master/include/boost/compute/algorithm/detail/find_extrema_with_atomics.hpp) functions. Probably a good first step at optimization would be to update `find_extrema_with_atomics()` to process multiple values per work item.

---

## Comment 9

> Username: coveralls  
> Created_at: 2014-12-11 10:41:53 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66601331  

[![Coverage Status](https://coveralls.io/builds/1594046/badge)](https://coveralls.io/builds/1594046)  
  
Coverage remained the same when pulling **8149e7f88b115ffd58f6fb8db959076ab271639e on skozilla:master** into **848d95601ea11fd9d29558e4f1db17cc28e84858 on kylelutz:develop**.

---

## Comment 10

> Username: coveralls  
> Created_at: 2014-12-11 11:06:22 UTC  
> Url: https://github.com/boostorg/compute/pull/337#issuecomment-66604043  

[![Coverage Status](https://coveralls.io/builds/1594849/badge)](https://coveralls.io/builds/1594849)  
  
Coverage remained the same when pulling **ae014dc75750fefbd6399868d34164ba4f537e9a on skozilla:master** into **8883a85fec8cb6cf173d1d290c25f0727e4a304c on kylelutz:develop**.

---
