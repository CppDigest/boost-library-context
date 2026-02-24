# #3 Foreach uses makes undocumented assumptions about type traits types [Merged]

> Username: jzmaddock  
> Created at: 2015-01-22 17:43:56 UTC  
> Updated at: 2015-03-05 00:33:56 UTC  
> Merged at: 2015-01-23 02:42:11 UTC  
> Closed at: 2015-01-23 02:42:11 UTC  
> Url: https://github.com/boostorg/foreach/pull/3  

Exposed by ongoing type traits rewrite / simplification: there is no documented inheritance from an mpl type to a type traits type.  This is probably the simplest fix for the problem.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2015-01-23 02:42:16 UTC  
> Url: https://github.com/boostorg/foreach/pull/3#issuecomment-71139243  

Thanks!

---

## Comment 2

> Username: ericniebler  
> Created_at: 2015-03-04 21:41:21 UTC  
> Url: https://github.com/boostorg/foreach/pull/3#issuecomment-77255475  

@jzmaddock I just tried merging this to master (locally), and it caused failures. Am I right in holding this change back for the upcoming release?

---

## Comment 3

> Username: ericniebler  
> Created_at: 2015-03-05 00:33:56 UTC  
> Url: https://github.com/boostorg/foreach/pull/3#issuecomment-77282347  

Nevermind. I was testing the wrong thing. This has been merged to master.

---
