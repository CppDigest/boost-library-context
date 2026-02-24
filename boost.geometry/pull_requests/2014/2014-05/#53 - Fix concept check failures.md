# #53 [geometry][test] Fix concept check failures. [Merged]

> Username: smspillaz  
> Created at: 2014-05-29 16:10:24 UTC  
> Updated at: 2014-05-30 12:45:41 UTC  
> Merged at: 2014-05-29 20:07:15 UTC  
> Closed at: 2014-05-29 20:07:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/53  

The functions in arithmetic.hpp would check the concepts Point and ConstPoint  
on Point2, instead of checking Point on Point1 and ConstPoint on Point2.  
  
This would cause concept check failures when attempting to operate from  
a ConstPoint.  
  
(boost-trac: #10077)

---

## Comment 1

> Username: brunolalande  
> Created_at: 2014-05-29 20:06:31 UTC  
> Updated_at: 2014-05-30 12:45:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/53#issuecomment-44578378  

This was most likely a mistake on my end when I wrote this, and the change + tests look good. In the light of the conversation in the other (closed) PR, I am now merging this one. Thanks for this!

---
