# #506 - Fixes merged into "develop" don't show up in release packages [Open]

> Username: Gei0r  
> Created at: 2021-04-21 08:16:47 UTC  
> Updated at: 2023-12-26 13:46:01 UTC  
> Url: https://github.com/boostorg/boost/issues/506  

Hi, I have concerns about the following PRs:  
  
- ~~https://github.com/boostorg/build/pull/651 merged on Sep 8, 2020~~ (nevermind, this one made it)  
- https://github.com/boostorg/boost/pull/494 merged on Mar 20, 2021  
  
Both fixes don't show up in the release tarballs for boost 1.76.0, released on Apr 16, 2021.  
  
How do we make sure the fixes make it into the next release?

---

## Comment 1

> Username: tanzislam  
> Created at: 2023-12-26 13:46:00 UTC  
> Url: https://github.com/boostorg/boost/issues/506#issuecomment-1869557625  

#494 eventually made it in commit e2ae786 (included in Boost v1.81.0 onwards).
