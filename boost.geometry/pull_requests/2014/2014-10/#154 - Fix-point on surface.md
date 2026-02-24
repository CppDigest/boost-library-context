# #154 Fix/point on surface [Merged]

> Username: awulkiew  
> Created at: 2014-10-10 13:47:25 UTC  
> Updated at: 2014-10-10 18:50:32 UTC  
> Merged at: 2014-10-10 18:31:48 UTC  
> Closed at: 2014-10-10 18:31:48 UTC  
> Url: https://github.com/boostorg/geometry/pull/154  

Fix for https://svn.boost.org/trac/boost/ticket/10643.  
  
The issue is caused by numerical errors for big FP values in centroid default strategy (bashein_detmer) which internally stores values as big as squares of coordinates.  
  
This PR replaces bashein-detmer with arithmetic mean which applied to extremes should give good results. I plan to add more tests to be sure about it.  
  
If everything was ok should a function `calculate_centroid()` be removed?  
  
As a side note. This shows that a `centroid()` function most certainly doesn't work in all cases but a fix for this function should be a part of other PR.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-10-10 14:24:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/154#issuecomment-58662486  

Test added, everything seems to be ok.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2014-10-10 17:52:01 UTC  
> Url: https://github.com/boostorg/geometry/pull/154#issuecomment-58691716  

Thanks!  
Could you also update the release notes? (incl. ticket number)?  
I'm OK for merging.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2014-10-10 18:31:38 UTC  
> Url: https://github.com/boostorg/geometry/pull/154#issuecomment-58697473  

Of course, I'll add release notes and remove the unneeded function after merging.

---
