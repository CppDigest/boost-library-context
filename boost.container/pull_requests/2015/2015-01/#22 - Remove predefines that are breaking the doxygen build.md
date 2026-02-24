# #22 Remove predefines that are breaking the doxygen build. [Closed]

> Username: danieljames  
> Created at: 2015-01-23 20:51:52 UTC  
> Updated at: 2015-01-26 22:59:33 UTC  
> Closed at: 2015-01-26 22:59:33 UTC  
> Url: https://github.com/boostorg/container/pull/22  

I have no idea why they're breaking the build, but the macros appear to be defined in the appropriate header, so maybe they're not needed anyway?  
  
Btw. to replicate the failure you need to delete the temporary files under bin.v2, as boost.build doesn't detect the failure, and will continue with the old copies from the last successful build. I'm using doxygen 1.8.8, maybe this is fixed in later versions?

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2015-01-25 23:23:36 UTC  
> Url: https://github.com/boostorg/container/pull/22#issuecomment-71399469  

There was an error in the PREDEFINED string. Let me know if this can fix the issue.  
  
https://github.com/boostorg/container/commit/8c686218033d05711edb695e53dd0d01b6fe9f2e

---

## Comment 2

> Username: danieljames  
> Created_at: 2015-01-26 22:59:33 UTC  
> Url: https://github.com/boostorg/container/pull/22#issuecomment-71556886  

Seems to be working now.

---
