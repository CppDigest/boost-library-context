# #15 Accommodate the latest changes in Boost.Iterator. [Closed]

> Username: Lastique  
> Created at: 2014-07-19 18:36:59 UTC  
> Updated at: 2014-07-30 01:35:02 UTC  
> Closed at: 2014-07-30 01:35:02 UTC  
> Url: https://github.com/boostorg/range/pull/15  

In pull request https://github.com/boostorg/iterator/pull/5 most implementation details were moved to the boost::iterators and boost::iterators::detail namespaces. This pull request updates Boost.Range to reflect these changes.  
  
The pull request partially depends on another pull request https://github.com/boostorg/iterator/pull/6 which makes pure_traversal_tag a public component and not an implementation detail.

---

## Comment 1

> Username: neilgroves  
> Created_at: 2014-07-26 14:52:49 UTC  
> Url: https://github.com/boostorg/range/pull/15#issuecomment-50236195  

I intend to wait until the release 1.56 is done before merging this into the develop branch and performing the usual procedure to promote to master. If I have misinterpreted the urgency of this request please let me know.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-07-26 15:41:44 UTC  
> Url: https://github.com/boostorg/range/pull/15#issuecomment-50237554  

That's fine with me, thanks.

---

## Comment 3

> Username: ericniebler  
> Created_at: 2014-07-30 01:35:02 UTC  
> Url: https://github.com/boostorg/range/pull/15#issuecomment-50563726  

Whoops, I didn't see this pull request, and just made the fix myself. It's not going to interfere with 1.56 as long as it stays on develop.

---
