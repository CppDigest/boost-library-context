# #83 fix comments about boost::detail::vector_matrix [Merged]

> Username: e-kwsm  
> Created at: 2016-11-29 05:20:32 UTC  
> Updated at: 2018-10-18 13:49:28 UTC  
> Merged at: 2018-10-17 17:38:12 UTC  
> Closed at: 2018-10-17 17:38:12 UTC  
> Url: https://github.com/boostorg/graph/pull/83  

`vector_matrix` does not seem non-copyable, and a non-copyable object should be passed by _reference_ or _move_ but not _value_.  
Anyway, this comment is confusing.

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2017-02-25 11:09:47 UTC  
> Url: https://github.com/boostorg/graph/pull/83#issuecomment-282477168  

Sorry, I mistook.

---

## Comment 2

> Username: anadon  
> Created_at: 2018-08-31 18:11:57 UTC  
> Url: https://github.com/boostorg/graph/pull/83#issuecomment-417747950  

I'm helping out with the PR backlog. Looks like you have a documentation change.  This will be in the second batch of merged PR's. This is to let you know and help me prioritize PR's.

---

## Comment 3

> Username: anadon  
> Created_at: 2018-10-15 19:55:48 UTC  
> Url: https://github.com/boostorg/graph/pull/83#issuecomment-429991038  

I'm disinclined on this PR.  I don't think the comment changes are improvements.  @jzmaddock Should this one be closed?

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2018-10-16 17:06:56 UTC  
> Url: https://github.com/boostorg/graph/pull/83#issuecomment-430317936  

Actually I think the OP is correct: the existing comment is both self-contradictory and in conflict with the code, so I'm more inclined to <shrug> and apply the patch - it would of course be better to fix the comment so that it reflects the original intent, but as no one (including the original author probably after all this time!) knows what that is, this might be the next best thing?

---

## Comment 5

> Username: anadon  
> Created_at: 2018-10-16 18:20:46 UTC  
> Url: https://github.com/boostorg/graph/pull/83#issuecomment-430344026  

Well, better is better than nothing.  Merge it in then?

---
