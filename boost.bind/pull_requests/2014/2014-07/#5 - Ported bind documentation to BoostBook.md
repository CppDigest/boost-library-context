# #5 Ported bind documentation to BoostBook [Merged]

> Username: K-ballo  
> Created at: 2014-07-27 18:52:35 UTC  
> Updated at: 2015-01-22 19:56:30 UTC  
> Merged at: 2015-01-22 19:51:47 UTC  
> Closed at: 2015-01-22 19:51:47 UTC  
> Url: https://github.com/boostorg/bind/pull/5  

First draft, request for comments.  
  
Links to external Boost libraries missing, I'm not sure how those should be done. Should they just point to the online boost doc, or try to point to the local version?  
  
Generated HTML files added temporarily to the PR as a convenience.

---

## Comment 1

> Username: K-ballo  
> Created_at: 2014-08-08 13:30:51 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-51600293  

Added missing links. Used `@boost:` for links to external Boost libraries. Replaced links to MSDN with links to cppreference.com

---

## Comment 2

> Username: pdimov  
> Created_at: 2014-08-21 11:33:12 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52908162  

Thank you for this work. I've just deleted the `ref` documentation from `bind` though (as it's now in core), which has invalidated the request. Sorry if that causes any problems. If you update the pull request, can you please not add the html directory to it? I have quickbook set up here.

---

## Comment 3

> Username: K-ballo  
> Created_at: 2014-08-21 13:01:18 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52916013  

Rebased, removed html directory, updated links to `ref`.

---

## Comment 4

> Username: K-ballo  
> Created_at: 2014-08-21 19:25:38 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52969454  

Before merging this, maybe we should consider that there would be a name clash with the `qbk` files for `mem_fn` (which I intend to convert too at some point).

---

## Comment 5

> Username: pdimov  
> Created_at: 2014-08-21 19:28:50 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52969902  

Perhaps we can leave `bind.qbk` in `doc` and move the other files into `doc/bind`? The `mem_fn` ones would then go into `doc/mem_fn`.

---

## Comment 6

> Username: K-ballo  
> Created_at: 2014-08-21 19:30:41 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52970156  

Makes sense, I will do that change.

---

## Comment 7

> Username: K-ballo  
> Created_at: 2014-08-21 21:20:49 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-52986526  

Went ahead and completed the port. Let me know if anything needs changing.

---

## Comment 8

> Username: pdimov  
> Created_at: 2015-01-22 19:56:30 UTC  
> Url: https://github.com/boostorg/bind/pull/5#issuecomment-71087307  

Thank you for your work, and sorry that it took me so long to get to it.

---
