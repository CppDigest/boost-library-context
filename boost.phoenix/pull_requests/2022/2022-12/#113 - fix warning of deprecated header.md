# #113 fix warning of deprecated header [Closed]

> Username: hdu-sdlzx  
> Created at: 2022-12-07 09:33:01 UTC  
> Updated at: 2024-06-28 03:36:55 UTC  
> Closed at: 2024-06-28 03:36:54 UTC  
> Url: https://github.com/boostorg/phoenix/pull/113  

We forgot to change test include path in  
commit 8b4cb05a4da41a819c8709ffef3846b7f4db0f2a ("Move debug.hpp out of core")  
  
Also move debug.cpp out of core  
  
Signed-off-by: Liu Zixian <liuzixian4@huawei.com>

---
