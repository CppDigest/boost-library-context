# #3 Fix Ticket #11123: Accumulators: adjust type fusion::nil to fusion::nil_ [Merged]

> Username: jaredgrubb  
> Created at: 2015-03-21 23:07:04 UTC  
> Updated at: 2015-03-23 17:17:17 UTC  
> Merged at: 2015-03-23 17:17:15 UTC  
> Closed at: 2015-03-23 17:17:15 UTC  
> Url: https://github.com/boostorg/accumulators/pull/3  

Fusion renamed the actual type to 'nil_' back in SVN r81628 in order to make it friendlier to ObjC++ (where "nil" is a keyword, sadly). This patch adjusts accumulators to match.

---

## Comment 1

> Username: ericniebler  
> Created_at: 2015-03-23 17:17:17 UTC  
> Url: https://github.com/boostorg/accumulators/pull/3#issuecomment-85103662  

Thanks!

---
