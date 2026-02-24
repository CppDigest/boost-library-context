# #12 Eliminate GCC 4.9.2 warning "'result' may be used uninitialized" [Merged]

> Username: plopresti  
> Created at: 2015-04-03 23:22:40 UTC  
> Updated at: 2015-04-15 15:19:02 UTC  
> Merged at: 2015-04-15 07:01:13 UTC  
> Closed at: 2015-04-15 07:01:14 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/12  

With "-Wall", the current release of GCC complains about the potential use of an uninitialized variable. This problem and fix are essentially identical to https://svn.boost.org/trac/boost/ticket/4946.

---

## Comment 1

> Username: apolukhin  
> Created_at: 2015-04-15 07:01:23 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/12#issuecomment-93225636  

Thanks for the patch!

---

## Comment 2

> Username: apolukhin  
> Created_at: 2015-04-15 07:11:38 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/12#issuecomment-93230633  

All the changes must go to the `develop` branch first. I've reverted this commit and moved the changes to develop: https://github.com/boostorg/lexical_cast/commit/bdc355759e3259209f1107ae21babee6dcf8d80b

---

## Comment 3

> Username: plopresti  
> Created_at: 2015-04-15 15:19:02 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/12#issuecomment-93445452  

Yeah, I realized that sometime after I created the patch. (My git  
experience is somewhat limited as of yet.) Sorry about that.  
  
Thanks for accepting.

---
