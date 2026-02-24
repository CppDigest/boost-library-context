# #184 GHA: Workaround node20 GLIBC issues in older Ubuntu containers [Merged]

> Username: Flamefire  
> Created at: 2024-08-25 11:55:25 UTC  
> Updated at: 2024-08-25 12:02:52 UTC  
> Merged at: 2024-08-25 12:02:49 UTC  
> Closed at: 2024-08-25 12:02:49 UTC  
> Url: https://github.com/boostorg/nowide/pull/184  

Avoid errors like  
> /__e/node20/bin/node: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.28' not found (required by /__e/node20/bin/node)  
when using Ubuntu 16/18 containers with the @v4 actions

---
