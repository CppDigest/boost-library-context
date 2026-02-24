# #224 Fix: replace access method for accessing the front element of rings [Merged]

> Username: mkaravel  
> Created at: 2015-02-13 13:22:01 UTC  
> Updated at: 2015-02-13 15:26:22 UTC  
> Merged at: 2015-02-13 15:21:45 UTC  
> Closed at: 2015-02-13 15:21:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/224  

Change access of a ring's front range element from using the `.front()` method by the `range::front` Boost.Geometry utility function.

---

## Comment 1

> Username: awulkiew  
> Created_at: 2015-02-13 14:17:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/224#issuecomment-74259459  

If there is a problem with added not being properly used as an object of a type adapted to Ring concept then why only front() was changed? AFAIU empty() and back() should also be replaced.

---

## Comment 2

> Username: mkaravel  
> Created_at: 2015-02-13 14:19:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/224#issuecomment-74259828  

@awulkiew I agree. See the last commit.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2015-02-13 14:20:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/224#issuecomment-74259920  

@awulkiew I am referring to commit 2f5d497

---
