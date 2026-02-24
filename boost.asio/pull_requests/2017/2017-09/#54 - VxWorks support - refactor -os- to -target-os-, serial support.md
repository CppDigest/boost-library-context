# #54 VxWorks support : refactor <os> to <target-os>, serial support [Closed]

> Username: kuhlenough  
> Created at: 2017-09-03 10:20:15 UTC  
> Updated at: 2018-05-01 12:37:52 UTC  
> Closed at: 2018-05-01 12:37:52 UTC  
> Url: https://github.com/boostorg/asio/pull/54  

Change many \<os\>NT to \<target-os\>windows,  
and a fix a few instances of LINUX as well.  
This enables cross build of VxWorks, with non-conflicting \<target-os\>vxworks

---

## Comment 1

> Username: kuhlenough  
> Created_at: 2017-09-25 17:57:24 UTC  
> Url: https://github.com/boostorg/asio/pull/54#issuecomment-331962193  

I have further changes to the code itself, but have been waiting on this pull request before submitting those.  
Will this pull be accepted soon?    
Perhaps you would like the additional changes in this pull?

---

## Comment 2

> Username: kuhlenough  
> Created_at: 2018-01-11 04:09:24 UTC  
> Url: https://github.com/boostorg/asio/pull/54#issuecomment-356819119  

This pull now changes all \<os\> instances to \<target-os\> to cleanly updates all of ASIO to current jam syntax, and allow not only **vxworks** to build, but should enable other cross build targets.  
  
The other commit (6f1cd9b994356ce7847dff5d659ec325b8dbcfbb) updates the code specifically for VxWorks.

---

## Comment 3

> Username: kuhlenough  
> Created_at: 2018-05-01 12:37:52 UTC  
> Url: https://github.com/boostorg/asio/pull/54#issuecomment-385661335  

I'm closing this pull, will open a clean one with Vxworks changes only since the Jamfile.v2 issues have been addressed.

---
