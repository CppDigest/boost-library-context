# #2 Cherry pick old changes from develop. [Closed]

> Username: danieljames  
> Created at: 2014-05-22 18:17:03 UTC  
> Updated at: 2017-11-06 02:16:25 UTC  
> Closed at: 2017-11-06 02:16:25 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/2  



---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-07-03 21:22:02 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/2#issuecomment-118427549  

Could this be merged please? As Boost.Test has been merged to master, most tests are failing now:  
http://www.boost.org/development/tests/master/developer/ptr_container.html  
  
This is fixed by  
https://github.com/danieljames/ptr_container/commit/543953a7134f2cd5df87a25234fbab5b06684cf4.

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-08-25 18:26:50 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/2#issuecomment-134693747  

I cherry-picked just the test fix. I'd rather someone else checked the other commits.  
  
Btw. there are more unmerged changes at:  
  
https://github.com/danieljames/ptr_container/tree/cherry-pick2

---

## Comment 3

> Username: pdimov  
> Created_at: 2017-11-06 02:16:25 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/2#issuecomment-342028985  

I went ahead and merged develop to master.

---
