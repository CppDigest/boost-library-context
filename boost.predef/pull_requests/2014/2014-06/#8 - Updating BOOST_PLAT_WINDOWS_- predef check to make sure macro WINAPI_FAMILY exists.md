# #8 Updating BOOST_PLAT_WINDOWS_* predef check to make sure macro WINAPI_FAMILY exists [Merged]

> Username: stgates  
> Created at: 2014-06-11 00:13:34 UTC  
> Updated at: 2014-06-30 19:31:31 UTC  
> Merged at: 2014-06-11 02:00:26 UTC  
> Closed at: 2014-06-11 02:00:26 UTC  
> Url: https://github.com/boostorg/predef/pull/8  

In my previous change to be safe I should have also made sure that the WINAPI_FAMILY macro exists before comparing to see if equal to WINAPI_FAMILY_PHONE_APP, for example.

---

## Comment 1

> Username: stgates  
> Created_at: 2014-06-11 02:01:03 UTC  
> Url: https://github.com/boostorg/predef/pull/8#issuecomment-45693942  

Thanks!

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2014-06-11 02:11:05 UTC  
> Url: https://github.com/boostorg/predef/pull/8#issuecomment-45694415  

Next time please do a request on the develop branch. I didn't notice the  
branch and now I have a merge in the "wrong" direction :-(  
  
On Tue, Jun 10, 2014 at 9:01 PM, stgates notifications@github.com wrote:  
  
> Thanks!  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/predef/pull/8#issuecomment-45693942.  
  
##   
  
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 3

> Username: stgates  
> Created_at: 2014-06-11 02:12:24 UTC  
> Url: https://github.com/boostorg/predef/pull/8#issuecomment-45694481  

Ah yes that was my accident, I wish it would could just default to the 'develop' branch.

---
