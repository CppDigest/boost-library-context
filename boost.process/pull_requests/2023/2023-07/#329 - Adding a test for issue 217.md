# #329 Adding a test for issue 217 [Closed]

> Username: SilverPlate3  
> Created at: 2023-07-08 11:22:10 UTC  
> Updated at: 2023-08-19 10:17:26 UTC  
> Closed at: 2023-08-19 10:17:26 UTC  
> Url: https://github.com/boostorg/process/pull/329  

Continuing conversation in issue: https://github.com/boostorg/process/issues/217  
Indeed the bug is solved.   
Here I added the test, the maintainer asked for.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2023-07-12 01:49:31 UTC  
> Url: https://github.com/boostorg/process/pull/329#issuecomment-1631729734  

Quick note that I probably probably won't merge this until the boost 1.83 release is out on 09.08, see the calender here https://www.boost.org/development/index.html  
  
I try to keep the develop branch clean just in case I need to hotfix something.  
  
  
Otherwise looks lit works, and confirms #217 is fixed. Not sure what is going on on FreeBSD

---

## Comment 2

> Username: klemens-morgenstern  
> Created_at: 2023-08-15 03:48:44 UTC  
> Url: https://github.com/boostorg/process/pull/329#issuecomment-1678388810  

If you want this merge please make it a single commit and use the sparring_partner, not a hard coded `sleep` command.

---
