# #11 Bugfix/tst map [Merged]

> Username: vtnerd  
> Created at: 2014-04-13 16:09:34 UTC  
> Updated at: 2014-06-14 07:59:01 UTC  
> Merged at: 2014-04-14 00:51:47 UTC  
> Closed at: 2014-04-14 00:51:47 UTC  
> Url: https://github.com/boostorg/spirit/pull/11  

This was posted on the mailing list a while back, but sat around. If the tst_map matches on the first character but fails on the second character, the iterator position is not properly rolled back to the position original provided as an argument. This updates the test to catch the issue, and fixes the bug in tst_map

---
