# #548 Timber OSX fixes [Merged]

> Username: jszuppe  
> Created at: 2016-01-07 20:25:55 UTC  
> Updated at: 2016-05-01 11:20:02 UTC  
> Merged at: 2016-01-13 18:56:58 UTC  
> Closed at: 2016-01-13 18:56:58 UTC  
> Url: https://github.com/boostorg/compute/pull/548  

These are the changes I think should fix some of the problems encountered [here](http://www.boost.org/development/tests/develop/developer/compute.html) on Timber OSX.  
  
The first and the third commits are obvious.  They fix: [warning: comparison between signed and unsigned integer expressions](http://www.boost.org/development/tests/develop/output/timber-osx-compute-darwin-5-3-0-warnings.html#test_image2d) (there is one exception, I can't locate the bug in `test_fill`) and [test_scatter_if fail](http://www.boost.org/development/tests/develop/developer/output/timber-osx-boost-bin-v2-libs-compute-test-test_scatter_if-test-darwin-5-3-0-debug.html).  
  
The second and the forth commits should fix [test_extrema fail](http://www.boost.org/development/tests/develop/developer/output/timber-osx-boost-bin-v2-libs-compute-test-test_extrema-test-darwin-5-3-0-debug.html) and [test_reduce_by_key fail](http://www.boost.org/development/tests/develop/developer/output/timber-osx-boost-bin-v2-libs-compute-test-test_reduce_by_key-test-darwin-5-3-0-debug.html). I think SIGABRT may be caused by the wrong barrier usage (my mistakes; see commits ). It's hard to see those bugs, because they don't show up and don't have any side-effect on most of the GPUs (I'm sure about AMD GPUs with official AMD APP and drivers). I hope they'll help with Timber OSX.

---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-01-09 17:38:43 UTC  
> Url: https://github.com/boostorg/compute/pull/548#issuecomment-170263945  

Unfortunately, links to test fails stopped working yesterday. I don't know if this is a temporary situation.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2016-01-13 18:57:29 UTC  
> Url: https://github.com/boostorg/compute/pull/548#issuecomment-171397542  

Thanks for fixing these! Yeah, the test runners seem to be turning over again. Hopefully there will be a new build soon.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2016-01-17 12:24:35 UTC  
> Url: https://github.com/boostorg/compute/pull/548#issuecomment-172318905  

There was a new build and my fixes worked. I'll do more if I get more free time ;)

---
