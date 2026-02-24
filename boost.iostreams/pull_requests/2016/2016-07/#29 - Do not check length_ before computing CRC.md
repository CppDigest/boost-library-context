# #29 Do not check length_ before computing CRC. [Merged]

> Username: joelnordell  
> Created at: 2016-07-12 23:01:42 UTC  
> Updated at: 2016-12-04 00:35:55 UTC  
> Merged at: 2016-12-04 00:35:55 UTC  
> Closed at: 2016-12-04 00:35:55 UTC  
> Url: https://github.com/boostorg/iostreams/pull/29  

This fixes a problem where the gzip_decompressor would fail the CRC  
check when reading a multipart gzip file that had been written using  
Z_FULL_FLUSH, and contains an empty part (with a 0 CRC).  
  
Including a unit test that exposes the bug.

---

## Comment 1

> Username: joelnordell  
> Created_at: 2016-07-12 23:03:08 UTC  
> Updated_at: 2016-07-12 23:05:26 UTC  
> Url: https://github.com/boostorg/iostreams/pull/29#issuecomment-232207951  

Is this the best way to submit a bugfix / patch to boostorg?

---

## Comment 2

> Username: joelnordell  
> Created_at: 2016-07-13 14:09:53 UTC  
> Url: https://github.com/boostorg/iostreams/pull/29#issuecomment-232366678  

I've also created a Trac ticket for this issue: https://svn.boost.org/trac/boost/ticket/12326

---
