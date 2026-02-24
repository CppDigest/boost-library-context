# #5 Fix Ticket #11134: Adjust boost::fusion::nil to boost::fusion::nil_ [Merged]

> Username: jaredgrubb  
> Created at: 2015-03-22 19:38:55 UTC  
> Updated at: 2017-05-23 13:05:35 UTC  
> Merged at: 2016-04-08 14:47:48 UTC  
> Closed at: 2016-04-08 14:47:48 UTC  
> Url: https://github.com/boostorg/msm/pull/5  

Fusion renamed the actual type to 'nil_' back in SVN r81628 in order to make it friendlier to ObjC++ (where "nil" is a keyword, sadly). This patch adjusts accumulators to match.  
  
Ran "b2" in the msm/test directory and things still look good.

---

## Comment 1

> Username: jaredgrubb  
> Created_at: 2015-11-08 19:45:06 UTC  
> Url: https://github.com/boostorg/msm/pull/5#issuecomment-154860765  

Can someone take a look at this patch? It's been outstanding for 6 months or so now.

---

## Comment 2

> Username: jaredgrubb  
> Created_at: 2016-05-15 20:33:19 UTC  
> Url: https://github.com/boostorg/msm/pull/5#issuecomment-219308893  

Thanks so much!

---

## Comment 3

> Username: arrtchiu  
> Created_at: 2017-05-23 13:05:35 UTC  
> Url: https://github.com/boostorg/msm/pull/5#issuecomment-303391344  

Thanks for fixing this, just hit the same issue in my code! I see it's been fixed for a year already - is there any chance of getting this into a proper boost release?

---
