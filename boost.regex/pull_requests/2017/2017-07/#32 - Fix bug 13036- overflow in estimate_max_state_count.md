# #32 Fix bug 13036: overflow in estimate_max_state_count [Closed]

> Username: bminard  
> Created at: 2017-07-03 21:45:32 UTC  
> Updated at: 2017-07-31 18:20:40 UTC  
> Closed at: 2017-07-31 18:20:40 UTC  
> Url: https://github.com/boostorg/regex/pull/32  

Address Bug 13036 [Boost.Regex: Integer overflow during calculation of max_state_count](https://svn.boost.org/trac10/ticket/13036).  
  
Add overflow check on states variable multiplied with itself and introduce guarantee that initialization of states can contain the full range of values potentially returned by re.size().  
  
Ran Boost.Regex test suite using g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-11) on CentOS 7-1611 using 3.10.0-514.21.2.el7.x86_64 and 3.10.0-514.21.2.el7.centos.plus.i686.  All tests passed. No warnings introduced as a result of this change.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2017-07-31 18:20:39 UTC  
> Url: https://github.com/boostorg/regex/pull/32#issuecomment-319153054  

Thanks for this, I made a very marginally different fix in https://github.com/boostorg/regex/commit/bc9b25b5d3c3784543158510c6087d41739ab64a.

---
