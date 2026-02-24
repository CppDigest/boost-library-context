# #403 If the vswhere lookup fails, keep searching for others [Merged]

> Username: teeks99  
> Created at: 2019-03-05 12:57:47 UTC  
> Updated at: 2021-10-02 21:19:12 UTC  
> Merged at: 2019-03-12 17:51:25 UTC  
> Closed at: 2019-03-12 17:51:25 UTC  
> Url: https://github.com/boostorg/build/pull/403  

Exiting with a non-zero errorlevel caused it to exit from guess_toolset.cmd, we want it to keep looking for other versions.  
  
How did bootstrap keep working on machines without VS2017/2019 installed?

---

## Comment 1

> Username: teeks99  
> Created_at: 2019-03-05 13:04:59 UTC  
> Url: https://github.com/boostorg/build/pull/403#issuecomment-469672241  

Let me test this a bit more before merging, I just wanted to get it out there for the sake of conversation in #401

---

## Comment 2

> Username: teeks99  
> Created_at: 2019-03-05 22:35:41 UTC  
> Updated_at: 2019-03-05 22:39:03 UTC  
> Url: https://github.com/boostorg/build/pull/403#issuecomment-469886253  

This fixes what happens when vswhere fails.   
  
Tested with:  
*  VS2019 only machine, cmd.exe, vswhere  
*  VS2019 only machine, cmd.exe, no-vshwere (fails as expected)  
*  VS2019 only machine, 2019 command prompt, vswhere, VS160COMNTOOLS set  
*  VS2019 only machine, 2019 command prompt, no-vswhere, VS160COMNTOOLS set (Fixes this case!)  
*  VS2015 only machine, cmd.exe VS140COMNTOOLS set  
*  Machine with vs2010 through 2019, cmd.exe (VS100, VS110, VS120, VS140 COMNTOOLS set)  
  
In all cases, bootstrap.bat completed successfully.

---

## Comment 3

> Username: swatanabe  
> Created_at: 2019-03-06 00:02:56 UTC  
> Url: https://github.com/boostorg/build/pull/403#issuecomment-469909931  

AMDG  
  
Aha!  That makes more sense than your first patch.  
Looks good to me.  
  
In Christ,  
Steven Watanabe

---
