# #147 Change _pRawDllMain and related types to use HINSTANCE instead of HANDLE [Merged]

> Username: Lastique  
> Created at: 2017-09-16 17:56:53 UTC  
> Updated at: 2017-09-16 23:28:57 UTC  
> Merged at: 2017-09-16 20:36:02 UTC  
> Closed at: 2017-09-16 20:36:02 UTC  
> Url: https://github.com/boostorg/thread/pull/147  

In MSVC-9 MFC sources, _pRawDllMain and ExtRawDllMain accept HINSTANCE as  
the first argument, not HANDLE. In strict mode these are different types,  
which creates the potential for ODR errors. This commit resolves that  
inconsistency.  
  
Resolves https://svn.boost.org/trac10/ticket/12323.

---

## Comment 1

> Username: viboes  
> Created_at: 2017-09-16 20:35:00 UTC  
> Url: https://github.com/boostorg/thread/pull/147#issuecomment-329993275  

Andrey, thank you very much for your help.

---
