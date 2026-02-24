# #196 Fix/closing iterator for empty ranges [Merged]

> Username: mkaravel  
> Created at: 2015-01-22 15:02:50 UTC  
> Updated at: 2015-02-03 20:00:37 UTC  
> Merged at: 2015-01-23 04:49:06 UTC  
> Closed at: 2015-01-23 04:49:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/196  

This PR fixes a bug in the `closing_iterator` when it is applied to an empty range  
  
The `closing_iterator` was not working for empty ranges as it was trying to append the first range item of an empty range to the end of the range.  
  
The proposed fix changes the index of the last item in the closing iterator's range to be 0 instead of 1 when the range is empty.

---
