# #12 Value-initialize m_desc [Closed]

> Username: tempoz  
> Created at: 2015-02-25 17:36:46 UTC  
> Updated at: 2015-04-13 09:27:18 UTC  
> Closed at: 2015-04-13 09:27:18 UTC  
> Url: https://github.com/boostorg/program_options/pull/12  

Value-initialize m_desc (to NULL) in this constructor to avoid uninitialized memory situations. This issue is related to Coverity CID12523 and was uncovered by Coverity.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-04-13 09:27:18 UTC  
> Url: https://github.com/boostorg/program_options/pull/12#issuecomment-92289903  

Zoey,  
  
thanks for the patch, it was applied.

---
