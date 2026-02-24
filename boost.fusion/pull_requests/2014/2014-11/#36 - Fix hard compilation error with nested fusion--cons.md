# #36 Fix hard compilation error with nested fusion::cons. [Merged]

> Username: ldionne  
> Created at: 2014-11-11 15:47:59 UTC  
> Updated at: 2014-11-11 21:53:39 UTC  
> Merged at: 2014-11-11 21:53:39 UTC  
> Closed at: 2014-11-11 21:53:39 UTC  
> Url: https://github.com/boostorg/fusion/pull/36  

The title pretty much says it all. I was investigating test failures in a project of mine, and I was able to bring it down to a problem in Fusion. See the test case below and the comments on the diff.  
  
I applied the patch on a local master branch and it all passed. However, there are spurious test failures on develop at the time of writing this, so I haven't tested there.

---

## Comment 1

> Username: ldionne  
> Created_at: 2014-11-11 15:51:49 UTC  
> Url: https://github.com/boostorg/fusion/pull/36#discussion_r20158245  

IIUC, instantiating `is_convertible<Sequence, cons>` caused us to recursively try to instantiate this constructor. The second way around, since we were in the process of instantiating `is_convertible<Sequence, cons>`, there was a hard error somewhere in the MPL when we tried to fetch `is_constructible<Sequence, cons>::value` of the still incomplete `is_constructible<Sequence, cons>`.

---
