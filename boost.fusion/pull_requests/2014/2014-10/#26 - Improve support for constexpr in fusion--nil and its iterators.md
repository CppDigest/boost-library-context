# #26 Improve support for constexpr in fusion::nil and its iterators. [Merged]

> Username: ldionne  
> Created at: 2014-10-27 14:17:44 UTC  
> Updated at: 2014-10-28 00:15:05 UTC  
> Merged at: 2014-10-27 14:29:56 UTC  
> Closed at: 2014-10-27 14:29:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/26  

`fusion::nil` is a pretty trivial type, and it would be nice to make it constexpr-constructible. The same goes for its iterators. The patch was tested with Apple's clang 6.0.

---

## Comment 1

> Username: djowel  
> Created_at: 2014-10-27 14:29:50 UTC  
> Url: https://github.com/boostorg/fusion/pull/26#issuecomment-60601256  

Thanks Louis. Indeed more constexpr the better :-)

---

## Comment 2

> Username: Flast  
> Created_at: 2014-10-27 16:24:04 UTC  
> Url: https://github.com/boostorg/fusion/pull/26#issuecomment-60621895  

It seems marged into master branch. IIRC, master is still open, but is this exepcted?  
Another constexpr supports didn't merged into master yet.

---

## Comment 3

> Username: djowel  
> Created_at: 2014-10-27 23:58:56 UTC  
> Url: https://github.com/boostorg/fusion/pull/26#issuecomment-60690416  

No this is not expected. My bad. We should be merging to develop, not master. I'll fix it.

---

## Comment 4

> Username: djowel  
> Created_at: 2014-10-28 00:15:05 UTC  
> Url: https://github.com/boostorg/fusion/pull/26#issuecomment-60691815  

I merged it back to develop. I'll let this one slip for now (the changes are innocuous, it seems). Next time I'll be more careful that merges only happen to develop. Thanks for spotting that, Kohei. And thanks again for the PR, Louis.

---
