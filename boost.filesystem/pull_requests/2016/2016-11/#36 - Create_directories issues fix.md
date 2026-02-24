# #36 Create_directories issues fix  [Closed]

> Username: colivi  
> Created at: 2016-11-12 09:19:48 UTC  
> Updated at: 2016-11-23 20:55:48 UTC  
> Closed at: 2016-11-23 15:32:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/36  

Hello,  
  
This is a fix for:  
- the 2 opened tickets  
     https://svn.boost.org/trac/boost/ticket/12495    
     https://svn.boost.org/trac/boost/ticket/9060  
- the crash when creating a directory on / with no sudo rights  
  
Thanks,  
Charles

---

## Comment 1

> Username: colivi  
> Created_at: 2016-11-15 07:43:24 UTC  
> Url: https://github.com/boostorg/filesystem/pull/36#issuecomment-260569574  

Hi,  
Any chance to have this PR reviewed ? :)

---

## Comment 2

> Username: Beman  
> Created_at: 2016-11-23 15:32:03 UTC  
> Url: https://github.com/boostorg/filesystem/pull/36#issuecomment-262547288  

Hi Charles,  
  
I've fixed the problem, making good use of your test cases. I reworked the fix, not because there was necessarily anything wrong with yours, but rather than I wanted to be sure my understanding of the fix was correct.  
  
Thanks,  
  
--Beman

---

## Comment 3

> Username: colivi  
> Created_at: 2016-11-23 20:55:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/36#issuecomment-262627883  

Thanks a lot Beman !!

---
