# #11 Explicitly initialize m_implicit [Closed]

> Username: tempoz  
> Created at: 2015-02-25 15:23:55 UTC  
> Updated at: 2015-04-13 09:26:29 UTC  
> Closed at: 2015-04-13 09:26:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/11  

m_implicit was previously uninitialized in this constructor, leading to possibly inconsistent functionality. Instead, we now initialize it to false. This issue is related to Coverity CID10559 and was uncovered by Coverity.

---

## Comment 1

> Username: vprus  
> Created_at: 2015-04-13 09:26:29 UTC  
> Url: https://github.com/boostorg/program_options/pull/11#issuecomment-92289631  

Zoey,  
  
I've applied this patch, thanks!

---
