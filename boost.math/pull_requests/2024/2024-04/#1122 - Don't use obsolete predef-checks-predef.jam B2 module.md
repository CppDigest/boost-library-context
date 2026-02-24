# #1122 Don't use obsolete predef/checks/predef.jam B2 module. [Merged]

> Username: grafikrobot  
> Created at: 2024-04-19 14:33:46 UTC  
> Updated at: 2024-04-22 06:41:53 UTC  
> Merged at: 2024-04-22 06:41:53 UTC  
> Closed at: 2024-04-22 06:41:53 UTC  
> Url: https://github.com/boostorg/math/pull/1122  



---

## Review 1 [Approved]

> Username: mborland  
> Created_at: 2024-04-22 06:41:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/math/pull/1122#pullrequestreview-2013879238  

There's a ton of CI failures on this but they all seem to be issue with ubuntu's mirrors:  
  
```  
Failed to fetch http://archive.ubuntu.com/ubuntu/dists/jammy-updates/main/binary-amd64/Packages.gz  File has unexpected size (2018947 != 2018948). Mirror sync in progress? [IP: 91.189.91.83 80]  
```  
  
Merging because it should be safe.

---
