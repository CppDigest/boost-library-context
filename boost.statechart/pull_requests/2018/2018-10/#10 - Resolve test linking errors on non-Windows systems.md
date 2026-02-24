# #10 Resolve test linking errors on non-Windows systems [Merged]

> Username: Lastique  
> Created at: 2018-10-27 20:30:51 UTC  
> Updated at: 2018-10-28 06:56:56 UTC  
> Merged at: 2018-10-28 02:31:41 UTC  
> Closed at: 2018-10-28 02:31:41 UTC  
> Url: https://github.com/boostorg/statechart/pull/10  

This PR only fixes the linking problem. The DllTestNormal test still fails at runtime, but, as fas as I understand, this test should not work on Windows either. boost/statechart/detail/rtti_policy.hpp in non-RTTI mode needs to be reworked to fix this test.  
  
Related to https://github.com/boostorg/statechart/issues/9.

---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-27 20:34:54 UTC  
> Url: https://github.com/boostorg/statechart/pull/10#issuecomment-433653209  

Thank you for getting to this so quickly.   Eventually I will get back around to adding the CMT CI suite to this repo.  It's in most of the others, however I'm adopting the expanded one from Boost.Assign after advice from @pdimov.

---
