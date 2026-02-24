# #27 VF2, fix assumption that index maps are default constructible. [Merged]

> Username: jakobandersen  
> Created at: 2014-12-18 15:40:58 UTC  
> Updated at: 2016-11-01 02:10:23 UTC  
> Merged at: 2016-11-01 02:10:23 UTC  
> Closed at: 2016-11-01 02:10:23 UTC  
> Url: https://github.com/boostorg/graph/pull/27  

The fix moves construction of some internal data structures into the relevant initialization list. The added test case triggers the issue in the previous revisions.

---

## Comment 1

> Username: murraycu  
> Created_at: 2016-08-12 07:56:22 UTC  
> Url: https://github.com/boostorg/graph/pull/27#issuecomment-239384223  

This does look much cleaner anyway.  
  
Maybe that test should have a comment saying what it is testing.  
  
(I'm not the maintainer.)

---
