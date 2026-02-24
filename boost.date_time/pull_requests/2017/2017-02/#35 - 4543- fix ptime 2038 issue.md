# #35 4543: fix ptime 2038 issue [Merged]

> Username: jeking3  
> Created at: 2017-02-21 21:40:48 UTC  
> Updated at: 2018-11-26 14:46:04 UTC  
> Merged at: 2017-03-28 20:56:49 UTC  
> Closed at: 2017-03-28 20:56:49 UTC  
> Url: https://github.com/boostorg/date_time/pull/35  



---

## Comment 1

> Username: jeking3  
> Created_at: 2017-03-28 16:25:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/35#issuecomment-289825702  

Could someone please comment on why this has not been merged?  Are there any additional things that need to be done?  This issue has been open for almost 8 years, so I'm trying to understand why this isn't getting any movement here or in boost trac #4543.

---

## Comment 2

> Username: jdmairs  
> Created_at: 2017-12-22 16:35:17 UTC  
> Url: https://github.com/boostorg/date_time/pull/35#issuecomment-353632702  

The mod to var_type in time_resolution_traits.hpp has changed the behavior of boost::serialization.  My archives (built with boost 1.64) using boost::serialization (inherently using time_serialization.hpp) no longer load because the hours, minutes, etc fields are now 8 bytes instead of 4 bytes.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-12-22 19:01:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/35#issuecomment-353657239  

Sounds like we're missing a unit test somewhere.  I'll add this as an issue.

---

## Comment 4

> Username: nohajc  
> Created_at: 2018-09-20 10:51:54 UTC  
> Url: https://github.com/boostorg/date_time/pull/35#issuecomment-423138493  

Hi, I'm afraid from_time_t is broken again in the release. By the very next commit:  
https://github.com/boostorg/date_time/commit/b1dc2b306ea6abb645b0e334e3d5bb1b5ed80818#diff-3e708bf547192e1f6a2d31d6d278496f

---

## Comment 5

> Username: jeking3  
> Created_at: 2018-09-20 12:53:24 UTC  
> Url: https://github.com/boostorg/date_time/pull/35#issuecomment-423171894  

I opened https://github.com/boostorg/date_time/issues/87 to track this - I assume that accurately captures the issue?  I think all the casting in that commit needs to be inspected for correctness.

---
