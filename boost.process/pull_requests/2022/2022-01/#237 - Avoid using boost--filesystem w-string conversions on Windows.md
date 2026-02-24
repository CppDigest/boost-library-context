# #237 Avoid using boost::filesystem w/string conversions on Windows [Closed]

> Username: achow101  
> Created at: 2022-01-21 21:04:01 UTC  
> Updated at: 2025-02-26 15:01:48 UTC  
> Closed at: 2025-02-26 15:01:48 UTC  
> Url: https://github.com/boostorg/process/pull/237  

Converting from wstring to string (or vice versa) with boost::filesystem requires Filesystem to be compiled. However if these conversions are avoided, then boost::filesystem will not be needed by downstream projects.  
  
A project I am working on is trying to remove boost filesystem and we noticed that doing so results in a linker error when cross compiling to windows. The root cause was identified to be that boost process for Windows ends up using some conversion functions which require boost filesystem to be compiled. This patch resolved those issues for us.

---

## Comment 1

> Username: fanquake  
> Created_at: 2022-08-05 21:21:15 UTC  
> Url: https://github.com/boostorg/process/pull/237#issuecomment-1206878808  

@klemens-morgenstern are you interested in merging this change?

---

## Comment 2

> Username: fanquake  
> Created_at: 2022-12-13 11:46:19 UTC  
> Url: https://github.com/boostorg/process/pull/237#issuecomment-1348341143  

Can this be closed after #264?

---
