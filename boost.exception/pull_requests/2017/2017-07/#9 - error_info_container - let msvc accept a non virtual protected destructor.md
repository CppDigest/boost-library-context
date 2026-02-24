# #9 error_info_container : let msvc accept a non virtual protected destructor [Closed]

> Username: pthom  
> Created at: 2017-07-08 13:38:38 UTC  
> Updated at: 2018-11-15 05:39:15 UTC  
> Closed at: 2018-11-15 05:39:15 UTC  
> Url: https://github.com/boostorg/exception/pull/9  

Hi,  
  
Here is a proposition for a patch on error_info_container : it has a non virtual protected destructor (which is legitimate). However, Visual Studio sees this as a bug, if you enable more warnings (as is often required, because the default warning level is insuffficient).  
  
For a full test and working example, see https://github.com/ivsgroup/boost_warnings_minimal_demo  
  
Important: I tested this patch on a raw boost 1.64.0 checkout, I could not test it on the develop branch.  
  
Hoping that this will be useful,  
Regards,  
Pascal Thomet

---

## Comment 1

> Username: pthom  
> Created_at: 2017-07-08 14:45:45 UTC  
> Url: https://github.com/boostorg/exception/pull/9#issuecomment-313859878  

I think the CI build failure is related to an older commit.

---
