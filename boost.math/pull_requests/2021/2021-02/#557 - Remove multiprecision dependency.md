# #557 Remove multiprecision dependency [Merged]

> Username: mborland  
> Created at: 2021-02-25 16:40:02 UTC  
> Updated at: 2021-02-27 12:58:00 UTC  
> Merged at: 2021-02-25 20:50:17 UTC  
> Closed at: 2021-02-25 20:50:17 UTC  
> Url: https://github.com/boostorg/math/pull/557  

Removes the only dependency on Boost.Multiprecision. This has not gone out in a release, and the only place it is called is a benchmark I wrote.

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-02-25 16:43:43 UTC  
> Url: https://github.com/boostorg/math/pull/557#issuecomment-786040188  

Looks a good diff to me.  
  
Hopefully we can remove all dependencies on the rest of boost and then make a boost.math package which is consumable from (say) a github download page.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-25 16:46:08 UTC  
> Url: https://github.com/boostorg/math/pull/557#issuecomment-786041839  

That is the goal. [Here is the list](https://github.com/mborland/math/issues/2) of dependencies that I pulled a couple of days ago. There are a fair amount of easy fixes.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2021-02-25 16:58:47 UTC  
> Url: https://github.com/boostorg/math/pull/557#issuecomment-786051143  

Fine by me.

---

## Comment 4

> Username: mborland  
> Created_at: 2021-02-25 20:02:06 UTC  
> Url: https://github.com/boostorg/math/pull/557#issuecomment-786164612  

@jzmaddock This one is green.

---
