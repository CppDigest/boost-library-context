# #26 Remove dependency on boost::prior. [Merged]

> Username: Lastique  
> Created at: 2017-07-09 15:34:14 UTC  
> Updated at: 2017-07-17 17:50:37 UTC  
> Merged at: 2017-07-09 15:59:19 UTC  
> Closed at: 2017-07-09 15:59:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/26  

This allows to use `boost::reverse_iterator` in `next_prior.hpp`. It also weakens the dependency on Boost.Utility, although not removes it.

---

## Comment 1

> Username: eldiener  
> Created_at: 2017-07-09 15:38:19 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-313927249  

I don't see the point of removing this dependency with hand-written code at all. I think Boost libraries should use other Boost libraries rather than rolling their own code.

---

## Comment 2

> Username: Lastique  
> Created_at: 2017-07-09 15:40:53 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-313927405  

The point is that currently `boost::reverse_iterator` cannot be used in `boost::prior` implementation because that would cause circular dependency. See comments to https://github.com/boostorg/utility/pull/30 for more details as to why that could be useful.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-07-09 15:42:18 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-313927487  

And also, for the functionality `boost::prior` provides in `boost::reverse_iterator` it seems rather heavy, IMHO.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-07-14 13:05:52 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-315354874  

@eldiener, could you merge this PR, https://github.com/boostorg/iterator/pull/27 and https://github.com/boostorg/iterator/pull/28 to master? The tests are passing, and I'd like to merge the dependent changes in `next_prior.hpp` to master as well before 1.65.  
  
Thanks.

---

## Comment 5

> Username: eldiener  
> Created_at: 2017-07-17 16:10:35 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-315800939  

I merged your change to 'master'. I would like to have waited for all the 'develop' tests to have recycled, since their have been a bunch of them that have not been running for awhile, but I think your change is correct based on the 'develop' tests that have run recently.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-07-17 17:50:37 UTC  
> Url: https://github.com/boostorg/iterator/pull/26#issuecomment-315829704  

Thank you, Edward.

---
